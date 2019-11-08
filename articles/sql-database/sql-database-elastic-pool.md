---
title: Správa více databází s elastickými fondy
description: Spravujte a škálujte více databází SQL – stovky a tisíce – pomocí elastických fondů. Jedna cena za prostředky, které můžete distribuovat tam, kde je to potřeba.
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
ms.openlocfilehash: 1b3ee75d1b24cab8211337cf1cdbd053327e16b8
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73823834"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-azure-sql-databases"></a>Elastické fondy vám pomůžou se správou a škálováním více databází Azure SQL.

Elastické fondy SQL Database je jednoduché a cenově výhodné řešení pro správu a škálování více databází s proměnlivými a nepředvídatelnými požadavky na využití. Databáze v elastickém fondu jsou na jednom Azure SQL Databaseovém serveru a sdílejí nastavený počet prostředků za stanovenou cenu. Elastické fondy v Azure SQL Database umožňují vývojářům SaaS optimalizovat poměr cena/výkon pro skupinu databází v rámci předem daného rozpočtu a pro všechny databáze přitom zajistit elasticitu výkonu.

## <a name="what-are-sql-elastic-pools"></a>Co jsou elastické fondy SQL

Vývojáři SaaS sestavují aplikace nad datovými úrovněmi velkého rozsahu, které se skládají z několika databází. Běžným aplikačním postupem je zřídit pro každého zákazníka izolovanou databázi. Ale různí zákazníci mají často proměnlivé a nepředvídatelné vzorce využití a je těžké odhadnout požadavky jednotlivých databázových uživatelů na prostředky. Tradičně jste měli dvě možnosti:

- Provisioning prostředků na základě špičkového využití a platby za použití
- Snížení nákladů na náklady na výkon a spokojenost zákazníků během špičky – zřizování

Elastické fondy tento problém řeší tím, že zajistí, že databáze získají prostředky výkonu, které potřebují, když je potřebují. Poskytují jednoduchý mechanismus přidělování prostředků v mezích předvídatelného rozpočtu. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Elastic-databases-helps-SaaS-developers-tame-explosive-growth/player]
>

> [!IMPORTANT]
> Pro elastické fondy se neúčtují žádné poplatky za databázi. Fakturuje se vám každá hodina existence fondu na nejvyšší eDTU nebo virtuální jádra, bez ohledu na využití nebo na to, jestli byl fond aktivní kratší dobu než hodinu.

Elastické fondy umožňují vývojářům koupit prostředky pro fond sdílený více databázemi a zajistit tak nepředvídatelné doby využití jednotlivými databázemi. Prostředky pro fond můžete nakonfigurovat na základě [nákupního modelu založeného na DTU](sql-database-service-tiers-dtu.md) nebo [nákupního modelu založeného na Vcore](sql-database-service-tiers-vcore.md). Požadavek na prostředky pro fond je určen agregovaným využitím jeho databází. Množství prostředků, které jsou k dispozici pro fond, řídí rozpočet pro vývojáře. Vývojář jednoduše do fondu přidá databáze, nastaví minimální a maximální prostředky pro databáze (minimální a maximální DTU nebo minimální nebo maximální virtuální jádra v závislosti na zvoleném modelu Resource) a pak nastaví prostředky fondu na základě jejich souhrnné. Vývojáři mohou fondy využít k tomu, aby zajistili elegantní růst svých služeb od úsporného startupu až po zralé podnikání, a to ve stále se zvětšujícím měřítku.

V rámci fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci stanovených parametrů. V případě velkého zatížení může databáze spotřebovávat více prostředků, aby splnila požadavky. Databáze v rámci světelné zátěže spotřebovávají méně a databáze bez zatížení nevyužívají žádné prostředky. Zřizováním prostředků pro celý fond, a nikoli pro jednotlivé databáze, se úkoly správy zjednodušují. Navíc máte předvídatelný rozpočet pro fond. Do existujícího fondu je možné přidat další prostředky bez výpadku databáze, s výjimkou případů, kdy je nutné databáze přesunout, aby bylo možné poskytnout další výpočetní prostředky pro novou rezervaci eDTU. Podobně platí, že pokud už další prostředky nepotřebujete, můžete je v jakémkoli okamžiku kdykoli odebrat z existujícího fondu. Navíc můžete databáze do fondu přidávat nebo je z něj odebírat. Pokud databáze podle předpokladu nedostatečně využívá prostředky, odeberte ji.

