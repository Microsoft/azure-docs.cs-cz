---
title: Správa několika databázemi SQL elastické fondy – Azure | Dokumentace Microsoftu
description: Spravovat a škálovat několika databázemi SQL - stovkami a tisíci - využívajících elastické fondy. Jednu cenu za prostředky, které můžete distribuovat místech.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
manager: craigg
ms.date: 02/07/2019
ms.openlocfilehash: 4e4de6d487aaad8a0d036928fe59b47f25157bf6
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55963841"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastické fondy umožňují spravovat a škálovat několika databázemi Azure SQL

Elastické fondy SQL Database je jednoduché a cenově výhodné řešení pro správu a škálování více databází s proměnlivými a nepředvídatelnými požadavky na využití. Databáze v elastickém fondu jsou na jednom serveru Azure SQL Database a sdílet stanovený počet prostředků za stanovenou cenu. Elastické fondy v Azure SQL Database umožňují vývojářům SaaS optimalizovat poměr cena/výkon pro skupinu databází v rámci předem daného rozpočtu a pro všechny databáze přitom zajistit elasticitu výkonu.

## <a name="what-are-sql-elastic-pools"></a>Co jsou elastické fondy SQL

Vývojáři SaaS sestavují aplikace nad datovými úrovněmi velkého rozsahu, které se skládají z několika databází. Běžným aplikačním postupem je zřídit pro každého zákazníka izolovanou databázi. Ale různí zákazníci mají často proměnlivé a nepředvídatelné vzorce využití a je těžké odhadnout požadavky jednotlivých databázových uživatelů na prostředky. Tradičně máte dvě možnosti:

- Zřizovat prostředky, které jsou založeny na využití ve špičce a platit, nebo
- Přidělovat náklady na úkor výkonu a spokojenosti zákazníků během špiček.

Elastické fondy tento problém řeší tím, že zajišťuje, aby databáze dostávaly prostředky výkonu, které potřebují, kdy ho potřebují. Poskytují jednoduchý mechanismus přidělování prostředků v mezích předvídatelného rozpočtu. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Neplatí žádné poplatky za databáze pro elastické fondy. Účtuje se vám každá hodina existence fondu na eDTU nejvyšší nebo virtuálních jader, bez ohledu na využití nebo na to, jestli byl fondu aktivní kratší dobu než hodinu.

Elastické fondy umožňují vývojářům zakoupit prostředky pro fond sdílený více databázemi na umožňují zajistit nepředvídatelná období využití strany jednotlivých databází. Prostředky můžete nakonfigurovat fond podle buď [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md). Požadavek na prostředek pro fond je určeno agregačním využitím jeho databází. Řídí rozpočet vývojáře určuje objem prostředků fondu k dispozici. Vývojář jednoduše do fondu přidá databáze, nastaví minimální a maximální prostředky, pro databáze (buď minimální a maximální počet jednotek Dtu nebo minimální nebo maximální virtuální jádra podle svého výběru pravděpodobně model) a pak nastaví prostředků fondu na základě jejich rozpočtu. Vývojáři mohou fondy využít k tomu, aby zajistili elegantní růst svých služeb od úsporného startupu až po zralé podnikání, a to ve stále se zvětšujícím měřítku.

V rámci fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci stanovených parametrů. V případě velkého zatížení může databáze spotřebovávat více prostředků podle potřeby. Nízkém zatížení databáze spotřebovávají méně edtu a databáze bez zatížení nespotřebovávají žádné prostředky. Zřizováním prostředků pro celý fond, a nikoli pro jednotlivé databáze, se úkoly správy zjednodušují. Kromě toho můžete předem odhadnout náklady pro fond. Další zdroje informací můžete přidat do existujícího fondu bez jakéhokoli výpadku databáze, s tím rozdílem, že možná bude nutné databáze přesunout zajištění dalších výpočetních prostředků pro vyhrazení nových eDTU. Podobně pokud už nepotřebujete další prostředky, může být odstraněn z existujícího fondu kdykoli v čase. Navíc můžete databáze do fondu přidávat nebo je z něj odebírat. Pokud databáze podle předpokladu nedostatečně využívá prostředky, odeberte ji.

> [!NOTE]
> Při přesouvání databází do nebo z elastického fondu, nedochází bez výpadků, s výjimkou krátkou dobu (v řádu sekund) na konci operace, při připojení k databázi se zahodí.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Pokud byste zvážit elastického fondu SQL Database

Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití.

Čím více databází je možné do fondu přidat, tím větší budou vaše úspory. V závislosti na vzorech využití aplikací je možné dosáhnout úspor už u pouhých dvou databází S3.

