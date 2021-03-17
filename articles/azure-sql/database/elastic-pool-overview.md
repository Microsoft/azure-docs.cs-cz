---
title: Správa více databází s elastickými fondy
description: Spravujte a škálujte více databází v Azure SQL Database – stovky a tisíce – pomocí elastických fondů. Jedna cena za prostředky, které můžete distribuovat tam, kde je to potřeba.
services: sql-database
ms.service: sql-database
ms.subservice: elastic-pools
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: oslake
ms.author: moslake
ms.reviewer: ninarn, sstein
ms.date: 12/9/2020
ms.openlocfilehash: c478edf95ae345d64da630400fbf63ac613b73a6
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653631"
---
# <a name="elastic-pools-help-you-manage-and-scale-multiple-databases-in-azure-sql-database"></a>Elastické fondy vám pomůžou se správou a škálováním více databází v Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Azure SQL Database elastické fondy představují jednoduché a nákladově výhodné řešení pro správu a škálování více databází, které mají různé a nepředvídatelné požadavky na použití. Databáze v elastickém fondu jsou na jednom serveru a sdílejí nastavený počet prostředků za stanovenou cenu. Elastické fondy v Azure SQL Database umožňují vývojářům SaaS optimalizovat poměr cena/výkon pro skupinu databází v rámci předem daného rozpočtu a pro všechny databáze přitom zajistit elasticitu výkonu.

## <a name="what-are-sql-elastic-pools"></a>Co jsou elastické fondy SQL

Vývojáři SaaS sestavují aplikace nad datovými úrovněmi velkého rozsahu, které se skládají z několika databází. Běžným aplikačním postupem je zřídit pro každého zákazníka izolovanou databázi. Ale různí zákazníci mají často proměnlivé a nepředvídatelné vzorce používání a je obtížné odhadnout požadavky na prostředky u jednotlivých uživatelů databáze. Tradičně jste měli dvě možnosti:

- Provisioning prostředků na základě špičkového využití a platby za použití
- Snížení nákladů na náklady na výkon a spokojenost zákazníků během špičky – zřizování

Elastické fondy tento problém řeší tím, že zajistí, že databáze získají prostředky výkonu, které potřebují, když je potřebují. Poskytují jednoduchý mechanismus přidělování prostředků v mezích předvídatelného rozpočtu. Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](saas-tenancy-app-design-patterns.md).
>
> [!IMPORTANT]
> Pro elastické fondy se neúčtují žádné poplatky za databázi. Fakturuje se vám každá hodina existence fondu na nejvyšší eDTU nebo virtuální jádra, bez ohledu na využití nebo na to, jestli byl fond aktivní kratší dobu než hodinu.

Elastické fondy umožňují vývojářům koupit prostředky pro fond sdílený více databázemi a zajistit tak nepředvídatelné doby využití jednotlivými databázemi. Prostředky pro fond můžete nakonfigurovat na základě [nákupního modelu založeného na DTU](service-tiers-dtu.md) nebo [nákupního modelu založeného na Vcore](service-tiers-vcore.md). Požadavek na prostředky pro fond je určen agregovaným využitím jeho databází. Množství prostředků, které jsou k dispozici pro fond, řídí rozpočet pro vývojáře. Vývojář jednoduše do fondu přidá databáze, volitelně nastaví minimální a maximální prostředky pro databáze (buď minimální a maximální DTU, nebo minimální nebo maximální virtuální jádra v závislosti na zvoleném modelu resourceing), a pak nastaví prostředky fondu na základě jejich rozpočtu. Vývojáři mohou fondy využít k tomu, aby zajistili elegantní růst svých služeb od úsporného startupu až po zralé podnikání, a to ve stále se zvětšujícím měřítku.

V rámci fondu disponují jednotlivé databáze flexibilní možností automatického škálování v rámci stanovených parametrů. V případě velkého zatížení může databáze spotřebovávat více prostředků, aby splnila požadavky. Databáze v rámci světelné zátěže spotřebovávají méně a databáze bez zatížení nevyužívají žádné prostředky. Zřizováním prostředků pro celý fond, a nikoli pro jednotlivé databáze, se úkoly správy zjednodušují. Navíc máte předvídatelný rozpočet pro fond. Do existujícího fondu je možné přidat další prostředky s minimálními výpadky. Podobně platí, že pokud už další prostředky nepotřebujete, můžete je v jakémkoli okamžiku kdykoli odebrat z existujícího fondu. A můžete přidat nebo odebrat databáze z fondu. Pokud databáze podle předpokladu nedostatečně využívá prostředky, odeberte ji.

