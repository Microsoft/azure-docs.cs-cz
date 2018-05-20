---
title: Ladění Azure mikroslužeb v systému Linux | Microsoft Docs
description: Zjistěte, jak sledovat a diagnostikovat vaše služby vytvořené pomocí Microsoft Azure Service Fabric na místním vývojovém počítači.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: 4eebe937-ab42-4429-93db-f35c26424321
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 563f9d73d5a8d56e834c36d03aed75812ec123ba
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a Diagnostika služby v instalačním programu pro vývoj místním počítači


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorování, zjišťování, Diagnostika a řešení potíží s povolit pro služby, chcete-li pokračovat s minimálním dopadem na činnost koncového uživatele. Monitorovací a diagnostické jsou kritické v skutečné nasazené produkční prostředí. Přijetí model podobně jako při vývoji služeb zajišťuje, že diagnostické kanálu funguje, když přesunete do provozního prostředí. Service Fabric usnadňuje vývojářům implementovat diagnostiky, které může bezproblémově fungovat v jednom počítači místní vývoj nastavení a nastavení clusteru skutečné produkční služby.


## <a name="debugging-service-fabric-java-applications"></a>Ladění aplikací Service Fabric Java

Pro aplikace Java [více rozhraní protokolování](http://en.wikipedia.org/wiki/Java_logging_framework) jsou k dispozici. Vzhledem k tomu `java.util.logging` je výchozí možností s prostředí JRE, používá se také pro [příklady v githubu kódu](http://github.com/Azure-Samples/service-fabric-java-getting-started).  Následující diskusi vysvětluje postup konfigurace `java.util.logging` framework.

Pomocí java.util.logging je paměti, výstupní datové proudy, soubory konzoly nebo sockets přesměrovat protokolů aplikace. Pro každou z těchto možností existují výchozí obslužné rutiny už zadané v rozhraní framework. Můžete vytvořit `app.properties` soubor pro konfiguraci obslužné rutiny souborů pro vaši aplikaci pro přesměrování všechny protokoly do místního souboru.

Následující fragment kódu obsahuje příklad konfigurace:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log             
```

Složka na kterou odkazuje `app.properties` soubor musí existovat. Po `app.properties` soubor je vytvořen, musíte také upravit skript vstupního bodu, `entrypoint.sh` v `<applicationfolder>/<servicePkg>/Code/` složky se nastavit vlastnost `java.util.logging.config.file` k `app.propertes` souboru. Položka by měl vypadat podobně jako následující fragment kódu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Tato konfigurace, které jsou výsledkem protokoly shromažďovaných rotační způsobem v `/tmp/servicefabric/logs/`. Soubor protokolu v tomto případě je názvem mysfapp%u.%g.log kde:
* **%u** je jedinečné číslo a vyřešte konflikty mezi současně spuštěných procesů Java.
* **%g** je číslo generace k rozlišení mezi otáčení protokoly.

Ve výchozím nastavení je pokud žádná obslužná rutina je explicitně nakonfigurován, zaregistrován obslužná rutina konzoly. V procesu syslog pod /var/log/syslog jeden můžete zobrazit protokoly.

Další informace najdete v tématu [příklady v githubu kódu](http://github.com/Azure-Samples/service-fabric-java-getting-started).  


## <a name="debugging-service-fabric-c-applications"></a>Ladění aplikací Service Fabric C#


Více rozhraní jsou k dispozici pro trasování CoreCLR aplikace v systému Linux. Další informace najdete v tématu [Githubu: protokolování](http:/github.com/aspnet/logging).  Vzhledem k tomu, že je dobře známé pro C# vývojáře, EventSource se tento článek používá EventSource pro trasování v CoreCLR ukázky v systému Linux.

Prvním krokem je na System.Diagnostics.Tracing, aby vaše protokoly může zapsat do paměti, výstupní datové proudy nebo soubory konzoly.  Pro protokolování použití EventSource, přidejte do souboru project.json následující projektu:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Vlastní EventListener můžete použít k naslouchání událostí služby a pak je správně přesměrovat na trasovací soubory. Následující fragment kódu ukazuje implementaci Ukázka protokolování pomocí EventSource a vlastní EventListener:


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
         Out.Write(" {0} ",  Write(eventData.Task.ToString(), eventData.EventName, eventData.EventId.ToString(), eventData.Level,""));
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


Výstupy protokoly do souboru v předchozím fragmentu `/tmp/MyServiceLog.txt`. Tento název souboru musí být správně aktualizován. V případě, že chcete přesměrovat protokoly do konzoly, použijte následující fragment kódu v třídě přizpůsobené EventListener:

```csharp
public static TextWriter Out = Console.Out;
```

Ukázky v [C# – ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) pomocí EventSource a vlastní EventListener protokolovat události do souboru.



## <a name="next-steps"></a>Další postup
Stejný kód trasování přidané do vaší aplikace funguje taky s diagnostikou vaší aplikace v clusteru služby Azure. Podívejte se na tyto články, které popisují různé možnosti pro nástroje a popisují, jak je nastavit.
* [Postup shromažďování protokolů pomocí Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
