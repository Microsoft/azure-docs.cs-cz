---
title: Čtení vstupu v libovolném formátu pomocí vlastních deserializátorů rozhraní .NET v Azure Stream Analytics
description: Tento článek vysvětluje formát serializace a rozhraní, která definují vlastní deserializátory .NET pro cloudové a hraniční úlohy Azure Stream Analytics.
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 270e9a31c28e7209cfe43ea8307b928ed3257a35
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76845254"
---
# <a name="read-input-in-any-format-using-net-custom-deserializers"></a>Čtení vstupu v libovolném formátu pomocí vlastních deserializátorů rozhraní .NET

Vlastní deserializátory rozhraní .NET umožňují vaší úloze Azure Stream Analytics číst data z formátů mimo tři [předdefinované formáty dat](stream-analytics-parsing-json.md). Tento článek vysvětluje formát serializace a rozhraní, která definují vlastní deserializátory .NET pro cloudové a hraniční úlohy Azure Stream Analytics. Existují také příklady deserializerů pro formát vyrovnávací paměti protokolu a CSV.

## <a name="net-custom-deserializer"></a>Vlastní deserializátor rozhraní .NET

Následující ukázky kódu jsou rozhraní, která definují vlastní deserializátor a implementovat `StreamDeserializer<T>`.

`UserDefinedOperator`je základní třída pro všechny vlastní operátory streamování. Inicializuje `StreamingContext`, který poskytuje kontext, který zahrnuje mechanismus pro publikování diagnostiky, pro které budete muset ladit všechny problémy s deserializer.

```csharp
    public abstract class UserDefinedOperator
    {
        public abstract void Initialize(StreamingContext streamingContext);
    }
```

Následující fragment kódu je deserializace pro streamování dat. 

Přeskočitelné chyby by měly být emitovány pomocí `IStreamingDiagnostics` metody `UserDefinedOperator`Inicializovat . Všechny výjimky budou považovány za chyby a deserializátor bude znovu vytvořen. Po určitém počtu chyb se úloha přejde do stavu selhání.

`StreamDeserializer<T>`deserializuje datový proud do objektu typu `T`. Musí být splněny tyto podmínky:

1. T je třída nebo struktura.
1. Všechna veřejná pole v T jsou buď
    1. Jeden z [sbyte, byte, short, ushort, int, uint, long, DateTime, string, float, double] nebo jejich ekvivalenty s možnou hodnotou null.
    1. Jiná struktura nebo třída podle stejných pravidel.
    1. Pole typu, `T2` které se řídí stejnými pravidly.
    1. IList,`T2` kde T2 dodržuje stejná pravidla.
    1. Nemá žádné rekurzivní typy.

Parametr `stream` je datový proud obsahující serializovaný objekt. `Deserialize`vrátí kolekci `T` instancí.

```csharp
    public abstract class StreamDeserializer<T> : UserDefinedOperator
    {
        public abstract IEnumerable<T> Deserialize(Stream stream);
    }
```

`StreamingContext`poskytuje kontext, který zahrnuje mechanismus pro publikování diagnostiky pro operátor uživatele.

```csharp
    public abstract class StreamingContext
    {
        public abstract StreamingDiagnostics Diagnostics { get; }
    }
```

`StreamingDiagnostics`je diagnostika pro uživatelem definované operátory včetně serializátoru, deserializátoru a uživatelem definovaných funkcí.

`WriteError`zapíše chybovou zprávu do diagnostických protokolů a odešle chybu do diagnostiky.

`briefMessage`je krátká chybová zpráva. Tato zpráva se zobrazí v diagnostice a používá produktový tým pro účely ladění. Nezahrnejte citlivé informace a zachovejte zprávu s méně než 200 znaky.

`detailedMessage`je podrobná chybová zpráva, která je přidána pouze do diagnostických protokolů ve vašem úložišti. Tato zpráva by měla být menší než 2000 znaků.

```csharp
    public abstract class StreamingDiagnostics
    {
        public abstract void WriteError(string briefMessage, string detailedMessage);
    }
```

## <a name="deserializer-examples"></a>Příklady deserializátorů

Tato část ukazuje, jak psát vlastní deserializery pro Protobuf a CSV. Další příklady, jako je například formát AVRO pro zachycení centra událostí, najdete [na webu Azure Stream Analytics na GitHubu](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

### <a name="protocol-buffer-protobuf-format"></a>Formát vyrovnávací paměti protokolu (Protobuf)

Toto je příklad pomocí formátu vyrovnávací paměti protokolu.

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

Spuštění `protoc.exe` z **Google.Protobuf.Tools** NuGet generuje .cs soubor s definicí. Zde není zobrazen vygenerovaný soubor.

Následující fragment kódu je implementace deserializátoru za předpokladu, že generovaný soubor je součástí projektu. Tato implementace je pouze tenký obal přes generovaný soubor.

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

Následující fragment kódu je jednoduchý deserializátor CSV, který také demonstruje chyby šíření.

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

## <a name="serialization-format-for-rest-apis"></a>Formát serializace pro api rest

Každý vstup Stream Analytics má **formát serializace**. Další informace o možnostech zadávání naleznete v dokumentaci [k rozhraní API vstupní ho rozhraní REST.](https://docs.microsoft.com/rest/api/streamanalytics/stream-analytics-input)

Následující kód Javascript u použití rozhraní REST API je příkladem formátu serializace deserializátoru .NET:

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

`serializationClassName`by měla být třída, která implementuje `StreamDeserializer<T>`. To je popsáno v následující části.

## <a name="region-support"></a>Podpora regionu

Tato funkce je k dispozici v následujících oblastech:

* USA – středozápad
* Severní Evropa
* USA – východ
* USA – západ
* USA – východ 2
* Západní Evropa

Můžete [požádat o podporu](https://aka.ms/ccodereqregion) pro další oblasti.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="when-will-this-feature-be-available-in-all-azure-regions"></a>Kdy bude tato funkce dostupná ve všech oblastech Azure?

Tato funkce je k dispozici v [6 oblastech](https://docs.microsoft.com/azure/stream-analytics/custom-deserializer-examples#region-support). Máte-li zájem o použití této funkce v jiné oblasti, můžete [odeslat žádost](https://aka.ms/ccodereqregion). Podpora pro všechny oblasti Azure je na plánu.

### <a name="can-i-access-metadatapropertyvalue-from-my-inputs-similar-to-getmetadatapropertyvalue-function"></a>Lze získat přístup k hodnotě MetadataPropertyValue ze vstupů podobných funkci GetMetadataPropertyValue?

Tato funkce není podporována. Pokud tuto funkci potřebujete, můžete hlasovat pro tento požadavek na [UserVoice](https://feedback.azure.com/forums/270577-stream-analytics/suggestions/38779801-accessing-input-metadata-properties-in-custom-dese).

### <a name="can-i-share-my-deserializer-implementation-with-the-community-so-that-others-can-benefit"></a>Mohu sdílet implementaci deserializátoru s komunitou, aby z toho měli prospěch ostatní?

Jakmile implementujete deserializátor, můžete pomoci ostatním tím, že jej sdílíte s komunitou. Odešlete svůj kód do [úložiště Azure Stream Analytics GitHub](https://github.com/Azure/azure-stream-analytics/tree/master/CustomDeserializers).

## <a name="next-steps"></a>Další kroky

* [Vlastní deserializátory .NET pro cloudové úlohy Azure Stream Analytics](custom-deserializer.md)