> [!NOTE]
> Při přesunu databází do elastického fondu nebo z něj nedochází k výpadku, s výjimkou krátkého časového období (v řádu sekund) na konci operace při zahození připojení databáze.

## <a name="when-should-you-consider-a-sql-database-elastic-pool"></a>Kdy byste měli zvážit SQL Database elastický fond

Fondy jsou vhodné pro velký počet databází se specifickými vzory využití. Pro danou databázi je tento vzor charakterizován nízkou mírou průměrného využití s relativně málo častými nárůsty využití. Naopak více databází, které mají trvalé střední-vysoké využití, by nemělo být umístěno do stejného elastického fondu.

Čím více databází je možné do fondu přidat, tím větší budou vaše úspory. V závislosti na způsobu využití vaší aplikace je možné vidět úspory s malým počtem databází S3.

Následující části vás seznámí s postupy, pomocí kterých můžete vyhodnotit, jestli pro vaši konkrétní kolekci databází bude použití fondu přínosné. V příkladech se používají fondy Standard, ale stejné principy platí také pro fondy Basic a Premium.

### <a name="assessing-database-utilization-patterns"></a>Vyhodnocení vzorů využití databáze

Na následujícím obrázku je příklad databáze, která je většinu doby nečinná, ale má také pravidelné špičky aktivity. Tento vzor využití se pro fond skvěle hodí:

   ![izolovaná databáze vhodná pro fond](./media/elastic-pool-overview/one-database.png)

Graf znázorňuje využití DTU v průběhu 1 hodiny v časovém intervalu od 12:00 do 1:00, kde má každý datový bod 1 minutu členitost. V 12:10 DB1 špičky až 90 DTU, ale celkové průměrné využití je méně než pět DTU. Pro spuštění této úlohy v izolovaných databázích se vyžaduje výpočetní velikost S3, ale tato akce zachová většinu prostředků nevyužitých během období nízké aktivity.

Fond umožňuje sdílet tyto nevyužité jednotky DTU napříč několika databázemi a snižuje tak počet potřebných jednotek DTU a celkové náklady.

Využijeme předchozí příklad a budeme předpokládat, že existují další databáze s podobnými vzory využití jako DB1. Na následujících dvou obrázcích jsou využití čtyř databází a 20 databází vrstveny do stejného grafu pro ilustraci překrývající se povahy jejich využití v čase pomocí nákupního modelu založeného na DTU:

   ![Čtyři databáze se vzorem využití, který je vhodný pro fond](./media/elastic-pool-overview/four-databases.png)

   ![Dvacet databází se vzorem využití, který je vhodný pro fond](./media/elastic-pool-overview/twenty-databases.png)

Agregované využití DTU napříč všemi 20 databázemi je na předchozím obrázku znázorněné černou čárou. Ukazuje se, že agregované využití DTU nikdy nepřekračuje 100 jednotek DTU. Znamená to, že těchto 20 databází může v průběhu tohoto časového období sdílet 100 jednotek eDTU. Výsledkem je 20krát snížení DTU a snížení ceny 13krát nižší v porovnání s umístěním jednotlivých databází v výpočetních velikostech S3 pro izolované databáze.

Tento příklad je ideální z následujících důvodů:

- Ukazuje velké rozdíly mezi využitím ve špičce a průměrným využitím jednotlivých databází.
- Špičky využití pro jednotlivé databáze nastávají v různých časových okamžicích.
- Jednotky eDTU jsou sdílené mezi mnoha databázemi.

V modelu nákupu DTU je cena fondu funkcí eDTU fondu. Přestože je cena ze jednotku eDTU pro fond 1,5krát vyšší než cena za jednotku DTU pro izolovanou databázi, **jednotky eDTU fondu může sdílet velký počet databází, a proto stačí menší celkový počet jednotek eDTU**. Tyto rozdíly v cenách a sdílení jednotek eDTU jsou základem potenciálních úspor, které fondy mohou nabídnout.

V modelu nákupu vCore je cena vCore jednotky pro elastické fondy stejná jako cena za jednotku vCore pro jednotlivé databáze.

## <a name="how-do-i-choose-the-correct-pool-size"></a>Návody zvolit správnou velikost fondu.

Nejlepší velikost pro fond závisí na agregovaných zdrojích potřebných pro všechny databáze ve fondu. To zahrnuje určení následujících možností:

