---
title: Správa více databází s elastickými fondy
description: Spravujte a škálujte více databází SQL – stovky a tisíce – pomocí elastických fondů. Jedna cena za prostředky, které můžete distribuovat v případě potřeby.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, carlrab
ms.date: 08/06/2019
ms.openlocfilehash: 3c476393153f6bc1d18d5c163bcd69484583eb15
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256273"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastické fondy umožňují spravovat a škálovat více databází Azure SQL

Elastické fondy SQL Database je jednoduché a cenově výhodné řešení pro správu a škálování více databází s proměnlivými a nepředvídatelnými požadavky na využití. Databáze v elastickém fondu se nachází na jednom serveru Azure SQL Database a sdílí stanovený počet prostředků za stanovenou cenu. Elastické fondy v Azure SQL Database umožňují vývojářům SaaS optimalizovat poměr cena/výkon pro skupinu databází v rámci předem daného rozpočtu a pro všechny databáze přitom zajistit elasticitu výkonu.

## <a name="what-are-sql-elastic-pools"></a>Co jsou elastické fondy SQL

Vývojáři SaaS sestavují aplikace nad datovými úrovněmi velkého rozsahu, které se skládají z několika databází. Běžným aplikačním postupem je zřídit pro každého zákazníka izolovanou databázi. Různí zákazníci však často mají různé a nepředvídatelné vzorce využití a je obtížné předpovědět požadavky na prostředky každého jednotlivého uživatele databáze. Tradičně jste měli dvě možnosti:

- Prostředky nadměrného poskytování na základě využití ve špičce a nad mzdy, nebo
- Nedostatečné poskytování pro úsporu nákladů na úkor výkonu a spokojenosti zákazníků během špiček.

Elastické fondy vyřešit tento problém tím, že zajistí, že databáze získat prostředky výkonu, které potřebují, když je potřebují. Poskytují jednoduchý mechanismus přidělování prostředků v mezích předvídatelného rozpočtu. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Neexistuje žádný poplatek za databázi pro elastické fondy. Fakturuje se vám za každou hodinu, kdy fond existuje na nejvyšší hodnotě eDTU nebo virtuálních jader, bez ohledu na využití nebo na to, zda byl fond aktivní méně než hodinu.

Elastické fondy umožňují vývojáři nakupovat prostředky pro fond sdílený více databázemi tak, aby vyhovovaly nepředvídatelným obdobím použití jednotlivými databázemi. Prostředky fondu můžete nakonfigurovat na základě [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md) nebo [nákupního modelu založeného na virtuálních jádrech](sql-database-service-tiers-vcore.md). Požadavek na prostředky pro fond je určen agregací využití jeho databází. Množství prostředků, které jsou k dispozici pro fond, je řízeno rozpočtem vývojáře. Vývojář jednoduše přidá databáze do fondu, nastaví minimální a maximální prostředky pro databáze (minimální a maximální DTU nebo minimální nebo maximální virtuální jádra v závislosti na vašem výběru modelu zdrojů) a pak nastaví prostředky fondu na základě jejich Rozpočtu. Vývojáři mohou fondy využít k tomu, aby zajistili elegantní růst svých služeb od úsporného startupu až po zralé podnikání, a to ve stále se zvětšujícím měřítku.

V rámci fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci stanovených parametrů. Při velkém zatížení může databáze spotřebovat více prostředků k uspokojení poptávky. Databáze při snímcích s potřebou méně a databáze bez zatížení nespotřebovávají žádné prostředky. Zřizováním prostředků pro celý fond, a nikoli pro jednotlivé databáze, se úkoly správy zjednodušují. Navíc máte předvídatelný rozpočet pro bazén. Další prostředky lze přidat do existujícího fondu bez prostojů databáze, s tím rozdílem, že databáze může být nutné přesunout poskytnout další výpočetní prostředky pro novou rezervaci eDTU. Podobně pokud další prostředky již nejsou potřeba, mohou být odebrány z existujícího fondu v libovolném okamžiku. Navíc můžete databáze do fondu přidávat nebo je z něj odebírat. Pokud databáze podle předpokladu nedostatečně využívá prostředky, odeberte ji.