Následující části vás seznámí s postupy, pomocí kterých můžete vyhodnotit, jestli pro vaši konkrétní kolekci databází bude použití fondu přínosné. V příkladech se používají fondy Standard, ale stejné principy platí také pro fondy Basic a Premium.

### <a name="assessing-database-utilization-patterns"></a>Vyhodnocení vzorů využití databáze

Na následujícím obrázku je příklad databáze, která je většinu doby nečinná, ale má také pravidelné špičky aktivity. Tento vzor využití se pro fond skvěle hodí:

   ![izolovaná databáze vhodná pro fond](./media/sql-database-elastic-pool/one-database.png)

Po uvedená pětiminutová období DB1 využívá až 90 jednotek DTU, ale celkové průměrné využití nedosahuje ani pěti jednotek DTU. Pro velikost výpočetních S3 se vyžaduje ke spuštění této úlohy v jedné databázi, ale to znamená většina prostředků nevyužitá období nízké aktivity.

Fond umožňuje sdílet tyto nevyužité jednotky DTU napříč několika databázemi a snižuje tak počet potřebných jednotek DTU a celkové náklady.

Využijeme předchozí příklad a budeme předpokládat, že existují další databáze s podobnými vzory využití jako DB1. V následujících dvou obrázcích níže využití čtyř databází a 20 databází zakresleno do stejného grafu pro ilustraci povaze překrývat jejich využití v čase pomocí nákupní model založený na DTU:

   ![Čtyři databáze se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/four-databases.png)

   ![Dvacet databází se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/twenty-databases.png)

Agregované využití DTU napříč všemi 20 databázemi je na předchozím obrázku znázorněné černou čárou. Ukazuje se, že agregované využití DTU nikdy nepřekračuje 100 jednotek DTU. Znamená to, že těchto 20 databází může v průběhu tohoto časového období sdílet 100 jednotek eDTU. Výsledkem je 20krát nižší v jednotkách Dtu a 13krát nižší cena ve srovnání se zařazením jednotlivých databází do S3 výpočtu velikosti pro izolované databáze.

Tento příklad je ideální z následujících důvodů:

- Ukazuje velké rozdíly mezi využitím ve špičce a průměrným využitím jednotlivých databází.
- Špičky využití pro jednotlivé databáze nastávají v různých časových okamžicích.
- Jednotky eDTU jsou sdílené mezi mnoha databázemi.

Cena za fond závisí na jednotkách eDTU fondu. Přestože je cena ze jednotku eDTU pro fond 1,5krát vyšší než cena za jednotku DTU pro izolovanou databázi, **jednotky eDTU fondu může sdílet velký počet databází, a proto stačí menší celkový počet jednotek eDTU**. Tyto rozdíly v cenách a sdílení jednotek eDTU jsou základem potenciálních úspor, které fondy mohou nabídnout.

Následující hrubé odhady související s počtem databází a jejich využitím pomáhají zajistit, že fond poskytuje snížení nákladů ve srovnání s použitím velikostí výpočetních pro izolované databáze.

### <a name="minimum-number-of-databases"></a>Maximální počet databází

Pokud agregační objem prostředků pro izolované databáze je více než 1, 5násobek prostředků potřebných pro fond, je elastický fond cenově výhodnější.

***Založený na DTU nákupní model příklad***<br>
100 jednotkami eDTU fond cenově výhodnější než použití velikostí výpočetních pro izolované databáze jsou potřeba nejméně dvě databáze S3 nebo nejméně 15 databází S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximální počet databází se souběžnými špičkami

Prostřednictvím sdílení prostředků, ne všechny databáze ve fondu současně používat prostředky až po limit dostupný pro izolované databáze. Čím méně databází má současně špičku, tím nižší prostředků fondu můžete nastavit a tím výhodnější fond bude. Obecně platí by měla více než 2/3 (nebo 67 %) databází ve fondu současně vrcholu do svého limitu prostředků.

***Založený na DTU nákupní model příklad***

Aby bylo možné snížit náklady pro tři databáze S3 ve fondu s 200 jednotkami eDTU, mohou nejvýše dvě z těchto databází dosahovat špičky svého využití současně. Pokud současně dosahují špičky více než dvě z těchto čtyř databází S3, bylo by nutné velikost fondu nastavit na více než 200 jednotek eDTU. Pokud je velikost fondu nastaví na více než 200 jednotek Edtu, další databáze S3, jinak bude nutné přidat do fondu náklady nebudou nižší než výpočtu velikosti pro izolované databáze.

Všimněte si, tento příklad nebere v úvahu využití ostatních databází ve fondu. Pokud se v libovolném konkrétním časovém okamžiku do určité míry využívají všechny databáze, může méně než 2/3 (nebo 67 %) z nich dosahovat špičky současně.

