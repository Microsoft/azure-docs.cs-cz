---
title: Profilace produkčních aplikací v Azure pomocí Application Insights Profiler
description: Identifikujte cestu k Hotu v kódu webového serveru pomocí profileru s nízkou úrovní.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: 0d3074d58560df5cb5bd6bdc2c0437a4be828918
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86499388"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilace produkčních aplikací v Azure pomocí Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Povolení Application Insights Profiler pro vaši aplikaci

Azure Application Insights Profiler poskytuje trasování výkonu pro aplikace, které běží v produkčním prostředí v Azure. Profiler automaticky zachycuje data ve velkém měřítku bez negativního vlivu na uživatele. Profiler vám pomůže identifikovat "horkou" cestu kódu, která při zpracování konkrétního webového požadavku zabere nejdelší čas. 

Profiler funguje s aplikacemi .NET nasazenými na následujících službách Azure. Konkrétní pokyny pro povolení profileru pro každý typ služby jsou uvedené v odkazech níže.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**Náhled** ASP.NET Core Azure Linux Web Apps](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Pokud jste povolili profilování, ale nevidíte trasování, podívejte se na naše [Průvodce odstraňováním potíží](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Zobrazit data profileru

Aby Profiler mohl nahrávat trasování, musí vaše aplikace aktivně zpracovávat požadavky. Pokud provádíte experiment, můžete vygenerovat žádosti do vaší webové aplikace pomocí [Application Insights testování výkonu](/vsts/load-test/app-service-web-app-performance-test). Pokud máte nově povolený Profiler, můžete spustit krátký zátěžový test. Když je spuštěn zátěžový test, v [podokně **Nastavení profileru** ](profiler-settings.md)vyberte tlačítko **profil nyní** . Když je profiler spuštěný, profiluje se náhodně přibližně jednou za hodinu a po dobu 2 minut. Pokud vaše aplikace zpracovává konstantní datový proud požadavků, profilery nahraje každou hodinu.

Poté, co vaše aplikace obdrží nějaký provoz a Profiler měl čas na nahrání trasování, byste měli mít trasování k zobrazení. Tento proces může trvat 5 až 10 minut. Chcete-li zobrazit trasování, v podokně **výkon** vyberte **provést akce**a pak vyberte tlačítko **Profiler trasování** .

![Application Insights náhledu podokna výkonu náhled trasování profileru][performance-blade]

Vyberte ukázku pro zobrazení rozpisu na úrovni kódu pro čas strávený prováděním požadavku.

![Průzkumník trasování Application Insights][trace-explorer]

V Průzkumníku trasování se zobrazí následující informace:

* **Zobrazit horkou cestu**: otevře největší uzel na list nebo alespoň něco blízko. Ve většině případů se tento uzel blíží nekritickému výkonu.
* **Label**: název funkce nebo události. Strom zobrazuje kombinaci kódu a událostí, ke kterým došlo, jako jsou události SQL a HTTP. Nejvyšší událost představuje celkovou dobu trvání žádosti.
* **Uplynulý**: časový interval mezi začátkem operace a koncem operace.
* **Kdy**: čas, kdy byla funkce nebo událost spuštěna ve vztahu k jiným funkcím.

## <a name="how-to-read-performance-data"></a>Jak číst údaje o výkonu

Microsoft Service Profiler používá kombinaci metod vzorkování a instrumentace k analýze výkonu aplikace. V případě, že probíhá podrobná kolekce, Profiler služby v každé milisekundě vyvzorkuje ukazatel na instrukci každého procesoru počítače. Každý vzorek zachytí úplný zásobník volání vlákna, které je právě prováděno. Poskytuje podrobné informace o tom, co vlákno provádělo, na nejvyšší úrovni a na nízké úrovni abstrakce. Profiler služby také shromažďuje další události pro sledování korelace aktivity a jejich příčiny, včetně událostí přepnutí kontextu, událostí paralelní knihovny (TPL) a událostí fondu vláken.

Zásobník volání zobrazený v zobrazení Časová osa je výsledkem vzorkování a instrumentace. Vzhledem k tomu, že každá ukázka zachycuje úplný zásobník volání vlákna, zahrnuje kód z Microsoft .NET Framework a z jiných rozhraní, na které odkazujete.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Přidělení objektů (CLR! JIT \_ New nebo CLR! JIT \_ Newarr1)

**CLR! JIT JIT \_ New** a **CLR! JIT \_ Newarr1** jsou pomocné funkce v .NET Framework, které přidělují paměť ze spravované haldy. **CLR! JIT \_ ** je vyvolána nově, když je objekt přidělen. **CLR! \_NEWARR1 JIT** se vyvolá při přidělení pole objektu. Tyto dvě funkce jsou obvykle rychlé a mohou trvat poměrně krátké množství času. Pokud **CLR! JIT \_ New** nebo **CLR! \_NEWARR1 JIT** na časové ose zabere spoustu času, kód může přidělit mnoho objektů a spotřebovávat značné množství paměti.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Načítání kódu (CLR! ThePreStub)

**CLR! ThePreStub** je pomocná funkce v .NET Framework, která připravuje kód pro první spuštění. Toto provedení obvykle zahrnuje, ale není omezeno na kompilaci JIT (just-in-time). Pro každou metodu jazyka C#, **CLR! ThePreStub** by mělo být vyvoláno nejvýše jednou během procesu.

Pokud **CLR! ThePreStub** trvá pro požadavek dlouhou dobu, jedná se o první požadavek, který tuto metodu spustí. Čas, kdy má modul runtime .NET Framework načíst první metodu, je významný. Můžete zvážit použití procesu zahřívání, který spustí tuto část kódu předtím, než k němu uživatelé přistupují, nebo můžete ve svých sestaveních spustit generátor nativních bitových kopií (ngen.exe).

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Zamykání zámků (CLR! JITutil \_ MonContention nebo CLR! JITutil \_ MonEnterWorker)

**CLR! JITutil \_ MonContention** nebo **CLR! JITutil \_ MonEnterWorker** označuje, že aktuální vlákno čeká na uvolnění zámku. Tento text se často zobrazuje při spuštění příkazu **zámku** jazyka C#, vyvolání metody **monitor. Enter** nebo vyvolání metody s atributem **MethodImplOptions. Synchronized** . K uzamčení zámku obvykle dochází, když vlákno _a získá zámek_ a vlákno _B_ se pokusí získat stejný zámek _před uvolněním_ vlákna.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Načítání kódu ([STUDENá])

Pokud název metody obsahuje **[studená]**, například **mscorlib.ni! [ STUDENá] System. Reflection. CustomAttribute.-defined**, .NET Framework runtime spouští kód poprvé, který není optimalizován [optimalizací](/cpp/build/profile-guided-optimizations)na základě profilu. Pro každou metodu by se měla v průběhu procesu zobrazit maximálně jednou.

Pokud načítání kódu pro požadavek trvá značnou dobu, je požadavek první z nich pro provedení neoptimalizované části metody. Zvažte použití procesu zahřívání, který spustí část kódu předtím, než k němu uživatelé přistupují.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Odeslat požadavek HTTP

Metody jako **HttpClient. Send** označují, že kód čeká na dokončení požadavku HTTP.

### <a name="database-operation"></a><a id="sqlcommand"></a>Databázová operace

Metody jako **SqlCommand.Exeroztomilá** označují, že kód čeká na dokončení operace databáze.

### <a name="waiting-await_time"></a><a id="await"></a>Čekání ( \_ čas čekání)

**Očekává \_ se ČAS** znamená, že kód čeká na dokončení jiné úlohy. K tomuto zpoždění obvykle dochází v příkazu C# **AWAIT** . V případě, že kód v jazyce C# **očekává**, vlákno odvíjí a vrátí řízení fondu vláken a neexistuje žádné vlákno, které je blokováno pro **dokončení čekání.** Logicky ale vlákno, které **čekalo** , je "blokované" a čeká na dokončení operace. Příkaz **AWAIT \_ Time** označuje čas zablokování, který čeká na dokončení úlohy.

### <a name="blocked-time"></a><a id="block"></a>Čas zablokování

**BLOCKED_TIME** označuje, že kód čeká na zpřístupnění jiného prostředku. Může například čekat na objekt synchronizace, pro vlákno k dispozici nebo pro požadavek na dokončení.

### <a name="unmanaged-async"></a>Nespravované asynchronní

Rozhraní .NET Framework generuje události ETW a předá ID aktivity mezi vlákny, aby bylo možné sledovat asynchronní volání napříč vlákny. Nespravovaný kód (nativní kód) a některé starší styly asynchronního kódu neobsahují tyto události a ID aktivit, takže profiler nemůže sdělit, které vlákno a jaké funkce jsou ve vlákně spuštěné. Toto je označeno jako nespravované asynchronní v zásobníku volání. Pokud stáhnete soubor trasování událostí pro Windows, možná budete moct pomocí [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md)  získat další přehled o tom, co se děje.

### <a name="cpu-time"></a><a id="cpu"></a>Čas procesoru

PROCESOR je zaneprázdněn prováděním pokynů.

### <a name="disk-time"></a><a id="disk"></a>Čas disku

Aplikace provádí operace na disku.

### <a name="network-time"></a><a id="network"></a>Čas sítě

Aplikace provádí síťové operace.

### <a name="when-column"></a><a id="when"></a>Když sloupec

Sloupec **when** je vizualizace, jak se shromažďují celkové vzorky pro uzel v průběhu času. Celkový rozsah žádosti je dělený na 32 časových intervalů. Zahrnuté vzorky pro tento uzel se shromažďují v těchto intervalech 32. Jednotlivé intervaly jsou reprezentovány jako pruhy. Výška pruhu představuje hodnotu škálované. Pro uzly, které jsou označeny **CPU_TIME** nebo **BLOCKED_TIME**nebo kde existuje zřejmá relace pro využívání prostředku (například CPU, disku nebo vlákna), představuje tento pruh spotřebu jednoho ze zdrojů během intervalu. Pro tyto metriky je možné získat hodnotu větší než 100% díky využívání více prostředků. Pokud například použijete v průměru dva procesory během intervalu, dostanete 200 procent.

## <a name="limitations"></a>Omezení

Výchozí doba uchovávání dat je pět dní. Maximální množství dat, která se ingestují za den, je 10 GB.

Za použití služby profileru se neúčtují žádné poplatky. Pokud ji chcete použít, musí být webová aplikace hostována alespoň na úrovni Basic funkce Web Apps Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmus režie a vzorkování

Profiler náhodně spustí dvě minuty každou hodinu na každém virtuálním počítači, který je hostitelem aplikace s povoleným profilerem pro zachytávání trasování. Když je profiler spuštěný, přidává se na server od 5 do 15 procent zatížení procesoru.

## <a name="next-steps"></a>Další kroky
Povolte Application Insights Profiler pro vaši aplikaci Azure. Další zdroje informací:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Azure Virtual Machines a Virtual Machine Scale Sets](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
