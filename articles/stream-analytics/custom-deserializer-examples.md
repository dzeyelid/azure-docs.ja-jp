---
title: Azure Stream Analytics ジョブ用の .NET 逆シリアライザーを使用する
description: この記事では、シリアル化の形式と、Azure Stream Analytics のクラウド ジョブとエッジ ジョブに対するカスタム .NET 逆シリアライザーが定義されているインターフェイスについて説明します。
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: f1452e56054948edffc6e9b3c98fa48d2589cb2a
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 10/29/2019
ms.locfileid: "73024934"
---
# <a name="use-net-deserializers-for-azure-stream-analytics-jobs"></a>Azure Stream Analytics ジョブ用の .NET 逆シリアライザーを使用する

カスタム .NET 逆シリアライザーを使用すると、Azure Stream Analytics ジョブで、3 つの[組み込みデータ形式](stream-analytics-parsing-json.md)に含まれない形式からデータを読み取ることができます。 この記事では、シリアル化の形式と、Azure Stream Analytics のクラウド ジョブとエッジ ジョブに対するカスタム .NET 逆シリアライザーが定義されているインターフェイスについて説明します。 また、プロトコル バッファーと CSV 形式に対する逆シリアライザーの例もあります。

## <a name="custom-net-deserializer"></a>カスタム .NET 逆シリアライザー

以下のコード サンプルのインターフェイスでは、カスタム逆シリアライザーが定義され、`StreamDeserializer<T>` が実装されています。

`UserDefinedOperator` は、すべてのカスタム ストリーミング演算子の基底クラスです。 それによって初期化される `StreamingContext` で提供されるコンテキストには、逆シリアライザーに関するすべての問題をデバッグする必要がある診断を発行するためのメカニズムが含まれます。

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

次のコード スニペットは、ストリーミング データに対する逆シリアル化です。 

スキップ可能なエラーは、`UserDefinedOperator` の Initialize メソッドを通して渡される `IStreamingDiagnostics` を使用して出力する必要があります。 すべての例外はエラーとして扱われ、逆シリアライザーが再作成されます。 特定の数のエラーが発生すると、ジョブは失敗状態になります。

`StreamDeserializer<T>` により、ストリームは `T` 型のオブジェクトに逆シリアル化されます。 次の条件を満たす必要があります。

1. T は、クラスまたは構造体です。
1. T のすべてのパブリック フィールドは、次のいずれかです
    1. [sbyte、byte、short、ushort、int、uint、long、DateTime、string、float、double] のいずれか、またはそれらと同等の Null 許容型。
    1. 同じ規則に従う別の構造体またはクラス。
    1. 同じ規則に従う `T2` 型の配列。
    1. T2 が同じ規則に従う IList`T2`。
    1. どのような再帰型も持たない。

パラメーター `stream` は、シリアル化されたオブジェクトを含むストリームです。 `Deserialize` からは、`T` インスタンスのコレクションが返されます。

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` からは、ユーザー演算子に対する診断を発行するためのメカニズムが含まれるコンテキストが提供されます。

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` は、シリアライザー、逆シリアライザー、ユーザー定義関数などのユーザー定義演算子に対する診断です。

`WriteError` を使うと、診断ログにエラー メッセージを書き込み、診断にエラーを送信することができます。

`briefMessage` は、簡単なエラー メッセージです。 このメッセージは診断に表示され、製品チームによってデバッグのために使用されます。 機密情報を含めないでください。また、メッセージが 200 文字以上にならないようにします

`detailedMessage` は、ストレージの診断ログにのみ追加される詳細なエラー メッセージです。 このメッセージは、2000 文字未満でなければなりません。

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>逆シリアライザーの例

このセクションでは、Protobuf と CSV のカスタム逆シリアライザーを記述する方法について説明します。 その他の例については、[GitHub の Azure Stream Analytics](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers) を参照してください。

### <a name="protocol-buffer-protobuf-format"></a>プロトコル バッファー (Protobuf) 形式

これは、プロトコル バッファー形式を使用する例です。

次のプロトコル バッファー定義を想定します。

```proto
syntax = "proto3";
// protoc.exe from nuget "Google.Protobuf.Tools" is used to generate .cs file from this schema definition.
// Run below command to generate the csharp class
// protoc.exe --csharp_out=. MessageBodyProto.proto

package SimulatedTemperatureSensor;
message MessageBodyProto {
    message Ambient {
      double temperature = 1;
      int64 humidity = 2;
    }

    message Machine {
      double temperature = 1;
      double pressure = 2;
    }

    Machine machine = 1;
    Ambient ambient = 2;
    string timeCreated = 3;
}
```

**Google.Protobuf.Tools** NuGet から `protoc.exe` を実行すると、定義を含む .cs ファイルが生成されます。 生成されたファイルは、ここには記載されていません。