> [!NOTE]
> Při přesouvání databází do nebo z elastického fondu, neexistuje žádné výpadky s výjimkou krátké časové období (v pořadí sekund) na konci operace při vynechání připojení databáze.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kdy byste měli zvážit elastický fond databáze SQL

Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití.

Čím více databází je možné do fondu přidat, tím větší budou vaše úspory. V závislosti na způsobu využití aplikace je možné zobrazit úspory s tak málo jako dvě databáze S3.

Následující části vás seznámí s postupy, pomocí kterých můžete vyhodnotit, jestli pro vaši konkrétní kolekci databází bude použití fondu přínosné. V příkladech se používají fondy Standard, ale stejné principy platí také pro fondy Basic a Premium.

### <a name="assessing-database-utilization-patterns"></a>Vyhodnocení vzorů využití databáze

Na následujícím obrázku je příklad databáze, která je většinu doby nečinná, ale má také pravidelné špičky aktivity. Tento vzor využití se pro fond skvěle hodí:

   ![izolovaná databáze vhodná pro fond](./media/sql-database-elastic-pool/one-database.png)

Po uvedená pětiminutová období DB1 využívá až 90 jednotek DTU, ale celkové průměrné využití nedosahuje ani pěti jednotek DTU. Výpočetní velikost S3 je vyžadována ke spuštění tohoto zatížení v jedné databázi, ale to ponechává většinu prostředků nevyužitých během období nízké aktivity.

Fond umožňuje sdílet tyto nevyužité jednotky DTU napříč několika databázemi a snižuje tak počet potřebných jednotek DTU a celkové náklady.

Využijeme předchozí příklad a budeme předpokládat, že existují další databáze s podobnými vzory využití jako DB1. V následujících dvou obrázcích níže je využití čtyř databází a 20 databází vrstveno do stejného grafu, aby ilustrovalo nepřekrývající se povahu jejich využití v průběhu času pomocí nákupního modelu založeného na DTU:

   ![Čtyři databáze se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/four-databases.png)

   ![Dvacet databází se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/twenty-databases.png)

Agregované využití DTU napříč všemi 20 databázemi je na předchozím obrázku znázorněné černou čárou. Ukazuje se, že agregované využití DTU nikdy nepřekračuje 100 jednotek DTU. Znamená to, že těchto 20 databází může v průběhu tohoto časového období sdílet 100 jednotek eDTU. To má za následek 20x snížení dtu a 13x snížení ceny ve srovnání s umístěním každé z databází ve výpočetních velikostech S3 pro jednotlivé databáze.

Tento příklad je ideální z následujících důvodů:

- Ukazuje velké rozdíly mezi využitím ve špičce a průměrným využitím jednotlivých databází.
- Špičky využití pro jednotlivé databáze nastávají v různých časových okamžicích.
- Jednotky eDTU jsou sdílené mezi mnoha databázemi.

Cena za fond závisí na jednotkách eDTU fondu. Přestože je cena ze jednotku eDTU pro fond 1,5krát vyšší než cena za jednotku DTU pro izolovanou databázi, **jednotky eDTU fondu může sdílet velký počet databází, a proto stačí menší celkový počet jednotek eDTU**. Tyto rozdíly v cenách a sdílení jednotek eDTU jsou základem potenciálních úspor, které fondy mohou nabídnout.

Následující pravidla palce týkající se počtu databází a využití databáze pomáhají zajistit, že fond poskytuje nižší náklady ve srovnání s použitím výpočetnívelikosti pro jednotlivé databáze.

### <a name="minimum-number-of-databases"></a>Maximální počet databází

Pokud agregované množství prostředků pro jednotlivé databáze je více než 1,5x prostředky potřebné pro fond, pak elastický fond je nákladově efektivnější.

