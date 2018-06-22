---
title: Správa více databází SQL s elastické fondy Azure | Microsoft Docs
description: Spravovat a škálování více databází SQL - stovky a s tisíci - pomocí elastické fondy. Jeden ceny pro prostředky, které můžete distribuovat, kde je potřeba.
keywords: více databází, databáze prostředků, výkon databáze
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.custom: DBs & servers
ms.date: 06/20/2018
ms.author: ninarn
ms.topic: conceptual
ms.openlocfilehash: 5ef32b231a77906a6840ad3550e81b631ddc0c13
ms.sourcegitcommit: 638599eb548e41f341c54e14b29480ab02655db1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36309650"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastických fondů pomáhají spravovat a škálování více databází Azure SQL

Fondy elastické databáze SQL jsou jednoduché a nákladově efektivní řešení pro správu a škálování více databází, které mají různou a nepředvídatelným nároky na využití. Databáze ve fondu elastické databáze na jednom serveru Azure SQL Database a sdílet se stanoveným počtem prostředky za cenu sady. Elastické fondy v Azure SQL Database umožňují vývojářům SaaS optimalizovat poměr cena/výkon pro skupinu databází v rámci předem daného rozpočtu a pro všechny databáze přitom zajistit elasticitu výkonu.

## <a name="what-are-sql-elastic-pools"></a>Jaké jsou elastické fondy SQL?

Vývojáři SaaS sestavují aplikace nad datovými úrovněmi velkého rozsahu, které se skládají z několika databází. Běžným aplikačním postupem je zřídit pro každého zákazníka izolovanou databázi. Ale různí zákazníci mají často proměnlivé a nepředvídatelné vzorce využití a je těžké odhadnout požadavky jednotlivých databázových uživatelů na prostředky. Tradičně máte dvě možnosti:

- Přepsání zřízení prostředků založenou na využití ve špičce a přes platím, nebo
- Snížení zřídit uložit náklady za cenu výkonu a k zákaznickým spokojenost během vrcholů.

Elastické fondy tento problém vyřešit tím, že zajistí, že databáze získat výkonu prostředky, které potřebují, kdy je potřebují. Poskytují jednoduchý mechanismus přidělování prostředků v mezích předvídatelného rozpočtu. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Elastické fondy povolit vývojáře o zakoupení prostředků pro fond sdílí víc databází pro přizpůsobení nepředvídatelným období využití jednotlivých databází. Můžete nakonfigurovat prostředky pro fondu buď na základě [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) nebo [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md). Požadavek na prostředek pro fond je určen podle agregační využití její databáze. Množství zdrojů do fondu k dispozici je řízena nároky developer. Vývojář jednoduše přidá databází do fondu, nastaví minimální a maximální prostředky pro databáze (buď minimální a maximální počet jednotek Dtu nebo minimální nebo maximální vCores v závislosti na zvoleném resourcing modelu) a poté nastaví prostředků fondu na základě jejich nároky. Vývojáři mohou fondy využít k tomu, aby zajistili elegantní růst svých služeb od úsporného startupu až po zralé podnikání, a to ve stále se zvětšujícím měřítku.

V rámci fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci stanovených parametrů. V případě velkého zatížení může databáze využívat další prostředky ke splnění požadavků. Databáze v rámci světla zatížení využívat menší a databází žádné zatížení využívat žádné prostředky. Zřizováním prostředků pro celý fond, a nikoli pro jednotlivé databáze, se úkoly správy zjednodušují. Plus máte předvídatelný nároky pro fond. Další zdroje informací lze přidat na existující fond bez výpadků databáze s tím rozdílem, že databáze možná potřebujete přesunout zajistit další výpočetní prostředky pro novou rezervaci eDTU. Podobně pokud už nejsou potřeba další prostředky budou lze odebrat z existujícího fondu v libovolném bodě v čase. Navíc můžete databáze do fondu přidávat nebo je z něj odebírat. Pokud databáze podle předpokladu nedostatečně využívá prostředky, odeberte ji.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Pokud byste zvážit elastického fondu SQL Database?

Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití.

Čím více databází je možné do fondu přidat, tím větší budou vaše úspory. V závislosti na vzorech využití aplikací je možné dosáhnout úspor už u pouhých dvou databází S3.

Následující části vás seznámí s postupy, pomocí kterých můžete vyhodnotit, jestli pro vaši konkrétní kolekci databází bude použití fondu přínosné. V příkladech se používají fondy Standard, ale stejné principy platí také pro fondy Basic a Premium.

### <a name="assessing-database-utilization-patterns"></a>Vyhodnocení vzorů využití databáze