> [!NOTE]
> Při přesunu databází do elastického fondu nebo z něj nedochází k výpadku, s výjimkou krátkého časového období (v řádu sekund) na konci operace při zahození připojení databáze.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kdy byste měli zvážit SQL Database elastický fond

Fondy jsou vhodné pro velký počet databází s konkrétními vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití.

Čím více databází je možné do fondu přidat, tím větší budou vaše úspory. V závislosti na vzorech využití aplikací je možné dosáhnout úspor už u pouhých dvou databází S3.

Následující části vás seznámí s postupy, pomocí kterých můžete vyhodnotit, jestli pro vaši konkrétní kolekci databází bude použití fondu přínosné. V příkladech se používají fondy Standard, ale stejné principy platí také pro fondy Basic a Premium.

### <a name="assessing-database-utilization-patterns"></a>Vyhodnocení vzorů využití databáze

Na následujícím obrázku je příklad databáze, která je většinu doby nečinná, ale má také pravidelné špičky aktivity. Tento vzor využití se pro fond skvěle hodí:

   ![izolovaná databáze vhodná pro fond](./media/sql-database-elastic-pool/one-database.png)

Po uvedená pětiminutová období DB1 využívá až 90 jednotek DTU, ale celkové průměrné využití nedosahuje ani pěti jednotek DTU. Pro spuštění této úlohy v izolovaných databázích se vyžaduje výpočetní velikost S3, ale tato akce zachová většinu prostředků nevyužitých během období nízké aktivity.

Fond umožňuje sdílet tyto nevyužité jednotky DTU napříč několika databázemi a snižuje tak počet potřebných jednotek DTU a celkové náklady.

Využijeme předchozí příklad a budeme předpokládat, že existují další databáze s podobnými vzory využití jako DB1. Na následujících dvou obrázcích jsou využití čtyř databází a 20 databází vrstveny do stejného grafu pro ilustraci překrývající se povahy jejich využití v čase pomocí nákupního modelu založeného na DTU:

   ![Čtyři databáze se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/four-databases.png)

   ![Dvacet databází se vzorem využití, který je vhodný pro fond](./media/sql-database-elastic-pool/twenty-databases.png)

Agregované využití DTU napříč všemi 20 databázemi je na předchozím obrázku znázorněné černou čárou. Ukazuje se, že agregované využití DTU nikdy nepřekračuje 100 jednotek DTU. Znamená to, že těchto 20 databází může v průběhu tohoto časového období sdílet 100 jednotek eDTU. Výsledkem je 20krát snížení DTU a snížení ceny 13krát nižší v porovnání s umístěním jednotlivých databází v výpočetních velikostech S3 pro izolované databáze.

Tento příklad je ideální z následujících důvodů:

- Ukazuje velké rozdíly mezi využitím ve špičce a průměrným využitím jednotlivých databází.
- Špičky využití pro jednotlivé databáze nastávají v různých časových okamžicích.
- Jednotky eDTU jsou sdílené mezi mnoha databázemi.

Cena za fond závisí na jednotkách eDTU fondu. Přestože je cena ze jednotku eDTU pro fond 1,5krát vyšší než cena za jednotku DTU pro izolovanou databázi, **jednotky eDTU fondu může sdílet velký počet databází, a proto stačí menší celkový počet jednotek eDTU**. Tyto rozdíly v cenách a sdílení jednotek eDTU jsou základem potenciálních úspor, které fondy mohou nabídnout.

Následující pravidla, která souvisí s počtem databází a s využitím databáze, zajistí, že fond nabízí snížené náklady v porovnání s používáním výpočetních velikostí pro izolované databáze.

### <a name="minimum-number-of-databases"></a>Maximální počet databází

Pokud je agregovaná velikost prostředků pro izolované databáze větší než 1,5 ×, který je pro fond potřebný, pak je elastický fond cenově výhodnější.