### <a name="resource-utilization-per-database"></a>Využití prostředků na databázi

Velký rozdíl mezi maximálním a průměrným využitím databáze ukazuje na delší doby nízkého využití a krátká období vysokého využití. Tento vzor využití je ideální pro sdílení prostředků mezi databázemi. Použití fondu pro databázi byste měli zvážit, pokud je její využití ve špičce přibližně 1,5krát větší než průměrné využití.

**Založený na DTU nákupní model příklad**: Databáze S3, která ve špičce využívá 100 DTU a průměrně využívá 67 DTU nebo méně, je vhodným kandidátem pro sdílení jednotek eDTU ve fondu. Databáze S1, která ve špičce využívá 20 DTU a průměrně využívá 13 DTU nebo méně, je vhodným kandidátem pro fond.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak můžu vybrat velikost fondu správné

Ideální velikost fondu závisí na agregační prostředky potřebné pro všechny databáze ve fondu. To zahrnuje určení následující:

- Maximální prostředky využitých všemi databázemi ve fondu (maximální počet jednotek Dtu nebo virtuálních jader pro maximální podle svého výběru pravděpodobně modelu).
- Maximální počet bajtů úložiště využitých všemi databázemi ve fondu

Úrovně služby k dispozici pro každý model prostředků, najdete v článku [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

V případech, kdy nejde používat nástroje, vám při odhadování, jestli je fond cenově výhodnější než izolované databáze, pomůže následující postup:

1. Odhad Edtu nebo virtuálních jader potřebných pro fond následujícím způsobem:

   Pro nákupní model založený na DTU: MAX(<*celkový počet databází* X *průměrné využití DTU na databázi*>,<br>  
   <*počet databází se souběžnou špičkou* X *využití DTU ve špičce na databázi*)

   Pro nákupní model založený na virtuálních jádrech: MAX (<*celkový počet databází* X *průměrné využití vCore na databázi*>,<br>  
   <*Počet špičkou databází* X *vCore využití ve špičce na databázi*)

2. Odhadněte potřebnou velikost úložiště pro fond (sečtěte počet bajtů potřebných pro všechny databáze ve fondu). Potom určete velikost fondu v jednotkách eDTU, která toto úložiště poskytuje.
3. Pro nákupní model založený na DTU trvat větší z odhadovaného počtu eDTU z kroku 1 a 2. Pro nákupní model založený na virtuálních jádrech trvat vCore odhad z kroku 1.
4. Zobrazit [stránce s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) a najděte fondu nejmenší velikost, která je větší než odhad z kroku 3.
5. Porovnejte cenu fondu z kroku 5 s cenou při použití odpovídajícími výpočetními velikosti pro izolované databáze.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Další funkce služby SQL Database pomocí elastických fondů

### <a name="elastic-jobs-and-elastic-pools"></a>Elastické úlohy a elastických fondů

U fondu jsou úlohy správy zjednodušené díky spouštění skriptů v **[elastických úlohách](sql-database-elastic-jobs-overview.md)**. Elastická úloha eliminuje většinu únavných úkolů spojených s velkým počtem databází. Pro začátek si přečtěte téma [Začínáme s elastickými úlohami](sql-database-elastic-jobs-getting-started.md).

Další informace o ostatních databázových nástrojích pro práci s více databázemi najdete v tématu [Horizontální navýšení kapacity se službou Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Možnosti kontinuity podnikových procesů pro databáze v elastickém fondu

Databáze ve fondu obecně podporují stejné [funkce provozní kontinuity](sql-database-business-continuity.md), jaké jsou dostupné pro izolované databáze.

- **Obnovení k určitému bodu v čase**

  Obnovení k určitému bodu v čase využívá automatické zálohování databází k obnovení databáze ve fondu k určitému bodu v čase. Viz [Obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Geo-restore**

  Geografické obnovení poskytuje výchozí možnost zotavení, pokud je databáze není k dispozici z důvodu incidentu v oblasti, kde se hostuje databázi. Viz [Obnovení služby Azure SQL Database a převzetí služeb při selhání sekundární lokalitou](sql-database-disaster-recovery.md).

- **Aktivní geografická replikace**

  Pro aplikace, které mají více vyššími požadavky na zotavení, než může nabídnout geografické obnovení, nakonfigurujte [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [-automatické převzetí služeb při selhání skupiny](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Vytvoření nového elastického fondu SQL Database pomocí webu Azure portal

Existují dva způsoby elastický fond můžete vytvořit na webu Azure Portal.

1. Elastický fond můžete vytvořit tak, že **elastický fond SQL** v **Marketplace** nebo kliknutím na možnost **+ přidat** o elastických fondech SQL okně procházení. Budete moci zadat nový nebo existující server prostřednictvím tohoto pracovního postupu zřizování fondu.
2. Nebo můžete vytvořit elastický fond tak, že přejdete na existujícím serveru SQL a kliknete **vytvořit fond** a vytvořit tak fond přímo do tohoto serveru. Jediný rozdíl zde je, že můžete přeskočit na krok umístění serveru během pracovního postupu zřizování fondu.

> [!NOTE]
> Můžete vytvořit více fondů na serveru, ale nemůžete přidat databáze z různých serverů do stejného fondu.

Úrovně služeb fondu určuje funkce, které jsou k dispozici elastických ve fondu a maximální dobu, prostředky dostupné pro každou databázi. Podrobnosti najdete v tématu limity pro elastické fondy v prostředků [DTU modelu](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Prostředek založený na virtuálních jádrech omezení pro elastické fondy najdete v tématu [omezení prostředků založený na virtuálních jádrech - elastických fondů](sql-database-vcore-resource-limits-elastic-pools.md).

Ke konfiguraci prostředků a ceny ve fondu, klikněte na tlačítko **konfigurace fondu**. Vyberte úroveň služby, přidejte databáze do fondu a nakonfigurovat omezení prostředků pro fond a jeho databázím.

Po dokončení konfigurace fondu můžete klepnutím na tlačítko použít, název fondu a klikněte na tlačítko "OK" k vytvoření fondu.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Sledování elastického fondu a jeho databázím

Na webu Azure Portal můžete monitorovat využití elastického fondu a databáze v tomto fondu. Také můžete provést sadu změn do elastického fondu a odešlete všechny změny ve stejnou dobu. Tyto změny zahrnout, přidání nebo odebrání databáze, změna nastavení služby elastického fondu nebo změna nastavení databáze.

K monitorování elastického fondu, najít a otevřít elastického fondu na portálu. Zobrazí se první obrazovku, která poskytuje přehled o stavu vašeho elastického fondu. To zahrnuje:

- Monitorování grafy zobrazující využití prostředků elastického fondu
- Nedávné výstrahy a doporučení, pokud je k dispozici pro elastický fond

Následující obrázek znázorňuje příklad elastického fondu:

![Zobrazení fondu](./media/sql-database-elastic-pool-manage-portal/basic.png)

Pokud chcete získat další informace o fondu můžete kliknout na kterýkoli z dostupné informace v tomto přehledu. Kliknutím na **využití prostředků** grafu přejdete na zobrazení monitorování Azure ve kterém můžete přizpůsobit okna metrik a času zobrazené v grafu. Kliknutím na všechny dostupné oznámení se dostanete na okno, které se zobrazí úplné podrobnosti o této výstrahy ani doporučení.

Pokud chcete monitorování databází ve fondu, můžete kliknout na **využití prostředků databáze** v **monitorování** prostředku nabídky na levé straně.

![Stránka využití prostředků databáze](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Chcete-li přizpůsobit zobrazení grafu

Můžete upravit graf a stránce metriky zobrazíte další metriky, jako je procento, procento datových v/v úrovně a protokolu vstupně-výstupních operací procento využití procesoru.

Na **upravit graf** formuláře, můžete vybrat pevného časového rozsahu nebo klikněte na tlačítko **vlastní** v posledních dvou týdnech vyberte jakékoli okno 24 hodin, a potom vyberte zdroje, které chcete monitorovat.

### <a name="to-select-databases-to-monitor"></a>Vyberte databáze pro monitorování

Ve výchozím nastavení graf **využití databázových prostředků** okno se zobrazí databáze top 5 DTU nebo využití procesoru (v závislosti na vaší úrovni služby). Do databáze v tomto grafu můžete přepínat výběrem a zrušení výběru databáze ze seznamu pod grafem pomocí zaškrtávacích políček na levé straně.

Můžete také vybrat další metriky pro zobrazení vedle sebe v této tabulce databáze, chcete-li získat podrobnější přehled výkonu vaší databáze.

Další informace najdete v tématu [vytvářet upozornění databáze SQL na webu Azure portal](sql-database-insights-alerts-portal.md).

## <a name="next-steps"></a>Další postup

- Škálování elastické fondy, najdete v článku [škálování elastických fondů](sql-database-elastic-pool.md) a [škálovat elastický fond – ukázky kódu](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Video najdete v tématu [videokurz Microsoft Virtual Academy o možnostech elastické databáze SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Kurz SaaS využívajících elastické fondy najdete v tématu [Úvod do aplikací SaaS aplikace Wingtip](sql-database-wtp-overview.md).