- Maximální počet výpočetních prostředků využitých všemi databázemi ve fondu.  Výpočetní prostředky se indexují buď pomocí eDTU, nebo virtuální jádra, podle toho, jak jste si zvolili nákupní model.
- Maximální počet bajtů úložiště využitých všemi databázemi ve fondu

V případě úrovní služeb a omezení prostředků v jednotlivých nákupních modelech si přečtěte [nákupní model založený na DTU](service-tiers-dtu.md) nebo [nákupní model založený na Vcore](service-tiers-vcore.md).

Následující kroky vám pomůžou odhadnout, jestli je fond cenově výhodnější než izolované databáze:

1. Odhadované eDTU nebo virtuální jádra, které jsou potřeba pro fond, následujícím způsobem:
   - Pro nákupní model založený na DTU:
     - Max (<*Celkový počet databáze* &times; *využití DTU na databázi*>, <*počet souběžnosti databáze* &times; *nejvyšší využití DTU na databázi*>)
   - Pro nákupní model založený na vCore:
     - Max (<*Celkový počet databáze* &times; *využití Vcore na DB*>, <*počet současně ve špičce databáze* &times; *Vcore využití na databázi*>)
2. Odhadem celkového prostoru úložiště potřebného pro fond přidejte velikost dat potřebnou pro všechny databáze ve fondu. Pro model nákupu DTU pak určete velikost fondu eDTU, která poskytuje toto množství úložiště.
3. V případě nákupního modelu založeného na DTU Vezměte v úvahu větší z odhadů eDTU z kroku 1 a krok 2. U nákupního modelu založeného na vCore proveďte odhad vCore z kroku 1.
4. Podívejte se na [stránku s cenami SQL Database](https://azure.microsoft.com/pricing/details/sql-database/) a najděte nejmenší velikost fondu, která je větší než odhad z kroku 3.
5. Porovnejte cenu fondu z kroku 4 s cenou za použití příslušných výpočetních velikostí pro izolované databáze.

> [!IMPORTANT]
> Pokud se počet databází ve fondu blíží maximální podporované hodnotě, nezapomeňte zvážit [správu prostředků v hustých elastických fondech](elastic-pool-resource-management.md).

## <a name="using-other-sql-database-features-with-elastic-pools"></a>Používání dalších funkcí SQL Database s elastickými fondy

### <a name="elastic-jobs-and-elastic-pools"></a>Elastické úlohy a elastické fondy

U fondu jsou úlohy správy zjednodušené díky spouštění skriptů v **[elastických úlohách](elastic-jobs-overview.md)**. Elastická úloha eliminuje většinu únavných úkolů spojených s velkým počtem databází.

Další informace o ostatních databázových nástrojích pro práci s více databázemi najdete v tématu [Horizontální navýšení kapacity se službou Azure SQL Database](elastic-scale-introduction.md).

### <a name="business-continuity-options-for-databases-in-an-elastic-pool"></a>Možnosti kontinuity podnikových aplikací pro databáze v elastickém fondu

Databáze ve fondu obecně podporují stejné [funkce provozní kontinuity](business-continuity-high-availability-disaster-recover-hadr-overview.md), jaké jsou dostupné pro izolované databáze.

- **Obnovení k určitému bodu v čase**

  Obnovení k určitému bodu v čase používá automatické zálohování databáze k obnovení databáze ve fondu do konkrétního bodu v čase. Viz [Obnovení k určitému bodu v čase](recovery-using-backups.md#point-in-time-restore).

- **Geografické obnovení**

  Geografické obnovení nabízí výchozí možnost obnovení, když databáze není k dispozici z důvodu incidentu v oblasti, ve které je databáze hostována. Viz [obnovení Azure SQL Database nebo převzetí služeb při selhání sekundárním](disaster-recovery-guidance.md)

- **Aktivní geografická replikace**

  Pro aplikace, které mají přísnější požadavky na obnovení než geografické obnovení, může nabídka konfigurovat [aktivní geografickou replikaci](active-geo-replication-overview.md) nebo [skupinu automatického převzetí služeb při selhání](auto-failover-group-overview.md).

## <a name="creating-a-new-sql-database-elastic-pool-using-the-azure-portal"></a>Vytvoření nového SQL Database elastického fondu pomocí Azure Portal

Existují dva způsoby, jak můžete vytvořit elastický fond v Azure Portal.

1. Pro vytvoření elastického fondu použijte [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **Azure SQL**.
2. Výběrem **+ Přidat** otevřete stránku **vybrat možnost nasazení SQL** . Další informace o elastických fondech můžete zobrazit tak, že na dlaždici **databáze** vyberete **Zobrazit podrobnosti** .
3. Na dlaždici **databáze** vyberte v rozevíracím seznamu **typ prostředku** možnost **elastický fond** a pak vyberte **vytvořit**:

   ![Vytvoření elastického fondu](./media/elastic-pool-overview/create-elastic-pool.png)

4. Nebo můžete vytvořit elastický fond tak, že přejdete na existující server a kliknutím na **+ Nový fond** vytvoříte fond přímo na tento server.

> [!NOTE]
> Na serveru můžete vytvořit více fondů, ale nemůžete přidávat databáze z různých serverů do stejného fondu.

Úroveň služby fondu určuje funkce, které jsou k dispozici pro elastické ve fondu, a maximální množství prostředků, které jsou k dispozici pro každou databázi. Podrobnosti najdete v tématu omezení prostředků pro elastické fondy v [modelu DTU](resource-limits-dtu-elastic-pools.md#elastic-pool-storage-sizes-and-compute-sizes). Omezení prostředků založených na vCore pro elastické fondy najdete v tématu [omezení prostředků založené na Vcore – elastické fondy](resource-limits-vcore-elastic-pools.md).

Pokud chcete nakonfigurovat prostředky a ceny fondu, klikněte na **Konfigurovat fond**. Pak vyberte úroveň služby, přidejte databáze do fondu a nakonfigurujte omezení prostředků pro fond a jeho databáze.

Po dokončení konfigurace fondu můžete kliknout na použít, pojmenovat fond a kliknutím na OK vytvořit fond.

## <a name="monitor-an-elastic-pool-and-its-databases"></a>Monitorování elastického fondu a jeho databází

V Azure Portal můžete monitorovat využití elastického fondu a databází v rámci tohoto fondu. Můžete také provést sadu změn elastického fondu a odeslat všechny změny ve stejnou dobu. Tyto změny zahrnují přidání nebo odebrání databází, změnu nastavení elastického fondu nebo změnu nastavení databáze.

Můžete použít integrované nástroje pro [monitorování výkonu](./performance-guidance.md) a [upozorňování](./alerts-insights-configure-portal.md)v kombinaci s hodnocením výkonu.  Kromě toho SQL Database možné [vygenerovat metriky a protokoly prostředků](./metrics-diagnostic-telemetry-logging-streaming-export-configure.md?tabs=azure-portal) pro snazší monitorování.

## <a name="customer-case-studies"></a>Zákaznické případové studie

- [SnelStart](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-snelstart/)

  SnelStart používal elastické fondy s Azure SQL Database pro rychlé rozšíření obchodních služeb rychlostí 1 000 nových databází Azure SQL za měsíc.

- [Umbraco](https://azure.microsoft.com/resources/videos/azure-sql-database-case-study-umbraco/)

  Umbraco využívá elastické fondy se Azure SQL Database k rychlému zřizování a škálování služeb pro tisíce klientů v cloudu.

- [Daxko/CSI](https://customers.microsoft.com/story/726277-csi-daxko-partner-professional-service-azure)

   Daxko/CSI používá elastické fondy s Azure SQL Database k urychlení vývojového cyklu a vylepšení služeb a výkonu zákazníků.

## <a name="next-steps"></a>Další kroky

- Informace o cenách najdete v tématu [ceny elastického fondu](https://azure.microsoft.com/pricing/details/sql-database/elastic).
- Pokud chcete škálovat elastické fondy, přečtěte si téma [škálování elastických fondů](elastic-pool-scale.md) a [škálování elastického fondu – vzorový kód](scripts/monitor-and-scale-pool-powershell.md) .
- Další informace o návrhových schématech aplikací SaaS využívajících elastické fondy najdete v tématu [Návrhová schémata pro víceklientské aplikace SaaS využívající službu Azure SQL Database](saas-tenancy-app-design-patterns.md).
- Kurz SaaS použití elastických fondů najdete v tématu [Úvod do aplikace Wingtip SaaS](saas-dbpertenant-wingtip-app-overview.md).
- Další informace o správě prostředků v elastických fondech s mnoha databázemi najdete v tématu [Správa prostředků v hustých elastických fondech](elastic-pool-resource-management.md).
