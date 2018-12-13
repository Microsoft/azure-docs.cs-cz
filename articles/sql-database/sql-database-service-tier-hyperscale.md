---
title: Azure SQL Database Hyperškálovatelného přehled | Dokumentace Microsoftu
description: Tento článek popisuje danou vrstvu služeb Hyperškálovatelného v založený na virtuálních jádrech nákupní model ve službě Azure SQL Database a vysvětluje, jak se liší od úrovně služeb pro obecné účely a pro důležité obchodní informace.
services: sql-database
ms.service: sql-database
ms.subservice: ''
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: CarlRabeler
ms.author: carlrab
ms.reviewer: ''
manager: craigg
ms.date: 10/17/2018
ms.openlocfilehash: 80e807a8fcbd6c087ad0995a4481180fa28ef42f
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872879"
---
# <a name="hyperscale-service-tier-preview-for-up-to-100-tb"></a>Velkokapacitní úrovni služeb (preview) pro až 100 TB

Azure SQL Database je založené na architektuře databázový stroj SQL serveru, která je upravená pro cloudové prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají ve službě Azure SQL Database:
- Obecné účely nebo Standard 
- Kritické obchodní nebo Premium
- Hyperškálování

Úroveň mírou škálování služby ve službě Azure SQL Database je nejnovější úroveň služby v nákupní model založený na virtuálních jádrech. Tato úroveň služby je vysoce škálovatelné úložiště a výpočetní úroveň výkonu, který využívá Azure architekturu pro horizontální navýšení kapacity úložiště a výpočetní prostředky pro službu Azure SQL Database podstatně nad limity, které jsou k dispozici pro obecné účely a Business Úrovně důležitých služeb.

> [!IMPORTANT]
> Velkokapacitní úroveň služby je aktuálně ve verzi public preview a je k dispozici v omezené oblastech Azure. Oblast úplný seznam najdete v tématu [velkokapacitní služby dostupné oblasti úroveň](#available-regions). Nedoporučujeme s jakékoli produkčními úlohami v databázích Hyperškálovatelného ještě. Velkokapacitní databáze nelze aktualizovat na další úrovně služeb. Pro testovací účely doporučujeme vytvořit kopii aktuální databáze a aktualizovat kopii Hyperškálovatelného vrstvu služby.
> [!NOTE]
> Podrobnosti o úrovních služeb pro obecné účely a pro důležité obchodní informace v nákupní model založený na virtuálních jádrech najdete v tématu [Obecné](sql-database-service-tier-general-purpose.md) a [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) úrovně služeb. Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-service-tiers.md).
> [!IMPORTANT]
> Úroveň velkokapacitní služby je aktuálně ve verzi public preview. Nedoporučujeme s jakékoli produkčními úlohami v databázích Hyperškálovatelného ještě. Velkokapacitní databáze nelze aktualizovat na další úrovně služeb. Pro testovací účely doporučujeme vytvořit kopii aktuální databáze a aktualizovat kopii Hyperškálovatelného vrstvu služby.

## <a name="what-are-the-hyperscale-capabilities"></a>Jaké jsou možnosti hyperškálovatelný systém

Úroveň mírou škálování služby ve službě Azure SQL Database poskytuje následující funkce:

- Podpora pro až 100 TB velikosti databáze
- Téměř okamžité zálohování (podle snímky souborů uložených ve službě Azure Blob storage) databáze bez ohledu na velikost bez jakéhokoli dopadu na vstupně-výstupních operací na výpočetní prostředky
- Rychlé obnovení databáze (podle snímky) v minutách nikoli hodin nebo dnů (nikoli velikost operace s daty)
- Vyšší výkon z důvodu větší propustnost v protokolu a kratší doby potvrzení transakce bez ohledu na to datové svazky
- Rychlé horizontální navýšení kapacity – můžete zřídit jednoho nebo více jen pro čtení uzlů pro přesměrování zpracování úlohy čtení a pro použití jako horkou – možnosti pro případ potřeby
- Rychlé škálování nahoru – můžete, v konstantním čase vertikální navýšení kapacity výpočetních prostředků pro plnění náročných úloh podle potřeby a pak škálovat výpočetní prostředky zase snížit, když nejsou potřeba.

