---
title: Ladění aplikací pro Azure Service Fabric v systému Linux | Microsoft Docs
description: Naučte se monitorovat a diagnostikovat Service Fabric služby na místním vývojovém počítači se systémem Linux.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: atsenthi
ms.openlocfilehash: 017b359f4c6da438f5179813fa3ed1ad2c536834
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2019
ms.locfileid: "72168863"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a diagnostika služeb v nastavení vývoje místního počítače


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorování, detekce, diagnostika a řešení potíží umožní službám pokračovat s minimálním přerušením uživatelského prostředí. Monitorování a diagnostika jsou klíčové v samotném nasazeném provozním prostředí. Při vytváření podobného modelu během vývoje služeb je zajištěno, že při přesunu do produkčního prostředí funguje diagnostický kanál. Service Fabric usnadňuje vývojářům služeb implementovat diagnostiku, která dokáže hladce fungovat v rámci místních nastavení pro vývoj v jednom počítači a v reálných instalacích produkčních clusterů.


## <a name="debugging-service-fabric-java-applications"></a>Ladění aplikací Service Fabric Java

Pro aplikace Java je k dispozici [více rozhraní protokolování](https://en.wikipedia.org/wiki/Java_logging_framework) . Vzhledem k tomu, že `java.util.logging` je výchozí možnost JRE, používá se také pro [Příklady kódu v GitHubu](https://github.com/Azure-Samples/service-fabric-java-getting-started). Následující diskuze vysvětluje, jak nakonfigurovat rozhraní `java.util.logging`.

Pomocí jazyka Java. util. log můžete přesměrovat protokoly aplikace na paměť, Výstupní proudy, soubory konzoly nebo sokety. Pro každou z těchto možností jsou již v rozhraní k dispozici výchozí obslužné rutiny. Můžete vytvořit soubor `app.properties` pro konfiguraci obslužné rutiny souboru pro vaši aplikaci pro přesměrování všech protokolů do místního souboru.

Následující fragment kódu obsahuje příklad konfigurace:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Složka, na kterou ukazuje soubor `app.properties`, musí existovat. Po vytvoření souboru `app.properties` je potřeba také změnit skript vstupního bodu, `entrypoint.sh` ve složce `<applicationfolder>/<servicePkg>/Code/`, chcete-li nastavit vlastnost `java.util.logging.config.file` na `app.properties`. Záznam by měl vypadat jako následující fragment kódu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Výsledkem této konfigurace je, že protokoly se shromažďují rotujícím způsobem při `/tmp/servicefabric/logs/`. Soubor protokolu v tomto případě se nazývá mysfapp% u.% g. log, kde:
* **% u** je jedinečné číslo, které řeší konflikty mezi souběžnými procesy Java.
* **% g** je číslo generace pro rozlišení mezi rotačními protokoly.

Ve výchozím nastavení, pokud není explicitně nakonfigurována žádná obslužná rutina, je obslužná rutina konzoly zaregistrována. Jedna z nich může zobrazit protokoly v syslogu v/var/log/syslog.

Další informace najdete v [příkladech kódu v GitHubu](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Ladění aplikací C# Service Fabric


K dispozici je více platforem pro trasování aplikací CoreCLR v systému Linux. Další informace najdete v tématu [GitHub: protokolování](http:/github.com/aspnet/logging).  Vzhledem k tomu, že C# technologie EventSource je pro vývojáře obeznámena, "Tento článek používá EventSource pro trasování v CoreCLR Samples on Linux.

Prvním krokem je zahrnutí System. Diagnostics. Tracing, abyste mohli zapisovat protokoly do paměti, výstupních datových proudů nebo souborů konzoly.  Pro protokolování pomocí EventSource přidejte do Project. JSON následující projekt:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Vlastní naslouchacího procesu událostí můžete použít k naslouchání události služby a jejich následnému přesměrování na trasovací soubory. Následující fragment kódu ukazuje ukázkovou implementaci protokolování pomocí EventSource a vlastní naslouchacího procesu událostí:


```csharp

public class ServiceEventSource : EventSource
{
        public static ServiceEventSource Current = new ServiceEventSource();

        [NonEvent]
        public void Message(string message, params object[] args)
        {
            if (this.IsEnabled())
            {
                var finalMessage = string.Format(message, args);
                this.Message(finalMessage);
            }
        }

        // TBD: Need to add method for sample event.

}

```


```csharp
internal class ServiceEventListener : EventListener
{

        protected override void OnEventSourceCreated(EventSource eventSource)
        {
            EnableEvents(eventSource, EventLevel.LogAlways, EventKeywords.All);
        }
        protected override void OnEventWritten(EventWrittenEventArgs eventData)
        {
                using (StreamWriter Out = new StreamWriter( new FileStream("/tmp/MyServiceLog.txt", FileMode.Append)))
                {
                        // report all event information
                        Out.Write(" {0} ", Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
                        if (eventData.Message != null)
                                Out.WriteLine(eventData.Message, eventData.Payload.ToArray());
                        else
                        {
                                string[] sargs = eventData.Payload != null ? eventData.Payload.Select(o => o.ToString()).ToArray() : null; 
                                Out.WriteLine("({0}).", sargs != null ? string.Join(", ", sargs) : "");
                        }
                }
        }
}
```


Předchozí fragment kódu výstupuje protokoly do souboru v `/tmp/MyServiceLog.txt`. Tento název souboru musí být řádně aktualizovaný. V případě, že chcete přesměrovat protokoly do konzoly, použijte následující fragment kódu v přizpůsobené třídě naslouchacího procesu událostí:

```csharp
public static TextWriter Out = Console.Out;
```

Ukázky v [ C# ukázkách](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) používají EventSource a vlastní naslouchacího procesu událostí k protokolování událostí do souboru.



## <a name="next-steps"></a>Další kroky
Stejný trasovací kód přidaný do vaší aplikace funguje také s diagnostikou vaší aplikace v clusteru Azure. Podívejte se na tyto články, které popisují různé možnosti nástrojů, a popište, jak je nastavit.
* [Postup shromažďování protokolů pomocí Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
