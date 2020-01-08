---
title: Ladění aplikací Azure Service Fabric v systému Linux
description: Naučte se monitorovat a diagnostikovat Service Fabric služby na místním vývojovém počítači se systémem Linux.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2019
ms.locfileid: "75526290"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Monitorování a diagnostika služeb v nastavení pro vývoj v místním počítači se systémem Linux


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorování, detekce, diagnostika a řešení potíží umožní službám pokračovat s minimálním přerušením uživatelského prostředí. Monitorování a diagnostika jsou klíčové v samotném nasazeném provozním prostředí. Při vytváření podobného modelu během vývoje služeb je zajištěno, že při přesunu do produkčního prostředí funguje diagnostický kanál. Service Fabric usnadňuje vývojářům služeb implementovat diagnostiku, která dokáže hladce fungovat v rámci místních nastavení pro vývoj v jednom počítači a v reálných instalacích produkčních clusterů.


## <a name="debugging-service-fabric-java-applications"></a>Ladění aplikací Service Fabric Java

Pro aplikace Java je k dispozici [více rozhraní protokolování](https://en.wikipedia.org/wiki/Java_logging_framework) . Vzhledem k tomu, že `java.util.logging` je výchozí možnost JRE, používá se také pro [Příklady kódu v GitHubu](https://github.com/Azure-Samples/service-fabric-java-getting-started). Následující diskuzi vysvětluje, jak nakonfigurovat `java.util.logging` Framework.

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

Složka, na kterou odkazuje soubor `app.properties`, musí existovat. Po vytvoření souboru `app.properties` budete muset také upravit skript vstupního bodu, ve `entrypoint.sh` ve složce `<applicationfolder>/<servicePkg>/Code/` nastavte vlastnost `java.util.logging.config.file` na `app.properties` soubor. Záznam by měl vypadat jako následující fragment kódu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Výsledkem této konfigurace je, že protokoly se shromažďují rotujícím způsobem při `/tmp/servicefabric/logs/`. Soubor protokolu v tomto případě se nazývá mysfapp% u .% g. log, kde:
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
