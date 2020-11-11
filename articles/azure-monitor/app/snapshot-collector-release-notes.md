---
title: Poznámky k verzi balíčku NuGet Microsoft. ApplicationInsights. SnapshotCollector – Application Insights
description: Zpráva k vydání verze balíčku NuGet Microsoft. ApplicationInsights. SnapshotCollector, kterou používá Application Insights Snapshot Debugger
ms.topic: conceptual
author: pharring
ms.author: pharring
ms.date: 11/10/2020
ms.openlocfilehash: faacb78ca7f055bb8e9a469afb894bc30ee87501
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94507456"
---
# <a name="release-notes-for-microsoftapplicationinsightssnapshotcollector"></a>Poznámky k verzi pro Microsoft. ApplicationInsights. SnapshotCollector

Tento článek obsahuje poznámky k vydání balíčku NuGet Microsoft. ApplicationInsights. SnapshotCollector pro aplikace .NET, které používá Application Insights Snapshot Debugger.

[Přečtěte si další informace](./snapshot-debugger.md) o Snapshot Debugger Application Insights pro aplikace .NET.

V případě zpráv o chybách a zpětná vazba otevřete problém na GitHubu. https://github.com/microsoft/ApplicationInsights-dotnet

## <a name="release-notes"></a>Poznámky k verzi

## <a name="1373"></a>[1.3.7.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7.3)
Bod vydání, který řeší několik problémů s vysokým dopadem.
### <a name="bug-fixes"></a>Opravy chyb
- Pevné zjišťování PDB ve složce Wwwroot/bin, která se přerušila, když jsme změnili algoritmus hledání symbolů v 1.3.6.
- Pevný ExtractWasCalledMultipleTimesException s vysokou úrovní v telemetrie.

## <a name="137"></a>[1.3.7](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.7)
### <a name="changes"></a>Změny
Cíl netcoreapp 2.0 SnapshotCollector závisí na Microsoft. ApplicationInsights. AspNetCore >= 2.1.1 (znovu). Tím se vrátí chování před 1.3.5. Snažili jsme se upgradovat v 1.3.6, ale podařilo přerušit některé scénáře Azure App Service.
### <a name="new-features"></a>Nové funkce
Snapshot Collector přečte a analyzuje ConnectionString z proměnné prostředí APPLICATIONINSIGHTS_CONNECTION_STRING nebo z TelemetryConfiguration. Primárně se používá k nastavení koncového bodu pro připojení ke službě snímku. Další informace najdete v dokumentaci k [připojovacím řetězcům](./sdk-connection-string.md).
### <a name="bug-fixes"></a>Opravy chyb
- Přepnuto na použití HttpClient pro všechny cíle s výjimkou Net45, protože v některých prostředích došlo k selhání WebRequest v důsledku nekompatibilního tato SecurityProtocol (vyžaduje TLS 1,2).

## <a name="136"></a>[1.3.6](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.6)
### <a name="changes"></a>Změny
- SnapshotCollector nyní závisí na Microsoft. ApplicationInsights >= 2.5.1 pro všechny cílové platformy. To může být zásadní změna, pokud vaše aplikace závisí na starší verzi sady Microsoft. ApplicationInsights SDK.
- Odeberte podporu TLS 1,0 a 1,1 v nástroji pro odeslání snímku.
- Období prohledávání PDB teď ve výchozím nastavení 24 hodin místo 15 minut. Konfigurovatelné prostřednictvím PdbRescanInterval v SnapshotCollectorConfiguration.
- Kontrola PDB vyhledává pouze složky nejvyšší úrovně namísto rekurzivního. To může být zásadní změna, pokud jsou symboly v podsložkách binární složky.
### <a name="new-features"></a>Nové funkce
- Log rotace v SnapshotUploader, abyste se vyhnuli vyplňování složky Logs starými soubory.
- Podpora deoptimalizace (prostřednictvím ReJIT při připojení) pro aplikace .NET Core 3,0
- Přidejte symboly do balíčku NuGet.
- Nastavte další metadata při nahrávání Mini výpisy.
- Do SnapshotCollectorTelemetryProcessor se přidala inicializovaná vlastnost. Je to CancellationToken, který se zruší, když se Snapshot Collector kompletně Inicializuje a připojí ke koncovému bodu služby.
- Snímky se teď dají zachytit pro výjimky v dynamicky generovaných metodách. Například stromy kompilovaných výrazů generované dotazy Entity Framework.
### <a name="bug-fixes"></a>Opravy chyb
- AmbiguousMatchException – načítání Snapshot Collector z důvodu Monitorování stavu.
- Metoda rozšíření GetSnapshotCollector nyní vyhledává všechny TelemetrySinks.
- Nespouštějte na nepodporovaných platformách odeslání snímku.
- Při deoptimalizaci dynamických metod zpracovat popisovač InvalidOperationException (například Entity Framework)