***Příklad nákupního modelu založeného na DTU***<br>
Alespoň dvě databáze S3 nebo alespoň 15 S0 databáze jsou potřebné pro 100 eDTU fondu nákladově efektivnější než pomocí výpočetní velikosti pro jednotlivé databáze.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximální počet databází se souběžnými špičkami

Sdílením prostředků nelze všechny databáze ve fondu současně používat prostředky až do limitu, který je k dispozici pro jednotlivé databáze. Čím méně databází, které současně vrchol, tím nižší prostředky fondu lze nastavit a nákladově efektivnější fondu se stane. Obecně platí, že ne více než 2/3 (nebo 67%) databází ve fondu by měly současně dosáhnout vrcholu svého limitu zdrojů.

***Příklad nákupního modelu založeného na DTU***

Aby bylo možné snížit náklady pro tři databáze S3 ve fondu s 200 jednotkami eDTU, mohou nejvýše dvě z těchto databází dosahovat špičky svého využití současně. Pokud současně dosahují špičky více než dvě z těchto čtyř databází S3, bylo by nutné velikost fondu nastavit na více než 200 jednotek eDTU. Pokud je velikost fondu velikost více než 200 eDTU, další databáze S3 by bylo nutné přidat do fondu zachovat nižší náklady než výpočetní velikosti pro jednotlivé databáze.

Všimněte si, že tento příklad nebere v úvahu využití jiných databází ve fondu. Pokud se v libovolném konkrétním časovém okamžiku do určité míry využívají všechny databáze, může méně než 2/3 (nebo 67 %) z nich dosahovat špičky současně.

### <a name="resource-utilization-per-database"></a>Využití prostředků na databázi

Velký rozdíl mezi maximálním a průměrným využitím databáze ukazuje na delší doby nízkého využití a krátká období vysokého využití. Tento vzor využití je ideální pro sdílení prostředků mezi databázemi. Použití fondu pro databázi byste měli zvážit, pokud je její využití ve špičce přibližně 1,5krát větší než průměrné využití.

**Příklad nákupního modelu založeného na DTU:** Databáze S3, která vrcholí na 100 DTU a v průměru používá 67 DTU nebo méně, je dobrým kandidátem pro sdílení eDTU ve fondu. Databáze S1, která ve špičce využívá 20 DTU a průměrně využívá 13 DTU nebo méně, je vhodným kandidátem pro fond.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Jak si vybrat správnou velikost fondu

Nejlepší velikost fondu závisí na agregační prostředky potřebné pro všechny databáze ve fondu. To zahrnuje určení následující:

- Maximální prostředky využité všemi databázemi ve fondu (maximální DTU nebo maximální virtuální jádra v závislosti na vašem modelu zdrojů).
- Maximální počet bajtů úložiště využitých všemi databázemi ve fondu

Dostupné úrovně služeb pro každý model prostředků najdete v [nákupním modelu založeném na DTU](sql-database-service-tiers-dtu.md) nebo [nákupním modelu založeném na virtuálních jádrech](sql-database-service-tiers-vcore.md).

V případech, kdy nejde používat nástroje, vám při odhadování, jestli je fond cenově výhodnější než izolované databáze, pomůže následující postup:

1. Odhadněte eDTU nebo virtuální jádra potřebné pro fond takto:

   Pro nákupní model založený na DTU: MAX(<*Celkový počet průměrných* *využití DTU* na DB>,<br>  
   <*počet databází se souběžnou špičkou* X *využití DTU ve špičce na databázi*)

   Pro nákupní model založený na virtuálních jádrech: MAX(<Celkový počet *průměrných využití virtuálních jader* *DBs* X na db>,<br>  
   <*Počet souběžně vrcholících disbů* *Využití virtuálních jader x ve špičce na DB*)