***Příklad nákupního modelu založeného na DTU***<br>
Minimálně dvě databáze S3 nebo nejméně 15 databází S0 je potřeba, aby fond 100 eDTU byl cenově výhodnější než použití výpočetních velikostí pro izolované databáze.

### <a name="maximum-number-of-concurrently-peaking-databases"></a>Maximální počet databází se souběžnými špičkami

Když sdílíte prostředky, ne všechny databáze ve fondu, můžou současně využívat prostředky až do limitu dostupného pro jednotlivé databáze. Čím méně databází má současně špičku, tím nižší je možné nastavení prostředků fondu a tím i cenově úsporného fondu. Obecně platí, že ne více než 2/3 (nebo 67%) databáze ve fondu by se měly současně vymezit omezením jejich prostředků.

***Příklad nákupního modelu založeného na DTU***

Aby bylo možné snížit náklady pro tři databáze S3 ve fondu s 200 jednotkami eDTU, mohou nejvýše dvě z těchto databází dosahovat špičky svého využití současně. Pokud současně dosahují špičky více než dvě z těchto čtyř databází S3, bylo by nutné velikost fondu nastavit na více než 200 jednotek eDTU. Pokud se velikost fondu změní na více než 200 eDTU, je potřeba do fondu přidat další databáze S3, aby náklady zůstaly méně než výpočetní velikosti pro izolované databáze.

Všimněte si, tento příklad nebere v úvahu využití ostatních databází ve fondu. Pokud se v libovolném konkrétním časovém okamžiku do určité míry využívají všechny databáze, může méně než 2/3 (nebo 67 %) z nich dosahovat špičky současně.

### <a name="resource-utilization-per-database"></a>Využití prostředků na databázi

Velký rozdíl mezi maximálním a průměrným využitím databáze ukazuje na delší doby nízkého využití a krátká období vysokého využití. Tento vzor využití je ideální pro sdílení prostředků mezi databázemi. Použití fondu pro databázi byste měli zvážit, pokud je její využití ve špičce přibližně 1,5krát větší než průměrné využití.

**Příklad nákupního modelu založený na DTU**: databáze S3, která je ve špičce 100 DTU a v průměru používá 67 DTU nebo méně, je vhodným kandidátem na sdílení eDTU ve fondu. Databáze S1, která ve špičce využívá 20 DTU a průměrně využívá 13 DTU nebo méně, je vhodným kandidátem pro fond.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Návody zvolit správnou velikost fondu.

Nejlepší velikost pro fond závisí na agregovaných zdrojích potřebných pro všechny databáze ve fondu. To zahrnuje určení následujících možností:

- Maximální počet prostředků využívaných všemi databázemi ve fondu (buď maximálně DTU nebo maximální virtuální jádra, v závislosti na zvoleném modelu resourceing).
- Maximální počet bajtů úložiště využitých všemi databázemi ve fondu

Dostupné úrovně služeb pro každý model prostředků najdete v tématu [nákupní model založený na DTU](sql-database-service-tiers-dtu.md) nebo v [nákupním modelu založeném na Vcore](sql-database-service-tiers-vcore.md).

V případech, kdy nejde používat nástroje, vám při odhadování, jestli je fond cenově výhodnější než izolované databáze, pomůže následující postup:

1. Odhadované eDTU nebo virtuální jádra, které jsou potřeba pro fond, následujícím způsobem:

   Pro nákupní model založený na DTU: MAX (<*Celkový počet databáze* X *průměrné využití DTU na databázi*>,<br>  
   <*počet databází se souběžnou špičkou* X *využití DTU ve špičce na databázi*)

   Pro nákupní model založený na vCore: MAX (<*Celkový počet databáze* X *průměrného využití vCore na DB*>,<br>  
   <*číslo současné špičky* *využití Vcore ve špičce databáze na databázi*)