## <a name="135"></a>[1.3.5](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.5)
- Přidání podpory pro cloudy v svrchovaném (starší verze nebudou fungovat ve cloudech v svrchovaném cloudu)
- Přidávání kolekce snímků bylo snazší pomocí AddSnapshotCollector (). Další informace najdete [tady](./snapshot-debugger-appservice.md).
- Pro ověřování bloků objektů BLOB použijte nastavení FISMA MD5. Tím se vyhnete výchozímu šifrovacímu algoritmu MD5 .NET, který není k dispozici, když je operační systém nastaven na režim kompatibilní se standardem FIPS.
- Při deoptimalizaci volání funkcí ignorovat .NET Framework snímky. Toto chování může být řízeno nastavením konfigurace DeoptimizeIgnoredModules.
- Přidejte `DeoptimizeMethodCount` nastavení konfigurace, které umožňuje deoptimalizaci více než jednoho volání funkce. Další informace

## <a name="134"></a>[1.3.4](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.4)
- Povolí strukturované klíče instrumentace.
- Zvýšit robustní SnapshotUploader – pokračovat v spouštění i v případě, že staré protokoly odeslání nejde přesunout.
- Opětovné povolení generování sestav další telemetrie, když se SnapshotUploader.exe okamžitě ukončí (byla zakázána v 1.3.3).
- Zjednodušení interní telemetrie.
- _Experimentální funkce_ : plány kolekce snímkovací bod: Add "snapshotOnFirstOccurence". Další informace najdete [tady](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).

## <a name="133"></a>[1.3.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.3)
- Opravili jsme chybu, která způsobila, že SnapshotUploader.exe zablokovat a Nenahrávat snímky pro aplikace .NET Core.

