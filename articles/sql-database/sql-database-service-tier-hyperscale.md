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
ms.date: 05/06/2019
ms.openlocfilehash: 9455b8488bdf7c36c662a8f771e6b26d1a27b13e
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2019
ms.locfileid: "65236557"
---
# <a name="hyperscale-service-tier-for-up-to-100-tb"></a>Velkokapacitní vrstvy služby pro až 100 TB

Azure SQL Database je založené na architektuře databázový stroj SQL serveru, která je upravená pro cloudové prostředí, aby bylo možné zajistit 99,99 % dostupnost i v případě selhání infrastruktury. Existují tři modely architektury, které se používají ve službě Azure SQL Database:
- Obecné účely nebo Standard 
-  Hyperškálování
-  Kritické obchodní nebo Premium

Úroveň mírou škálování služby ve službě Azure SQL Database je nejnovější úroveň služby v nákupní model založený na virtuálních jádrech. Tato úroveň služby je vysoce škálovatelné úložiště a výpočetní úroveň výkonu, který využívá Azure architekturu pro horizontální navýšení kapacity úložiště a výpočetní prostředky pro službu Azure SQL Database podstatně nad limity, které jsou k dispozici pro obecné účely a Business Úrovně důležitých služeb.

> 
> [!NOTE]
> Podrobnosti o úrovních služeb pro obecné účely a pro důležité obchodní informace v nákupní model založený na virtuálních jádrech najdete v tématu [Obecné](sql-database-service-tier-general-purpose.md) a [pro důležité obchodní informace](sql-database-service-tier-business-critical.md) úrovně služeb. Porovnání nákupní model s založený na DTU nákupní model založený na virtuálních jádrech najdete v tématu [nákupu modely a prostředků Azure SQL Database](sql-database-service-tiers.md).


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

  Jednotková cena Hyperškálovatelný výpočetní je na repliku. [Zvýhodněné hybridní využití Azure](https://azure.microsoft.com/pricing/hybrid-benefit/) cena se použije ke čtení replik škálování automaticky. Vytvoříme primární repliku a jeden repliky jen pro čtení na databázi Hyperškálovatelného ve výchozím nastavení.  Uživatelé mohou nastavit celkového počtu replik včetně primárních z 1 – 5.

- **Úložiště**:

  Není nutné zadat velikost maximální dat při konfiguraci databáze hyperškálovatelný systém. Ve vrstvě s hyperškálováním se účtuje úložiště pro databázi podle skutečného využití. Úložiště je alokováno automaticky mezi 10 GB a 100 TB, v přírůstcích po, které jsou dynamicky upravit 10GB až 40GB.  

Další informace o cenách hyperškálovatelný systém, najdete v části [Azure SQL Database – ceny](https://azure.microsoft.com/pricing/details/sql-database/single/)

## <a name="distributed-functions-architecture"></a>Funkce distribuovaná architektura

Na rozdíl od tradičních databázovým strojům, které mají centralizované všechny funkce správy dat v jednom umístění/procesu (i tak volaná distribuované databáze v produkčním prostředí ještě dnes mají několik kopií monolitický modul) odděluje mírou škálování databáze modul zpracování dotazů, kde sémantiku různých datových modulů odchýlení od součásti, které poskytují dlouhodobé úložiště a odolnost pro data. Tímto způsobem, kapacita úložiště můžete bez problémů škálovat Pokud, podle potřeby (počáteční cíl je 100 TB). Repliky jen pro čtení sdílet stejné komponenty úložiště, aby žádná kopie dat je potřeba aktivovat nový čitelných replik. 

Následující diagram znázorňuje různé typy uzlů v Hyperškálovacím databázi:

![Architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Velkokapacitní databáze obsahuje následující typy uzlů:

### <a name="compute-node"></a>Výpočetní uzel

Výpočetní uzel je, kde se nachází relačním stroji, tak všechny prvky jazyka, zpracování dotazů a tak dále, dojde k. Všechny interakce uživatele s Hyperškálováním databází možné prostřednictvím těchto výpočetních uzlů. Výpočetní uzly mají mezipaměti založené na discích SSD (označené jako RBPEX – rozšíření odolného fondu vyrovnávací paměti na předchozím obrázku) Chcete-li minimalizovat počet síťových přenosů muset načíst stránku dat. Existuje jedna primární výpočetním uzlu, kde se zpracovávají všechny úlohy čtení a zápis a transakce. Nejsou k dispozici jeden nebo více sekundárních výpočetních uzlů, které fungují jako aktivní pohotovostní uzly pro účely převzetí služeb při selhání, jakož i fungovat jako jen pro čtení výpočetních uzlů pro snižování zátěže při čtení úloh (Pokud tato funkce se vyžaduje).

### <a name="page-server-node"></a>Uzel serveru stránky

Stránka servery jsou systémy představující modul s horizontálním navýšením kapacity úložiště.  Každý server stránky je zodpovědná za podmnožinu stránky v databázi.  Formálně každý server ovládací prvky stránky 128 GB až 1 TB dat. Žádná data se sdílí na více než jeden server stránky (mimo repliky, které jsou zachovány redundanci a dostupnost). Úloha server stránky je obsluhovat stránky databáze do výpočetních uzlů na vyžádání a ponechat aktualizovat, protože transakce aktualizovat data. Stránka servery jsou tak pořád aktuální přehráním záznamy protokolu od služby protokolování. Stránka servery udržují také založené na discích SSD mezipaměti ke zvýšení výkonu. Dlouhodobé ukládání stránky dat se ukládají ve službě Azure Storage pro další spolehlivost.

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
CREATE DATABASE [HyperScaleDB1] (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```
Tím se vytvoří databáze Hyperškálovatelného na hardwaru Gen5 se 4 jádry.

## <a name="migrate-an-existing-azure-sql-database-to-the-hyperscale-service-tier"></a>Migrovat existující databázi SQL Azure na vrstvu služby hyperškálovatelný systém

Můžete přesunout existující databáze Azure SQL do Hyperškálovatelného pomocí [webu Azure portal](https://portal.azure.com), [T-SQL](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-current), [Powershell](https://docs.microsoft.com/powershell/module/azurerm.sql/set-azurermsqldatabase) nebo [CLI](https://docs.microsoft.com/cli/azure/sql/db#az-sql-db-update). V současné době to je jednosměrná migrace. Nelze přesunout databáze z Hyperškálovatelného na jinou úroveň služby. Doporučujeme vytvořit kopii provozní databáze a migrace na Hyperškálovatelného pro testování konceptů (upozorníme).

Pomocí následujícího příkazu T-SQL, přesune databázi do vrstvy velkokapacitní služby. Je nutné zadat cíl edition i služby v `ALTER DATABASE` příkazu.

```sql
-- Alter a database to make it a HyperScale Database
ALTER DATABASE [DB2] MODIFY (EDITION = 'HyperScale', SERVICE_OBJECTIVE = 'HS_Gen5_4');
GO
```

## <a name="connect-to-a-read-scale-replica-of-a-hyperscale-database"></a>Připojíte k replice škálování pro čtení mírou škálování databáze

V Hyperškálovacím databází `ApplicationIntent` v připojovacím řetězci, který klient poskytl argument určuje, zda připojení se směruje do repliky zápisu nebo do sekundární repliky jen pro čtení. Pokud `ApplicationIntent` nastavena na `READONLY` a databáze nemá na sekundární repliku, připojení se budou směrovat na primární repliku a výchozí hodnota je `ReadWrite` chování.

```cmd
-- Connection string with application intent
Server=tcp:<myserver>.database.windows.net;Database=<mydatabase>;ApplicationIntent=ReadOnly;User ID=<myLogin>;Password=<myPassword>;Trusted_Connection=False; Encrypt=True;
```
## <a name="disaster-recovery-for-hyperscale-databases"></a>Zotavení po havárii pro databáze hyperškálovatelný systém
### <a name="restoring-a-hyperscale-database-to-a-different-geography"></a>Obnovení databáze Hyperškálovatelného na jiném datovém typu geography.
Pokud budete potřebovat k obnovení Azure SQL Database Hyperškálovatelného DB do jiné oblasti, než ten, který je aktuálně hostovaný, v rámci operace zotavení po havárii nebo procházení, přemístění nebo z jiného důvodu, hlavní metodou je provést geografické obnovení databáze.  To zahrnuje přesně stejný postup jako byste použili pro jakékoli jiné služby AZURE SQL DB obnovit do jiné oblasti:
1. Vytvořte server služby SQL Database v cílové oblasti, pokud již nemáte příslušný server existuje.  Tento server musí být ve stejném předplatném jako původní server (zdroj) ve vlastnictví.
2. Postupujte podle pokynů [geografické obnovení](https://docs.microsoft.com/azure/sql-database/sql-database-recovery-using-backups#geo-restore) téma na stránce o obnovení databází Azure SQL Database z automatických záloh.

#### <a name="notes-on-geo-restores-of-a-hyperscale-database"></a>Poznámky k geografické obnovení mírou škálování databáze
Protože zdroj a cíl nacházejí v oblastech, databázi nelze sdílet úložiště snímku s zdrojové databáze stejně jako v jiné geografické obnovení, které velmi rychlé dokončení.  V případě geografického obnovení databáze hyperškálovatelný systém bude velikost datové operace, i v případě, že cíl je v párované oblasti geograficky replikovaného úložiště.  To znamená, že provedení geografického obnovení bude trvat dobu přímo úměrná velikosti databáze, který se má obnovit.  Pokud je cílem v párované oblasti, kopie bude v rámci datového centra, která bude mnohem rychlejší než kopii přestupný přes internet, ale budou stále zkopírovány všechny bity.

## <a name=regions></a>Dostupné oblasti

Azure SQL Database Hyperškálovatelného úroveň je momentálně dostupná v těchto oblastech:

- Austrálie – východ
- Austrálie – jihovýchod
- Brazílie – jih
- Kanada – střed
- USA – střed
- Čína – východ 2
- Čína – sever 2
- Východní Asie
- USA – východ
- Východ USA 2
- Francie – střed
- Japonsko – východ
- Japonsko – západ
- Korea – střed
- Jižní Korea – jih
- Středoseverní USA
- Severní Evropa
- Jižní Afrika – sever
- Středojižní USA
- Jihovýchodní Asie
- Velká Británie – jih
- Spojené království – západ
- Západní Evropa
- Západní USA
- Západní USA 2

Pokud chcete vytvořit databázi Hyperškálovatelného v oblasti, který není označen jako podporovaný, můžete odeslat požadavek registrace prostřednictvím portálu Azure portal. Pracujeme na rozbalte seznam podporovaných oblastí proto prosím zkontrolujte zpět pro nejnovější seznam oblastí.

Požádat o možnost vytvoření Hyperškálovatelného databází v oblastech, které nejsou uvedené:

1. Přejděte na [Azure Nápověda a podpora](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview)

2. Klikněte na [ **nová žádost o podporu**](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

    ![Azure Nápověda a podpora](media/sql-database-service-tier-hyperscale/whitelist-request-screen-1.png)

3. Pro **typ problému**vyberte **limity služby a předplatného (kvóty)**

4. Zvolte předplatné, které můžete použít k vytvoření databáze

5. Pro **typ kvóty**vyberte **SQL database**

6. Klikněte na tlačítko **Další: Řešení**

1. Klikněte na tlačítko **zadejte podrobnosti**

    ![Podrobnosti o problému](media/sql-database-service-tier-hyperscale/whitelist-request-screen-2.png)

8. Zvolte **typ kvóty SQL databáze**: **Jiná žádost o kvótu**

9. Vyplňte následující šablony:

    ![Podrobnosti o kvótě](media/sql-database-service-tier-hyperscale/whitelist-request-screen-3.png)

    V šabloně zadejte následující informace

    > Požadavek na vytvoření databáze SQL Azure Hyperškálovatelného v nové oblasti<br/> Oblast: [Vyplňte požadovaná oblast]  <br/>
    > COMPUTE SKU/celkem jader včetně čitelné repliky <br/>
    > Počet předpokládaných TB 
    >

10. Zvolte **Severity C** (Závažnost C).

11. Vyberte vhodnou metodu kontaktu a vyplňte podrobnosti.

12. Klikněte na tlačítko **Uložit** a **pokračovat**

## <a name="known-limitations"></a>Známá omezení
Jedná se o aktuální omezení na vrstvu služby Hyperškálovatelného od obecné dostupnosti.  Aktivně pracujeme na odebrat libovolný počet těchto omezení, jako je to možné.

| Problém | Popis |
| :---- | :--------- |
| V podokně Správa zálohování pro logický server nezobrazují Hyperškálovatelného databáze bude filtrováno ze serveru SQL server ->  | Velkokapacitní má samostatné metodě pro správu zálohování a jako takový dlouhodobé uchovávání dat a bod v nastavení uchovávání záloh čas se nevztahují / nejsou zneplatněny. Podle toho Hyperškálovatelného databází se nezobrazují v podokně Správa zálohování. |
| Obnovení k určitému bodu v čase | Po migraci databáze do vrstvy služby hyperškálovatelný systém se nepodporuje obnovení k určitému bodu v čase před migrací.|
| Obnovení z jiných – velkokapacitní DB Hypserscale (a naopak) | Velkokapacitní databáze nelze obnovit do databáze hyperškálovatelný systém ani mohli obnovit databáze hyperškálovatelný systém do databáze Hyperškálováním.|
| Pokud je soubor databáze roste během migrace z důvodu aktivní úlohy a překročí 1 TB za hranice souboru, se migrace nezdaří | Omezení rizik: <br> – Pokud je to možné, migrace databáze, pokud neexistuje žádné aktualizace zátěži.<br> – Zkuste to znovu migrace, bude úspěšné, tak dlouho, dokud není překročí hranice 1 TB během migrace.|
| MI | Azure SQL Database Managed Instance se momentálně nepodporuje s mnoha databázemi. |
| Elastické fondy |  Elastické fondy nejsou aktuálně podporovány s Hyperškálováním databáze SQL.|
| Migrace do Hyperškálovatelného je aktuálně Jednosměrná operace | Po migraci databáze na Hyperškálovatelného, není možné migrovat přímo do úrovně služeb-mírou škálování. V současné době je jediný způsob, jak migrovat databázi z Hyperškálovatelného do bez Hyperškálovatelného export a import pomocí souborů BACPAC.|
| Migrace databází pomocí objektů v paměti | Třeba vyřadit a znovu vytvořen jako objekty bez v paměti před migrací databáze na vrstvu služby Hyperškálovatelného objektů v paměti.|
| Data sledování změn | Nebudete moct pomocí řešení Change Data Tracking s mnoha databázemi. |
| Geografická replikace  | Zatím nelze konfigurace geografické replikace pro Azure SQL Database Hyperškálovatelného.  Můžete provést geografické obnovení, (obnovení databáze v jiné geografické oblasti, pro zotavení po Havárii nebo pro jiné účely) |
| TDE/AKV Integration | Transparentnímu šifrování dat pomocí služby Azure Key Vault (obvykle označuje jako Bring-Your-vlastní-Key nebo BYOK) se ještě nepodporuje pro Azure SQL Database Hyperškálovatelného však transparentní šifrování dat s použitím klíčů spravovaných Service je plně podporovaná. |
|Inteligentní databázové funkce | 1. Vytvořit Index, Drop Index Poradce modely nejsou školení pro Hyperškálovatelného databází. <br/>2. Problém schématu, DbParameterization – nedávno přidaly poradci nejsou podporovány pro Hyperškálovatelného databáze.|



## <a name="next-steps"></a>Další postup

- Nejčastější dotazy na hyperškálovatelný systém, najdete v části [nejčastější dotazy ohledně Hyperškálovatelného](sql-database-service-tier-hyperscale-faq.md).
- Informace o úrovních služeb najdete v tématu [úrovně služeb](sql-database-service-tiers.md)
- Zobrazit [Přehled prostředků omezuje na logickém serveru](sql-database-resource-limits-logical-server.md) informace o omezeních na úrovni serveru a předplatné.
- Zakoupení modelu omezení pro jednu databázi, naleznete v tématu [založený na virtuálních jádrech zakoupení modelu omezení pro jednu databázi Azure SQL Database](sql-database-vcore-resource-limits-single-databases.md).
- Pro funkce a seznam porovnání, naleznete v tématu [běžné funkce SQL](sql-database-features.md).
