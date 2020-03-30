---
title: Profilování produkčních aplikací v Azure pomocí Profileru přehledů aplikací
description: Identifikujte horkou cestu v kódu webového serveru pomocí profileru s nízkým imstopací.
ms.topic: conceptual
author: cweining
ms.author: cweining
ms.date: 08/06/2018
ms.reviewer: mbullwin
ms.openlocfilehash: ce952bd248640d03fcff43284707614577df8469
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77671643"
---
# <a name="profile-production-applications-in-azure-with-application-insights"></a>Profilprodukční aplikace v Azure s Application Insights
## <a name="enable-application-insights-profiler-for-your-application"></a>Povolení profileru přehledů aplikací pro vaši aplikaci

Azure Application Insights Profiler poskytuje sledování výkonu pro aplikace, které běží v produkčním prostředí v Azure. Profiler zachytí data automaticky ve velkém měřítku bez negativního ovlivnění uživatelů. Profiler pomáhá identifikovat "horké" cesta kódu, která trvá nejdelší dobu, když zpracovává konkrétní webový požadavek. 

Profiler pracuje s aplikacemi .NET, které jsou nasazené na následujících službách Azure. Konkrétní pokyny pro povolení Profiler pro každý typ služby jsou v odkazech níže.

* [Azure App Service](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače Azure a škálovací sady virtuálních počítačů](profiler-vm.md?toc=/azure/azure-monitor/toc.json)
* [**NÁHLED** ASP.NET základní webové aplikace Azure Pro Linux](profiler-aspnetcore-linux.md?toc=/azure/azure-monitor/toc.json) 

Pokud jste povolili Profiler, ale nevidíte trasování, podívejte se do našeho [průvodce odstraňováním potíží](profiler-troubleshooting.md?toc=/azure/azure-monitor/toc.json).

## <a name="view-profiler-data"></a>Zobrazení dat profileru

