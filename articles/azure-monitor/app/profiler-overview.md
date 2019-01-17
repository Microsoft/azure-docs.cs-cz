---
title: Profil produkční aplikace v Azure s využitím Application Insights Profiler | Dokumentace Microsoftu
description: Identifikujte kritická cesta v kódu webového serveru pomocí profileru s nízkými nároky.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 08/06/2018
ms.author: mbullwin
ms.openlocfilehash: 7f212d7dafb4aaf88fc226f702b068a3711f81b6
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54359506"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil produkční aplikace v Azure pomocí Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Povolit Application Insights Profiler pro vaši aplikaci

Azure Application Insights Profiler poskytuje trasování výkonu pro aplikace, které jsou spuštěny v produkčním prostředí v Azure. Profiler zaznamená data o automaticky ve velkém měřítku bez negativně ovlivňovat vaše uživatele. Profiler vám pomůže určit "horkou" cestu kódu, který zabere nejvíce času, kdy zpracovává konkrétní webový požadavek. 

Profiler funguje s aplikacemi .NET, které jsou nasazeny na následujících služeb Azure. Konkrétní pokyny pro povolení Profiler pro každý typ služby jsou v níže uvedených odkazů.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a virtuálních počítačů škálovací sady](profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Pokud jste povolili Profiler, ale nezobrazují trasování, zkontrolujte naše [Průvodce odstraňováním potíží](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Zobrazení dat Profiler

Pro Profiler k nahrání trasování aplikace musí být aktivně zpracování žádostí. Pokud provádíte experiment, můžete generovat požadavků do vaší webové aplikace pomocí [testování výkonu Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Pokud jste povolili nově Profiler, spuštěním krátkého zátěžového testu. Zatímco zátěžový test běží, vyberte **profilu nyní** tlačítko [ **nastavení Profiler** podokně](profiler-settings.md#profiler-settings-pane). Když Profiler běží, profily to náhodně přibližně jednou za hodinu a po dobu dvou minut. Pokud vaše aplikace zpracovává nepřetržitý proud požadavků, Profiler odešle trasování každou hodinu.

Poté, co vaše aplikace obdrží část provozu a Profiler má určitá doba odesílání trasování, měli byste mít trasování můžete zobrazit. Tento proces může trvat 5 až 10 minut. Chcete-li zobrazit trasování, v **výkonu** vyberte **provést akce**a pak vyberte **Profiler trasování** tlačítko.

![Trasuje Profiler Application Insights výkon podokno náhledu][performance-blade]

Vyberte ukázku zobrazit rozpis úroveň kódu čas provádění požadavku.

![Průzkumník trasování Application Insights][trace-explorer]

V Průzkumníku trasování zobrazí následující informace:

* **Zobrazit kritickou cestu**: Otevře se největší listové uzly, nebo aspoň něco zavřít. Ve většině případů se tento uzel se blíží výkonu kritickým bodem.
* **Popisek**: Název funkce nebo události. Strom zobrazuje kombinace kódu a události, ke kterým došlo, jako jsou SQL a události protokolu HTTP. Hlavní událost představuje celková doba trvání žádosti.
* **Uplynulý**: Časový interval mezi začátkem operace a konec operace.
* **Když**: Čas, kdy funkce nebo událostí byl spuštěn v relaci dalších funkcí.

## <a name="how-to-read-performance-data"></a>Čtení dat o výkonu

Profileru služeb Microsoft používá kombinaci metody odběru vzorků a instrumentace k analýze výkonu aplikace. Pokud probíhá shromažďování podrobné, profileru služeb ukázky ukazatele na instrukci každý počítač procesoru každých milisekund. Každá ukázka zaznamená úplný zásobník volání vlákna, který aktuálně spouští. Poskytuje podrobné informace o co bylo vlákno dělal, na vysoké úrovni a nízké úrovni abstrakce. Profileru služeb shromažďuje také další události pro sledování aktivity korelace a příčiny, včetně kontextu přepínání události, události Task Parallel Library (TPL) a události fondu vláken.

Zásobník volání, který se zobrazí v zobrazení časové osy je výsledkem vzorkování a instrumentaci. Vzhledem k tomu každý vzorek zachytí úplný zásobník volání vlákna, obsahuje kód z rozhraní Microsoft .NET Framework a další architektury, které odkazujete.

### <a id="jitnewobj"></a>Přidělování objektů (clr! JIT\_nový nebo clr! JIT\_Newarr1)

**CLR! JIT\_nový** a **clr! JIT\_Newarr1** jsou pomocné funkce v rozhraní .NET Framework, které přidělují paměť ze spravované haldy. **CLR! JIT\_nový** je voláno, když je přidělen objektu. **CLR! JIT\_Newarr1** je vyvolána při přidělování pole objektu. Tyto dvě funkce jsou obvykle rychlé a trvat poměrně malé množství času. Pokud **clr! JIT\_nový** nebo **clr! JIT\_Newarr1** trvá spoustu času na vaší časové ose, kód může být přidělení mnoho objektů a spotřebovávat značné množství paměti.

### <a id="theprestub"></a>Kód pro načtení (clr! ThePreStub)

**CLR! ThePreStub** je pomocná funkce v rozhraní .NET Framework, který připraví kód pro spuštění poprvé. Toto spuštění obvykle zahrnuje, ale není omezený na kompilace just-in-time (JIT). Pro každou C# metody **clr! ThePreStub** by měla být volána alespoň jednou během procesu.

Pokud **clr! ThePreStub** trvá dlouhou dobu pro požadavek, žádost je první z nich ke spuštění této metodě. Čas potřebný pro modul runtime rozhraní .NET Framework se načíst první způsob je důležité. Zvažte použití zahřívání procesu, který se spustí předtím, než vaši uživatelé k němu přístup, nebo zvažte spuštění Native Image Generator (ngen.exe) část kódu na vaše sestavení.

### <a id="lockcontention"></a>Kolize zámků (clr! JITutil\_MonContention nebo clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** nebo **clr! JITutil\_MonEnterWorker** označuje, že aktuální vlákno čeká na zámek a tím uvolnit. Tento text se zobrazí často při spouštění C# **ZÁMEK** příkaz, vyvolat **Monitor.Enter** metody nebo volání metody s **MethodImplOptions.Synchronized**atribut. Kolize zámků obvykle dochází, když vlákno _A_ získá uzamčení a vlákna _B_ pokusí získat stejné zámek před vlákno _A_ ho uvolní.

### <a id="ngencold"></a>Kód pro načtení ([STUDENÉHO])

Pokud název metody obsahuje **[STUDENOU]**, jako například **mscorlib.ni! [ COLD]System.Reflection.CustomAttribute.IsDefined**, modul runtime rozhraní .NET Framework spouští kód poprvé, která není optimalizována tak [profilováním řízená optimalizace](https://msdn.microsoft.com/library/e7k32f4k.aspx). Pro každou metodu by se měla zobrazit maximálně jednou během procesu.

Pokud načítání kódu trvá podstatnou část času požadavku, požadavek je první z nich provádět neoptimalizované část metody. Zvažte použití zahřívání proces, který provede část kódu, než vaši uživatelé k němu přístup.

### <a id="httpclientsend"></a>Odeslání požadavku HTTP

Metody jako **HttpClient.Send** znamenat, že kód čeká na dokončení požadavku HTTP.

### <a id="sqlcommand"></a>Operace databáze

Metody jako **SqlCommand.Execute** znamenat, že kód čeká na dokončení operace databáze.

### <a id="await"></a>Čekání (AWAIT\_čas)

**AWAIT\_čas** označuje, že kód čeká na dokončení jiné úlohy. Toto zpoždění obvykle dochází, se C# **AWAIT** příkazu. Když kód provádí C# **AWAIT**, vlákno unwinds a vrátí řízení do fondu vláken a neexistuje žádná vlákna, který je blokovaný, čeká **AWAIT** na dokončení. Však logicky, vlákna, která nebyla **AWAIT** "blokovaný," a čeká na dokončení operace. **AWAIT\_čas** příkaz označuje čas zablokování čekání na dokončení úlohy.

### <a id="block"></a>Čas zablokování

**BLOCKED_TIME** označuje, že kód je čekání na jiný prostředek k dispozici. Například může čekat pro synchronizační objekt, má být k dispozici vlákno nebo požadavek dokončit.

### <a id="cpu"></a>Čas procesoru

Procesor je zaneprázdněný, provádění pokynů.

### <a id="disk"></a>Doba disku

Aplikace provádí operace disku.

### <a id="network"></a>Čas sítě

Aplikace je provádění síťové operace.

### <a id="when"></a>Když sloupec

**Při** sloupec je vizualizace jak celkových vzorků shromážděných pro uzel mění v čase. Celkový rozsah žádosti je rozdělen na 32 časovým intervalům. Inkluzivní vzorky pro tento uzel se započítávají tyto kbelíky 32. Každý blok je vyjádřena jako panel. Výška panelu představuje škálovaný hodnotu. Pro uzly, které jsou označeny **CPU_TIME** nebo **BLOCKED_TIME**, nebo tam, kde je zřejmé vztah k využívání prostředků (například procesoru, disku nebo vlákno), na panelu představuje využití jednoho z prostředky během intervalu. Pro tyto metriky je možné získat hodnotu větší než 100 % jeho obsahu podle spotřebovávat více prostředků. Například pokud chcete použít, v průměru dva procesory během intervalu, získáte 200 procent.

## <a name="limitations"></a>Omezení

Výchozí dobu uchování dat je pět dní. Maximální množství dat, který se ingestuje za den je 10 GB.

Neúčtují žádné poplatky za používání služby Profiler. Abyste mohli používat, musí být webové aplikace hostované v alespoň na úrovni basic funkce Web Apps služby Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Režijní náklady a algoritmus vzorkování

Profiler se spustí náhodně dvě minuty každou hodinu na každém virtuálním počítači, který je hostitelem aplikace, která má Profiler pro zachytávání trasování povoleno. Když Profiler běží, přidá se z 5 na 15 procent zatížení procesoru na serveru.

## <a name="next-steps"></a>Další postup
Povolte Application Insights Profiler pro vaše aplikace Azure. Viz také:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a virtuálních počítačů škálovací sady](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