Úroveň služby Hyperškálovatelného odebere řadu praktickým limitům tradičně vidět v cloudových databázích. Kde většina jiných databází se uplatňuje limit vycházející prostředkům dostupným v jednom uzlu, databáze v úrovni služby Hyperškálovatelného mít žádné takové omezení. S jeho architektuře flexibilního úložiště roste úložiště podle potřeby. Ve skutečnosti Hyperškálovatelného databáze nejsou vytvořeny s definovaný maximální velikost. Roste mírou škálování databáze podle potřeby – a se účtují jenom za kapacitu, kterou používáte. Pro úlohy náročné na čtení úroveň služby Hyperškálovatelného poskytuje rychlé škálování tím, že zajistíte další čtení repliky podle potřeby pro snižování zátěže úlohami pro čtení.

Kromě toho doby potřebné k vytvoření zálohy databáze nebo pokud chcete vertikálně navýšit nebo dolů už není svázaný s objemem dat v databázi. Velkokapacitní databáze může být zálohovány prakticky okamžitě. Můžete škálovat také databázi v desítky terabajtů navýšit nebo snížit kapacitu během několika minut. Tato možnost umožní vám z obavy týkající se poli podle vaší volby počáteční konfiguraci.

Další informace o velikostech výpočetních pro danou vrstvu služeb hyperškálovatelný systém, najdete v části [služby Vlastnosti vrstvy](sql-database-service-tiers-vcore.md#service-tier-characteristics).

## <a name="who-should-consider-the-hyperscale-service-tier"></a>Kdo by měl zvážit úroveň služby hyperškálovatelný systém

Velkokapacitní, kterou úroveň služby je primárně určena pro zákazníky, kteří mají velké databáze buď v místním a chcete k modernizaci aplikací díky přesunu do cloudu nebo pro zákazníky, kteří jsou už v cloudu a se uplatňuje limit vycházející maximální velikosti databáze omezení (1 – 4 TB). Také je určena pro zákazníky, kteří hledají vysoký výkon a vysokou škálovatelnost pro úložiště a výpočetní.

Úroveň služby hyperškálovatelný systém podporuje všechny úlohy SQL serveru, ale je primárně optimalizováno pro OLTP. Úroveň velkokapacitní služby podporuje i hybridní a analytických úloh (datové Tržiště).

> [!IMPORTANT]
> Elastické fondy na úrovni služby hyperškálovatelný systém nepodporují.

## <a name="hyperscale-pricing-model"></a>Velkokapacitní cenový model

Velkokapacitní úrovně služeb je k dispozici pouze [modelu virt. jader](sql-database-service-tiers-vcore.md). Aby bylo v souladu s novou architekturu, cenový model se mírně liší od úrovně služeb pro obecné účely nebo pro důležité obchodní informace:

- **COMPUTE**:

  Jednotková cena Hyperškálovatelný výpočetní je na repliku. [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) cena se použije ke čtení replik škálování automaticky. Ve verzi public preview vytvoříme dvě repliky na databázi Hyperškálovatelného ve výchozím nastavení.

- **Úložiště**:

  Není nutné zadat velikost maximální dat při konfiguraci databáze hyperškálovatelný systém. Ve vrstvě s hyperškálováním se účtuje úložiště pro databázi podle skutečného využití. Úložiště se přiděluje dynamicky v rozsahu 5 GB až 100 TB s krokem 1 GB.  

Další informace o cenách hyperškálovatelný systém, najdete v části [Azure SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Funkce distribuovaná architektura

Na rozdíl od tradičních databázovým strojům, které mají centralizované všechny funkce správy dat v jednom umístění/procesu (i tak volaná distribuované databáze v produkčním prostředí ještě dnes mají několik kopií monolitický modul) odděluje mírou škálování databáze modul zpracování dotazů, kde sémantiku různých datových modulů odchýlení od součásti, které poskytují dlouhodobé úložiště a odolnost pro data. Tímto způsobem, kapacita úložiště můžete bez problémů škálovat Pokud, podle potřeby (počáteční cíl je 100 TB). Repliky jen pro čtení sdílí stejné komponenty výpočetní prostředky, proto žádná kopie dat je potřeba aktivovat nový čitelných replik. Během období preview se podporuje jenom 1 repliky jen pro čtení.

Následující diagram znázorňuje různé typy uzlů v Hyperškálovacím databázi:

![Architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Velkokapacitní databáze obsahuje následující typy uzlů:

### <a name="compute-node"></a>Výpočetní uzel

Výpočetní uzel je, kde se nachází relačním stroji, tak všechny prvky jazyka, zpracování dotazů a tak dále, dojde k. Všechny interakce uživatele s Hyperškálováním databází možné prostřednictvím těchto výpočetních uzlů. Výpočetní uzly mají mezipaměti založené na discích SSD (označené jako RBPEX – rozšíření odolného fondu vyrovnávací paměti na předchozím obrázku) Chcete-li minimalizovat počet síťových přenosů muset načíst stránku dat. Existuje jedna primární výpočetním uzlu, kde se zpracovávají všechny úlohy čtení a zápis a transakce. Nejsou k dispozici jeden nebo více sekundárních výpočetních uzlů, které fungují jako aktivní pohotovostní uzly pro účely převzetí služeb při selhání, jakož i fungovat jako jen pro čtení výpočetních uzlů pro snižování zátěže při čtení úloh (Pokud tato funkce se vyžaduje).

### <a name="page-server-node"></a>Uzel serveru stránky

Stránka servery jsou systémy představující modul s horizontálním navýšením kapacity úložiště.  Každý server stránky je zodpovědná za podmnožinu stránky v databázi.  Každý server stránky řídí formálně, 1 terabajt šířky data. Žádná data se sdílí na více než jeden server stránky (mimo repliky, které jsou zachovány redundanci a dostupnost). Úloha server stránky je obsluhovat stránky databáze do výpočetních uzlů na vyžádání a ponechat aktualizovat, protože transakce aktualizovat data. Stránka servery jsou tak pořád aktuální přehráním záznamy protokolu od služby protokolování. Stránka servery udržují také založené na discích SSD mezipaměti ke zvýšení výkonu. Dlouhodobé ukládání stránky dat se ukládají ve službě Azure Storage pro další spolehlivost.

### <a name="log-service-node"></a>Uzel služeb log

Uzel služby protokolu přijímá záznamy protokolu z primární výpočetního uzlu, přetrvává v mezipaměti na trvalý a předává záznamy protokolu do zbytku výpočetních uzlů (tak mohou aktualizovat své mezipaměti) a také servery příslušné stránce tak, aby data mohou být aktualizovaný t Tady. Tímto způsobem jsou všechny změny dat z primární výpočetního uzlu šířena přes službu protokolu pro všechny sekundární výpočetních uzlů a stránky servery. Záznamy protokolu jsou nakonec proniknout do dlouhodobého úložiště ve službě Azure Storage, což je úložiště nekonečné úložiště. Tento mechanismus odebere nezbytná pro zkrácení časté protokolu. Služba protokol má také místní mezipaměť ke zrychlení přístupu.

### <a name="azure-storage-node"></a>Uzel úložiště Azure

Uzel Azure storage je konečný cíl dat ze serverů stránky. Toto úložiště se používá pro účely zálohování stejně jako u replikace mezi oblastmi Azure. Zálohování se skládají z snímky dat souborů. Obnovit operace jsou rychlé z těchto snímků a data můžete obnovit do libovolného bodu v čase.

## <a name="backup-and-restore"></a>Zálohování a obnovení

Zálohy snímků souborů základní a proto jsou téměř okamžité. Oddělení úložiště a výpočetního výkonu povolte doručením (push) dolů operace zálohování a obnovení do vrstvy úložiště překážky zpracování na primární výpočetním uzlu. V důsledku toho zálohy velké databáze nemá vliv na výkon primární výpočetního uzlu. Podobně obnovení provádí kopírování snímku souboru a jako taková nejsou velikost datové operace. Pro obnovení v rámci stejného účtu úložiště je rychlá operace obnovení.

## <a name="scale-and-performance-advantages"></a>Výhody škálování a výkon

Díky možnosti rozjedete směrem nahoru nebo dolů dalších jen pro čtení výpočetních uzlů Hyperškálovatelného architektura umožňuje významné čtení možnosti škálování a můžete také uvolnit primární výpočetní uzel obsluhuje další požadavky na zápis. Také výpočetní uzly je možné škálovat směrem nahoru nebo dolů rychle vzhledem k architektuře úložiště sdíleného v rámci architektury mírou škálování.

## <a name="create-a-hyperscale-database"></a>Vytvoření Hyperškálovatelného databáze

Velkokapacitní databázi lze vytvořit pomocí [webu Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/create-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/new-azurermsqldatabase) nebo [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-create). Velkokapacitní databáze jsou k dispozici pouze prostřednictvím [nákupní model založený na virtuálních jádrech](sql-database-service-tiers-vcore.md).

Pomocí následujícího příkazu T-SQL vytvoří databázi mírou škálování. Je nutné zadat cíl edition i služby v `CREATE DATABASE` příkazu.

```sql
-- Create a HyperScale Database
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrovat existující databázi SQL Azure na vrstvu služby hyperškálovatelný systém

Můžete přesunout existující databáze Azure SQL do Hyperškálovatelného pomocí [webu Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). Ve verzi public preview Toto je jednosměrná migrace. Nelze přesunout databáze z Hyperškálovatelného na jinou úroveň služby. Doporučujeme vytvořit kopii provozní databáze a migrace na Hyperškálovatelného pro testování konceptů (upozorníme).

Pomocí následujícího příkazu T-SQL, přesune databázi do vrstvy velkokapacitní služby. Je nutné zadat cíl edition i služby v `ALTER DATABASE` příkazu.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen4_4');
GO
```

> [!IMPORTANT]
> [Transparentní šifrování databáze (TDE)](transparent-data-encryption-azure-sql.md) mělo vypnout před změnou databáze hyperškálovatelný systém hyperškálovatelný systém.

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Připojíte k replice škálování pro čtení mírou škálování databáze

V Hyperškálovacím databází `ApplicationIntent` v připojovacím řetězci, který klient poskytl argument určuje, zda připojení se směruje do repliky zápisu nebo do sekundární repliky jen pro čtení. Pokud `ApplicationIntent` nastavena na `READONLY` a databáze nemá na sekundární repliku, připojení se budou směrovat na primární repliku a výchozí hodnota je `ReadWrite` chování.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```

## <a name="available-regions"></a>Dostupné oblasti

Úroveň velkokapacitní služby je aktuálně ve verzi public preview a je k dispozici v následujících oblastech Azure: EastUS1 EastUS2, WestUS2, CentralUS, NorthCentralUS, WestEurope, NorthEurope, UKWest, AustraliaEast, AustraliaSouthEast, SouthEastAsia, JapanEast, KoreaCentral

## <a name="known-limitations"></a>Známá omezení

| Problém | Popis |
| :---- | :--------- |
| V podokně Správa zálohování pro logický server nezobrazují Hyperškálovatelného databáze bude filtrováno ze serveru SQL server ->  | Velkokapacitní má samostatné metodě pro správu zálohování a jako takový dlouhodobé uchovávání dat a bod v nastavení uchovávání záloh čas se nevztahují / nejsou zneplatněny. Podle toho Hyperškálovatelného databází se nezobrazují v podokně Správa zálohování. |
| Obnovení k určitému bodu v čase | Po migraci databáze do vrstvy služby hyperškálovatelný systém se nepodporuje obnovení k určitému bodu v čase před migrací.|
| Pokud je soubor databáze roste během migrace z důvodu aktivní úlohy a překročí 1 TB za hranice souboru, se migrace nezdaří | Omezení rizik: <br> – Pokud je to možné, migrace databáze, pokud neexistuje žádné aktualizace zátěži.<br> – Zkuste to znovu migrace, bude úspěšné, tak dlouho, dokud není překročí hranice 1 TB během migrace.|
| Managed Instance se momentálně nepodporuje. | Aktuálně se nepodporuje. |
| Migrace do Hyperškálovatelného je aktuálně Jednosměrná operace | Po migraci databáze na Hyperškálovatelného, není možné migrovat přímo do úrovně služeb-mírou škálování. V současné době je jediný způsob, jak migrovat databázi z Hyperškálovatelného do bez Hyperškálovatelného export a import pomocí souborů BACPAC.|
| Migrace databází pomocí objektů v paměti se aktuálně nepodporuje. | Třeba vyřadit a znovu vytvořen jako objekty bez v paměti před migrací databáze na vrstvu služby Hyperškálovatelného objektů v paměti.

## <a name="next-steps"></a>Další postup

- Nejčastější dotazy na hyperškálovatelný systém, najdete v části [nejčastější dotazy ohledně Hyperškálovatelného](sql-database-service-tier-hyperscale-faq.md).
- Informace o úrovních služeb najdete v tématu [úrovně služeb](sql-database-service-tiers.md)
- Zobrazit [Přehled prostředků omezuje na logickém serveru](sql-database-resource-limits-logical-server.md) informace o omezeních na úrovni serveru a předplatné.
- Zakoupení modelu omezení pro jednu databázi, naleznete v tématu [založený na virtuálních jádrech zakoupení modelu omezení pro jednu databázi Azure SQL Database](sql-database-vcore-resource-limits-single-databases.md).
- Pro funkce a seznam porovnání, naleznete v tématu [běžné funkce SQL](sql-database-features.md).
