---
title: Čtení vstupu v jakémkoli formátu pomocí vlastního deserializace rozhraní .NET v Azure Stream Analytics
description: Tento článek vysvětluje formát serializace a rozhraní, která definují vlastní deserializace rozhraní .NET pro Azure Stream Analytics úlohy cloudu a Edge.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: 4adf0b8a7c12abed9689b9ac0cc9c5d5c8c3f980
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94488430"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Čtení vstupu v jakémkoli formátu pomocí vlastních deserializátorů .NET

Vlastní deserializace rozhraní .NET umožňují, aby vaše úloha Azure Stream Analytics četla data z formátů mimo tři [předdefinované formáty dat](stream-analytics-parsing-json.md). Tento článek vysvětluje formát serializace a rozhraní, která definují vlastní deserializace rozhraní .NET pro Azure Stream Analytics úlohy cloudu a Edge. K dispozici jsou také ukázkové deserializace pro vyrovnávací paměť protokolů a formát CSV.

## <a name="net-custom-deserializer"></a>Vlastní deserializace rozhraní .NET

Následující ukázky kódu jsou rozhraní, která definují vlastní deserializaci a implementují `StreamDeserializer<T>` .

`UserDefinedOperator` je základní třídou pro všechny vlastní operátory streamování. Inicializuje `StreamingContext` , což poskytuje kontext, který zahrnuje mechanismus pro publikování diagnostiky, pro který budete potřebovat ladit jakékoli problémy s deserializací.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Následující fragment kódu je deserializace pro streamovaná data. 

Přeskočené chyby by měly být generovány pomocí `IStreamingDiagnostics` inicializační metody předané prostřednictvím `UserDefinedOperator` metody Initialize. Všechny výjimky budou považovány za chyby a deserializátor bude znovu vytvořen. Po určitém počtu chyb bude úloha přejít na stav selhání.

`StreamDeserializer<T>` deserializace Stream do objektu typu `T` . Musí být splněny následující podmínky:

1. T je třída nebo struktura.
1. Všechna veřejná pole v T jsou buď
    1. Jedna z hodnot [SByte, Byte, Short, UShort, int, uint, Long, DateTime, String, float, Double] nebo jejich ekvivalentů s možnou hodnotou null.
    1. Jiná struktura nebo třída za stejnými pravidly.
    1. Pole typu `T2` , které se řídí stejnými pravidly
    1. IList `T2` , kde T2 dodržuje stejná pravidla.
    1. Nemá žádné rekurzivní typy.

Parametr `stream` je datový proud obsahující serializovaný objekt. `Deserialize` Vrátí kolekci `T` instancí.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext` poskytuje kontext, který zahrnuje mechanismus pro publikování diagnostiky pro operátora uživatele.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics` je diagnostikou uživatelsky definovaných operátorů, včetně serializátoru, deserializace a uživatelsky definovaných funkcí.

`WriteError` zapíše chybovou zprávu do protokolů prostředků a pošle chybu do diagnostiky.

`briefMessage` je Stručná chybová zpráva. Tato zpráva se zobrazí v diagnostickém prostředí a je používána produktovým týmem pro účely ladění. Nezahrnujte citlivé informace a nechte zprávu kratší než 200 znaků.

`detailedMessage` je podrobná chybová zpráva, která se přidá jenom do vašich protokolů prostředků v úložišti. Tato zpráva by měla být kratší než 2000 znaků.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Příklady deserializace

V této části se dozvíte, jak psát vlastní deserializace pro Protobuf a CSV. Další příklady, jako je AVRO Format pro zachytávání centra událostí, Azure Stream Analytics najdete na webu [GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formát vyrovnávací paměti protokolu (Protobuf)

Toto je příklad použití formátu vyrovnávací paměti protokolu.

Předpokládejme následující definici vyrovnávací paměti protokolu.

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

Spuštění `protoc.exe` z rozhraní NuGet **Google. Protobuf. Tools** vygeneruje soubor. cs s definicí. Vygenerovaný soubor zde není zobrazen. Musíte zajistit, aby verze Protobuf NuGet, kterou použijete v projektu Stream Analytics, odpovídala verzi Protobuf, která byla použita k vygenerování vstupu. 

Následující fragment kódu je implementace deserializace za předpokladu, že vygenerovaný soubor je zahrnutý v projektu. Tato implementace je pouze tenkou obálkou pro vygenerovaný soubor.

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

Následující fragment kódu je jednoduchý deserializace CSV, který také ukazuje šíření chyb.

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

## <a name="serialization-format-for-rest-apis"></a>Formát serializace pro rozhraní REST API

Každý vstup Stream Analytics má **formát serializace**. Další informace o možnostech vstupu najdete v dokumentaci ke [vstupu REST API](/rest/api/streamanalytics/2016-03-01/inputs) .

Následující kód jazyka JavaScript je příkladem formátu serializace deserializace rozhraní .NET při použití REST API:

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

`serializationClassName` měla by být třída, která implementuje `StreamDeserializer<T>` . Tento postup je popsaný v následující části.

## <a name="region-support"></a>Podpora oblastí

Tato funkce je k dispozici v následujících oblastech:

* USA – středozápad
* Severní Evropa
* East US
* USA – západ
* USA – východ 2
* West Europe

Můžete [požádat o podporu](https://aka.ms/ccodereqregion) pro další oblasti.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Kdy bude tato funkce dostupná ve všech oblastech Azure?

Tato funkce je k dispozici v [6 oblastech](#region-support). Pokud vás zajímá použití této funkce v jiné oblasti, můžete [Odeslat žádost](https://aka.ms/ccodereqregion). Podpora všech oblastí Azure je v plánu.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Můžu získat přístup k MetadataPropertyValue ze svých vstupů, podobně jako funkce GetMetadataPropertyValue?

Tato funkce není podporována. Pokud tuto funkci potřebujete, můžete hlasovat o této žádosti na [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Můžu svou implementaci deserializace sdílet s komunitou, aby k nim ostatní mohli těžit?

Po implementaci nástroje pro deserializaci můžete ostatním uživatelům usnadnit sdílení pomocí komunity. Odešlete kód do [úložiště Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="what-are-the-other-limitations-of-using-custom-deserializers-in-stream-analytics"></a>Jaká jsou další omezení používání vlastních deserializátorů v Stream Analytics?

Pokud je váš vstup ve formátu Protobuf pomocí schématu obsahujícího `MapField` typ, nebudete moci implementovat vlastní deserializaci. Vlastní deserializace také nepodporuje ukázková data ani data ve verzi Preview. 

## <a name="next-steps"></a>Další kroky

* [Vlastní deserializace rozhraní .NET pro cloudové úlohy Azure Stream Analytics](custom-deserializer.md)