Aby profiler mohl nahrávat trasování, musí vaše aplikace aktivně zpracovávat požadavky. Pokud provádíte experiment, můžete generovat požadavky na webovou aplikaci pomocí [testování výkonu Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Pokud jste nově povolili Profiler, můžete spustit krátký zátěžový test. Během zátěžového testu vyberte tlačítko **Profil nyní** v [podokně Nastavení **profileru** ](profiler-settings.md). Při profileru je spuštěn, profily náhodně asi jednou za hodinu a po dobu dvou minut. Pokud vaše aplikace zpracovává stálý proud požadavků, Profiler nahraje trasování každou hodinu.

Poté, co vaše aplikace obdrží nějaký provoz a Profiler má čas nahrát trasování, měli byste mít trasování k zobrazení. Tento proces může trvat 5 až 10 minut. Chcete-li zobrazit trasování, vyberte v podokně **Výkon** **položku Provést akce**a pak vyberte tlačítko **Trasování profileru.**

![Trasování profileru profilování náhledu přehledů přehledů aplikací][performance-blade]

Vyberte ukázku, chcete-li zobrazit rozpis času stráveného prováděním požadavku na úrovni kódu.

![Průzkumník trasování Přehledy aplikací][trace-explorer]

Průzkumník trasování zobrazuje následující informace:

* **Zobrazit horkou cestu:** Otevře největší listový uzel nebo alespoň něco blízkého. Ve většině případů se tento uzel nachází v blízkosti kritického místa výkonu.
* **Popisek**: Název funkce nebo události. Strom zobrazuje kombinaci kódu a událostí, ke kterým došlo, jako jsou například události SQL a HTTP. Hlavní událost představuje celkovou dobu trvání požadavku.
* **Uplynulý**: Časový interval mezi zahájením operace a koncem operace.
* **Kdy**: Čas, kdy byla spuštěna funkce nebo událost ve vztahu k ostatním funkcím.

## <a name="how-to-read-performance-data"></a>Jak číst údaje o výkonu

Profilování služby společnosti Microsoft používá kombinaci metod vzorkování a instrumentace k analýze výkonu vaší aplikace. Když probíhá podrobné shromažďování, profiler služby vzorky ukazatel instrukce každého procesoru počítače každou milisekundu. Každá ukázka zachycuje kompletní zásobník volání vlákna, který je aktuálně spuštěn. Poskytuje podrobné informace o tom, co toto vlákno dělalo, a to jak na vysoké úrovni, tak na nízké úrovni abstrakce. Profiler služby také shromažďuje další události ke sledování korelace aktivita a příčinné souvislosti, včetně události přepínání kontextu, události paralelní knihovny úloh (TPL) a události fondu vláken.

Zásobník volání, který je zobrazen v zobrazení časové osy, je výsledkem vzorkování a instrumentace. Vzhledem k tomu, že každá ukázka zachycuje celý zásobník volání vlákna, obsahuje kód z rozhraní Microsoft .NET Framework a z jiných rozhraní, na které odkazujete.

### <a name="object-allocation-clrjit_new-or-clrjit_newarr1"></a><a id="jitnewobj"></a>Přidělení objektu (clr! JIT\_Nové nebo CLR! JIT\_Newarr1)

**Clr! JIT\_Nové** a **CLR! JIT\_Newarr1** jsou pomocné funkce v rozhraní .NET Framework, které přidělují paměť ze spravované haldy. **clr! JIT\_New** je vyvolána při přidělení objektu. **clr! JIT\_Newarr1** je vyvolána při přidělení pole objektu. Tyto dvě funkce jsou obvykle rychlé a trvat relativně malé množství času. Pokud **CLR! JIT\_Nové** nebo **CLR! JIT\_Newarr1** trvá hodně času ve vaší časové ose, kód může být přidělování mnoho objektů a spotřebovává značné množství paměti.

### <a name="loading-code-clrtheprestub"></a><a id="theprestub"></a>Nakládací kód (clr! PreStub)

**clr! PreStub** je pomocná funkce v rozhraní .NET Framework, která připraví kód ke spuštění poprvé. Toto spuštění obvykle zahrnuje, ale není omezena na kompilaci just-in-time (JIT). Pro každou metodu Jazyka C# **clr! PreStub** by měl být vyvolán maximálně jednou během procesu.

Pokud **CLR! PreStub** trvá dlouhou dobu pro požadavek, požadavek je první, kdo provede tuto metodu. Čas pro spuštění rozhraní .NET Framework pro načtení první metody je významný. Můžete zvážit použití procesu zahřívání, který spustí tuto část kódu před tím, než k němu uživatelé přistupují, nebo zvažte spuštění generátoru nativních bitových obrázků (ngen.exe) v sestaveních.

### <a name="lock-contention-clrjitutil_moncontention-or-clrjitutil_monenterworker"></a><a id="lockcontention"></a>Zámek tvrzení (clr! JITutil\_MonContention nebo clr! JITutil\_MonEnterWorker)

**Clr! JITutil\_MonContention** nebo **clr! JITutil\_MonEnterWorker** označuje, že aktuální vlákno čeká na uvolnění zámku. Tento text se často zobrazuje při spuštění příkazu C# **LOCK,** vyvolání metody **Monitor.Enter** nebo vyvolání metody s atributem **MethodImplOptions.Synchronized.** Zámek tvrzení obvykle dochází, když vlákno _A_ získá zámek a vlákno _B_ se pokusí získat stejný zámek před vlákno _a_ uvolní.

### <a name="loading-code-cold"></a><a id="ngencold"></a>Nakládací kód ([COLD])

Pokud název metody obsahuje **[COLD]**, například **mscorlib.ni![ COLD]System.Reflection.CustomAttribute.IsDefined**, modul runtime rozhraní .NET Framework poprvé provádí kód, který není optimalizován [optimalizací s asistencí profilu](/cpp/build/profile-guided-optimizations). Pro každou metodu by měla být zobrazena maximálně jednou během procesu.

Pokud načítání kódu trvá značné množství času pro požadavek, požadavek je první, kdo provede neoptimalizovanou část metody. Zvažte použití zahřívací proces, který spustí tuto část kódu před uživateli přístup.

### <a name="send-http-request"></a><a id="httpclientsend"></a>Odeslat požadavek HTTP

Metody, jako je **httpclient.send** označují, že kód čeká na dokončení požadavku HTTP.

### <a name="database-operation"></a><a id="sqlcommand"></a>Operace databáze

Metody, jako je **sqlcommand.execute** označují, že kód čeká na dokončení operace databáze.

### <a name="waiting-await_time"></a><a id="await"></a>Čekání (AWAIT\_TIME)

**AWAIT\_TIME** označuje, že kód čeká na dokončení jiné úlohy. K tomuto zpoždění obvykle dochází s příkazem C# **AWAIT.** Když kód provádí C# **AWAIT**, vlákno odvíjí a vrátí ovládací prvek do fondu vláken a neexistuje žádné vlákno, které je blokováno čekání na dokončení **aWAIT.** Logicky je však vlákno, které provedlo **aWAIT** , "blokováno" a čeká na dokončení operace. Příkaz **AWAIT\_TIME** označuje blokovaný čas čekání na dokončení úkolu.

### <a name="blocked-time"></a><a id="block"></a>Blokovaný čas

**BLOCKED_TIME** označuje, že kód čeká na jiný prostředek k dispozici. Může například čekat na objekt synchronizace, na vlákno, které má být k dispozici, nebo pro dokončení požadavku.

### <a name="unmanaged-async"></a>Nespravovaná synchronizace

Rozhraní .NET Framework vyzařuje události ETW a předává ID aktivity mezi vlákny, takže asynchronní volání lze sledovat napříč vlákny. Nespravovaný kód (nativní kód) a některé starší styly asynchronního kódu chybí tyto události a ID aktivity, takže profiler nemůže zjistit, jaké vlákno a jaké funkce jsou spuštěny ve vlákně. To je označeno jako "Nespravovaná asynchronizace" v zásobníku volání. Pokud si stáhnete soubor ETW, budete moci použít [PerfView](https://github.com/Microsoft/perfview/blob/master/documentation/Downloading.md) získat více informací o tom, co se děje.

### <a name="cpu-time"></a><a id="cpu"></a>Čas procesoru

Procesor je zaneprázdněn prováděním pokynů.

### <a name="disk-time"></a><a id="disk"></a>Čas disku

Aplikace provádí operace na disku.

### <a name="network-time"></a><a id="network"></a>Čas sítě

Aplikace provádí síťové operace.

### <a name="when-column"></a><a id="when"></a>Když sloupec

Když **When** sloupec je vizualizace, jak inclusive vzorky shromážděné pro uzel se liší v čase. Celkový rozsah požadavku je rozdělen do 32 časových intervalů. Včetně vzorky pro tento uzel jsou nahromaděny v těchto 32 kbelíky. Každý kbelík je reprezentován jako bar. Výška pruhu představuje hodnotu měřítka. U uzlů, které jsou označeny **CPU_TIME** nebo **BLOCKED_TIME**nebo kde existuje zřejmý vztah ke spotřebování prostředku (například procesor, disk nebo vlákno), představuje pruh spotřebu jednoho z prostředků během bloku. Pro tyto metriky je možné získat hodnotu větší než 100 procent tím, že spotřebuje více prostředků. Pokud například během intervalu použijete v průměru dva procesory, získáte 200 procent.

## <a name="limitations"></a>Omezení

Výchozí doba uchovávání dat je pět dní. Maximální počet dat, která se ingutuje za den, je 10 GB.

Za používání služby Profiler se neúčtují žádné poplatky. Abyste ji měli používat, musí být vaše webová aplikace hostovaná alespoň v základní vrstvě funkce Webové aplikace služby Azure App Service.

## <a name="overhead-and-sampling-algorithm"></a>Algoritmus režie a vzorkování

Profiler náhodně spustí dvě minuty každou hodinu na každém virtuálním počítači, který je hostitelem aplikace, která má profiler povolen pro zachycení trasování. Při profileru je spuštěna, přidá od 5 do 15 procent režie procesoru na server.

## <a name="next-steps"></a>Další kroky
Povolte Profiler přehledů aplikací pro vaši aplikaci Azure. Další zdroje informací:
* [App Services](profiler.md?toc=/azure/azure-monitor/toc.json)
* [Azure Cloud Services](profiler-cloudservice.md?toc=/azure/azure-monitor/toc.json)
* [Azure Service Fabric](profiler-servicefabric.md?toc=/azure/azure-monitor/toc.json)
* [Virtuální počítače Azure a škálovací sady virtuálních počítačů](profiler-vm.md?toc=/azure/azure-monitor/toc.json)


[performance-blade]: ./media/profiler-overview/performance-blade-v2-examples.png
[trace-explorer]: ./media/profiler-overview/trace-explorer.png