## <a name="132"></a>[1.3.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.2)
- _Experimentální funkce_ : plány kolekce snímkovací bod. Další informace najdete [tady](https://gist.github.com/alexaloni/5b4d069d17de0dabe384ea30e3f21dfe).
- SnapshotUploader.exe ukončí, když modul runtime uvolní třídu AppDomain, ze které je načteno SnapshotCollector, namísto čekání na ukončení procesu. Tím se zlepší spolehlivost sběrače při hostování ve službě IIS.
- Přidejte konfiguraci pro povolení více instancí SnapshotCollector, které používají stejný klíč instrumentace ke sdílení stejného SnapshotUploader procesu: ShareUploaderProcess (standardně `true` ).
- Ohlásit další telemetrii, když se SnapshotUploader.exe okamžitě ukončí.
- Byl snížen počet souborů podpory SnapshotUploader.exe musí zapisovat na disk.

## <a name="131"></a>[1.3.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.1)
- Odeberte podporu pro shromažďování snímků pomocí rozhraní RtlCloneUserProcess API a PssCaptureSnapshots API.
- Zvyšte výchozí limit počtu snímků, které mohou být zachyceny během 10 minut od 1 do 3.
- Povolí SnapshotUploader.exe vyjednávat TLS 1,1 a 1,2.
- Ohlásit další telemetrii, když SnapshotUploader zaznamená upozornění nebo chybu
- Zastavení pořizování snímků, když back-end služba hlásí denní kvótu (50 snímků za den)
- Přidejte další vrácení se změnami SnapshotUploader.exe, aby nepovolovala spuštění dvou instancí ve stejnou dobu.

## <a name="130"></a>[1.3.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.3.0)
### <a name="changes"></a>Změny
- Pro aplikace, které cílí na .NET Framework, Snapshot Collector teď závisí na Microsoft. ApplicationInsights verze 2.3.0 nebo vyšší.
Používá se 2.2.0 nebo vyšší.
Domníváme se, že se nejedná o problém pro většinu aplikací, ale dejte nám prosím jistotu, že pokud se vám tato změna zabrání v použití nejnovější Snapshot Collector.
- Při opakovaném pokusu o nezdařená odeslání použijte exponenciální prodlevy v nástroji pro odeslání snímku.
- Pro spolehlivou tvorbu sestav telemetrie použijte ServerTelemetryChannel (Pokud je k dispozici).
- Při počátečním připojení ke službě Snapshot Debugger použijte ' SdkInternalOperationsMonitor ', aby byla ignorována sledováním závislostí.
- Vylepšete telemetrii kolem počátečního připojení ke službě Snapshot Debugger.
- Ohlásit další telemetrii pro:
  - Verze Azure App Service
  - Výpočetní instance Azure.
  - Kontejnery.
  - Aplikace funkcí Azure
### <a name="bug-fixes"></a>Opravy chyb
- Když je interval Resetování čítače problému nastavený na 24 dní, interpretuje se to za 24 hodin.
- Opravili jsme chybu, kdy proces odeslání snímku přestal zpracovávat nové snímky, pokud došlo k výjimce při likvidaci snímku.

## <a name="123"></a>[1.2.3](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.3)
- Opravte podepisování silného názvu pomocí binárních souborů pro odeslání snímků.

## <a name="122"></a>[1.2.2](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.2)
### <a name="changes"></a>Změny
- Soubory potřebné pro SnapshotUploader (64). exe jsou nyní vloženy jako prostředky do hlavní knihovny DLL. To znamená, že složka SnapshotCollectorFiles již není vytvořena, což zjednodušuje sestavování a nasazování a snižuje přehlednost Průzkumník řešení. Při upgradu se ujistěte, že jste provedli upgrade a zkontrolovali změny v `.csproj` souboru. `Microsoft.ApplicationInsights.SnapshotCollector.targets`Soubor už není potřeba.
- Telemetrii se do vašeho prostředku Application Insights zaznamená i v případě, že je ProvideAnonymousTelemetry nastavené na false. Je to proto, že v Azure Portal můžeme implementovat funkci kontroly stavu. ProvideAnonymousTelemetry má vliv pouze na telemetrii odeslanou společnosti Microsoft za účelem podpory a zlepšování produktů.
- Když jsou TempFolder nebo ShadowCopyFolder přesměrované na proměnné prostředí, udržujte kolektor nečinný, dokud tyto proměnné prostředí nebudou nastaveny.
- Pro aplikace, které se připojují k Internetu prostřednictvím proxy server, Snapshot Collector teď automaticky zjistí všechna nastavení proxy serveru a předají je SnapshotUploader.exe.
- Snižte prioritu SnapshotUplaoder procesu (Pokud je to možné). Tuto prioritu lze přepsat pomocí možnosti IsLowPrioirtySnapshotUploader.
- Do TelemetryConfiguration se přidala metoda rozšíření GetSnapshotCollector pro scénáře, ve kterých chcete Snapshot Collector programově nakonfigurovat.
- Nastavte Application Insights verzi sady SDK (místo verze aplikace) v telemetrie pro zákazníky.
- Odešle první událost prezenčního signálu po dvou minutách.
### <a name="bug-fixes"></a>Opravy chyb
- Opravte NullReferenceException, pokud výjimky mají hodnoty null nebo neměnné datové slovníky.
- V nástroji pro odeslání se pokusy o opětovné spuštění porovnává s PDB, pokud se zobrazí porušení sdílení.
- Opravte duplicitní telemetrii, když při spuštění více než jedno vlákno zavolá do kanálu telemetrie.

## <a name="121"></a>[1.2.1](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.1)
### <a name="changes"></a>Změny
- V balíčku NuGet jsou teď zahrnuté soubory s komentáři dokumentů XML.
- Přidali jsme metodu rozšíření ExcludeFromSnapshotting `System.Exception` pro scénáře, kde víte, že máte výjimku s vysokou úrovní šumu a chcete se vyhnout vytváření snímků.
- Byla přidána vlastnost konfigurace IsEnabledWhenProfiling, výchozí hodnota je true. Jedná se o změnu z předchozích verzí, kde bylo vytvoření snímku dočasně zakázáno, pokud Application Insights Profiler provádělo podrobnou kolekci. Staré chování lze obnovit nastavením této vlastnosti na hodnotu NEPRAVDA.
### <a name="bug-fixes"></a>Opravy chyb
- SnapshotUploader64.exe správně podepsat.
- Chraňte před dvojitou inicializací procesoru telemetrie.
- Zabraňuje dvojitému protokolování telemetrie v aplikacích s více kanály.
- Opravte chybu s časem vypršení platnosti plánu shromažďování dat, což by mohlo bránit snímků po 24 hodinách.

## <a name="120"></a>[1.2.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.2.0)
Největší změnou v této verzi (tedy přesun na nové číslo podverze) je přepis kanálu vytváření a zpracování snímku. V předchozích verzích byla tato funkce implementována v nativním kódu (ProductionBreakpoints *. dll a SnapshotHolder*. exe). Nová implementace je veškerý spravovaný kód s voláním nespravovaného kódu. U této první verze pomocí nového kanálu jsme neosamoceni z původního chování. Nová implementace umožňuje lepší zasílání zpráv o chybách a nastavení pro budoucí vylepšení.

### <a name="other-changes-in-this-version"></a>Další změny v této verzi
- MinidumpUploader.exe bylo přejmenováno na SnapshotUploader.exe (nebo SnapshotUploader64.exe).
- Přidání telemetrie časování pro deoptimalizaci/reoptimalizaci požadavků.
- Přidání komprese GZip pro nahrávání s minimálním výpisem.
- Opravili jsme problém, kdy soubory PDB byly zamčené, aby se zabránilo upgradu lokality.
- Při stínovém kopírování Zaprotokolujte původní název složky (SnapshotCollectorFiles).
- Upravte omezení paměti pro 64 procesy, aby se zabránilo restartování webu z důvodu OOM.
- Oprava problému, kdy byly snímky stále shromážděny i po zakázání.
- Zaprotokoluje události prezenčního signálu do prostředku AI zákazníka.
- Zvyšte rychlost snímku odebráním zdroje z ID problému.

## <a name="112"></a>[bodu](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.2)
### <a name="changes"></a>Změny
Telemetrie rozšíření využití
- Detekce a hlášení verze .NET a operačního systému
- Zjišťování a hlášení dalších prostředí Azure (cloudová služba, Service Fabric)
- Zaznamenejte a sestavte metriky výjimek (počet výjimek s první pravděpodobností a počet volání TrackException) v telemetrie prezenčního signálu.
### <a name="bug-fixes"></a>Opravy chyb
- Správné zpracování SqlException, kde není vyvolána vnitřní výjimka (Win32Exception).
- Ořezávání koncových mezer ve složkách symbolů, které způsobily nesprávnou analýzu argumentů příkazového řádku MinidumpUploader.
- Zabraňte nekonečnému opakování neúspěšných připojení ke koncovému bodu agenta Snapshot Debugger.

## <a name="110"></a>[1.1.0](https://www.nuget.org/packages/Microsoft.ApplicationInsights.SnapshotCollector/1.1.0)
### <a name="changes"></a>Změny
- Byla přidána ochrana paměti hostitele. Tato funkce snižuje dopad na paměť hostitelského počítače.
- Vylepšete možnosti zobrazení snímků Azure Portal.