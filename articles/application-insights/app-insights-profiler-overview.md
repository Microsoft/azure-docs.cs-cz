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
ms.openlocfilehash: c7266d840a8d60275a40cf31291d3a8f524131f9
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/04/2019
ms.locfileid: "54016734"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profil produkční aplikace v Azure pomocí Application Insights
## <a name="enable-profiler-for-your-application"></a>Povolit Profiler pro vaši aplikaci

Application Insights Profiler poskytuje trasování výkonu pro aplikace běžící v produkčním prostředí v Azure. Zachycení automaticky škálovaných dat avšak s negativním dopadem na koncové uživatele. Profiler vám umožní identifikovat "horkou" cestu kódu, který zabere nejvíce času při zpracování konkrétního webového požadavku. 

Profiler funguje s aplikacemi .net nasadit na následujících služeb Azure. Konkrétní pokyny pro povolení profileru pro každý typ služby jsou v níže uvedených odkazů.

* [App Services](../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače a Scalesets virtuálního počítače](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)

Pokud jste povolili Profiler, ale nezobrazují trasování, zkontrolujte naše [Průvodce řešením potíží.](app-insights-profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json)

Spuštění v místním prostředí Profiler není oficiálně podporován, ale jsme našli [pokynů můžete vyzkoušet.](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers)

## <a name="view-profiler-data"></a>Zobrazení dat profileru