Na následujícím obrázku je příklad databáze, která je většinu doby nečinná, ale má také pravidelné špičky aktivity. Tento vzor využití se pro fond skvěle hodí:

   ![izolovaná databáze vhodná pro fond](./media/sql-database-elastic-pool/one-database.png)

Po uvedená pětiminutová období DB1 využívá až 90 jednotek DTU, ale celkové průměrné využití nedosahuje ani pěti jednotek DTU. Ke spuštění této úlohy v izolované databázi se vyžaduje úroveň výkonu S3. To ale znamená, že v období nízké aktivity je většina prostředků nevyužitá.

Fond umožňuje sdílet tyto nevyužité jednotky DTU napříč několika databázemi a snižuje tak počet potřebných jednotek DTU a celkové náklady.

Využijeme předchozí příklad a budeme předpokládat, že existují další databáze s podobnými vzory využití jako DB1. V následujících dvou níže uvedené údaje jsou vrstvený využití čtyři databáze a 20 databáze do stejného grafu pro ilustraci povaha nepřekrývají jejich využití v čase pomocí na základě DTU nákupní model:

   ![Čtyři databáze se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/four-databases.png)

   ![Dvacet databází se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/twenty-databases.png)

Agregované využití DTU napříč všemi 20 databázemi je na předchozím obrázku znázorněné černou čárou. Ukazuje se, že agregované využití DTU nikdy nepřekračuje 100 jednotek DTU. Znamená to, že těchto 20 databází může v průběhu tohoto časového období sdílet 100 jednotek eDTU. Výsledkem je 20krát nižší počet DTU a 13krát nižší cena ve srovnání se zařazením jednotlivých databází do úrovní výkonu S3 pro izolované databáze.

Tento příklad je ideální z následujících důvodů:

* Ukazuje velké rozdíly mezi využitím ve špičce a průměrným využitím jednotlivých databází.
* Špičky využití pro jednotlivé databáze nastávají v různých časových okamžicích.
* Jednotky eDTU jsou sdílené mezi mnoha databázemi.

Cena za fond závisí na jednotkách eDTU fondu. Přestože je cena ze jednotku eDTU pro fond 1,5krát vyšší než cena za jednotku DTU pro izolovanou databázi, **jednotky eDTU fondu může sdílet velký počet databází, a proto stačí menší celkový počet jednotek eDTU**. Tyto rozdíly v cenách a sdílení jednotek eDTU jsou základem potenciálních úspor, které fondy mohou nabídnout.

Následující hrubé odhady související s počtem databází a jejich využitím pomáhají zajistit, že fond ve srovnání s použitím úrovní výkonu pro izolované databáze poskytuje snížení nákladů.

### <a name="minimum-number-of-databases"></a>Maximální počet databází

Pokud agregační objem prostředků pro izolované databáze více než 1,5 x prostředky potřebné k fondu, větší finanční efektivita je fondu elastické databáze.

***Na základě DTU nákupu příklad modelu***<br>
K tomu, aby fond se 100 jednotkami eDTU byl cenově výhodnější než použití úrovní výkonu pro izolované databáze, jsou potřeba nejméně dvě databáze S3 nebo nejméně 15 databází S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximální počet databází se souběžnými špičkami

Sdílení prostředků, ne všechny databáze ve fondu současně pomocí prostředky až do limitu k dispozici pro izolované databáze. Méně databáze, který současně špičkovým, nižší fondu prostředků můžete nastavit a více nákladově efektivní stane fondu. Obecně platí více než 2 nebo 3 (nebo 67 %) databází ve fondu musí současně ve špičkách limitu jejich prostředky.

***Na základě DTU nákupu příklad modelu***<br>
Aby bylo možné snížit náklady pro tři databáze S3 ve fondu s 200 jednotkami eDTU, mohou nejvýše dvě z těchto databází dosahovat špičky svého využití současně. Pokud současně dosahují špičky více než dvě z těchto čtyř databází S3, bylo by nutné velikost fondu nastavit na více než 200 jednotek eDTU. Pokud se velikost fondu nastaví na více než 200 jednotek eDTU, bude nutné do fondu přidat další databáze S3, jinak náklady nebudou nižší než při použití úrovní výkonu pro izolované databáze.

Všimněte si, tento příklad nebere v úvahu využití ostatních databází ve fondu. Pokud se v libovolném konkrétním časovém okamžiku do určité míry využívají všechny databáze, může méně než 2/3 (nebo 67 %) z nich dosahovat špičky současně.

### <a name="resource-utilization-per-database"></a>Využití prostředků na databázi
Velký rozdíl mezi maximálním a průměrným využitím databáze ukazuje na delší doby nízkého využití a krátká období vysokého využití. Tento vzor využití je ideální pro sdílení prostředků mezi databázemi. Použití fondu pro databázi byste měli zvážit, pokud je její využití ve špičce přibližně 1,5krát větší než průměrné využití.

