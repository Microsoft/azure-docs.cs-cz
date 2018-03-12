---
title: "Správa více databází SQL s elastické fondy Azure | Microsoft Docs"
description: "Spravovat a škálování více databází SQL - stovky a s tisíci - pomocí elastické fondy. Jeden ceny pro prostředky, které můžete distribuovat, kde je potřeba."
keywords: "více databází, databáze prostředků, výkon databáze"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: b46e7fdc-2238-4b3b-a944-8ab36c5bdb8e
ms.service: sql-database
ms.custom: DBs & servers
ms.devlang: NA
ms.date: 03/02/2018
ms.author: carlrab
ms.workload: Active
ms.topic: article
ms.openlocfilehash: e89d348c4b21a249401254c64e3887ec36283022
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastických fondů pomáhají spravovat a škálování více databází Azure SQL

Fondy elastické databáze SQL jsou jednoduché a nákladově efektivní řešení pro správu a škálování více databází, které mají různou a nepředvídatelným nároky na využití. Databáze ve fondu elastické databáze na jednom serveru Azure SQL Database a sdílet se stanoveným počtem prostředky ([jednotky transakcí elastické databáze](sql-database-what-is-a-dtu.md) (Edtu)) za cenu sady. Elastické fondy v Azure SQL Database umožňují vývojářům SaaS optimalizovat poměr cena/výkon pro skupinu databází v rámci předem daného rozpočtu a pro všechny databáze přitom zajistit elasticitu výkonu. 

## <a name="what-are-sql-elastic-pools"></a>Jaké jsou elastické fondy SQL? 

Vývojáři SaaS sestavují aplikace nad datovými úrovněmi velkého rozsahu, které se skládají z několika databází. Běžným aplikačním postupem je zřídit pro každého zákazníka izolovanou databázi. Ale různí zákazníci mají často proměnlivé a nepředvídatelné vzorce využití a je těžké odhadnout požadavky jednotlivých databázových uživatelů na prostředky. Tradičně máte dvě možnosti: 

- Přepsání zřízení prostředků založenou na využití ve špičce a přes platím, nebo
- Snížení zřídit uložit náklady za cenu výkonu a k zákaznickým spokojenost během vrcholů. 

Elastické fondy tento problém vyřešit tím, že zajistí, že databáze získat výkonu prostředky, které potřebují, kdy je potřebují. Poskytují jednoduchý mechanismus přidělování prostředků v mezích předvídatelného rozpočtu. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

Elastické fondy povolit vývojáři zakoupit [jednotky transakcí elastické databáze](sql-database-what-is-a-dtu.md) (Edtu) pro fond sdílí víc databází pro přizpůsobení nepředvídatelným období využití jednotlivých databází. Požadavek fondu na eDTU je určený agregačním využitím jeho databází. Rozpočet vývojáře určuje počet jednotek eDTU, které má fond k dispozici. Vývojář jednoduše do fondu přidá databáze, nastaví minimální a maximální počet jednotek eDTU pro tyto databáze a potom na základě svého rozpočtu nastaví eDTU fondu. Vývojáři mohou fondy využít k tomu, aby zajistili elegantní růst svých služeb od úsporného startupu až po zralé podnikání, a to ve stále se zvětšujícím měřítku.

V rámci fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci stanovených parametrů. V případě velkého zatížení může databáze spotřebovávat více eDTU, aby splnila požadavky. Databáze při nízkém zatížení spotřebovávají méně eDTU a databáze bez zatížení nespotřebovávají žádné eDTU. Zřizováním prostředků pro celý fond, a nikoli pro jednotlivé databáze, se úkoly správy zjednodušují. Kromě toho můžete předem odhadnout náklady na fond. Další eDTU lze do existujícího fondu přidat bez jakéhokoli výpadku databáze, nicméně možná bude nutné databáze přesunout za účelem zajištění dalších výpočetních prostředků pro vyhrazení nových eDTU. Podobně platí, že pokud již přidané eDTU nejsou potřebné, lze je z existujícího fondu kdykoli odebrat. Navíc můžete databáze do fondu přidávat nebo je z něj odebírat. Pokud databáze podle předpokladu nedostatečně využívá prostředky, odeberte ji.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Pokud byste zvážit elastického fondu SQL Database?

Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití.

