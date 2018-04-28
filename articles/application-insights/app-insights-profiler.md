---
title: Profil za provozu webové aplikace v Azure s Application Insights profileru | Microsoft Docs
description: Identifikujte aktivní trase v kódu webového serveru s nízkými nároky profileru.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: mbullwin
ms.openlocfilehash: a4b1e30f1350a5e5886899069b05b8b87bb7000d
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
---
# <a name="profile-live-azure-web-apps-with-application-insights"></a>Profil za provozu Azure web apps s Application Insights

*Tato funkce Azure Application Insights je obecně dostupné pro funkce Web Apps služby Azure App Service a je ve verzi preview pro výpočetní prostředky Azure.*

Tento článek popisuje množství času stráveného v každá metoda za provozu webové aplikace při použití [Application Insights](app-insights-overview.md). Nástroj Application Insights profileru zobrazí podrobné profily živé požadavky, které se spouští vaše aplikace. Profileru klade důraz *aktivní trase* používající nejvíce času. Na základě vzorkování jsou profilovaným požadavků s různé doby odezvy. S využitím různých technik, můžete snížit režijní náklady, který je spojen s aplikací.

Profileru aktuálně funguje pro webové aplikace ASP.NET a ASP.NET Core, které jsou spuštěny na webové aplikace. Základní úrovně služby nebo vyšší je potřeba použít profileru.

## <a id="installation"></a> Povolit profileru pro webové aplikace webových aplikací
Pokud už je aplikace publikována do webové aplikace, ale ještě nepracovali ve zdrojovém kódu použít Application Insights, postupujte takto:
1. Přejděte na **App Services** podokně na portálu Azure.
2. V části **monitorování**, vyberte **Application Insights**a pak buď postupujte podle pokynů v podokně vytvořte nový prostředek nebo vyberte existující prostředek Application Insights k monitorování vaší webové aplikace.

   ![Povolit aplikaci Insights na portálu aplikační služby][appinsights-in-appservices]

3. Pokud máte přístup k projektu zdrojový kód, [nainstalujte službu Application Insights](app-insights-asp-net.md).  
   Pokud je již nainstalována, ujistěte se, že máte nejnovější verzi. Kontroluje nejnovější verzi, v Průzkumníku řešení klikněte pravým tlačítkem na projekt a potom vyberte **balíčky spravovat balíčky NuGet** > **aktualizace** > **aktualizovat všechny balíčky**. Potom nasaďte aplikaci.

Aplikace ASP.NET Core vyžadují instalaci 2.1.0-beta6 balíček Microsoft.ApplicationInsights.AspNetCore NuGet nebo novější pro práci s profileru. Od verze 27. června 2017 starší verze nepodporují.

