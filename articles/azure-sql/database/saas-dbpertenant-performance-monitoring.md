---
title: 'Aplikace SaaS: sledování výkonu mnoha databází'
description: Monitorování a Správa výkonu Azure SQL Database v SaaS aplikaci pro více tenantů
services: sql-database
ms.service: sql-database
ms.subservice: scenario
ms.custom: seo-lt-2019, sqldbrb=1
ms.devlang: ''
ms.topic: tutorial
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 01/25/2019
ms.openlocfilehash: 21c0a7a3fe6d5be9d99ea53dbfa74cf72e163272
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92780661"
---
# <a name="monitor-and-manage-performance-of-azure-sql-database-in-a-multi-tenant-saas-app"></a>Monitorování a Správa výkonu Azure SQL Database v SaaS aplikaci pro více tenantů
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

V tomto kurzu se prozkoumá několik klíčových scénářů správy výkonu používaných v aplikacích SaaS. Pomocí generátoru zatížení pro simulaci aktivity napříč všemi databázemi tenanta se prokáže integrované funkce monitorování a upozorňování SQL Database a elastických fondů.

Aplikace Wingtip Tickets SaaS Database na tenanta používá datový model s jedním tenantům, kde každé místo (tenant) má svou vlastní databázi. Stejně jako u většiny aplikací SaaS je předpokládaný vzorek úloh tenanta nepředvídatelný a sporadický. Jinými slovy to znamená, že prodej lístků může probíhat kdykoli. Aby bylo možné využít výhod tohoto typického vzoru použití databáze, jsou databáze tenantů nasazeny do elastických fondů. Elastické fondy optimalizují náklady na řešení prostřednictvím sdílení prostředků mezi mnoha databázemi. S tímto typem vzorců je důležité monitorovat využití databáze a prostředků fondu k zajištění, že jsou přiměřeně vyvážená přetížení mezi jednotlivými fondy. Je také potřeba zajistit, že jednotlivé databáze mají adekvátní prostředky a že fondy nedosahují limitů [eDTU](purchasing-models.md#dtu-based-purchasing-model). Tento kurz se věnuje způsobům monitorování a správy databází a fondů a uvádí, jak se provádějí nápravné akce v reakci na variace v úloze.

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> 
> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze


Předpokladem dokončení tohoto kurzu je splnění následujících požadavků:

* Aplikace Wingtip Tickets SaaS Database na tenanta je nasazená. Postup nasazení za méně než pět minut najdete v tématu [nasazení a prozkoumání aplikace Wingtip Tickets SaaS Database na klienta](./saas-dbpertenant-get-started-deploy.md) .
* Je nainstalované prostředí Azure PowerShell. Podrobnosti najdete v článku [Začínáme s prostředím Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="introduction-to-saas-performance-management-patterns"></a>Seznámení se vzory správy výkonu SaaS

Správa výkonu databáze sestává z kompilování a analýz dat výkonu a následného reagování na tato data prostřednictvím úpravy parametrů pro řízení přijatelné doby odezvy na aplikaci. Při hostování více tenantů představují elastické fondy nákladově efektivní způsob, jak poskytnout a spravovat prostředky pro skupinu databází s nepředvídatelnými úlohami. Při určitých vzorcích úloh může být správa ve fondu užitečná pro pouhé dvě databáze S3.

![diagram aplikace](./media/saas-dbpertenant-performance-monitoring/app-diagram.png)

Fondy a databáze ve fondech by se měly monitorovat, aby se zajistilo jejich udržení v přijatelném rozsahu výkonu. Vyladěním konfigurace fondu tak, aby splňovala požadavky na agregované úlohy všech databází, a zajistí, aby byl fond eDTU vhodný pro celou úlohu. Upravte maximální a minimální hodnoty eDTU jednotlivých databází na vhodné hodnoty pro vaše konkrétní aplikační požadavky.

### <a name="performance-management-strategies"></a>Strategie výkonu aplikací

* Aby nedocházelo k ručnímu monitorování výkonu, je nejúčinnější **nastavit výstrahy, které se aktivují, když se databáze nebo fondy odstanou z normálních rozsahů**.
* Aby bylo možné reagovat na krátkodobé výkyvy v agregované výpočetní velikosti fondu, **můžete škálovat nebo snížit kapacitu na úrovni eDTU fondu**. Pokud k tomuto kolísání dochází pravidelně nebo je předvídatelné, **je možné naplánovat automatické škálování fondu**. Pokud například víte, že je úloha malého rozsahu, třeba přes noc nebo o víkendech, můžete vertikálně snížit kapacitu.
* Pokud chcete reagovat na dlouhodobější kolísání nebo změny počtu databází, **je možné přesunout jednotlivé databáze do jiných fondů**.
* Aby bylo možné reagovat na krátkodobé zvýšení v *jednotlivých* **databázích, je možné vyřadit jednotlivé databáze z fondu a přiřadit individuální výpočetní velikost**. Po snížení zatížení je možné databázi vrátit do fondu. V případě, že je tato možnost předem známa, je možné databáze přesunout zpět, aby databáze vždy měla potřebné prostředky a aby se zabránilo dopadu na další databáze ve fondu. Pokud je tento požadavek předvídatelný, například v místě, kde se předpokládá navýšení prodeje lístků na oblíbenou akci, je možné toto chování správy začlenit do aplikace.

[Azure Portal](https://portal.azure.com) poskytuje integrované monitorování a upozorňování pro většinu prostředků. Monitorování a upozorňování je k dispozici na databázích a fondech. Toto integrované monitorování a upozorňování je specifické pro konkrétní prostředky, takže je vhodné použít pro malý počet prostředků, ale není velmi výhodné při práci s mnoha prostředky.

U scénářů s vysokým objemem, kde pracujete s mnoha prostředky, je možné použít [protokoly Azure monitor](./saas-dbpertenant-log-analytics.md) . Jedná se o samostatnou službu Azure, která poskytuje analýzy prostřednictvím vygenerovaných protokolů shromážděných v pracovním prostoru Log Analytics. Protokoly Azure Monitor můžou shromažďovat telemetrii z mnoha služeb a používat je k dotazování a nastavování výstrah.

## <a name="get-the-wingtip-tickets-saas-database-per-tenant-application-scripts"></a>Získání skriptů aplikace Wingtip Tickets SaaS Database na klientské aplikace

V úložišti GitHubu [WingtipTicketsSaaS-DbPerTenant](https://github.com/Microsoft/WingtipTicketsSaaS-DbPerTenant) jsou k dispozici skripty SaaS s více klienty a zdrojový kód aplikace. Projděte si [Obecné pokyny](saas-tenancy-wingtip-app-guidance-tips.md) ke stažení a odblokování skriptů Wingtip Tickets SaaS.

## <a name="provision-additional-tenants"></a>Zřízení dalších tenantů

I když fondy můžou být nákladově efektivní jenom se dvěma databázemi S3, platí, že čím více databází fond obsahuje, tím efektivnější z hlediska nákladů začne být efekt zprůměrování. Pro zajištění správného porozumění fungování monitorování a správy výkonu na škále vyžaduje tento kurz, abyste měli nasazených nejméně 20 databází.

Pokud jste již v předchozím kurzu zřídili dávku tenantů, přejděte k části [simulace využití ve všech databázích tenantů](#simulate-usage-on-all-tenant-databases) .

1. V **prostředí POWERSHELL ISE** otevřete... \\ \\Demo-PerformanceMonitoringAndManagement.ps1pro monitorování a správu výkonu výukových modulů \\ ** Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavení **$DemoScenario**  =  **1** **zřídí dávku tenantů** .
1. Stisknutím klávesy **F5** spusťte skript.

Skript nasadí 17 tenantů za méně než pět minut.

Skript *New-TenantBatch* používá vnořenou nebo propojenou sadu [Správce prostředků](../../azure-resource-manager/index.yml) šablon, které vytvářejí dávku tenantů, která ve výchozím nastavení kopíruje databázi **basetenantdb** na serveru katalogu, aby vytvořila nové databáze tenantů, a pak je zaregistruje v katalogu a nakonec je inicializuje s názvem tenanta a typem místa. To je konzistentní s tím, jak aplikace zřídí nového tenanta. Všechny změny provedené v *basetenantdb* se aplikují na všechny nové klienty zřízené později. Informace o tom, jak provádět změny schématu ve *stávajících* databázích tenanta (včetně databáze *basetenantdb* ), najdete v [kurzu Správa schématu](saas-tenancy-schema-management.md) .

## <a name="simulate-usage-on-all-tenant-databases"></a>Simulace využití ve všech databázích tenantů

*Demo-PerformanceMonitoringAndManagement.ps1* skript je k dispozici pro simulaci zatížení běžícího na všech databázích tenanta. Zatížení je generováno pomocí jednoho z dostupných scénářů načítání:

| Ukázka | Scenario |
|:--|:--|
| 2 | Generování normálního zatížení svítivosti (přibližně 40 DTU) |
| 3 | Generování zatížení s delšími a častějšími nárůsty zatížení na databázi|
| 4 | Generování zátěže s vyšším nárůstem DTU na databázi (přibližně 80 DTU)|
| 5 | Generování normálního zatížení plus vysoké zatížení v jednom tenantovi (přibližně 95 DTU)|
| 6 | Generování nevyváženého zatížení mezi více fondy|

Generátor zatížení použije *syntetické* zatížení jenom pro CPU na každé databázi tenantů. Generátor spustí úlohu pro každou databázi tenantů, která pravidelně volá uloženou proceduru generující zatížení. Úrovně zatížení (v eDTU), doba trvání a intervaly jsou napříč všemi databázemi různé, což simuluje nepředvídanou činnost tenanta.

1. V **prostředí POWERSHELL ISE** otevřete... \\ \\Demo-PerformanceMonitoringAndManagement.ps1pro monitorování a správu výkonu výukových modulů \\ ** Tento skript nechte otevřený, protože během tohoto kurzu budete spouštět několik scénářů.
1. Nastavte **$DemoScenario**  =  **2**, *vygenerujte normální zatížení intenzity*.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

Společnost Wingtip Tickets SaaS Database na tenanta je SaaS aplikace a reálné zatížení pro SaaS aplikaci je obvykle občas a nepředvídatelné. Abychom takovýto scénář nasimulovali, vytváří generátor zatížení náhodné zatížení rozdělené mezi všechny tenanty. Pro vzorek zatížení je potřeba několik minut, proto spusťte generátor zatížení po dobu 3-5 minut, než se pokusíte monitorovat zatížení v následujících oddílech.

> [!IMPORTANT]
> Generátor zatížení běží jako řada úloh v místní relaci prostředí PowerShell. Nechte otevřenou kartu *Demo-PerformanceMonitoringAndManagement.ps1*. Pokud tuto kartu zavřete nebo pozastavíte počítač, generátor zatížení se zastaví. Generátor zatížení zůstane ve stavu *vyvolání úlohy* , kde generuje zatížení pro všechny nové klienty zřízené po spuštění generátoru. Pomocí *kombinace kláves CTRL-C* můžete zastavit vyvolání nových úloh a ukončit skript. Generátor zatížení bude nadále běžet, ale pouze ve stávajících klientech.

## <a name="monitor-resource-usage-using-the-azure-portal"></a>Monitorování využití prostředků pomocí Azure Portal

Pokud chcete monitorovat využití prostředků, které vyplývá z zatížení, otevřete portál ve fondu obsahujícím databáze tenantů:

1. Otevřete [Azure Portal](https://portal.azure.com) a přejděte k *tenants1-DPT- &lt; &gt; User* serveru.
1. Přejděte dolů na elastické fondy a klikněte na **Pool1**. Tento fond obsahuje všechny dosud vytvořené databáze tenantů.

Sledujte grafy **monitorování elastického fondu** a **elastické databáze** .

Využití prostředků fondu je agregované využití databáze pro všechny databáze ve fondu. Graf databáze znázorňuje pět databází nejžhavějších:

![databázový graf](./media/saas-dbpertenant-performance-monitoring/pool1.png)

Vzhledem k tomu, že ve fondu existují další databáze nad rámec pěti pěti, využití fondu zobrazuje aktivitu, která se neprojeví v grafu 5 hlavních databází. Další podrobnosti získáte kliknutím na možnost **využití prostředků databáze**:

![využití prostředků databáze](./media/saas-dbpertenant-performance-monitoring/database-utilization.png)


## <a name="set-performance-alerts-on-the-pool"></a>Nastavení výstrah výkonu ve fondu

Nastavte výstrahu pro fond, který aktivuje \> 75% využití následujícím způsobem:

1. Otevřete *Pool1* (na *tenants1-DPT \<user\> -* Server) v [Azure Portal](https://portal.azure.com).
1. Klikněte na **Pravidla výstrah** a potom na **+ Přidat výstrahu**:

   ![přidání výstrahy](./media/saas-dbpertenant-performance-monitoring/add-alert.png)

1. Zadejte název, například **High DTU**,
1. Nastavte následující hodnoty:
   * **Metrika = procento eDTU**
   * **Podmínka = větší než**
   * **Prahová hodnota = 75**
   * **Perioda = za posledních 30 minut**
1. Přidejte e-mailovou adresu do pole *Další e-maily správce* a klikněte na **OK**.

   ![nastavení upozornění](./media/saas-dbpertenant-performance-monitoring/alert-rule.png)


## <a name="scale-up-a-busy-pool"></a>Vertikální navýšení kapacity zaneprázdněného fondu

Pokud se agregovaná úroveň zatížení ve fondu zvýší na bod, která fond navyšuje na maximální hodnotu a dosáhne 100% využití eDTU, má to vliv na individuální výkon databáze a potenciálně vykazuje pomalejší doby odezvy pro všechny databáze ve fondu.

**Krátkodobé**, zvažte horizontální navýšení kapacity fondu a poskytování dalších prostředků nebo odebrání databází z fondu (jejich přesun do jiných fondů nebo mimo fond do samostatné vrstvy služeb).

**Už** je vhodné zvážit optimalizaci dotazů nebo využití indexu a zvýšit tak výkon databáze. Podle toho, jak je aplikace citlivá na problémy s výkonem, se osvědčilo navýšit kapacitu fondu předtím, než dosáhne 100% využití eDTU. Použijte výstrahu, abyste byli předem upozornění.

Zaneprázdněný fond můžete simulovat tak, že zvýšíte zatížení vytvořené generátorem. Způsob, jakým se databáze rozrůstá častěji, a tím déle zvyšuje agregované zatížení ve fondu, aniž by bylo potřeba měnit požadavky jednotlivých databází. Škálování fondu se snadno provádí na portálu nebo z prostředí PowerShell. Při tomto cvičení se používá portál.

1. Nastavte *$DemoScenario*  =  **3**, _vygenerujte zatížení s delším a častými nárůsty na databázi_ , aby se zvýšila intenzita agregovaného zatížení ve fondu, aniž by došlo ke změně zatížení ve špičce požadovaném jednotlivými databázemi.
1. Stisknutím klávesy **F5** použijte zatížení u všech databází tenantů.

1. Přejít na **Pool1** v Azure Portal.

Sledujte vyšší využití eDTU fondu v horním grafu. Novému načtení nového zatížení trvá několik minut, ale měli byste rychle vidět, že se fond spouští k dosažení maximálního využití, a jako zátěžový stabilizuje do nového vzoru ho rychle přetěžuje.

1. Pokud chcete škálovat fond, klikněte na **Konfigurovat fond** v horní části stránky **Pool1** .
1. Upravte nastavení **EDTU fondu** na **100**. Změna eDTU fondu se nemění podle nastavení databáze (což je stále maximálně 50 eDTU na databázi). Nastavení pro jednotlivé databáze můžete zobrazit na pravé straně stránky **Konfigurovat fond** .
1. Kliknutím na **Uložit** odešlete žádost o horizontální navýšení kapacity fondu.

Vraťte se zpět na **Pool1**  >  **Přehled** a zobrazte grafy monitorování. Sledujte vliv poskytování fondu na více prostředků (i když s několika databázemi a náhodným zatížením to není vždycky snadné zobrazit, dokud to nebudete mít nějakou dobu spuštěno). Při interpretaci grafů mějte na paměti, že 100 % v horním grafu teď reprezentuje 100 eDTU, zatímco v dolním grafu je 100 % vždy 50 eDTU, protože maximum na databázi je 50 eDTU.

Databáze je v průběhu procesu online a plně dostupná. A nakonec – protože je každá databáze připravená k aktivaci s eDTU nového fondu, bude každé aktivní propojení přerušeno. Kód aplikace by měl vždy být zapsán tak, aby se znovu pokoušel obnovit porušené propojení a tím pádem se znovu připojí k databázi ve fondu s navýšenou kapacitou.

## <a name="load-balance-between-pools"></a>Vyrovnávání zatížení mezi fondy

Jako alternativu navýšení kapacity fondu vytvořte druhý fond a přemístěte do něho databáze, aby se vyrovnalo zatížení mezi dvěma fondy. K tomu je potřeba vytvořit tento nový fond na stejném serveru jako první.

1. V [Azure Portal](https://portal.azure.com)otevřete **tenants1-DPT- &lt; &gt; User** Server.
1. Kliknutím na **+ Nový fond** vytvořte fond na aktuálním serveru.
1. V šabloně **elastického fondu** :

   1. Nastavte **název** na *Pool2*.
   1. Cenovou úroveň nechte jako **Fond Standard**.
   1. Klikněte na **Konfigurovat fond**.
   1. Nastavte **eDTU fondu** na *50 eDTU*.
   1. Kliknutím na **přidat databáze** zobrazíte seznam databází na serveru, které je možné přidat do *Pool2*.
   1. Vyberte libovolné 10 databází, které chcete přesunout do nového fondu, a potom klikněte na **Vybrat**. Pokud jste spustili generátor zatížení, služba již ví, že váš profil výkonu vyžaduje větší fond než výchozí velikost 50 eDTU a doporučuje začít s nastavením 100 eDTU.

      ![základě](./media/saas-dbpertenant-performance-monitoring/configure-pool.png)

   1. Pro tento kurz ponechte výchozí hodnotu v 50 eDTU a znovu klikněte na **Vybrat** .
   1. Vyberte **OK** , pokud chcete vytvořit nový fond a přesunout do něj vybrané databáze.

Vytvoření fondu a přesunutí databází trvá několik minut. Když přesunete databáze, zůstávají i nadále online a plně přístupné, dokud nebudete velmi poslední okamžik, kdy se všechna otevřená připojení zavřou. Pokud máte nějakou logiku opakování, klienti se pak připojí k databázi v novém fondu.

Přejděte na **Pool2** (na *tenants1-DPT- \<user\>* Server) a otevřete fond a sledujte jeho výkon. Pokud ho nevidíte, počkejte, než se dokončí zřizování nového fondu.

Teď vidíte, že využití prostředků v *Pool1* bylo vyřazeno a že *Pool2* se teď načetlo podobně.

## <a name="manage-performance-of-an-individual-database"></a>Správa výkonu jednotlivé databáze

Pokud v jednotlivých databázích ve fondu dojde k trvalému vysokému zatížení v závislosti na konfiguraci fondu, může se stát, že se prostředky ve fondu budou podotknout a budou mít vliv na ostatní databáze. Pokud aktivita bude pravděpodobně v určitou dobu pokračovat, databáze může být dočasně přesunuta z fondu. To umožňuje, aby databáze měla další prostředky, které potřebuje, a izoluje ji od ostatních databází.

Toto cvičení simuluje vliv vysokého zatížení při prodeji lístků na populární koncert v Koncertním sále Contoso.

1. V **prostředí POWERSHELL ISE** otevřete... \\ *Demo-PerformanceMonitoringAndManagement.ps1* skript.
1. Nastavte **$DemoScenario = 5, vygenerujte normální zatížení plus vysoké zatížení v jednom tenantovi (přibližně 95 DTU).**
1. Nastavte **$SingleTenantDatabaseName = contosoconcerthall**
1. Skript proveďte pomocí **F5**.


1. V [Azure Portal](https://portal.azure.com)přejděte na seznam databází na *tenants1-DPT- \<user\>* Server. 
1. Klikněte na databázi **contosoconcerthall** .
1. Klikněte na fond, ve kterém je **contosoconcerthall** . Vyhledejte fond v části **elastický fond** .

1. Prohlédněte si graf **monitorování elastického fondu** a vyhledejte zvýšené využití eDTU fondu. Po jedné až dvou minutách by mělo začít zvýšené zatížení a měli byste rychle zaznamenat, že fond dosáhl 100% využití.
2. Prozkoumejte zobrazení **monitorování elastické databáze** , které zobrazuje databáze nejžhavějších za poslední hodinu. Databáze *contosoconcerthall* by se brzy zobrazovala jako jedna z pěti databází nejžhavějších.
3. Klikněte na **graf** **monitorování elastické databáze** a otevře se stránka **využití databázových prostředků** , kde můžete monitorovat kteroukoli z databází. To vám umožní izolovat zobrazení databáze *contosoconcerthall* .
4. V seznamu databází klikněte na **contosoconcerthall**.
5. Klikněte na **cenová úroveň (DTU škálování)** . otevře se stránka **Konfigurovat výkon** , kde můžete nastavit samostatnou výpočetní velikost pro databázi.
6. Kliknutím na kartu **Standardní** otevřete možnosti škálování ve standardní vrstvě.
7. Posunutím **posuvníku DTU** doprava k výběru **100** DTU. Všimněte si, že to odpovídá cíli služby, **S3**.
8. Kliknutím na **Apply (použít** ) přesuňte databázi z fondu a nastavte ji jako databázi *Standard S3* .
9. Po dokončení škálování Sledujte efekt databáze contosoconcerthall a Pool1 v elastickém fondu a v okně databáze.

Po vysokém zatížení databáze contosoconcerthall byste měli dotaz vrátit do fondu, aby se snížily náklady. Pokud není jasné, kdy k tomu dojde, můžete nastavit výstrahu v databázi, která se aktivuje, jakmile využití DTU ve fondu poklesne pod maximum na databázi. Přesunutí databáze do fondu je popsáno v cvičení 5.

## <a name="other-performance-management-patterns"></a>Další vzory správy výkonu

**Škálování před preemptivní** V cvičení výše, kde jste prozkoumali, jak škálovat izolovanou databázi, jste věděli, jakou databázi hledáte. Pokud byla správa wingtips společnosti Contoso informována o nadcházejícím prodeji lístků, databáze by mohla být bez přerušení přesunuta z fondu. Jinak by to vyžadovalo výstrahu ve fondu nebo v databázi s cílem zjistit, co se stalo. Pravděpodobně byste to nechtěli zjistit proto, že by si ostatní tenanti ve fondu stěžovali na snížený výkon. A kdyby mohli tenanti předvídat, na jak dlouho by potřebovali další prostředky, můžete nastavit runbook Azure Automation na přesunutí databáze mimo fond a potom zpět podle předem definovaného plánu.

**Samoobslužné škálování tenanta** Protože škálování je úkol, který se snadno volá prostřednictvím rozhraní API pro škálu, můžete snadno vytvořit možnost škálovat databáze tenantů do aplikace směřující k tenantovi a nabízet ji jako funkci služby SaaS. Můžete například umožnit tenantům samoobslužné vertikální navýšení a snížení kapacity navázané přímo na fakturaci.

**Vertikální navýšení a snížení kapacity podle plánu v souladu se vzorci využití**

Tam, kde agregované využití tenanta probíhá podle předvídatelných vzorců použití, můžete pomocí Azure Automation podle plánu vertikálně zvyšovat nebo snižovat kapacitu fondu. Můžete například snížit kapacitu fondu po 6. hodině večer a navýšit před 6. hodinou ráno ve dnech, kdy víte, že existuje pokles požadavků na prostředky.



## <a name="next-steps"></a>Další kroky

Co se v tomto kurzu naučíte:

> [!div class="checklist"]
> * Simulace použití v databázích tenantů pomocí dodaného generátoru zatížení
> * Monitorování databází tenantů při reakci na zvýšení zatížení
> * Vertikální navýšení kapacity elastického fondu v reakci na zvýšení zatížení databáze
> * Zřízení druhého elastického fondu pro vyrovnání zatížení činnosti databáze

[Kurz Obnovení jednoho tenanta](saas-dbpertenant-restore-single-tenant.md)


## <a name="additional-resources"></a>Další zdroje informací

* Další [kurzy, které se sestavují na základě SaaS databáze Wingtip Tickets pro každé klientské nasazení aplikace](saas-dbpertenant-wingtip-app-overview.md#sql-database-wingtip-saas-tutorials)
* [Elastické fondy SQL](elastic-pool-overview.md)
* [Automatizace Azure](../../automation/automation-intro.md)
* [Azure monitor protokoly](./saas-dbpertenant-log-analytics.md) – kurz nastavení a používání protokolů Azure monitor