Čím více databází je možné do fondu přidat, tím větší budou vaše úspory. V závislosti na vzorech využití aplikací je možné dosáhnout úspor už u pouhých dvou databází S3. 

Následující části vás seznámí s postupy, pomocí kterých můžete vyhodnotit, jestli pro vaši konkrétní kolekci databází bude použití fondu přínosné. V příkladech se používají fondy Standard, ale stejné principy platí také pro fondy Basic a Premium.

### <a name="assessing-database-utilization-patterns"></a>Vyhodnocení vzorů využití databáze

Na následujícím obrázku je příklad databáze, která je většinu doby nečinná, ale má také pravidelné špičky aktivity. Tento vzor využití se pro fond skvěle hodí:

   ![izolovaná databáze vhodná pro fond](./media/sql-database-elastic-pool/one-database.png)

Po uvedená pětiminutová období DB1 využívá až 90 jednotek DTU, ale celkové průměrné využití nedosahuje ani pěti jednotek DTU. Ke spuštění této úlohy v izolované databázi se vyžaduje úroveň výkonu S3. To ale znamená, že v období nízké aktivity je většina prostředků nevyužitá.

Fond umožňuje sdílet tyto nevyužité jednotky DTU napříč několika databázemi a snižuje tak počet potřebných jednotek DTU a celkové náklady.

Využijeme předchozí příklad a budeme předpokládat, že existují další databáze s podobnými vzory využití jako DB1. Na následujících dvou obrázcích je využití čtyř databází a 20 databází zakresleno do stejného grafu. Vidíte tak, že se jejich využití v čase nepřekrývá:

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

