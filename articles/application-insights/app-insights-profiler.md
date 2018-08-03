---
title: Profilování živých webových aplikací v Azure pomocí Application Insights Profiler | Dokumentace Microsoftu
description: Identifikujte kritická cesta v kódu webového serveru pomocí profileru s nízkými nároky.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: conceptual
ms.reviewer: cawa
ms.date: 07/13/2018
ms.author: mbullwin
ms.openlocfilehash: 52f0e6c90aa9fa0dc439eae3801b2d4ac29387a9
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39429676"
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profilování živých webových aplikací Azure pomocí Application Insights

Tato funkce služby Azure Application Insights je všeobecně dostupné pro funkci Web Apps služby Azure App Service a je ve verzi preview pro výpočetní prostředky Azure. Informace týkající se [v místním prostředí pomocí profileru](https://docs.microsoft.com/azure/application-insights/enable-profiler-compute#enable-profiler-on-on-premises-servers).

Tento článek popisuje množství času, který byl stráven v každé metodě živé webové aplikace při použití [Application Insights](app-insights-overview.md). Nástroj Application Insights Profiler zobrazuje podrobné profily živé požadavky, které byly vaše aplikace obsluhuje. Profiler zvýrazní *kritickou cestu* , která používá nejvíce času. Požadavky s různými doby odezvy mají profil na základě vzorkování. S využitím různých technik, můžete minimalizovat režii spojenou s aplikací.

Profiler v současné době používá pro webové aplikace ASP.NET a ASP.NET Core, které běží ve službě Web Apps. Základní úroveň služby nebo vyšší je potřeba použít Profiler.

## <a id="installation"></a> Povolit Profiler pro svoje webové aplikace

Jakmile nasadíte webovou aplikaci, bez ohledu na to, pokud jste zahrnuli App Insights SDK do zdrojového kódu, postupujte takto:

1. Přejděte **App Services** podokně webu Azure Portal.
1. Přejděte do **Nastavení > monitorování** podokně.

   ![Povolit App Insights na portálu služby App Services](./media/app-insights-profiler/AppInsights-AppServices.png)

1. Buď postupujte podle pokynů v podokně a vytvořte nový prostředek nebo vyberte existující prostředek App Insights monitorovat webové aplikace. Přijměte všechny výchozí možnosti. **Diagnostika na úrovni kódu** ve výchozím nastavení zapnutý a umožňuje Profiler.

   ![Přidat rozšíření webu App Insights][Enablement UI]

1. Profiler je teď nainstalovaná s rozšířením Web App Insights a se aktivuje pomocí nastavení aplikace služby App.

    ![Nastavení aplikace, které pro Profiler][profiler-app-setting]

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Povolit Profiler pro výpočetní prostředky Azure (preview)

Informace najdete v tématu [verze preview Profiler pro výpočetní prostředky Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Zobrazení dat profileru

Ujistěte se, že přijímá provoz vaší aplikace. Pokud provádíte experiment, můžete vygenerovat požadavky pro vaši webovou aplikaci pomocí [testování výkonu Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Pokud jste povolili nově Profiler, můžete spustit krátký zátěžový test pro přibližně 15 minut, které by měl generovat trasování profileru. Pokud máte povolené už nějakou dobu Profiler udržovat v paměti, že Profiler běží náhodně dvakrát za hodinu a po dobu dvou minut pokaždé, když ji spustí. Doporučujeme nejprve spustit zátěžový test pro jednu hodinu, než Ujistěte se, že získáte ukázkový trasování profileru.

Poté, co vaše aplikace obdrží část provozu, přejděte na **výkonu** vyberte **provést akce** zobrazení trasování profileru, a pak vyberte **Profiler trasování** tlačítko.

![Trasuje Profiler Application Insights výkon podokno náhledu][performance-blade-v2-examples]

Vyberte ukázku zobrazit rozpis úroveň kódu čas provádění požadavku.

![Průzkumník trasování Application Insights][trace-explorer]

V Průzkumníku trasování zobrazí následující informace:

* **Zobrazit kritickou cestu**: Otevře největší listové uzly, nebo aspoň něco zavřete. Ve většině případů se tento uzel je vedle výkonu kritickým bodem.
* **Popisek**: název funkce nebo události. Strom zobrazuje kombinaci kódu a události, ke kterým došlo (jako jsou SQL a HTTP události). Hlavní událost představuje celková doba trvání žádosti.
* **Uplynulý**: časový interval mezi začátkem operace a konec operace.
* **Když**: čas, kdy funkce nebo událostí byl spuštěn v relaci dalších funkcí.

## <a name="how-to-read-performance-data"></a>Čtení dat o výkonu

Profileru služeb Microsoft používá kombinaci metody odběru vzorků a instrumentace k analýze výkonu aplikace. Pokud probíhá shromažďování podrobné, profileru služeb ukázky ukazatele na instrukci každý počítač procesoru každých milisekund. Každá ukázka zaznamená úplný zásobník volání vlákna, který aktuálně spouští. Poskytuje podrobné informace o co bylo vlákno dělal, na vysoké úrovni a na nízké úrovni abstrakce. Profileru služeb shromažďuje také další události pro sledování aktivity korelace a příčiny, včetně kontextu přepínání události, události Task Parallel Library (TPL) a události fondu vláken.

Zásobník volání, který se zobrazí v zobrazení časové osy je výsledkem vzorkování a instrumentaci. Vzhledem k tomu každý vzorek zachytí úplný zásobník volání vlákna, obsahuje kód z rozhraní Microsoft .NET Framework a další architektury, které odkazujete.

### <a id="jitnewobj"></a>Přidělování objektů (clr! JIT\_nový nebo clr! JIT\_Newarr1)

**CLR! JIT\_nový** a **clr! JIT\_Newarr1** jsou pomocné funkce v rozhraní .NET Framework, které přidělují paměť ze spravované haldy. **CLR! JIT\_nový** je voláno, když je přidělen objektu. **CLR! JIT\_Newarr1** je vyvolána při přidělování pole objektu. Tyto dvě funkce jsou obvykle rychlé a trvat poměrně malé množství času. Pokud se zobrazí **clr! JIT\_nový** nebo **clr! JIT\_Newarr1** trvat značné množství času na vaší časové ose, znamená to, že kód může být přidělení mnoho objektů a spotřebovávat značné množství paměti.

### <a id="theprestub"></a>Kód pro načtení (clr! ThePreStub)

**CLR! ThePreStub** je pomocná funkce v rozhraní .NET Framework, který připraví kód pro spuštění poprvé. To obvykle zahrnuje, ale není omezena pouze na kompilace just-in-time (JIT). Pro každý metoda C# **clr! ThePreStub** má být volána po celou dobu životnosti procesu nejvýše jednou.

Pokud **clr! ThePreStub** trvá značné množství času požadavku, to znamená, že jsou první z nich, který se spustí dané metody. Čas potřebný pro modul runtime rozhraní .NET Framework se načíst první způsob je důležité. Zvažte použití zahřívání procesu, který se spustí předtím, než vaši uživatelé k němu přístup, nebo zvažte spuštění Native Image Generator (ngen.exe) část kódu na vaše sestavení.

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

Další servery, které jsou k dispozici pro hostování aplikací, má Profiler k menší dopad na výkon aplikací. Je to proto, že algoritmus vzorkování výsledkem Profiler běží na jenom 5 procent serverů v každém okamžiku. Další servery jsou k dispozici vyřizovat webové žádosti odsazení nároky na server způsobené spuštěn Profiler.

## <a name="disable-profiler"></a>Zakázat Profiler

Pro zastavení nebo restartování Profiler pro instance jednotlivých webovou aplikaci, v části **webové úlohy**, přejděte do prostředku webové aplikace. Chcete-li odstranit Profiler, přejděte na **rozšíření**.

![Zakázat Profiler pro webové úlohy][disable-profiler-webjob]

Doporučujeme, abyste měli Profiler povolena na všech svých webových aplikací se co nejdříve zjistit problémy s výkonem.

Pokud pomocí nasazení webu nasadit změny do vaší webové aplikace, ujistěte se, vyloučit složky App_Data odstranit během nasazení. V opačném případě rozšíření Profiler soubory jsou odstraněny při příštím nasazení webové aplikace do Azure.


## <a id="troubleshooting"></a>Řešení potíží

### <a name="too-many-active-profiling-sessions"></a>Příliš mnoho aktivní relace profilování

V současné době můžete povolit Profiler na maximálně čtyři Azure webové aplikace a sloty nasazení, které jsou spuštěny v rámci stejného plánu služby. Pokud Profiler webové úlohy je příliš mnoho aktivních relací profilování, přesuňte některé webové aplikace k plánu jiné služby.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak zjistím, zda je spuštěna Application Insights Profiler?

Profiler běží jako průběžné webové úlohy ve webové aplikaci. Můžete otevřít v prostředku webové aplikace [webu Azure portal](https://portal.azure.com). V **WebJobs** podokně zkontrolujte stav **ApplicationInsightsProfiler**. Pokud není spuštěná, Otevřít **protokoly** zobrazíte další informace.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Proč nelze najít veškeré ukázky, zásobník, i když je spuštěn Profiler?

Tady je pár věcí, které můžete zkontrolovat:

* Ujistěte se, že je web plán služby app service na úrovni Basic nebo vyšší.
* Ujistěte se, zda má vaše webové aplikace Application Insights SDK 2.2 Beta nebo později povolené.
* Ujistěte se, že má vaše webová aplikace **APPINSIGHTS_INSTRUMENTATIONKEY** nastavení nakonfigurované se stejným klíčem instrumentace, který používá sadu SDK Application Insights.
* Ujistěte se, že vaše webová aplikace běží na rozhraní .NET Framework 4.6.
* Pokud je vaše aplikace webové aplikace ASP.NET Core, zkontrolujte [požadované závislosti](#aspnetcore).

Po spuštění Profiler je krátký zahřívání období, během které Profiler aktivně shromažďuje několik trasování výkonu. Poté Profiler shromáždí trasování výkonu pro dvě minuty každou hodinu.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Můžu se pomocí profileru služeb Azure. Co se stalo se to?

Když povolíte Application Insights Profiler, agent profileru služeb Azure je zakázaná.

### <a id="double-counting"></a>Double, počítací v paralelních vláken

V některých případech se celková doba metriky v prohlížeči zásobníku je větší než doba trvání žádosti.

Tato situace může nastat, když dva nebo více vláken jsou spojené se žádostí a jsou prováděny současně. V takovém případě čas celkový počet vláken je větší než uplynulý čas. Jedno vlákno může čekat na dokončení druhé. V prohlížeči, pokusí se to rozpozná a vynechá nezajímavé čekání, ale chybuje Toolbar zobrazení příliš mnoho informací o spíše vynechat, které mohou být důležitým informacím vždy.

Při paralelních vláken se zobrazí v trasování, určete, která vlákna čekající tak můžete zjistit kritickou cestu pro žádost. Ve většině případů vlákno, které rychle přejde do stavu čekání jednoduše čeká na ostatní vlákna. Soustředit se na ostatní vlákna a ignorovat časové čekajících vláken.

### <a id="issue-loading-trace-in-viewer"></a>Žádná data profilace

Tady je pár věcí, které můžete zkontrolovat:

* Pokud data, která se pokoušíte zobrazit, je starší než několik týdnů, zkuste omezení časový filtr a zkuste to znovu.
* Ujistěte se, že proxy nebo brány firewall nejsou zablokoval přístup k https://gateway.azureserviceprofiler.net.
* Ujistěte se, že Instrumentační klíč Application Insights, který používáte ve vaší aplikaci je stejný jako prostředek Application Insights, který jste použili k povolit profilaci. Klíč je obvykle v souboru ApplicationInsights.config, ale může být také v souboru web.config nebo app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Zprávu o chybách v prohlížeči profilování

Vyplňte lístek podpory na portálu. Nezapomeňte zahrnout ID korelace z chybové zprávy.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Chyba nasazení: adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh.

Pokud nasazujete webovou aplikaci do prostředku webové aplikace s Profiler povolené, může se zobrazit zpráva podobná následující:

*Adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh.*

K této chybě dochází při spuštění nasazení webu pomocí skriptů nebo z kanálu nasazení služby Visual Studio Team Services. Řešením je přidat následující parametry další nasazení do úlohy nasazení webu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Tyto parametry odstranit složku, která používají Application Insights Profiler a odblokování procesu opětovného nasazení. Neovlivňují Profiler instanci, na kterém aktuálně běží.

## <a name="manual-installation"></a>Ruční instalace

Když konfigurujete Profiler, jsou provedeny aktualizace nastavení webové aplikace. Pokud vaše prostředí vyžaduje ji můžete ručně aplikovat aktualizace. Příkladem může být, že vaše aplikace běží v prostředí s Web Apps pro PowerApps.

1. V **webový ovládací prvek aplikace** otevřeným podoknem **nastavení**.
1. Nastavte **rozhraní .net Framework verze** k **v4.6**.
1. Nastavte **Always On** k **na**.
1. Přidat **APPINSIGHTS_INSTRUMENTATIONKEY** aplikace nastavení a nastavte hodnotu na stejný klíč instrumentace, který se používá sada SDK.
1. Otevřít **Rozšířené nástroje**.
1. Vyberte **Přejít** otevřete web Kudu.
1. Na webu Kudu, vyberte **rozšířením webu**.
1. Nainstalujte **Application Insights** z Galerie Azure Web Apps.
1. Restartujte webovou aplikaci.

## <a id="profileondemand"></a> Ručně aktivovat Profiler

Profiler lze spustit ručně kliknutím na jedno tlačítko. Předpokládejme, že jste spustili v testu výkonnosti webu. Budete potřebovat trasování, které vám pomohou pochopit, jaký je výkon vaší webové aplikace v zatížení. Máte kontrolu nad při zaznamenat trasování je zásadní, protože nevíte, kdy bude spuštěn zátěžový test, ale náhodný vzorkovací přijít.
Následující postup ukazuje, jak v tomto scénáři funguje:

### <a name="optional-step-1-generate-traffic-to-your-web-app-by-starting-a-web-performance-test"></a>(Volitelné) Krok 1: Vytvořte provozu do vaší webové aplikace tím, že spuštění testu výkonnosti webu

Pokud vaše webová aplikace už obsahuje příchozí provoz nebo pokud chcete jen vygenerovat ručně provoz, přeskočte tuto část a přejděte ke kroku 2.

Přejděte na portál Application Insights **konfigurovat > testování výkonu**. Klikněte na tlačítko Spustit nový test výkonnosti.
![Vytvořit nový test výkonnosti][create-performance-test]

V **nový test výkonnosti** podokně nakonfigurovat cílová adresa URL testu. Přijměte všechna výchozí nastavení a spuštění zátěžového testu.

![Konfigurace zátěžového testu][configure-performance-test]

Zobrazí se že nový test se do fronty zařadí nejprve, za nímž následuje ve stavu Probíhá.

![zátěžový test je odeslána a zařazeny do fronty][load-test-queued]

![v průběhu je spuštěn zátěžový test][load-test-in-progress]

### <a name="step-2-start-profiler-on-demand"></a>Krok 2: Spuštění profilování na vyžádání

Jakmile je spuštěn zátěžový test, můžeme začít profiler k zaznamenání trasování ve webové aplikaci, zatímco přijímá zatížení.
Přejděte do podokna Profiler konfigurace:

![Nakonfigurujte záznamy v podokně profileru][configure-profiler-entry]

Na **nakonfigurujte Profiler podokně**, je **profilu nyní** tlačítko aktivovat profiler na všech instancích propojené webové aplikace. Kromě toho jsou k dispozici viditelnost na při profiler byl spuštěn v minulosti.

![Profiler na vyžádání][profiler-on-demand]

Zobrazí se oznámení a stav změnit stav spuštění profileru.

### <a name="step-3-view-traces"></a>Krok 3: Zobrazení trasování

Po doběhnutí profileru, postupujte podle pokynů v oznámení pro přechod na okno a zobrazení trasování výkonu.

### <a name="troubleshooting-on-demand-profiler"></a>Řešení potíží s profilování na vyžádání

V některých případech může se zobrazit chybová zpráva vypršení časového limitu Profiler po relaci na vyžádání:

![Chyba časového limitu Profiler][profiler-timeout]

Může existovat dva důvody, proč se zobrazí tato chyba:

1. Relace profilování na vyžádání bylo úspěšné, ale trvalo déle zpracovat shromážděná data, Application Insights. Pokud data nebyla dokončena, zpracování v 15minutovém, na portálu se zobrazí zpráva vypršení časového limitu. Když po chvíli se zobrazí trasování Profiler. V takovém případě prosím jen chybovou zprávu prozatím ignorujte. Aktivně pracujeme na opravě

1. Webové aplikace má starší verzi agenta Profiler, který nemá žádné funkce na vyžádání. Pokud jste dříve povolili profil Application Insights, je pravděpodobné, že je potřeba aktualizovat váš Profiler agenta chcete začít používat funkce na vyžádání.
  
Postupujte podle těchto kroků zkontrolujte a nainstalujte nejnovější Profiler:

1. Přejděte do nastavení aplikace služby App a zaškrtněte, pokud následující nastavení mají hodnotu:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: nahraďte správné Instrumentační klíč Application Insights.
    * **APPINSIGHTS_PORTALINFO**: TECHNOLOGIE ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0 Pokud některé z těchto nastavení nejsou nastavená, přejděte do podokna povolení Application Insights se nainstalovat nejnovější rozšíření lokality.

1. Přejděte do podokna Application Insights na portálu služby App Services.

    ![Povolit Application Insights na portálu služby App Services][enable-app-insights]

1. Pokud se zobrazí na tlačítko 'Aktualizovat' v následující stránku, klikněte na něj aktualizovat rozšíření webu Application Insights, který nainstaluje nejnovější verzi agenta Profiler.
![Aktualizovat rozšíření webu][update-site-extension]

1. Pak klikněte na tlačítko **změnit** chcete zajistit, Profiler zapnutý a vyberte **OK** a uložte změny.

    ![Změnit a uložit app insights][change-and-save-appinsights]

1. Přejděte zpět na **nastavení aplikace** jsou nastaveny kartu pro App Service a zkontrolujte následující položky nastavení aplikace:
    * **APPINSIGHTS_INSTRUMENTATIONKEY**: nahraďte správné Instrumentační klíč pro službu application insights.
    * **APPINSIGHTS_PORTALINFO**: TECHNOLOGIE ASP.NET
    * **APPINSIGHTS_PROFILERFEATURE_VERSION**: 1.0.0

    ![nastavení aplikace pro profiler][app-settings-for-profiler]

1. Volitelně můžete zaškrtnutím verze rozšíření a ujistěte se, že není k dispozici žádná aktualizace.

    ![Vyhledat aktualizace rozšíření][check-for-extension-update]

## <a name="next-steps"></a>Další postup

* [Práce s Application Insights v sadě Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
[Enablement UI]: ./media/app-insights-profiler/Enablement_UI.png
[profiler-app-setting]:./media/app-insights-profiler/profiler-app-setting.png
[performance-blade]: ./media/app-insights-profiler/performance-blade.png
[performance-blade-examples]: ./media/app-insights-profiler/performance-blade-examples.png
[performance-blade-v2-examples]:./media/app-insights-profiler/performance-blade-v2-examples.png
[trace-explorer]: ./media/app-insights-profiler/trace-explorer.png
[trace-explorer-toolbar]: ./media/app-insights-profiler/trace-explorer-toolbar.png
[trace-explorer-hint-tip]: ./media/app-insights-profiler/trace-explorer-hint-tip.png
[trace-explorer-hot-path]: ./media/app-insights-profiler/trace-explorer-hot-path.png
[enable-profiler-banner]: ./media/app-insights-profiler/enable-profiler-banner.png
[disable-profiler-webjob]: ./media/app-insights-profiler/disable-profiler-webjob.png
[linked app services]: ./media/app-insights-profiler/linked-app-services.png
[create-performance-test]: ./media/app-insights-profiler/new-performance-test.png
[configure-performance-test]: ./media/app-insights-profiler/configure-performance-test.png
[load-test-queued]: ./media/app-insights-profiler/load-test-queued.png
[load-test-in-progress]: ./media/app-insights-profiler/load-test-inprogress.png
[profiler-on-demand]: ./media/app-insights-profiler/Profiler-on-demand.png
[configure-profiler-entry]: ./media/app-insights-profiler/configure-profiler-entry.png
[enable-app-insights]: ./media/app-insights-profiler/enable-app-insights-blade-01.png
[update-site-extension]: ./media/app-insights-profiler/update-site-extension-01.png
[change-and-save-appinsights]: ./media/app-insights-profiler/change-and-save-appinsights-01.png
[app-settings-for-profiler]: ./media/app-insights-profiler/appsettings-for-profiler-01.png
[check-for-extension-update]: ./media/app-insights-profiler/check-extension-update-01.png
[profiler-timeout]: ./media/app-insights-profiler/profiler-timeout.png