次のコード スニペットは、生成されたファイルがプロジェクトに含まれていることを想定した逆シリアライザーの実装です。 この実装は、生成されたファイルに対するシン ラッパーにすぎません。

```csharp
    public class MessageBodyDeserializer : StreamDeserializer<SimulatedTemperatureSensor.MessageBodyProto>
    {
        public override IEnumerable<SimulatedTemperatureSensor.MessageBodyProto> Deserialize(Stream stream)
        {
            while (stream.Position < stream.Length)
            {
                yield return SimulatedTemperatureSensor.MessageBodyProto.Parser.ParseDelimitedFrom(stream);
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
        }
    }
```

### <a name="csv"></a>CSV

次のコード スニペットは、エラーの伝達も示す簡単な CSV の逆シリアライザーです。

```csharp
using System.Collections.Generic;
using System.IO;

using Microsoft.Azure.StreamAnalytics;
using Microsoft.Azure.StreamAnalytics.Serialization;

namespace ExampleCustomCode.Serialization
{
    public class CustomCsvDeserializer : StreamDeserializer<CustomEvent>
    {
        private StreamingDiagnostics streamingDiagnostics;

        public override IEnumerable<CustomEvent> Deserialize(Stream stream)
        {
            using (var sr = new StreamReader(stream))
            {
                string line = sr.ReadLine();
                while (line != null)
                {
                    if (line.Length > 0 && !string.IsNullOrWhiteSpace(line))
                    {
                        string[] parts = line.Split(',');
                        if (parts.Length != 3)
                        {
                            streamingDiagnostics.WriteError("Did not get expected number of columns", $"Invalid line: {line}");
                        }
                        else
                        {
                            yield return new CustomEvent()
                            {
                                Column1 = parts[0],
                                Column2 = parts[1],
                                Column3 = parts[2]
                            };
                        }
                    }

                    line = sr.ReadLine();
                }
            }
        }

        public override void Initialize(StreamingContext streamingContext)
        {
            this.streamingDiagnostics = streamingContext.Diagnostics;
        }
    }

    public class CustomEvent
    {
        public string Column1 { get; set; }

        public string Column2 { get; set; }

        public string Column3 { get; set; }
    }
}

```

## <a name="serialization-format-for-rest-apis"></a>REST API のシリアル化形式

Stream Analytics のすべての入力には、**シリアル化形式**があります。 入力オプションについて詳しくは、[入力 REST API](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input) のドキュメントをご覧ください。

次の JavaScript コードは、REST API を使用する場合の .NET 逆シリアライザーのシリアル化形式の例です。

```javascript
{    
   "properties":{    
      "type":"stream",  
      "serialization":{    
         "type":"CustomCLR",  
         "properties":{    
            "serializationDllPath":"<path to the dll inside UserCustomCode\CLR\ folder>", 
            "serializationClassName":"<Full name of the deserializer class name>" 
         }  
      }
   }  
}  
```

`serializationClassName` は、`StreamDeserializer<T>` を実装するクラスでなければなりません。 これについては、次のセクションで説明します。

## <a name="region-support"></a>リージョンのサポート

この機能は、次のリージョンで使用できます。

* 米国中西部 (使用可能)
* 北ヨーロッパ (使用可能)
* 米国東部 (使用可能)
* 米国西部 (まもなくロールアウト)
* 米国東部 2 (まもなくロールアウト)
* 西ヨーロッパ (まもなくロールアウト)

追加リージョンの[サポートを要求する](https://aka.ms/ccodereqregion)ことができます。

## <a name="frequently-asked-questions"></a>よく寄せられる質問

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>この機能がすべての Azure リージョンで使用できるようになるのはいつですか?

この機能は 6 つのリージョンで利用できます (「リージョンのサポート」)。 別のリージョンでこの機能を使用することに関心がある場合は、[要求を提出する](https://aka.ms/ccodereqregion)ことができます。 すべての Azure リージョンでのサポートは、ロードマップ上にあります。

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>GetMetadataPropertyValue 関数と同様の入力から MetadataPropertyValue にアクセスすることはできますか?

この機能はサポートされていません。 この機能が必要な場合は、[UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese) でこの要求に投票することができます。

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>他のユーザーが利用できるように、逆シリアライザーの実装をコミュニティと共有できますか?

逆シリアライザーを実装した後は、コミュニティと共有することにより、他のユーザーに役立てることができます。 コードを [Azure Stream Analytics GitHub リポジトリ](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers)に送信してください。

## <a name="next-steps"></a>次の手順

* [Azure Stream Analytics クラウド ジョブ用のカスタム .NET 逆シリアライザー](custom-deserializer.md)