Pokud je součet jednotek DTU úrovní výkonu pro izolované databáze větší než 1,5násobek počtu jednotek eDTU potřebných pro fond, je elastický fond cenově výhodnější. Dostupné velikosti najdete v tématu [Omezení úložiště a eDTU pro elastické fondy a elastické databáze](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

***Příklad***<br>
K tomu, aby fond se 100 jednotkami eDTU byl cenově výhodnější než použití úrovní výkonu pro izolované databáze, jsou potřeba nejméně dvě databáze S3 nebo nejméně 15 databází S0.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximální počet databází se souběžnými špičkami

Vzhledem k tomu, že jednotky eDTU se sdílejí, nemohou je současně využít všechny databáze ve fondu limit až po limit dostupný při použití úrovní výkonu pro izolované databáze. Čím méně databází má současně špičku, tím nižší počet jednotek eDTU ve fondu je možné nastavit a tím výhodnější fond bude. Obecně platí, že svůj limit eDTU by mělo současně využívat nejvýše 2/3 (nebo 67 %) databází ve fondu.

***Příklad***<br>
Aby bylo možné snížit náklady pro tři databáze S3 ve fondu s 200 jednotkami eDTU, mohou nejvýše dvě z těchto databází dosahovat špičky svého využití současně. Pokud současně dosahují špičky více než dvě z těchto čtyř databází S3, bylo by nutné velikost fondu nastavit na více než 200 jednotek eDTU. Pokud se velikost fondu nastaví na více než 200 jednotek eDTU, bude nutné do fondu přidat další databáze S3, jinak náklady nebudou nižší než při použití úrovní výkonu pro izolované databáze.

Všimněte si, tento příklad nebere v úvahu využití ostatních databází ve fondu. Pokud se v libovolném konkrétním časovém okamžiku do určité míry využívají všechny databáze, může méně než 2/3 (nebo 67 %) z nich dosahovat špičky současně.

### <a name="dtu-utilization-per-database"></a>Využití DTU na databázi
Velký rozdíl mezi maximálním a průměrným využitím databáze ukazuje na delší doby nízkého využití a krátká období vysokého využití. Tento vzor využití je ideální pro sdílení prostředků mezi databázemi. Použití fondu pro databázi byste měli zvážit, pokud je její využití ve špičce přibližně 1,5krát větší než průměrné využití.

***Příklad***<br>
Databáze S3, která ve špičce využívá 100 DTU a průměrně využívá 67 DTU nebo méně, je vhodným kandidátem pro sdílení jednotek eDTU ve fondu. Databáze S1, která ve špičce využívá 20 DTU a průměrně využívá 13 DTU nebo méně, je vhodným kandidátem pro fond.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak vybrat velikost fondu správné?

Ideální velikost fondu závisí na agregovaných jednotkách eDTU a prostředcích úložiště potřebných pro všechny databáze ve fondu. To znamená, že je potřeba určit větší z následujících dvou hodnot:

* Maximální počet jednotek DTU využitých všemi databázemi ve fondu
* Maximální počet bajtů úložiště využitých všemi databázemi ve fondu

Dostupné velikosti najdete v tématu [Omezení úložiště a eDTU pro elastické fondy a elastické databáze](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

SQL Database automaticky vyhodnotí historické údaje používání prostředků databází na existujícím serveru SQL Database a doporučí odpovídající konfigurace fondu na webu Azure Portal. Kromě těchto doporučení integrované prostředí odhaduje využití eDTU pro vlastní skupinu databází na serveru. Interaktivním přidáváním databází do fondu a jejich odebíráním díky tomu můžete prostřednictvím citlivostních analýz získat analýzy využití prostředků a rady týkající se požadované velikosti dřív, než potvrdíte požadované změny. Postupy najdete v tématu [Monitorování, správa a nastavení velikosti elastického fondu](sql-database-elastic-pool-manage-portal.md).

V případech, kdy nejde používat nástroje, vám při odhadování, jestli je fond cenově výhodnější než izolované databáze, pomůže následující postup:

1. Následujícím způsobem odhadněte počet jednotek eDTU potřebných pro fond:

   MAX(<*celkový počet databází* X *průměrné využití DTU na databázi*>,<br>
   <*počet databází se souběžnou špičkou* X *využití DTU ve špičce na databázi*)
2. Odhadněte potřebnou velikost úložiště pro fond (sečtěte počet bajtů potřebných pro všechny databáze ve fondu). Potom určete velikost fondu v jednotkách eDTU, která toto úložiště poskytuje. Omezení úložiště fondů na základě velikosti fondu v jednotkách eDTU najdete v tématu [Omezení úložiště a eDTU pro elastické fondy a elastické databáze](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).
3. Použijte větší z odhadovaného počtu eDTU z kroku 1 a kroku 2.
4. Prohlédněte si [stránku s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) a najděte nejmenší velikost fondu v jednotkách eDTU, která je větší než odhad z kroku 3.
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

## <a name="manage-elastic-pools-and-databases-using-the-azure-portal"></a>Správa elastické fondy a databází pomocí portálu Azure

### <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Vytvoření nového elastického fondu SQL Database pomocí portálu Azure

Existují dva způsoby fondu elastické databáze můžete vytvořit na portálu Azure. Pokud znáte cílovou konfiguraci fondu, můžete ho vytvořit od začátku sami, nebo můžete začít s fondem, který vám doporučí služba. SQL Database má vestavěné inteligentní, která doporučuje instalační program elastického fondu, pokud je cenově výhodnější založené na posledních telemetrii využití pro své databáze. 

Vytvoření fondu elastické databáze z existující stránky serveru portálu je nejjednodušší způsob, jak přesunout existující databáze do pružného fondu. Můžete také vytvořit fondu elastické databáze vyhledávání **elastický fond SQL** v **Marketplace** nebo kliknutím na tlačítko **+ přidat** na stránce elastické fondy SQL. Budete moci zadat nové nebo existující server prostřednictvím tohoto fondu zřizování pracovního postupu.

> [!NOTE]
> Můžete vytvořit více fondů na serveru, ale nemůžete přidat databáze z různých serverů do stejného fondu.
> 

Cenová úroveň fondu určuje funkce, které jsou k dispozici pro elastics ve fondu a maximální počet jednotek Edtu (eDTU MAX) a úložiště (GB) k dispozici pro každou databázi. Podrobnosti najdete v tématu [prostředků omezení pro elastické fondy](sql-database-resource-limits.md#elastic-pool-storage-sizes-and-performance-levels).

Chcete-li změnit cenovou úroveň fondu, klikněte na **Cenová úroveň**, vyberte požadovanou cenovou úroveň a klikněte na **Vybrat**.

> [!IMPORTANT]
> Až vyberete cenovou úroveň a potvrdíte svoji volbu kliknutím na **OK** v posledním kroku, nebude už možné cenovou úroveň fondu změnit. Pokud chcete změnit cenovou úroveň existujícího elastického fondu, vytvoření fondu elastické databáze v s požadovanou cenovou úrovní a migrace databáze do tohoto nového fondu.
>

Pokud databáze, s kterými pracujete, mají dostatek historických telemetrických dat, graf **Odhadované eDTU a využití GB** a pruhový graf **Skutečné využití eDTU** se aktualizují, aby vám pomohly s rozhodováním o hodnotách konfigurace. Služba vám také může zobrazovat doporučení s cílem nastavit optimální velikost fondu.

Služba SQL Database vyhodnocuje historii využití a doporučí použití jednoho nebo několika fondů, jakmile to začne být cenově výhodnější než použití izolované databáze. Každé doporučení je konfigurováno pro jedinečnou podmnožinu databází serveru, která je pro fond nejvhodnější.

![doporučený fond](./media/sql-database-elastic-pool-create-portal/recommended-pool.png) 

Doporučení fondu zahrnuje:

- cenovou úroveň pro fond (Basic, Standard nebo Premium),
- vhodnou hodnotu **POOL eDTU** (označovanou také jako Max eDTU pro fond),
- hodnoty **eDTU MAX** a **eDTU MIN** pro každou databázi,
- seznam doporučených databází pro fond.

> [!IMPORTANT]
> Při vytváření doporučení bere služba v úvahu telemetrická data za posledních 30 dní. Aby databáze mohla být zařazena mezi doporučené fondu elastické databáze musí existovat alespoň 7 dní. Databáze, které již v elastickém fondu jsou, se nepovažují za kandidáty pro doporučení elastického fondu.
>

Služba vyhodnocuje potřebné prostředky a cenovou výhodnost přesunu jednotlivých databází v každé úrovni služby do fondu ve stejné úrovni. Například pro všechny databáze na serveru, které jsou v úrovni Standard, se zvažuje výhodnost jejich přesunu do elastického fondu také s úrovní Standard. To znamená, že služba nikdy nenavrhne přesun databáze mezi úrovněmi, například přesun databáze s úrovní Standard do fondu s úrovní Premium.

Po přidání databází do fondu, doporučení se dynamicky vygeneruje, na základě historie využití databází, které jste vybrali. Tato doporučení jsou zobrazeny v eDTU a GB využití graf a v hlavičce doporučení v horní části **konfigurace fondu** stránky. Tato doporučení jsou určeny k usnadnění vytváření fondu elastické databáze optimalizované pro vaše konkrétní databáze.

![dynamická doporučení](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

### <a name="manage-and-monitor-an-elastic-pool"></a>Správa a sledování fondu elastické databáze

Na portálu Azure můžete sledovat využití fondu elastické databáze a databáze v tomto fondu. Můžete také nastavit sadu změn do pružného fondu a odeslat všechny změny ve stejnou dobu. Tyto změny zahrnují přidávání nebo odebírání databáze, změna nastavení služby elastického fondu nebo změna nastavení databáze.

Následující obrázek znázorňuje příklad elastickém fondu. Zobrazení zahrnuje:

* Grafy pro sledování využití prostředků elastický fond a databází obsažené ve fondu.
* **Konfigurace** tlačítka fondu změny do elastického fondu.
* **Vytvořit databázi** tlačítko, které vytvoří databázi a přidává ji k aktuální elastického fondu.
* Elastické úlohy, které vám pomůžou spravovat velké počty databází pomocí spouštění skriptů Transact SQL pro všechny databáze v seznamu.

![Zobrazení fondu](./media/sql-database-elastic-pool-manage-portal/basic.png)

Můžete přejít na konkrétní fond zobrazíte jeho využití prostředků. Ve výchozím nastavení je fond konfigurována pro zobrazení využití úložiště a eDTU za poslední hodinu. Graf lze konfigurovat zobrazíte jiné metriky v různých časových oken. Klikněte **využití prostředků** grafu v části **elastického fondu monitorování** zobrazíte podrobné zobrazení zadaný metrik přes zadané časové okno.

![Sledování elastického fondu](./media/sql-database-elastic-pool-manage-portal/basic-2.png)

![Metriky stránky](./media/sql-database-elastic-pool-manage-portal/metric.png)

### <a name="to-customize-the-chart-display"></a>Chcete-li přizpůsobit zobrazení grafu

Můžete upravit graf a metriky stránky zobrazíte další metriky jako procento, procento vstupů/výstupů dat a protokolu vstupně-výstupní operace % využití procesoru.

![Klikněte na tlačítko Upravit](./media/sql-database-elastic-pool-manage-portal/edit-metric.png)

Na **upravit graf** formuláře, můžete vybrat časové rozmezí (po hodině, ještě dnes, nebo za minulý týden), nebo klikněte na tlačítko **vlastní** a vybrat libovolnou oblast datum v poslední dva týdny. Můžete zvolit pás nebo spojnicový graf a pak vyberte zdroje, které chcete monitorovat.

> [!Note]
> Pouze metriky se stejnou jednotkou míry lze zobrazit v grafu ve stejnou dobu. Například pokud zvolíte možnost "eDTU procento" pak můžete zvolit pouze další metriky s procentem jako měrné jednotky.
>

![Klikněte na tlačítko Upravit](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

### <a name="manage-and-monitor-databases-in-an-elastic-pool"></a>Spravovat a monitorovat databází v elastickém fondu

Jednotlivé databáze je možné monitorovat také pro potenciální problémy. V části **elastické databáze monitorování**, je graf, který zobrazí metriky pro pět databáze. Ve výchozím nastavení grafu zobrazí top 5 databáze ve fondu podle využití eDTU průměrná za poslední hodinu. 

![Sledování elastického fondu](./media/sql-database-elastic-pool-manage-portal/basic-3.png)

Klikněte **využití eDTU pro databáze za poslední hodinu** pod **elastické databáze monitorování**. Tím se otevře **využití prostředků databáze** a poskytuje podrobný přehled o využití databáze ve fondu. Pomocí mřížky v dolní části stránky, můžete vybrat všechny databáze ve fondu se mají zobrazit jeho použití v grafu (až 5 databáze). Můžete taky přizpůsobit okno metriky a času zobrazené v grafu kliknutím **upravit graf**.

![Stránka využití prostředků databáze](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-view"></a>Chcete-li přizpůsobit zobrazení

Můžete upravit graf vyberte časový interval (za hodinu nebo za posledních 24 hodin), nebo kliknutím na tlačítko **vlastní** chcete vybrat jiný den v posledních 2 týdny k zobrazení.

![Klikněte na tlačítko Upravit graf](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

![Klikněte na tlačítko Vlastní](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

Můžete také kliknutím **porovnat databází tak, že** rozevírací seznam a vyberte jiné metriky pro použití při porovnávání databáze.

![Upravit graf](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### <a name="to-select-databases-to-monitor"></a>Chcete-li vybrat databáze k monitorování

V seznamu databáze na **využití prostředků databáze** stránky, můžete vyhledat konkrétní databáze tak, že vyhledá prostřednictvím stránky v seznamu nebo zadáním názvu databáze. Vyberte databázi, použijte zaškrtávací políčko.

![Vyhledejte databáze k monitorování](./media/sql-database-elastic-pool-manage-portal/select-dbs.png)


### <a name="add-an-alert-to-an-elastic-pool-resource"></a>Přidání oznámení na prostředek elastického fondu

Pravidla můžete přidat do pružného fondu, který odeslání e-mailu na osoby nebo výstrahu řetězce adresy URL koncových bodů, pokud se elastický fond dotkne prahovou hodnotu využití, které jste nastavili.

**Postup přidání upozornění k jakémukoli prostředku:**

1. Klikněte na tlačítko **využití prostředků** graf a otevřete **metrika** klikněte na tlačítko **přidat upozornění**a pak zadejte informace do **přidání pravidla výstrahy** stránky (**prostředků** je automaticky nastavena si být fondu pracujete s).
2. Zadejte **název** a **popis** identifikující výstrahy pro vás i příjemce.
3. Vyberte **metrika** , kterou chcete výstrahu ze seznamu.

   Graf zobrazuje dynamicky využití prostředků pro tuto metriku, které vám pomohou zvolit prahovou hodnotu.

4. Vyberte **podmínku** (větší než menší než atd) a **prahová hodnota**.
5. Vyberte **období** času, který metriky pravidlo je nutné splnit před výstrahy aktivačních událostí.
6. Klikněte na **OK**.

Další informace najdete v tématu [vytvářet výstrahy, SQL Database na portálu Azure](sql-database-insights-alerts-portal.md).

### <a name="move-a-database-into-an-elastic-pool"></a>Přesun databáze do pružného fondu

Můžete přidat nebo odebrat databáze z existující fond. Databáze může být v jiných fondech. Ale můžete přidat pouze databáze, které jsou na stejného logického serveru.

 ![Klikněte na tlačítko Konfigurovat fond](./media/sql-database-elastic-pool-manage-portal/configure-pool.png)

![Klikněte na tlačítko Přidat do fondu](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

![Vyberte databáze, které chcete přidat.](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

![Přidání čekající fondu](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

![Kliknutí na Uložit](./media/sql-database-elastic-pool-manage-portal/click-save.png)

### <a name="move-a-database-out-of-an-elastic-pool"></a>Přesunutí databáze z fondu elastické databáze

![výpis databáze](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

### <a name="change-performance-settings-of-an-elastic-pool"></a>Změňte nastavení výkonu elastického fondu

Při sledování využití prostředků elastického fondu, může se stát, že některé změny jsou potřeba. Možná fondu musí změnu omezení výkon nebo úložiště. Pravděpodobně budete chtít změnit nastavení databáze ve fondu. Můžete změnit nastavení fondu kdykoli získat nejlepší kompromis výkonu a nákladů. V tématu [při fondu elastické databáze slouží?](sql-database-elastic-pool.md) Další informace.

Chcete-li změnit omezení Edtu nebo úložiště na každý fond a Edtu na databázi:

![Využití elastického fondu prostředků](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

## <a name="manage-elastic-pools-and-databases-using-powershell"></a>Správa elastické fondy a databáze pomocí prostředí PowerShell

Pokud chcete vytvořit a spravovat fondy elastické databáze SQL Azure PowerShell, použijte následující rutiny prostředí PowerShell. Pokud je potřeba nainstalovat nebo upgradovat prostředí PowerShell najdete v tématu [modul nainstalovat Azure PowerShell](/powershell/azure/install-azurerm-ps). Chcete-li vytvořit a spravovat databáze, servery a pravidla brány firewall, přečtěte si téma [vytvořit a spravovat servery Azure SQL Database a databáze pomocí prostředí PowerShell](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-powershell). 

> [!TIP]
> Příklad skriptů prostředí PowerShell, najdete v části [vytvořit elastické fondy a přesunutí databází mezi fondy a z fondu pomocí prostředí PowerShell](scripts/sql-database-move-database-between-pools-powershell.md) a [použijte PowerShell ke sledování a škálování elastický fond SQL v Azure SQL Database](scripts/sql-database-monitor-and-scale-pool-powershell.md).
>

| Rutina | Popis |
| --- | --- |
|[New-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/new-azurermsqlelasticpool)|Vytvoří fond elastické databáze na logický SQL server.|
|[Get-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/get-azurermsqlelasticpool)|Získá elastické fondy a jejich hodnoty vlastností na logický SQL server.|
|[Set-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/set-azurermsqlelasticpool)|Upraví vlastnosti fondu elastické databáze na logický SQL server. Například použít **StorageMB** vlastnost změnit maximální úložiště elastického fondu.|
|[Remove-AzureRmSqlElasticPool](/powershell/module/azurerm.sql/remove-azurermsqlelasticpool)|Odstraní fond elastické databáze na logický SQL server.|
|[Get-AzureRmSqlElasticPoolActivity](/powershell/module/azurerm.sql/get-azurermsqlelasticpoolactivity)|Získá stav operace v elastickém fondu na logický SQL server.|
|[New-AzureRmSqlDatabase](/powershell/module/azurerm.sql/new-azurermsqldatabase)|Vytvoří novou databázi v existující fond nebo jedné databáze. |
|[Get-AzureRmSqlDatabase](/powershell/module/azurerm.sql/get-azurermsqldatabase)|Získá jednu nebo více databází.|
|[Set-AzureRmSqlDatabase](/powershell/module/azurerm.sql/set-azurermsqldatabase)|Nastaví vlastnosti pro databázi nebo existující databázi se přesune do, mimo nebo mezi elastické fondy.|
|[Remove-AzureRmSqlDatabase](/powershell/module/azurerm.sql/remove-azurermsqldatabase)|Odebere databáze.|


> [!TIP]
> Vytváření mnoho databází v elastickém fondu může trvat dobu, pokud se provádí pomocí portálu nebo rutiny prostředí PowerShell, který současně vytvořit pouze jednu databázi. K automatizaci vytváření fondu elastické databáze, najdete v části [CreateOrUpdateElasticPoolAndPopulate](https://gist.github.com/billgib/d80c7687b17355d3c2ec8042323819ae).
>

## <a name="manage-elastic-pools-and-databases-using-the-azure-cli"></a>Správa elastické fondy a databází pomocí rozhraní příkazového řádku Azure

K vytváření a správě fondů elastické databáze SQL se [rozhraní příkazového řádku Azure](/cli/azure), použijte následující [databáze SQL Azure CLI](/cli/azure/sql/db) příkazy. Rozhraní příkazového řádku můžete spustit v prohlížeči pomocí [Cloud Shellu](/azure/cloud-shell/overview) nebo [nainstalovat](/cli/azure/install-azure-cli) v systémech macOS, Linux nebo Windows. 

> [!TIP]
> Příklad skriptů příkazového řádku Azure CLI, najdete v části [použití rozhraní příkazového řádku přesouvat Azure SQL database v elastický fond SQL](scripts/sql-database-move-database-between-pools-cli.md) a [použití Azure CLI škálování elastický fond SQL v Azure SQL Database](scripts/sql-database-scale-pool-cli.md).
>

| Rutina | Popis |
| --- | --- |
|[Vytvoření az sql elastického fondu](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_create)|Vytvoří fondu elastické databáze.|
|[seznam elastického fondu sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list)|Vrátí seznam hodnot elastické fondy na serveru.|
|[seznam – databáze az sql elastického fondu](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_dbs)|Vrátí seznam databází v elastickém fondu.|
|[seznam edicí az sql elastického fondu](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_list_editions)|Také zahrnuje nastavení DTU fondu k dispozici, limity úložiště a pro nastavení databáze. Chcete-li snížit podrobností, limity další úložiště a na databázi nastavení jsou skryté. ve výchozím nastavení.|
|[aktualizace elastického fondu sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_update)|Aktualizace fondu elastické databáze.|
|[odstranění elastického fondu sql az](/cli/azure/sql/elastic-pool#az_sql_elastic_pool_delete)|Odstraní elastický fond.|

## <a name="manage-databases-within-elastic-pools-using-transact-sql"></a>Správa databází v rámci elastické fondy pomocí jazyka Transact-SQL

Vytvoření a přesunutí databází v rámci existující elastické fondy nebo k vrácení informací o elastického fondu SQL Database pomocí jazyka Transact-SQL, pomocí následujících příkazů T-SQL. Můžete použít tyto příkazy pomocí portálu Azure [SQL Server Management Studio](/sql/ssms/use-sql-server-management-studio), [Visual Studio Code](https://code.visualstudio.com/docs), nebo jiný program, který se může připojit k serveru Azure SQL Database a předat Transact-SQL příkazy. Chcete-li vytvořit a spravovat databáze, servery a pravidla brány firewall, přečtěte si téma [vytvořit a spravovat servery Azure SQL Database a databází pomocí jazyka Transact-SQL](sql-database-servers-databases.md#manage-azure-sql-servers-databases-and-firewalls-using-transact-sql).

> [!IMPORTANT]
> Nelze vytvořit, aktualizovat nebo odstranit fondu elastické databáze Azure SQL Database pomocí jazyka Transact-SQL. Můžete přidat nebo odebrat z fondu elastické databáze a zobrazení dynamické správy můžete použít k vrácení informací o existující elastické fondy.
>

| Příkaz | Popis |
| --- | --- |
|[Vytvoření databáze (Azure SQL Database)](/sql/t-sql/statements/create-database-azure-sql-database)|Vytvoří novou databázi v existující fond nebo jedné databáze. Musíte být připojení k hlavní databázi a vytvořte novou databázi.|
| [Příkaz ALTER DATABASE (databáze Azure SQL)](/sql/t-sql/statements/alter-database-azure-sql-database) |Přesun databáze do, mimo nebo mezi elastické fondy.|
|[DROP DATABASE (Transact-SQL)](/sql/t-sql/statements/drop-database-transact-sql)|Odstraní databázi.|
|[sys.elastic_pool_resource_stats (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-elastic-pool-resource-stats-azure-sql-database)|Vrátí statistiku využití prostředků pro všechny fondy elastické databáze v logický server. Pro každý fond elastické databáze je jeden řádek pro každou 15 sekundu reporting okno (čtyři řádky za minutu). To zahrnuje využití procesoru, vstupně-výstupní operace, protokolu, spotřebu úložiště a souběžných požadavku nebo relace využití všech databází ve fondu.|
|[sys.database_service_objectives (Azure SQL Database)](/sql/relational-databases/system-catalog-views/sys-database-service-objectives-azure-sql-database)|Vrátí edition (vrstva služby), cíl služby (cenové úrovně) a název elastického fondu, pokud existuje, pro databázi Azure SQL nebo Azure SQL Data Warehouse. Pokud přihlášení k hlavní databázi serveru Azure SQL Database, vrátí informace na všechny databáze. Pro Azure SQL Data Warehouse musí být připojen k hlavní databázi.|

## <a name="manage-elastic-pools-and-databases-using-the-rest-api"></a>Správa elastické fondy a databází pomocí rozhraní REST API

Vytvoření a správa Elastická databáze SQL pomocí fondy tyto požadavky REST API.

| Příkaz | Popis |
| --- | --- |
|[Elastické fondy - vytvořit nebo aktualizovat](/rest/api/sql/elasticpools/createorupdate)|Vytvoří nový elastický fond nebo aktualizuje existující elastickém fondu.|
|[Elastické fondy - odstranění](/rest/api/sql/elasticpools/delete)|Odstraní elastický fond.|
|[Elastické fondy - Get](/rest/api/sql/elasticpools/get)|Získá fondu elastické databáze.|
|[Elastické fondy - seznamu serverem](/rest/api/sql/elasticpools/listbyserver)|Vrátí seznam hodnot elastické fondy na serveru.|
|[Elastické fondy - aktualizace](/rest/api/sql/elasticpools/update)|Aktualizace existujícího elastického fondu.|
|[Počet doporučených fondů elastické - Get](/rest/api/sql/recommendedelasticpools/get)|Získá recommented elastického fondu.|
|[Počet doporučených fondů elastické - seznamu serverem](/rest/api/sql/recommendedelasticpools/listbyserver)|Vrátí počet doporučených fondů elastické.|
|[Počet doporučených fondů elastické - seznamu metriky](/rest/api/sql/recommendedelasticpools/listmetrics)|Vrátí recommented metriky elastického fondu.|
|[Elastický fond aktivity](/rest/api/sql/elasticpoolactivities)|Vrátí aktivity elastického fondu.|
|[Fond elastické databáze aktivity](/rest/api/sql/elasticpooldatabaseactivities)|Vrátí aktivita u databází v elastickém fondu.|
|[Databáze - vytvořit nebo aktualizovat](/rest/api/sql/databases/createorupdate)|Vytvoří novou databázi nebo aktualizuje existující databázi.|
|[Databáze - Get](/rest/api/sql/databases/get)|Získá databáze.|
|[Databáze - získat elastického fondu](/rest/api/sql/databases/getbyelasticpool)|Získá databáze v elastickém fondu.|
|[Získat doporučený fond Elastických databází –](/rest/api/sql/databases/getbyrecommendedelasticpool)|Získá databázi uvnitř recommented elastického fondu.|
|[Databáze – seznam podle elastického fondu](/rest/api/sql/databases/listbyelasticpool)|Vrátí seznam databází v elastickém fondu.|
|[Databáze – seznam podle doporučených elastického fondu](/rest/api/sql/databases/listbyrecommendedelasticpool)|Vrátí seznam databází uvnitř recommented elastického fondu.|
|[Databáze - seznamu serverem](/rest/api/sql/databases/listbyserver)|Vrátí seznam databází na serveru.|
|[Databáze - aktualizace](/rest/api/sql/databases/update)|Aktualizuje existující databázi.|

## <a name="next-steps"></a>Další postup

* Video najdete v tématu [Microsoft Virtual Academy video kurzu na možnostech elastické databáze SQL Azure](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
* Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
* SaaS pomocí elastické fondy, na adrese [Úvod k aplikaci Wingtip SaaS](sql-database-wtp-overview.md).