2. Odhadněte potřebnou velikost úložiště pro fond (sečtěte počet bajtů potřebných pro všechny databáze ve fondu). Potom určete velikost fondu v jednotkách eDTU, která toto úložiště poskytuje.
3. U nákupního modelu založeného na DTU pořiďte větší odhady eDTU z kroku 1 a kroku 2. Pro nákupní model založený na virtuálních jádrech, vzít odhad virtuálních jader z kroku 1.
4. Podívejte se na [stránku s cenami databáze SQL](https://azure.microsoft.com/pricing/details/sql-database/) a najděte nejmenší velikost fondu, která je větší než odhad z kroku 3.
5. Porovnejte cenu fondu z kroku 5 na cenu pomocí příslušné výpočetní velikosti pro jednotlivé databáze.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Použití dalších funkcí databáze SQL s elastické fondy

### <a name="elastic-jobs-and-elastic-pools"></a>Elastické úlohy a elastické bazény

U fondu jsou úlohy správy zjednodušené díky spouštění skriptů v **[elastických úlohách](elastic-jobs-overview.md)**. Elastická úloha eliminuje většinu únavných úkolů spojených s velkým počtem databází.

Další informace o ostatních databázových nástrojích pro práci s více databázemi najdete v tématu [Horizontální navýšení kapacity se službou Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Možnosti kontinuity provozu pro databáze v elastickém fondu

Databáze ve fondu obecně podporují stejné [funkce provozní kontinuity](sql-database-business-continuity.md), jaké jsou dostupné pro izolované databáze.

- **Obnovení k určitému bodu v čase**

  Obnovení v době, kdy používá automatické zálohování databáze k obnovení databáze ve fondu do určitého bodu v čase. Viz [Obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Geografické obnovení**

  Geografické obnovení poskytuje výchozí možnost obnovení, pokud databáze není k dispozici z důvodu incidentu v oblasti, kde je databáze hostována. Viz [Obnovení databáze Azure SQL nebo převzetí služeb při selhání na sekundární](sql-database-disaster-recovery.md)

- **Aktivní geografická replikace**

  Pro aplikace, které mají agresivnější požadavky na obnovení, než může nabídnout geografické obnovení, nakonfigurujte [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [skupinu automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Vytvoření nového elastického fondu databáze SQL pomocí portálu Azure

Existují dva způsoby, jak můžete vytvořit elastický fond na webu Azure Portal.

1. Přejděte na [portál Azure](https://portal.azure.com) a vytvořte elastický fond. Vyhledejte a vyberte **Azure SQL**.
2. Výběrem **možnosti +Přidat** otevřete stránku **možnosti Vybrat nasazení SQL.** Další informace o elastických fondech můžete zobrazit výběrem **možnosti Zobrazit podrobnosti** na **dlaždici Databáze.**
3. Na dlaždici **Databáze** vyberte **elastický fond** v rozevíracím seznamu **Typ prostředku** a pak vyberte **Vytvořit**:

   ![Vytvoření elastického fondu](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Nebo můžete vytvořit elastický fond přechodem na existující server Azure SQL a kliknutím na **+ Nový fond** a vytvořit fond přímo na tento server.

> [!NOTE]
> Na serveru můžete vytvořit více fondů, ale do stejného fondu nelze přidat databáze z různých serverů.

Úroveň služeb fondu určuje funkce, které jsou k dispozici pro elastické prvky ve fondu a maximální množství prostředků, které jsou k dispozici pro každou databázi. Podrobnosti naleznete v tématu Omezení prostředků pro elastické fondy v [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Omezení prostředků založených na virtuálních jádrech pro elastické fondy najdete v [tématu omezení prostředků založených na virtuálních jádrech – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Chcete-li nakonfigurovat prostředky a ceny fondu, klepněte na tlačítko **Konfigurovat fond**. Pak vyberte úroveň služby, přidejte databáze do fondu a nakonfigurujte omezení prostředků pro fond a jeho databáze.

Po dokončení konfigurace fondu můžete kliknout na tlačítko Použít, pojmenovat fond a vytvořit fond kliknutím na tlačítko OK.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Sledování elastického fondu a jeho databází

Na webu Azure Portal můžete sledovat využití elastického fondu a databází v rámci tohoto fondu. Můžete také provést sadu změn v elastickém fondu a odeslat všechny změny současně. Mezi tyto změny patří přidání nebo odebrání databází, změna nastavení elastického fondu nebo změna nastavení databáze.

Chcete-li začít sledovat elastický fond, najděte a otevřete elastický fond na portálu. Nejprve se zobrazí obrazovka, která poskytuje přehled o stavu elastického fondu. To zahrnuje:

- Grafy monitorování zobrazující využití prostředků elastického fondu
- Nedávná upozornění a doporučení, jsou-li k dispozici, pro elastický fond

Následující obrázek znázorňuje příklad elastického fondu:

![Zobrazení bazénu](./media/sql-database-elastic-pool-manage-portal/basic.png)

Chcete-li získat další informace o fondu, můžete kliknout na některou z dostupných informací v tomto přehledu. Kliknutím na graf **využití prostředků** se dostanete do zobrazení Azure Monitoring, kde můžete přizpůsobit metriky a časové okno zobrazené v grafu. Kliknutím na všechna dostupná oznámení se dostanete do okna, které zobrazuje úplné podrobnosti o tomto upozornění nebo doporučení.

Pokud chcete sledovat databáze uvnitř fondu, můžete kliknout na **využití prostředků databáze** v části **Monitorování** nabídky prostředků na levé straně.

![Stránka využití databázových prostředků](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Přizpůsobení zobrazení grafu

Graf a stránku metriky můžete upravit tak, aby zobrazovalo další metriky, jako je procento procesoru, procento vod vo a procento vo protokolu.

Ve formuláři **Upravit graf** můžete vybrat pevný časový rozsah nebo klepnutím na **vlastní** vyberte libovolné 24hodinové okno za poslední dva týdny a pak vybrat zdroje, které chcete sledovat.

### <a name="to-select-databases-to-monitor"></a>Výběr databází ke sledování

Ve výchozím nastavení se v grafu v okně **Využití prostředků databáze** zobrazí prvních 5 databází podle DTU nebo CPU (v závislosti na vaší úrovni služby). Databáze v tomto grafu můžete přepnout výběrem a zrušením výběru databází ze seznamu pod grafem pomocí zaškrtávacích políček vlevo.

Můžete také vybrat další metriky pro zobrazení vedle sebe v této databázové tabulce a získat tak úplnější zobrazení výkonu databází.

Další informace najdete v tématu [vytváření výstrah databáze SQL na webu Azure Portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Zákaznické případové studie

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart používal elastické fondy s Azure SQL Database k rychlému rozšíření svých obchodních služeb rychlostí 1 000 nových databází Azure SQL za měsíc.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco používá elastické fondy s Azure SQL Database k rychlému zřizování a škálování služeb pro tisíce klientů v cloudu.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI používá elastické fondy s Azure SQL Database k urychlení svého vývojového cyklu a ke zvýšení svých zákaznických služeb a výkonu.   

## <a name="next-steps"></a>Další kroky

- Informace o cenách najdete v [tématu Ceny elastického fondu](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Chcete-li škálovat elastické fondy, přečtěte si informace [o změně velikosti elastických fondů](sql-database-elastic-pool-scale.md) a [škálování elastického fondu – ukázkový kód](scripts/sql-database-monitor-and-scale-pool-powershell.md)
- Video najdete v článku [Video kurz Microsoft Virtual Academy o elastických funkcích Azure SQL Database.](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Kurz SaaS pomocí elastických fondů najdete [v tématu Úvod do aplikace Wingtip SaaS](sql-database-wtp-overview.md).
- Další informace o správě prostředků v elastických fondech s mnoha databázemi naleznete [v tématu Správa prostředků v hustých elastických fondech](sql-database-elastic-pool-resource-management.md).