***Na základě DTU nákupu příklad modelu***<br>
Databáze S3, která ve špičce využívá 100 DTU a průměrně využívá 67 DTU nebo méně, je vhodným kandidátem pro sdílení jednotek eDTU ve fondu. Databáze S1, která ve špičce využívá 20 DTU a průměrně využívá 13 DTU nebo méně, je vhodným kandidátem pro fond.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak vybrat velikost fondu správné?

Velikost doporučené pro fond závisí na agregační prostředky potřebné pro všechny databáze ve fondu. To zahrnuje určení následující:

* Maximální prostředků využívaných všechny databáze ve fondu (maximální počet jednotek Dtu nebo maximální vCores v závislosti na zvoleném resourcing modelu).
* Maximální počet bajtů úložiště využitých všemi databázemi ve fondu

Úrovně služeb dostupné pro každý model prostředků, najdete v článku [na základě DTU nákupní model](sql-database-service-tiers-dtu.md) nebo [nákupní model (preview) na základě vCore](sql-database-service-tiers-vcore.md).

SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Kromě těchto doporučení integrované prostředí odhaduje využití eDTU pro vlastní skupinu databází na serveru. Interaktivním přidáváním databází do fondu a jejich odebíráním díky tomu můžete prostřednictvím citlivostních analýz získat analýzy využití prostředků a rady týkající se požadované velikosti dřív, než potvrdíte požadované změny. Postupy najdete v tématu [Monitorování, správa a nastavení velikosti elastického fondu](#monitor-an-elastic-pool-and-its-databases).

V případech, kdy nejde používat nástroje, vám při odhadování, jestli je fond cenově výhodnější než izolované databáze, pomůže následující postup:

1. Odhad Edtu nebo vCores potřebné pro fond následujícím způsobem:

   Na základě DTU nákupu modelu: maximální počet (<*celkový počet databází* X *průměrné využití DTU na databázi*>,<br>
   <*počet databází se souběžnou špičkou* X *využití DTU ve špičce na databázi*)

   Na základě vCore nákupu modelu (preview): maximální počet (<*celkový počet databází* X *průměrné využití vCore na databázi*>,<br>
   <*Počet souběžně peaking databází* X *ve špičce vCore využití za DB*)

2. Odhadněte potřebnou velikost úložiště pro fond (sečtěte počet bajtů potřebných pro všechny databáze ve fondu). Potom určete velikost fondu v jednotkách eDTU, která toto úložiště poskytuje.
3. Na základě DTU nákupu modelu trvat větší z odhadované eDTU z kroku 1 a 2 krok. Na základě vCore nákupu modelu (preview) trvat odhad vCore z kroku 1.
4. Najdete v článku [SQL Database stránce s cenami](https://azure.microsoft.com/pricing/details/sql-database/) a najít fondu nejmenší velikost, která je větší než odhad z kroku 3.
5. Porovnejte cenu fondu z kroku 5 s cenou při použití odpovídajících úrovní výkonu pro izolované databáze.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Další funkce SQL Database pomocí elastické fondy

### <a name="elastic-jobs-and-elastic-pools"></a>Elastické úlohy a elastické fondy

U fondu jsou úlohy správy zjednodušené díky spouštění skriptů v **[elastických úlohách](sql-database-elastic-jobs-overview.md)**. Elastická úloha eliminuje většinu únavných úkolů spojených s velkým počtem databází. Pro začátek si přečtěte téma [Začínáme s elastickými úlohami](sql-database-elastic-jobs-getting-started.md).

Další informace o ostatních databázových nástrojích pro práci s více databázemi najdete v tématu [Horizontální navýšení kapacity se službou Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Možnosti kontinuity podnikových procesů u databází v elastickém fondu
Databáze ve fondu obecně podporují stejné [funkce provozní kontinuity](sql-database-business-continuity.md), jaké jsou dostupné pro izolované databáze.

- **Obnovení bodu v čase**: obnovení bodu v čase používá automatické zálohování databází k obnovení databáze ve fondu k určitému bodu v čase. Viz [Obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Geografické obnovení**: geografické obnovení poskytuje výchozí možnost, když databáze není k dispozici z důvodu incidentu v oblasti, který je hostitelem databáze. Viz [Obnovení služby Azure SQL Database a převzetí služeb při selhání sekundární lokalitou](sql-database-disaster-recovery.md).

- **Aktivní geografickou replikací**: aplikace, které mají agresivnější požadavky na obnovení než nabízejí geografické obnovení, nakonfigurujte [aktivní geografickou replikací](sql-database-geo-replication-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Vytvoření nového elastického fondu SQL Database pomocí portálu Azure

Existují dva způsoby fondu elastické databáze můžete vytvořit na portálu Azure.
1. Elastický fond můžete vytvořit tak, že **elastický fond SQL** v **Marketplace** nebo kliknutím na tlačítko **+ přidat** na elastické fondy SQL Procházet okna. Budete moci zadat nové nebo existující server prostřednictvím tohoto fondu zřizování pracovního postupu.
2. Nebo můžete vytvořit fond elastické navigace na existující server SQL a kliknutím na **vytvořit fond** vytvoření fondu přímo do tohoto serveru. Jediným rozdílem je, že přeskočte krok umístění serveru během fondu zřizování pracovního postupu.

> [!NOTE]
> Můžete vytvořit více fondů na serveru, ale nemůžete přidat databáze z různých serverů do stejného fondu.

Služba úroveň fondu určuje funkce, které jsou dostupné pro elastics ve fondu a maximální objem prostředků, které jsou k dispozici pro každou databázi. Podrobnosti najdete v tématu omezení prostředků pro elastické fondy v [DTU modelu](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-performance-levels). Na základě vCore prostředků limity pro elastické fondy, najdete v části [limitů prostředků na základě vCore - elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Konfigurace prostředků a ceny fondu, klikněte na tlačítko **konfigurace fondu**. Potom vyberte vrstvu služby, přidat databáze do fondu a nakonfigurovat omezení prostředků pro fond a její databáze.

Když dokončíte konfiguraci fondu, můžete kliknutím na tlačítko použít, název fondu a klikněte na tlačítko "OK", pokud chcete vytvořit fond.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorování fondu elastické databáze a její databáze

Na portálu Azure můžete sledovat využití fondu elastické databáze a databáze v tomto fondu. Můžete také nastavit sadu změn do pružného fondu a odeslat všechny změny ve stejnou dobu. Tyto změny zahrnují přidávání nebo odebírání databáze, změna nastavení služby elastického fondu nebo změna nastavení databáze.

Jak začít monitorovat elastického fondu, najít a otevřít fondu elastické databáze na portálu. Zobrazí se první obrazovka, která poskytuje přehled o stavu elastického fondu. To zahrnuje:

* Monitorování grafy znázorňující využití prostředků elastického fondu
* Nedávné výstrahy a doporučení, pokud je k dispozici pro elastický fond

Následující obrázek ukazuje příklad elastickém fondu:

![Zobrazení fondu](./media/sql-database-elastic-pool-manage-portal/basic.png)

Pokud chcete další informace o fondu kliknutím na všechny dostupné informace v tomto přehledu. Kliknutím na **využití prostředků** grafu přejdete na zobrazení Azure Monitoring kde můžete přizpůsobit okno metriky a času zobrazené v grafu. Kliknutím na jakékoliv dostupné oznámení se dostanete na okno, které zobrazuje podrobnosti o této doporučení nebo výstrah.

Pokud chcete monitorování databází ve fondu, můžete kliknutím na **databáze využití prostředků** v **monitorování** prostředků nabídky na levé straně.

![Stránka využití prostředků databáze](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Chcete-li přizpůsobit zobrazení grafu

Můžete upravit graf a metriky stránky zobrazíte další metriky jako procento, procento vstupů/výstupů dat a protokolu vstupně-výstupní operace % využití procesoru.

Na **upravit graf** formuláře, můžete vybrat buď fixní dobu rozsahu nebo klikněte na tlačítko **vlastní** vyberte všechny časové období 24 hodin v poslední dva týdny, a potom vyberte zdroje, které chcete monitorovat.

### <a name="to-select-databases-to-monitor"></a>Chcete-li vybrat databáze k monitorování

Ve výchozím nastavení grafu v **využití prostředků databáze** okno se zobrazí databáze top 5 DTU nebo využití procesoru (v závislosti na vaší vrstvy služby). Můžete přepnout do databáze v tomto grafu výběrem a unselecting databáze ze seznamu pod grafem prostřednictvím políček na levé straně.

Můžete také vybrat další metriky do zobrazení vedle sebe v této tabulce databáze získat ucelený přehled o výkon databáze.

Další informace najdete v tématu [vytvářet výstrahy, SQL Database na portálu Azure](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>Další postup

- Škálování elastické fondy, najdete v části [škálování elastické fondy](sql-database-elastic-pool.md) a [škálování fondu elastické databáze – ukázkový kód](scripts/sql-database-monitor-and-scale-pool-powershell.md)
* Video najdete v tématu [Microsoft Virtual Academy video kurzu na možnostech elastické databáze SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* SaaS pomocí elastické fondy, na adrese [Úvod k aplikaci Wingtip SaaS](sql-database-wtp-overview.md).