2. Odhadněte potřebnou velikost úložiště pro fond (sečtěte počet bajtů potřebných pro všechny databáze ve fondu). Potom určete velikost fondu v jednotkách eDTU, která toto úložiště poskytuje.
3. V případě nákupního modelu založeného na DTU Vezměte v úvahu větší z odhadů eDTU z kroku 1 a krok 2. U nákupního modelu založeného na vCore proveďte odhad vCore z kroku 1.
4. Podívejte se na [stránku s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) a najděte nejmenší velikost fondu, která je větší než odhad z kroku 3.
5. Porovnejte cenu fondu z kroku 5 s cenou za použití příslušných výpočetních velikostí pro izolované databáze.

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Používání dalších funkcí SQL Database s elastickými fondy

### <a name="elastic-jobs-and-elastic-pools"></a>Elastické úlohy a elastické fondy

U fondu jsou úlohy správy zjednodušené díky spouštění skriptů v **[elastických úlohách](elastic-jobs-overview.md)** . Elastická úloha eliminuje většinu únavných úkolů spojených s velkým počtem databází.

Další informace o ostatních databázových nástrojích pro práci s více databázemi najdete v tématu [Horizontální navýšení kapacity se službou Azure SQL Database](sql-database-elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Možnosti kontinuity podnikových aplikací pro databáze v elastickém fondu

Databáze ve fondu obecně podporují stejné [funkce provozní kontinuity](sql-database-business-continuity.md), jaké jsou dostupné pro izolované databáze.

- **Obnovení k bodu v čase**

  Obnovení k určitému bodu v čase používá automatické zálohování databáze k obnovení databáze ve fondu do konkrétního bodu v čase. Viz [Obnovení k určitému bodu v čase](sql-database-recovery-using-backups.md#point-in-time-restore).

- **Geografické obnovení**

  Geografické obnovení nabízí výchozí možnost obnovení, když databáze není k dispozici z důvodu incidentu v oblasti, ve které je databáze hostována. Viz [Obnovení služby Azure SQL Database a převzetí služeb při selhání sekundární lokalitou](sql-database-disaster-recovery.md).

- **Aktivní geografická replikace**

  Pro aplikace, které mají přísnější požadavky na obnovení než geografické obnovení, může nabídka konfigurovat [aktivní geografickou replikaci](sql-database-active-geo-replication.md) nebo [skupinu automatického převzetí služeb při selhání](sql-database-auto-failover-group.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Vytvoření nového SQL Database elastického fondu pomocí Azure Portal

Existují dva způsoby, jak můžete vytvořit elastický fond v Azure Portal.

1. V nabídce na levé straně Azure Portal vyberte **Azure SQL** . Pokud Azure SQL není v seznamu, vyberte **všechny služby**a do vyhledávacího pole zadejte *Azure SQL* .
2. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o elastických fondech můžete zobrazit tak, že na dlaždici **databáze** vyberete **Zobrazit podrobnosti** .
3. Na dlaždici **databáze** vyberte v rozevíracím seznamu **typ prostředku** možnost **elastický fond** a pak vyberte **vytvořit**:

   ![Vytvoření elastického fondu](./media/sql-database-elastic-pool/create-elastic-pool.png)


1. Nebo můžete vytvořit elastický fond tak, že přejdete na existující server SQL Azure a kliknutím na **+ Nový fond** vytvoříte fond přímo do tohoto serveru.

> [!NOTE]
> Na serveru můžete vytvořit více fondů, ale nemůžete přidávat databáze z různých serverů do stejného fondu.

Úroveň služby fondu určuje funkce, které jsou k dispozici pro elastické ve fondu, a maximální množství prostředků, které jsou k dispozici pro každou databázi. Podrobnosti najdete v tématu omezení prostředků pro elastické fondy v [modelu DTU](sql-database-dtu-resource-limits-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Omezení prostředků založených na vCore pro elastické fondy najdete v tématu [omezení prostředků založené na Vcore – elastické fondy](sql-database-vcore-resource-limits-elastic-pools.md).

Pokud chcete nakonfigurovat prostředky a ceny fondu, klikněte na **Konfigurovat fond**. Pak vyberte úroveň služby, přidejte databáze do fondu a nakonfigurujte omezení prostředků pro fond a jeho databáze.

Po dokončení konfigurace fondu můžete kliknout na použít, pojmenovat fond a kliknutím na OK vytvořit fond.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorování elastického fondu a jeho databází

V Azure Portal můžete monitorovat využití elastického fondu a databází v rámci tohoto fondu. Můžete také provést sadu změn elastického fondu a odeslat všechny změny ve stejnou dobu. Tyto změny zahrnují přidání nebo odebrání databází, změnu nastavení elastického fondu nebo změnu nastavení databáze.

Pokud chcete začít monitorovat elastický fond, najděte a otevřete elastický fond na portálu. Zobrazí se první obrazovka, která vám poskytne přehled o stavu elastického fondu. To zahrnuje:

- Monitorování grafů znázorňujících využití prostředků elastického fondu
- Nedávné výstrahy a doporučení, pokud jsou k dispozici pro elastický fond

Následující obrázek znázorňuje příklad elastického fondu:

![Zobrazení fondu](./media/sql-database-elastic-pool-manage-portal/basic.png)

Pokud chcete získat další informace o fondu, můžete kliknout na kteroukoli z dostupných informací v tomto přehledu. Kliknutím na graf **využití prostředků** přejdete do zobrazení monitorování Azure, kde můžete přizpůsobit metriky a časová období zobrazená v grafu. Kliknutím na libovolné dostupné oznámení přejdete do okna, ve kterém se zobrazí úplné podrobnosti o této výstraze nebo doporučení.

Pokud chcete monitorovat databáze v rámci fondu, můžete kliknout na **využití databázových prostředků** v části **monitorování** v nabídce prostředků na levé straně.

![Stránka využití databázových prostředků](./media/sql-database-elastic-pool-manage-portal/db-utilization.png)

### <a name="to-customize-the-chart-display"></a>Přizpůsobení zobrazení grafu

Graf a stránku metriky můžete upravit tak, aby zobrazovaly další metriky, jako je procento využití procesoru, procento v/v za použití dat a použité procento v/v v protokolu.

Na formuláři **Upravit graf** můžete vybrat pevný časový rozsah nebo kliknutím na tlačítko **vlastní** vybrat libovolné 24hodinové okno za poslední dva týdny a pak vybrat prostředky, které chcete monitorovat.

### <a name="to-select-databases-to-monitor"></a>Výběr databází ke sledování

Ve výchozím nastavení bude graf v okně **využití databázových prostředků** zobrazovat hlavní 5 databází podle DTU nebo procesoru (v závislosti na vaší úrovni služby). Databáze v tomto grafu můžete přepínat tak, že vyberete a zrušíte výběr databází v seznamu níže v grafu pomocí zaškrtávacích políček vlevo.

Můžete také vybrat další metriky pro zobrazení vedle sebe v této tabulce databáze a získat tak úplnější přehled o výkonu databází.

Další informace najdete v tématu [vytvoření výstrah SQL Database v Azure Portal](sql-database-insights-alerts-portal.md).

## <a name="customer-case-studies"></a>Zákaznické případové studie

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart používal elastické fondy s Azure SQL Database pro rychlé rozšíření obchodních služeb rychlostí 1 000 nových databází Azure SQL za měsíc.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco využívá elastické fondy se Azure SQL Database k rychlému zřizování a škálování služeb pro tisíce klientů v cloudu.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)    

   Daxko/CSI používá elastické fondy s Azure SQL Database k urychlení vývojového cyklu a vylepšení služeb a výkonu zákazníků.   

## <a name="next-steps"></a>Další kroky

- Pokud chcete škálovat elastické fondy, přečtěte si téma [škálování elastických fondů](sql-database-elastic-pool-scale.md) a [škálování elastického fondu – vzorový kód](scripts/sql-database-monitor-and-scale-pool-powershell.md) .
- Video najdete v tématu [Microsoft Virtual Academy video kurz o Azure SQL Database elastické možnosti](https://mva.microsoft.com/training-courses/elastic-database-capabilities-with-azure-sql-db-16554)
- Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](sql-database-design-patterns-multi-tenancy-saas-applications.md).
- Kurz SaaS použití elastických fondů najdete v tématu [Úvod do aplikace Wingtip SaaS](sql-database-wtp-overview.md).
