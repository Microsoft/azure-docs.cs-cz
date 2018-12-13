---
title: Ladění Azure Service Fabric aplikace v Linuxu | Dokumentace Microsoftu
description: Zjistěte, jak monitorovat a diagnostikovat služby Service Fabric na místním vývojovém počítači Linux.
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
ms.openlocfilehash: 9f0c4789e73659e5965440989c23a8cf673f7cd2
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53309157"
---
# <a name="monitor-and-diagnose-services-in-a-local-machine-development-setup"></a>Monitorování a Diagnostika služeb v nastavení vývojového místního počítače


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorování, zjišťování, Diagnostika a řešení potíží s povolit pro služby, abyste mohli pokračovat s minimálním dopadem na uživatelské prostředí. Monitorování a Diagnostika jsou kritické skutečný nasazené produkčního prostředí. Přijetím modelu podobně jako při vývoji služeb zajišťuje, že kanál diagnostiku funguje při přesunutí do produkčního prostředí. Service Fabric umožňuje vývojářům služby k implementaci diagnostiku, která můžete bez problémů fungují s jeden počítač místního vývojového nastavení i nastavení skutečné produkční cluster.


## <a name="debugging-service-fabric-java-applications"></a>Ladění aplikací Service Fabric v Javě

Pro aplikace v Javě [více rozhraní protokolování](http://en.wikipedia.org/wiki/Java_logging_framework) jsou k dispozici. Protože `java.util.logging` je výchozí možnost, pomocí prostředí JRE, se také používá pro [příklady v Githubu kódu](http://github.com/Azure-Samples/service-fabric-java-getting-started). Následující diskuse vysvětluje postup konfigurace `java.util.logging` rozhraní framework.

Pomocí java.util.logging můžete přesměrovat protokolů aplikace, paměti, výstupní datové proudy, soubory konzoly nebo sokety. Pro každou z těchto možností jsou obsaženy v rámci výchozích obslužných rutin. Můžete vytvořit `app.properties` souboru nakonfigurujte popisovač souboru pro vaši aplikaci do místního souboru přesměrovat všechny protokoly.

Následující fragment kódu obsahuje příklad konfigurace:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Složka na které odkazují `app.properties` soubor musí existovat. Po `app.properties` se vytvoří soubor, budete muset také upravit váš skript vstupního bodu `entrypoint.sh` v `<applicationfolder>/<servicePkg>/Code/` složku, kterou chcete nastavit vlastnost `java.util.logging.config.file` k `app.propertes` souboru. Ji by měl vypadat jako následující fragment kódu:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Tuto konfiguraci výsledky v protokolech shromažďovaných otáčení způsobem v `/tmp/servicefabric/logs/`. Soubor protokolu v tomto případě je pojmenován mysfapp%u.%g.log kde:
* **%u** je jedinečné číslo řešení konfliktů mezi souběžných procesů Java.
* **%g** je číslo generace k rozlišení mezi otáčení protokoly.

Ve výchozím nastavení je-li explicitně nastavená žádná obslužná rutina, registrována obslužná rutina konzoly. V protokolu syslog v rámci /var/log/syslog jeden můžete zobrazit protokoly.

Další informace najdete v tématu [příklady v Githubu kódu](http://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Ladění aplikace Service Fabric C#


Jsou k dispozici pro trasování CoreCLR aplikace v Linuxu více rozhraní. Další informace najdete v tématu [Githubu: protokolování](http:/github.com/aspnet/logging).  Protože EventSource je dobře známé pro C# vývojáři, "EventSource tomto článku se používá pro trasování v CoreCLR ukázky v Linuxu.

Prvním krokem je zahrnout System.Diagnostics.Tracing tak, aby vaše protokoly můžete zapisovat do paměti, výstupní datové proudy nebo soubory konzoly.  Pro protokolování událostí EventSource, přidejte do vašeho souboru project.json následující projekt:

```
    "System.Diagnostics.StackTrace": "4.0.1"
```

Vlastní naslouchacího procesu událostí můžete použít k naslouchání události služby a potom je odpovídajícím způsobem přesměrovat na trasovací soubory. Následující fragment kódu ukazuje ukázková implementace protokolování pomocí EventSource a vlastní naslouchacího procesu událostí:


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


Vypíše protokoluje události do souboru v předchozím fragmentu kódu `/tmp/MyServiceLog.txt`. Tento název souboru musí být správně aktualizována. V případě, že chcete přesměrovat protokoly do konzoly, použijte následující fragment kódu ve své vlastní třídě naslouchacího procesu událostí:

```csharp
public static TextWriter Out = Console.Out;
```

Ukázky v [ukázky jazyka C#](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) použít EventSource a vlastní naslouchacího procesu událostí do protokolu událostí do souboru.



## <a name="next-steps"></a>Další postup
Stejný kód trasování do své aplikace přidali funguje taky s diagnostikou aplikace do clusteru Azure. Prohlédněte si tyto články, které popisují různé možnosti pro nástroje a popisují, jak je nastavit.
* [Shromažďování protokolů pomocí Azure Diagnostics](service-fabric-diagnostics-how-to-setup-lad.md)
