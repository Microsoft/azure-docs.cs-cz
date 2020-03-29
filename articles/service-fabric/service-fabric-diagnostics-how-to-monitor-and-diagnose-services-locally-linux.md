---
title: Ladění aplikací Azure Service Fabric v Linuxu
description: Přečtěte si, jak monitorovat a diagnostikovat služby Service Fabric na místním vývojovém počítači s Linuxem.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: d8b5ec2f2190586f5eced5eee112b190a82504c3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75526290"
---
# <a name="monitor-and-diagnose-services-in-a-local-linux-machine-development-setup"></a>Sledování a diagnostika služeb v místním nastavení vývoje linuxových strojů


> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)
> * [Linux](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md)
>
>

Monitorování, zjišťování, diagnostika a řešení potíží umožňují, aby služby pokračovaly s minimálním narušením uživatelského prostředí. Monitorování a diagnostika jsou důležité ve skutečném nasazeném produkčním prostředí. Přijetí podobného modelu během vývoje služeb zajišťuje, že diagnostický kanál funguje při přechodu do produkčního prostředí. Service Fabric usnadňuje vývojářům služeb implementovat diagnostiku, která může bezproblémově fungovat jak v nastavení místního vývoje jednoho počítače, tak v reálných produkčních nastaveních clusteru.


## <a name="debugging-service-fabric-java-applications"></a>Ladění aplikací Java aplikace Service Fabric

Pro aplikace Java je k dispozici [více architektur protokolování.](https://en.wikipedia.org/wiki/Java_logging_framework) Vzhledem k tomu, `java.util.logging` že je výchozí možnost s JRE, používá se také pro [příklady kódu v GitHub](https://github.com/Azure-Samples/service-fabric-java-getting-started). Následující diskuse vysvětluje, jak `java.util.logging` nakonfigurovat rozhraní.

Pomocí java.util.logging můžete přesměrovat protokoly aplikací do paměti, výstupních datových proudů, souborů konzoly nebo soketů. Pro každou z těchto možností existují výchozí obslužné rutiny již k dispozici v rámci. Můžete vytvořit `app.properties` soubor pro konfiguraci obslužné rutiny souboru pro vaši aplikaci přesměrovat všechny protokoly do místního souboru.

Následující fragment kódu obsahuje ukázkovou konfiguraci:

```java
handlers = java.util.logging.FileHandler

java.util.logging.FileHandler.level = ALL
java.util.logging.FileHandler.formatter = java.util.logging.SimpleFormatter
java.util.logging.FileHandler.limit = 1024000
java.util.logging.FileHandler.count = 10
java.util.logging.FileHandler.pattern = /tmp/servicefabric/logs/mysfapp%u.%g.log
```

Složka, na kterou `app.properties` soubor ukazuje, musí existovat. Po `app.properties` vytvoření souboru je třeba také upravit skript `entrypoint.sh` vstupního bodu ve `<applicationfolder>/<servicePkg>/Code/` složce a nastavit vlastnost `java.util.logging.config.file` do `app.properties` souboru. Položka by měla vypadat jako následující úryvek:

```sh
java -Djava.library.path=$LD_LIBRARY_PATH -Djava.util.logging.config.file=<path to app.properties> -jar <service name>.jar
```


Výsledkem této konfigurace jsou protokoly shromažďovány `/tmp/servicefabric/logs/`rotujícím způsobem na adrese . Soubor protokolu v tomto případě se nazývá mysfapp%u.%g.log, kde:
* **%u** je jedinečné číslo pro řešení konfliktů mezi simultánními procesy javy.
* **%g** je číslo generace, které má být rozlišováno mezi rotujícími protokoly.

Ve výchozím nastavení, pokud není explicitně nakonfigurována žádná obslužná rutina, je obslužná rutina konzoly registrována. Lze zobrazit protokoly v syslog u /var/log/syslog.

Další informace naleznete v [příkladech kódu na GitHubu](https://github.com/Azure-Samples/service-fabric-java-getting-started).


## <a name="debugging-service-fabric-c-applications"></a>Ladění aplikací Jazyka C fabric služby


Pro sledování aplikací CoreCLR v Linuxu je k dispozici více architektur. Další informace naleznete v tématu [GitHub: protokolování](http:/github.com/aspnet/logging).  Vzhledem k tomu, EventSource je obeznámen s vývojáři Jazyka C#, 'tento článek používá EventSource pro trasování v ukázkách CoreCLR na Linuxu.

Prvním krokem je zahrnout System.Diagnostics.Tracing tak, aby bylo možné zapisovat protokoly do paměti, výstupní datové proudy nebo soubory konzoly.  Pro protokolování pomocí EventSource přidejte do souboru project.json následující projekt:

```json
    "System.Diagnostics.StackTrace": "4.0.1"
```

Vlastní EventListener můžete použít k naslouchání události služby a potom je odpovídajícím způsobem přesměrovat na trasovací soubory. Následující fragment kódu zobrazuje ukázkovou implementaci protokolování pomocí EventSource a vlastního EventListeneru:


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


Předchozí výstřižek vyveze protokoly `/tmp/MyServiceLog.txt`do souboru v . Tento název souboru musí být odpovídajícím způsobem aktualizován. V případě, že chcete přesměrovat protokoly na konzolu, použijte následující úryvek ve vaší vlastní třídě EventListener:

```csharp
public static TextWriter Out = Console.Out;
```

Ukázky na [C# Ukázky](https://github.com/Azure-Samples/service-fabric-dotnet-core-getting-started) použít EventSource a vlastní EventListener protokolu událostí do souboru.



## <a name="next-steps"></a>Další kroky
Stejný kód trasování přidaný do vaší aplikace funguje také s diagnostikou vaší aplikace v clusteru Azure. Podívejte se na tyto články, které popisují různé možnosti pro nástroje a popisují, jak je nastavit.
* [Jak shromažďovat protokoly pomocí Diagnostika Azure](service-fabric-diagnostics-how-to-setup-lad.md)