1. V [portálu Azure](https://portal.azure.com), otevřete prostředek Application Insights pro webové aplikace. 
2. Vyberte **výkonu** > **povolit Application Insights profileru**.

   ![Vyberte Banner informující o povolení profileru][enable-profiler-banner]

3. Alternativně můžete vybrat **profileru** konfigurace k zobrazení stavu a povolení nebo zakázání profileru.

   ![Vyberte konfiguraci profileru][performance-blade]

   Webové aplikace, které jsou nakonfigurovány s Application Insights, jsou uvedeny v **profileru** podokně Konfigurace. Pokud jste postupovali podle předchozích kroků, by měl být nainstalován agent nástroje profileru. 

4. V **profileru** konfigurace podokně, vyberte **povolit profileru**.

5. V případě potřeby pokynů pro instalaci agenta profileru. Pokud byly nakonfigurovány žádné webové aplikace pomocí Application Insights, vyberte **přidat propojené aplikace**.

   ![Konfigurovat možnosti podokna][linked app services]

Na rozdíl od webových aplikací, které jsou hostované pomocí plánů webové aplikace aplikace, které jsou hostované v Azure výpočetní prostředky (například virtuální počítače Azure, sady škálování virtuálního počítače, Azure Service Fabric nebo Azure Cloud Services) nespravuje přímo Azure. V takovém případě není žádná webová aplikace propojení. Místo propojení na aplikaci, vyberte **povolit profileru** tlačítko.

### <a name="enable-profiler-for-azure-compute-resources-preview"></a>Povolit profileru pro výpočetní prostředky Azure (preview)

Informace najdete v tématu [verze preview profileru pro výpočetní prostředky Azure](https://go.microsoft.com/fwlink/?linkid=848155).

## <a name="view-profiler-data"></a>Zobrazení dat profileru

Ujistěte se, že vaše aplikace přijímá provoz. Při provádění experiment, můžete vygenerovat požadavky na vaši webovou aplikaci pomocí [testování výkonu Application Insights](https://docs.microsoft.com/vsts/load-test/app-service-web-app-performance-test). Pokud jste povolili nově profileru, můžete spustit krátké zátěžového testu pro o 15 minut, který by měl generovat profileru trasování. Pokud jste předtím profileru povolené už nějakou dobu, udržování v paměti, že profileru spouští náhodně dvakrát každou hodinu a po dobu dvou minut pokaždé, když ji spustí. Doporučujeme nejprve spuštění zátěžového testu pro jednu hodinu a ujistěte se, že získají ukázková profiler trasování.

Po aplikaci obdrží některé provozy, přejděte na **výkonu** podokně, vyberte **provést akce** zobrazení profileru trasování, a pak vyberte **profileru trasování** tlačítko.

![Trasuje profileru preview podokně Statistika výkonu aplikace][performance-blade-v2-examples]

Vyberte ukázku zobrazit rozpis úrovni kódu čas provádění požadavku.

![Application Insights trasování explorer][trace-explorer]

Trasování explorer zobrazí následující informace:

* **Zobrazit aktivní trase**: Otevře biggest list uzlu, nebo aspoň něco zavřete. Ve většině případů je tento uzel u kritických bodů výkonu.
* **Popisek**: název funkce nebo událostí. Stromu zobrazí směs kód a události, které došlo k chybě (např. SQL a HTTP událostí). Horní událost představuje celkovou dobu trvání požadavku.
* **Uplynulý čas**: časový interval mezi začátkem operace a konci operace.
* **Když**: čas, když pro funkci nebo událostí byl spuštěn v vztah do dalších funkcí.

## <a name="how-to-read-performance-data"></a>Informace o načtení dat výkonu

Profileru služby Microsoft používá kombinaci metod vzorkování a instrumentace analyzovat výkon vaší aplikace. Když probíhá podrobné kolekce, služba profileru ukázky ukazatel instrukce každý počítač procesoru každých milisekundu. Každá ukázka zaznamená zásobníku dokončení volání vlákna, která je aktuálně spuštěné. Nabízí podrobné informace o daném vláknu byl činnosti, na vysoké úrovni i na nízké úrovni abstrakce. Služba profileru taky shromažďuje dalších událostí, sledovat aktivity korelace a příčiny, včetně kontext přepínání události, Task Parallel Library (TPL) události a události fondu vláken.

Zásobník volání, který se zobrazí v zobrazení časové osy je výsledkem vzorkování a instrumentace. Protože každá ukázka zaznamená zásobníku dokončení volání vlákna, obsahuje kód z rozhraní Microsoft .NET Framework a z dalších rozhraní, které odkazovat.

### <a id="jitnewobj"></a>Objekt přidělení (clr! JIT\_nový nebo clr! JIT\_Newarr1)
**CLR! JIT\_nový** a **clr! JIT\_Newarr1** jsou pomocných funkcí v rozhraní .NET Framework, které přidělit paměť z spravovaná halda. **CLR! JIT\_nový** je volána, když je přidělen objektu. **CLR! JIT\_Newarr1** je volána, když je přidělen pole objektu. Tyto dvě funkce jsou obvykle rychlé a proveďte relativně malé množství času. Pokud se zobrazí **clr! JIT\_nový** nebo **clr! JIT\_Newarr1** trvat významné množství času v časový harmonogram, znamená to, že kód mohou být přidělování mnoho objektů a využívají poměrně velké množství paměti.

### <a id="theprestub"></a>Načítání kódu (clr! ThePreStub)
**CLR! ThePreStub** je pomocné funkce v rozhraní .NET Framework, který připraví kód při prvním spuštění. To obvykle zahrnuje, ale není omezeno na kompilace v běhu (JIT). Pro každou C# metodu **clr! ThePreStub** by měla být volána alespoň jednou po dobu životnosti procesu.

Pokud **clr! ThePreStub** trvá značně času pro žádost o to označuje, že požadavek je první, kterou provádí dané metody. Čas načtení první metodu modulu runtime rozhraní .NET Framework je důležité. Můžete zvážit použití zahřívání proces, který spouští před vaši uživatelé k němu přístup nebo zvažte spuštění generátor (ngen.exe) část kód ve vaší sestavení.

### <a id="lockcontention"></a>Spory uzamčení (clr! JITutil\_MonContention nebo clr! JITutil\_MonEnterWorker)
**CLR! JITutil\_MonContention** nebo **clr! JITutil\_MonEnterWorker** označuje, že je aktuální vlákno čekání na zámek k uvolnění. Tento text se obvykle zobrazí při spuštění C# **ZÁMKU** při vyvolání příkazu **Monitor.Enter** metoda, nebo při vyvolání metody s **MethodImplOptions.Synchronized** atribut. Obvykle dojde k uzamčení kolize při vláken _A_ získá zámek a vlákno _B_ pokusí získat stejné zámek před vlákno _A_ uvolněním.

### <a id="ngencold"></a>Načítání kódu ([STUDENÝ])
Pokud název metody obsahuje **[COLD]**, jako například **mscorlib.ni! [COLD]System.Reflection.CustomAttribute.IsDefined**, modul runtime rozhraní .NET Framework je provádění kódu poprvé není optimalizována podle <a href="https://msdn.microsoft.com/library/e7k32f4k.aspx">optimalizace na základě profilu</a>. Pro každou metodu by se měla zobrazit maximálně jednou po dobu životnosti procesu.

Pokud podstatnou část času pro žádost o načtení kód přijímá, to znamená, že požadavek je první z nich provést neoptimalizované část metody. Zvažte použití zahřívání proces, který provede část kódu, potom vaši uživatelé přístup.

### <a id="httpclientsend"></a>Odeslání požadavku HTTP
Metody jako **HttpClient.Send** znamenat, že kód čeká na dokončení požadavku HTTP.

### <a id="sqlcommand"></a>Operace databáze
Metody jako **SqlCommand.Execute** znamenat, že kód čeká na dokončení operace databáze.

### <a id="await"></a>Čeká se na (AWAIT\_čas)
**AWAIT\_čas** označuje, že kód čeká na dokončení jiné úlohy. To se obvykle stává pomocí jazyka C# **AWAIT** příkaz. Pokud nemá kód C# **AWAIT**, vlákno unwinds a vrátí prvek fond vláken a neexistuje žádný vlákno, které se zablokuje čekáním na **AWAIT** ukončíte. Však logicky, vlákno, nebyla **AWAIT** je "Uzavřeno" a čeká na dokončení operace. **AWAIT\_čas** příkaz označuje blokované dobu čekání na dokončení úlohy.

### <a id="block"></a>Blokované čas
**BLOCKED_TIME** označuje, že kód čeká na jiný prostředek k dispozici. Například může čekání na synchronizační objekt, vlákno být k dispozici nebo dokončení požadavku.

### <a id="cpu"></a>Čas procesoru
Procesor je zaneprázdněný prováděna podle pokynů.

### <a id="disk"></a>Doba disku
Aplikace provádí operace disku.

### <a id="network"></a>Čas sítě
Aplikace je provádění síťových operací.

### <a id="when"></a>Pokud sloupec
**Při** sloupec je vizualizaci jak včetně ukázky shromážděné pro uzel lišit v průběhu času. Celkový rozsah požadavku je rozdělené do 32 kbelíků čas. V těchto 32 kbelíků počítají včetně ukázky pro tento uzel. Každý blok je reprezentována jako pruh. Výška panelu reprezentuje hodnotu škálovat. Pro uzly, které jsou označené **CPU_TIME** nebo **BLOCKED_TIME**, nebo tam, kde je zřejmé vztah k využívání prostředků (například CPU, disku nebo vlákno), na panelu představuje spotřeby mezi prostředky během období této sady. Pro tyto metriky je možné získat hodnotu větší než 100 procent spotřebovávat více prostředků. Například pokud chcete použít, v průměru dva procesory během intervalu, získat 200 procent.

## <a name="limitations"></a>Omezení

Výchozí dobu uchování dat je pět dní. Maximální data, která je konzumována za den je 10 GB.

Neexistují žádné poplatky za používání služby profileru. Abyste mohli používat službu profileru, musí být webové aplikace hostované v aspoň základní úroveň webové aplikace.

## <a name="overhead-and-sampling-algorithm"></a>Režijní náklady a algoritmus vzorkování

Profileru náhodně spustí dvě minuty každou hodinu na každém virtuálním počítači, který je hostitelem aplikace, která profileru pro zaznamenání trasování povoleno. Pokud profileru běží, přidá se z 5 procent do 15 procent zatížení procesoru na server.

Další servery, které jsou k dispozici pro hostování aplikace, menší dopad profileru má na výkon aplikací. Je to proto, že algoritmus vzorkování výsledkem profileru systémem jenom 5 procent servery kdykoli. Další servery jsou k dispozici pro webové žádosti k posunutí nároky na serveru kvůli spuštěním profileru.

## <a name="disable-profiler"></a>Zakázat profileru
Pro zastavení nebo restartování profileru pro jednotlivé webové aplikace instance, v části **webové úlohy**, přejděte k prostředku webové aplikace. Chcete-li odstranit profileru, přejděte na **rozšíření**.

![Webovou úlohu zakázat profileru][disable-profiler-webjob]

Doporučujeme, abyste měli profileru, aby co nejdříve zjistit jakékoli problémy s výkonem na všechny webové aplikace povolena.

Pokud používáte k nasazení změny k vaší webové aplikaci WebDeploy, ujistěte se, abyste vyloučili složce App_Data před odstraněním během nasazení. Jinak hodnota rozšíření profileru soubory se odstraní při příštím nasazení webové aplikace do Azure.


## <a id="troubleshooting"></a>Řešení potíží

### <a name="too-many-active-profiling-sessions"></a>Příliš mnoho aktivních relací profilace

V současné době můžete povolit profileru na maximálně čtyři webové aplikace Azure a nasazovací sloty, které jsou spuštěny v stejný plán, který služba. Pokud webovou úlohu profileru hlásí příliš mnoho aktivních relací profilování, přesunete některé webové aplikace do plánu jinou službu.

### <a name="how-do-i-determine-whether-application-insights-profiler-is-running"></a>Jak zjistím, zda je spuštěn profileru Application Insights?

Profileru spouští jako nepřetržité webové úlohy ve webové aplikaci. Můžete otevřít prostředek webové aplikace [portál Azure](https://portal.azure.com). V **WebJobs** podokně, zkontrolujte stav **ApplicationInsightsProfiler**. Pokud není spuštěna, otevřete **protokoly** získat další informace.

### <a name="why-cant-i-find-any-stack-examples-even-though-profiler-is-running"></a>Proč nelze najít žádné příklady zásobníku, i když je spuštěna profileru?

Zde je několik věcí, které můžete zkontrolovat:

* Ujistěte se, že je váš plán služby app service web úroveň Basic nebo vyšší.
* Ujistěte se, že vaše webová aplikace je Application Insights SDK 2.2 Beta nebo později povoleno.
* Ujistěte se, že má vaše webová aplikace **APPINSIGHTS_INSTRUMENTATIONKEY** nastavení nakonfigurované stejný klíč instrumentace, který je používán Application Insights SDK.
* Ujistěte se, že vaše webová aplikace běží v rozhraní .NET Framework 4.6.
* Pokud vaše webová aplikace je aplikace ASP.NET Core, zkontrolujte [požadované závislosti](#aspnetcore).

Po spuštění profileru není krátké zahřívání období, během které profileru aktivně shromažďuje několik trasování výkonu. Potom profileru shromažďuje trasování výkonu pro dvě minuty každou hodinu.

### <a name="i-was-using-azure-service-profiler-what-happened-to-it"></a>Použití služby Azure profileru. Co se stalo na ni?

Když povolíte profileru Application Insights, profileru agenta služby Azure je zakázána.

### <a id="double-counting"></a>Dvojitý počítání v paralelních vláken

V některých případech metrika celkový čas v prohlížeči. zásobník je větší než doba trvání požadavku.

Tato situace může nastat, pokud dva nebo více podprocesů spojená se žádostí o a jsou provozu paralelně. Celkový počet vláken čas v takovém případě je větší než uplynulý čas. Jedno vlákno může čeká na dalším dokončit. V prohlížeči se pokusí o to zjistit a vynechá nezajímavá čekání, ale jeho chybuje na straně zobrazení příliš velkého množství informací místo vynechat, které mohou být důležitých informací.

Až uvidíte paralelních vláken ve vašem trasování, určete, které vláken čekají, můžete zjistit kritická cestu pro žádost. Ve většině případů je jednoduše čekání vlákno, které rychle přejde do stavu čekání na jiná vlákna. Soustředit se jenom na jiné vláken a ignoruje čas v vláken čekání.

### <a id="issue-loading-trace-in-viewer"></a>Žádná data profilování

Zde je několik věcí, které můžete zkontrolovat:

* Pokud data, která se pokoušíte zobrazení je starší než několik týdnů, pokuste se omezit váš filtr času a akci opakujte.
* Ujistěte se, není mít proxy nebo brána firewall zablokovala přístup k https://gateway.azureserviceprofiler.net.
* Zajistěte, aby byl klíč instrumentace Application Insights, kterou používáte ve vaší aplikaci stejná jako prostředek Application Insights, který jste použili k profilace povoleno. Klíč je obvykle v soubor ApplicationInsights.config, ale může být také v souboru web.config nebo app.config.

### <a name="error-report-in-the-profiling-viewer"></a>Zprávy o chybách v profilaci prohlížeči.

Odešlete lístek podpory na portálu. Nezapomeňte zahrnout ID korelace z chybovou zprávu.

### <a name="deployment-error-directory-not-empty-dhomesitewwwrootappdatajobs"></a>Chyba nasazení: adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh

Pokud vaší webové aplikace na prostředek webové aplikace jsou opětovného nasazení s profileru povolené, může se zobrazit zpráva podobná této:

*Adresář není prázdný ' D:\\domácí\\lokality\\wwwroot\\App_Data\\úloh*

K této chybě dojde, je-li spustit nasazení webu z skripty nebo kanálu nasazení služby Visual Studio Team Services. Řešení je přidat následující parametry další nasazení do úlohy nasazení webu:

```
-skip:Directory='.*\\App_Data\\jobs\\continuous\\ApplicationInsightsProfiler.*' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs\\continuous$' -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data\\jobs$'  -skip:skipAction=Delete,objectname='dirPath',absolutepath='.*\\App_Data$'
```

Tyto parametry odstranit složku, která je používána profileru Application Insights a odblokování znovu ho zaveďte proces. Neovlivňují profileru instanci, která je aktuálně spuštěna.


## <a name="manual-installation"></a>Ruční instalace

Když konfigurujete profileru, jsou provedeny aktualizace nastavení webové aplikace. Aktualizace můžete použít ručně, pokud to vyžaduje prostředí. Příkladem může být, že vaše aplikace běží v prostředí webové aplikace pro PowerApps.

1. V **ovládací prvek webu aplikace** open podokně **nastavení**.
2. Nastavit **rozhraní .net Framework verze** k **4.6**.
3. Nastavit **Always On** k **na**.
4. Přidat **APPINSIGHTS_INSTRUMENTATIONKEY** aplikace nastavení a nastavte hodnotu na stejný klíč instrumentace, který je používán sady SDK.
5. Otevřete **pokročilé nástroje**.
6. Vyberte **přejděte** otevřete web Kudu.
7. Na webu Kudu vyberte **lokality rozšíření**.
8. Nainstalujte **Application Insights** z Galerie Azure webové aplikace.
9. Restartování webové aplikace.

## <a id="profileondemand"></a> Aktivovat ručně profileru
Když jsme vyvinut profileru, jsme přidali rozhraní příkazového řádku, abychom může otestovat profileru na aplikační služby. Pomocí této stejné rozhraní, uživatelé můžete také upravit, jak profileru spustí. Na vysoké úrovni používá profileru systému webové aplikace Kudu ke správě profilace na pozadí. Při instalaci rozšíření Application Insights, vytvoříme nepřetržité webové úlohy který je hostitelem profileru. Tato technologie používáme k vytvoření nové webové úlohy, kterou si můžete přizpůsobit svým potřebám.

Tato část vysvětluje, jak:

* Vytvoříte webovou úlohu, která můžete spustit profileru pro dvě minuty. stisknutím tlačítka.
* Vytvoříte webovou úlohu, která můžete naplánovat profileru ke spuštění.
* Nastavit argumenty profileru.


### <a name="set-up"></a>Nastavení
První Seznamte se s řídicího panelu webové úlohy. V části **nastavení**, vyberte **WebJobs** kartě.

![okno webové úlohy](./media/app-insights-profiler/webjobs-blade.png)

Jak můžete vidět, tento řídicí panel zobrazuje všechny webové úlohy, které jsou aktuálně nainstalovány na váš web. Můžete zobrazit ApplicationInsightsProfiler2 webovou úlohu, která je spuštěná úloha profileru. Toto je, kde vytvoříme nové webové úlohy pro ruční a naplánované profilace.

Chcete-li získat binární soubory, které potřebujete, postupujte takto:

1.  Na webu Kudu na **nástroje pro vývoj** vyberte **Rozšířené nástroje** s Kudu logo a pak vyberte **přejděte**.  
   Otevře novou lokalitu a jste přihlášeni automaticky.
2.  Pokud chcete stáhnout profileru binární soubory, přejděte do Průzkumníka souborů prostřednictvím **ladění konzoly** > **CMD**, nacházející se v horní části stránky.
3.  Vyberte **lokality** > **wwwroot** > **App_Data** > **úlohy**  >   **Průběžné**.  
   Měli byste vidět složku s názvem *ApplicationInsightsProfiler2*. 
4. V levém složky, vyberte **Stáhnout** ikonu.  
   Tato akce stáhne *ApplicationInsightsProfiler2.zip* souboru. Doporučujeme vytvořit vyčistit adresář přesunout tento archiv zip.

### <a name="setting-up-the-web-job-archive"></a>Nastavení webové úlohy archivu
Když přidáte novou webovou úlohu do webové stránky Azure, můžete vytvořit v podstatě archivu zip s *run.cmd* souboru uvnitř. *Run.cmd* souboru řekne systému webové úlohy, jak postupovat při spuštění webové úlohy.

1.  Vytvořte novou složku (například *RunProfiler2Minutes*).
2.  Zkopírujte soubory z extrahované *ApplicationInsightProfiler2* složku do této nové složky.
3.  Vytvořte novou *run.cmd* souboru.  
    Pro usnadnění práce můžete otevřít pracovní složky ve Visual Studio Code před zahájením.
4.  V souboru, přidejte příkaz `ApplicationInsightsProfiler.exe start --engine-mode immediate --single --immediate-profiling-duration 120`. Příkazy jsou popsány takto:

    * `start`: Informuje profileru ke spuštění.  
    * `--engine-mode immediate`: Informuje profileru zahájíte profilace okamžitě.  
    * `--single`: Informuje profileru ke spuštění a automaticky zastaví.  
    * `--immediate-profiling-duration 120`: Informuje profileru ke spuštění 120 sekundách nebo 2 minut.

5.  Uložte provedené změny.
6.  Archivovat složku pravým tlačítkem myši a potom výběrem **poslat** > **komprimované složky (ZIP)**.  
   Tato akce vytvoří soubor .zip, který používá název složky.

![Spuštění příkazu profileru](./media/app-insights-profiler/start-profiler-command.png)

Nyní jste vytvořili soubor webové úlohy .zip, který můžete použít k nastavení webové úlohy ve vaší lokalitě.

### <a name="add-a-new-web-job"></a>Přidejte novou webovou úlohu
V této části přidejte novou webovou úlohu na váš web. Následující příklad ukazuje, jak přidat ručně spouštěná webovou úlohu. Po přidání ručně spouštěná webovou úlohu, proces je skoro stejný pro naplánovanou webovou úlohu.

1.  Přejděte na **webové úlohy** řídicího panelu.
2.  Na panelu nástrojů vyberte **přidat**.
3.  Zadejte název webové úlohy.  
    Pro přehlednost, může pomoct, aby odpovídal názvu vašem archivu a otevřete ho pro různé verze *run.cmd* souboru.
4.  V **nahrávání souborů** oblasti formuláře, vyberte **otevření souboru** ikonu a pak vyhledejte soubor .zip, který jste vytvořili v předchozí části.

    a.  V **typ** vyberte **aktivované**.  
    b.  V **aktivační události** vyberte **ruční**.

5.  Vyberte **OK**.

![Spuštění příkazu profileru](./media/app-insights-profiler/create-webjob.png)

### <a name="run-profiler"></a>Spustit profileru

Teď, když máte nové webovou úlohu, která můžete spouštět ručně, můžete zkusit spustit podle pokynů v této části.

Návrh, může mít jen jeden *ApplicationInsightsProfiler.exe* proces, který běží na počítači v daném okamžiku. Ano, než začnete, zakažte *souvislý* webovou úlohu z tohoto řídicího panelu. 
1. Vyberte řádek s novou webovou úlohu a pak vyberte **Zastavit**. 
2. Na panelu nástrojů vyberte **aktualizovat**a potvrďte, že stav označuje, že je úloha zastavena.
3. Vyberte řádek s novou webovou úlohu a pak vyberte **spustit**.
4. Vyberte řádek vybraný, na panelu nástrojů **protokoly** příkaz.  
    Tím se otevře řídicí panel webové úlohy pro novou webovou úlohu a vypíše nejnovější spustí a jejich výsledky.
5. Vyberte instanci spustit, kterou jste právě spuštěna.  
    Pokud jste úspěšně aktivuje novou úlohu web, můžete zobrazit některé diagnostické protokoly pocházejících z profileru potvrzující, že profilace bylo zahájeno.

### <a name="things-to-consider"></a>Co je třeba zvážit

Když tato metoda je relativně jednoduché, zvažte následující:

* Protože webové úlohy nespravuje naši službu, máme žádný způsob, jak aktualizovat agenta binární soubory pro webové úlohy. Jsme nyní není k dispozici na stránce stabilní stahování pro naše binární soubory, tak, aby jediný způsob, jak získat nejnovější binární soubory aktualizace rozšíření a metodou z *souvislý* složky, které se v předchozích krocích.

* Protože tento proces využívá argumenty příkazového řádku, které byly původně navrženy pro vývojáře, nikoli koncoví uživatelé, může v budoucnu změnit argumenty. Mějte na paměti možné změn při upgradu. Neměl by být mnohem problému, protože můžete přidat webovou úlohu, spusťte ji a testování, aby se ujistěte, že pracuje. Nakonec vytvoříme uživatelského rozhraní pro toto zpracování bez proces ručního nastavení.

* Tato funkce webové úlohy webových aplikací je jedinečný. Když je spuštěna webovou úlohu, zajišťuje, že váš proces má stejné proměnné prostředí a nastavení aplikace, které budou mít vašeho webu. To znamená, že není potřeba předat klíč instrumentace pomocí příkazového řádku profileru. Profileru by měl vyzvedávat klíč instrumentace z prostředí. Ale pokud chcete spustit profileru vaší dev pole nebo na počítači mimo webové aplikace, budete muset zadat kód instrumentace. Můžete to provést pomocí předání argument, `--ikey <instrumentation-key>`. Tato hodnota musí odpovídat klíč instrumentace, který vaše aplikace používá. Výstup protokolu profileru poznáte, které ikey profileru začít s a jestli jsme zjistili aktivity z tento klíč instrumentace při jsme byly profilace.

* Prostřednictvím webové háku můžete spustit ručně aktivované webové úlohy. Tuto adresu URL můžete získat tak, že kliknete pravým tlačítkem na webovou úlohu na řídicím panelu a zobrazení vlastností. Nebo na panelu nástrojů můžete vybrat **vlastnosti** po výběru webovou úlohu v tabulce. Tento přístup otevírá nekonečné možnosti, jako je například spouštění profileru z vašeho kanálu CI/CD (např. služby VSTS) nebo něco podobného jako Microsoft Flow (https://flow.microsoft.com/en-us/). Nakonec, výběr závisí na tom, jak složitá, který chcete provést vaše *run.cmd* soubor (také může být *run.ps1* soubor), ale možnost je k dispozici.

## <a name="next-steps"></a>Další postup

* [Práce s Application Insights v sadě Visual Studio](https://docs.microsoft.com/azure/application-insights/app-insights-visual-studio)

[appinsights-in-appservices]:./media/app-insights-profiler/AppInsights-AppServices.png
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