Aby k odesílání trasování profileru vaše aplikace aktivně zpracování požaduje. Pokud provádíte experiment, můžete vygenerovat požadavky na vaši webovou aplikaci s použitím [testování výkonu Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Pokud jste povolili nově Profiler, spuštěním krátkého zátěžového testu. Je spuštěn zátěžový test, stiskněte **nyní profilu** tlačítko [ **stránka nastavení Profiler**](app-insights-profiler-settings.md#profiler-settings-page). Po spuštění profileru se bude Profilovat náhodně zhruba jednou za hodinu a po dobu dvou minut. Pokud vaše aplikace zpracovává nepřetržitý proud požadavků, Profiler odešlete trasování každou hodinu.

Poté, co vaše aplikace obdrží část provozu a profiler má určitá doba k nahrání trances, měli byste mít trasování můžete zobrazit. Tento proces může trvat 5 až 10 minut. Chcete-li zobrazit trasování, přejděte na **výkonu** vyberte **provést akce** zobrazení trasování profileru, a pak vyberte **Profiler trasování** tlačítko.

![Trasuje Profiler Application Insights výkon podokno náhledu][performance-blade]

Vyberte ukázku zobrazit rozpis úroveň kódu čas provádění požadavku.

![Průzkumník trasování Application Insights][trace-explorer]

V Průzkumníku trasování zobrazí následující informace:

* **Zobrazit kritickou cestu**: Otevře se největší listové uzly, nebo aspoň něco zavřít. Ve většině případů se tento uzel se blíží výkonu kritickým bodem.
* **Popisek**: Název funkce nebo události. Strom zobrazuje kombinaci kódu a události, ke kterým došlo (jako jsou SQL a HTTP události). Hlavní událost představuje celková doba trvání žádosti.
* **Uplynulý**: Časový interval mezi začátkem operace a konec operace.
* **Když**: Čas, kdy funkce nebo událostí byl spuštěn v relaci dalších funkcí.

## <a name="how-to-read-performance-data"></a>Čtení dat o výkonu

Profileru služeb Microsoft používá kombinaci metody odběru vzorků a instrumentace k analýze výkonu aplikace. Pokud probíhá shromažďování podrobné, profileru služeb ukázky ukazatele na instrukci každý počítač procesoru každých milisekund. Každá ukázka zaznamená úplný zásobník volání vlákna, který aktuálně spouští. Poskytuje podrobné informace o co bylo vlákno dělal, na vysoké úrovni a na nízké úrovni abstrakce. Profileru služeb shromažďuje také další události pro sledování aktivity korelace a příčiny, včetně kontextu přepínání události, události Task Parallel Library (TPL) a události fondu vláken.

Zásobník volání, který se zobrazí v zobrazení časové osy je výsledkem vzorkování a instrumentaci. Vzhledem k tomu každý vzorek zachytí úplný zásobník volání vlákna, obsahuje kód z rozhraní Microsoft .NET Framework a další architektury, které odkazujete.

### <a id="jitnewobj"></a>Přidělování objektů (clr! JIT\_nový nebo clr! JIT\_Newarr1)

**CLR! JIT\_nový** a **clr! JIT\_Newarr1** jsou pomocné funkce v rozhraní .NET Framework, které přidělují paměť ze spravované haldy. **CLR! JIT\_nový** je voláno, když je přidělen objektu. **CLR! JIT\_Newarr1** je vyvolána při přidělování pole objektu. Tyto dvě funkce jsou obvykle rychlé a trvat poměrně malé množství času. Pokud se zobrazí **clr! JIT\_nový** nebo **clr! JIT\_Newarr1** trvat značné množství času na vaší časové ose, znamená to, že kód může být přidělení mnoho objektů a spotřebovávat značné množství paměti.

### <a id="theprestub"></a>Kód pro načtení (clr! ThePreStub)

**CLR! ThePreStub** je pomocná funkce v rozhraní .NET Framework, který připraví kód pro spuštění poprvé. To obvykle zahrnuje, ale není omezený na kompilace just-in-time (JIT). Pro každý metoda C# **clr! ThePreStub** má být volána po celou dobu životnosti procesu nejvýše jednou.

Pokud **clr! ThePreStub** přijímá velké množství času požadavku, to znamená, je první z nich, který se spustí dané metody požadavku. Čas potřebný pro modul runtime rozhraní .NET Framework se načíst první způsob je důležité. Zvažte použití zahřívání procesu, který se spustí předtím, než vaši uživatelé k němu přístup, nebo zvažte spuštění Native Image Generator (ngen.exe) část kódu na vaše sestavení.

### <a id="lockcontention"></a>Kolize zámků (clr! JITutil\_MonContention nebo clr! JITutil\_MonEnterWorker)

**CLR! JITutil\_MonContention** nebo **clr! JITutil\_MonEnterWorker** označuje, že aktuální vlákno čeká na zámek a tím uvolnit. Tento text se zobrazí často při spouštění jazyka C# **ZÁMEK** příkaz při vyvolání **Monitor.Enter** metodu, nebo při vyvolání metody s **MethodImplOptions.Synchronized** atribut. Kolize zámků obvykle dochází, když vlákno _A_ získá uzamčení a vlákna _B_ pokusí získat stejné zámek před vlákno _A_ ho uvolní.

### <a id="ngencold"></a>Kód pro načtení ([STUDENÉHO])

Pokud název metody obsahuje **[COLD]**, jako například **mscorlib.ni! [COLD]System.Reflection.CustomAttribute.IsDefined**, modul runtime rozhraní .NET Framework je provádění kódu poprvé není optimalizována podle <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimalizace na základě profilu</a>. Pro každou metodu by se měla zobrazit maximálně jednou po celou dobu životnosti procesu.

Pokud načítání kódu trvá podstatnou část času pro žádost, to znamená, že jsou první z nich provádět neoptimalizované část metody. Zvažte použití zahřívání proces, který provede část kódu, než vaši uživatelé k němu přístup.

### <a id="httpclientsend"></a>Odeslání požadavku HTTP

Metody, jako jsou **HttpClient.Send** znamenat, že kód čeká na dokončení požadavku HTTP.

### <a id="sqlcommand"></a>Operace databáze

Metody, jako jsou **SqlCommand.Execute** znamenat, že kód čeká na dokončení operace databáze.

### <a id="await"></a>Čekání (AWAIT\_čas)

**AWAIT\_čas** označuje, že kód čeká na dokončení jiné úlohy. To obvykle dochází v jazyce C# **AWAIT** příkazu. Když kód provádí C# **AWAIT**, vlákno unwinds a vrátí řízení do fondu vláken a neexistuje žádná vlákna, který je blokovaný, čeká **AWAIT** na dokončení. Však logicky, vlákna, která nebyla **AWAIT** je "Uzavřeno" a čeká na dokončení operace. **AWAIT\_čas** příkaz označuje čas zablokování čekání na dokončení úlohy.

### <a id="block"></a>Čas zablokování

**BLOCKED_TIME** označuje, že kód je čekání na jiný prostředek k dispozici. Například může čekat pro synchronizační objekt, má být k dispozici vlákno nebo požadavek dokončit.

### <a id="cpu"></a>Čas procesoru

Procesor je zaneprázdněný, provádění pokynů.

### <a id="disk"></a>Doba disku

Aplikace provádí operace disku.

### <a id="network"></a>Čas sítě

Aplikace je provádění síťové operace.

### <a id="when"></a>Když sloupec

**Při** sloupec je vizualizace jak celkových vzorků shromážděných pro uzel mění v čase. Celkový rozsah žádosti je rozdělen na 32 časovým intervalům. Inkluzivní vzorky pro tento uzel se započítávají tyto kbelíky 32. Každý blok je vyjádřena jako panel. Výška panelu představuje škálovaný hodnotu. Pro uzly, které jsou označeny **CPU_TIME** nebo **BLOCKED_TIME**, nebo tam, kde je zřejmé vztah k využívání prostředků (například procesoru, disku nebo vlákno), na panelu představuje využití jednoho z prostředky v období tohoto sektoru. Pro tyto metriky je možné získat hodnotu větší než 100 % jeho obsahu podle spotřebovávat více prostředků. Například pokud chcete použít, v průměru dva procesory během intervalu, získáte 200 procent.

## <a name="limitations"></a>Omezení

Výchozí dobu uchování dat je pět dní. Maximální množství dat, který se ingestuje za den je 10 GB.

Neúčtují žádné poplatky za používání služby Profiler. Chcete-li použít službu Profiler, musí být webové aplikace hostované v alespoň na úrovni Basic služby Web Apps.

## <a name="overhead-and-sampling-algorithm"></a>Režijní náklady a algoritmus vzorkování

Profiler se spustí náhodně dvě minuty každou hodinu na každém virtuálním počítači, který je hostitelem aplikace, která má Profiler pro zachytávání trasování povoleno. Když Profiler běží, přidá se z 5 procent do 15 procent zatížení procesoru na serveru.

## <a name="next-steps"></a>Další kroky
Povolit Application Insights Profiler pro vaše aplikace Azure
* [App Services](../azure-monitor/app/profiler.md?toc=/azure/azure-monitor/toc.json)
* [Cloud Services](app-insights-profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Aplikace Service Fabric](app-insights-profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače a Scalesets virtuálního počítače](app-insights-profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/app-insights-profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler-overview/trace-explorer.png
