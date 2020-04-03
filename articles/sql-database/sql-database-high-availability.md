---
title: Vysoká dostupnost
description: Informace o možnostech a funkcích služby Azure SQL Database s vysokou dostupností
services: sql-database
ms.service: sql-database
ms.subservice: high-availability
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: sashan
ms.author: sashan
ms.reviewer: carlrab, sashan
ms.date: 04/02/2020
ms.openlocfilehash: 1c4ed77112e8c06db1946d756239e02cb187f3ef
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80618511"
---
# <a name="high-availability-and-azure-sql-database"></a>Vysoká dostupnost a azure SQL databáze

Cílem architektury vysoké dostupnosti v Azure SQL Database je zaručit, že vaše databáze je v provozu minimálně 99,99 % času (Další informace týkající se konkrétní sla pro různé úrovně naleznete [ve službě SLA pro Azure SQL Database](https://azure.microsoft.com/support/legal/sla/sql-database/)), aniž byste se museli obávat dopadu operací údržby a výpadků. Azure automaticky zpracovává důležité úlohy údržby, jako jsou opravy, zálohy, upgrady Windows a SQL, stejně jako neplánované události, jako je základní hardware, software nebo selhání sítě.  Když je základní instance SQL opravena nebo převezme služby při selhání, prostoje není patrné, pokud [používáte logiku opakování](sql-database-develop-overview.md#resiliency) ve vaší aplikaci. Azure SQL Database můžete rychle obnovit i v těch nejkritičtějších okolností zajistit, že vaše data jsou vždy k dispozici.

Řešení s vysokou dostupností je navrženo tak, aby zajistilo, že potvrzená data nebudou nikdy ztracena z důvodu selhání, že operace údržby neovlivní vaše pracovní vytížení a že databáze nebude jediným bodem selhání v architektuře softwaru. Neexistují žádná okna údržby nebo prostoje, které by měly vyžadovat zastavení úlohy při upgradu nebo údržbě databáze. 

Existují dva architektury vysoké dostupnosti modely, které se používají v Azure SQL Database:

- Standardní model dostupnosti, který je založen na oddělení výpočetních prostředků a úložiště.  Spoléhá se na vysokou dostupnost a spolehlivost úrovně vzdáleného úložiště. Tato architektura se zaměřuje na obchodní aplikace orientované na rozpočet, které mohou tolerovat určité snížení výkonu během činností údržby.
- Model dostupnosti Premium, který je založen na clusteru procesů databázového stroje. Spoléhá se na skutečnost, že vždy existuje kvorum dostupných uzlů databázového stroje. Tato architektura se zaměřuje na klíčové aplikace s vysokým výkonem vi, vysokou rychlostí transakcí a zaručuje minimální dopad na výkon vašeho pracovního vytížení během činností údržby.

Azure SQL Database běží na nejnovější stabilní verzi SQL Server Database Engine a Operační systém Windows a většina uživatelů by si nevšiml, že upgrady jsou prováděny nepřetržitě.

## <a name="basic-standard-and-general-purpose-service-tier-availability"></a>Základní, standardní a obecná dostupnost úrovně služeb

Tyto úrovně služeb využívají architekturu standardní dostupnosti. Následující obrázek znázorňuje čtyři různé uzly s vrstvami oddělených výpočetních prostředků a úložiště.

![Oddělení výpočetních prostředků a úložiště](media/sql-database-high-availability/general-purpose-service-tier.png)

Standardní model dostupnosti obsahuje dvě vrstvy:

- Bezstavová výpočetní vrstva, která spouští `sqlservr.exe` proces a obsahuje pouze přechodná data a data uložená v mezipaměti, jako je například TempDB, databáze modelů na připojeném ssd a plánování mezipaměti, fondu vyrovnávací paměti a fondu columnstore v paměti. Tento bezstavový uzel je provozován Azure Service Fabric, který inicializuje `sqlservr.exe`, řídí stav uzlu a v případě potřeby provede převzetí služeb při selhání do jiného uzlu.
- Stavová datová vrstva s databázovými soubory (.mdf/.ldf), které jsou uložené v úložišti objektů Blob Azure. Úložiště objektů blob Azure má integrovanou funkci dostupnosti dat a redundance. Zaručuje, že každý záznam v souboru protokolu nebo stránky v datovém souboru bude zachována i v případě, že dojde k chybě procesu SQL Server.

Kdykoli je upgradován databázový stroj nebo operační systém nebo je zjištěna chyba, Azure Service Fabric přesune bezstavový proces SQL Server do jiného bezstavového výpočetního uzlu s dostatečnou volnou kapacitou. Data v úložišti objektů blob Azure nejsou přesunutím ovlivněna a soubory dat/protokolu jsou připojeny k nově inicializovanému procesu serveru SQL Server. Tento proces zaručuje 99,99 % dostupnost, ale velké zatížení může dojít k určité snížení výkonu během přechodu od nové instance SQL Server začíná studené mezipaměti.

## <a name="premium-and-business-critical-service-tier-availability"></a>Dostupnost úrovně služeb Premium a Business Critical

Úrovně služeb Premium a Business Critical využívají model dostupnosti Premium, který integruje výpočetní prostředky (proces databázového stroje SQL Serveru) a úložiště (místně připojené SSD) na jednom uzlu. Vysoké dostupnosti je dosaženo replikací výpočetních prostředků i úložiště do dalších uzlů, které vytvářejí cluster se třemi až čtyřmi uzly. 

![Cluster uzlů databázového stroje](media/sql-database-high-availability/business-critical-service-tier.png)

Podkladové databázové soubory (.mdf/.ldf) jsou umístěny na připojeném úložišti SSD, aby poskytovaly vstupně-výstupní řízení s velmi nízkou latencí pro vaše úlohy. Vysoká dostupnost je implementována pomocí technologie podobné SQL Server [always on Availability Groups](https://docs.microsoft.com/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cluster obsahuje jednu primární repliku (proces SQL Serveru), která je přístupná pro úlohy zákazníků pro čtení a zápis, a až tři sekundární repliky (výpočetní prostředky a úložiště) obsahující kopie dat. Primární uzel neustále odesílá změny do sekundárních uzlů v pořadí a zajišťuje, že data jsou synchronizována alespoň do jedné sekundární repliky před potvrzením každé transakce. Tento proces zaručuje, že pokud primární uzel dojde k chybě z jakéhokoli důvodu, je vždy plně synchronizovaný uzel převzetí služeb při selhání. Převzetí služeb při selhání je iniciováno azure service fabric. Jakmile se sekundární replika stane novým primárním uzlem, vytvoří se další sekundární replika, která zajistí, že cluster má dostatek uzlů (sada kvora). Po dokončení převzetí služeb při selhání jsou připojení SQL automaticky přesměrována na nový primární uzel.

Jako další výhodu model dostupnosti premium zahrnuje možnost přesměrovat připojení SQL jen pro čtení do jedné ze sekundárních replik. Tato funkce se nazývá [Horizontální navýšení kapacity pro čtení](sql-database-read-scale-out.md). Poskytuje 100 % další výpočetní kapacity bez příplatku k off-load jen pro čtení operace, jako jsou analytické úlohy, z primární repliky.

## <a name="hyperscale-service-tier-availability"></a>Dostupnost úrovně služby hyperškálování

Architektura úrovně služby Hyperscale je popsána v [architektuře distribuovaných funkcí](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#distributed-functions-architecture). 

![Hyperscale funkční architektura](./media/sql-database-hyperscale/hyperscale-architecture.png)

Model dostupnosti v hyperškálování obsahuje čtyři vrstvy:

- Bezstavová výpočetní vrstva, která spouští `sqlservr.exe` procesy a obsahuje pouze přechodná data a data uložená v mezipaměti, jako je například nezakrývaná mezipaměť RBPEX, databáze tempdb, databáze modelu atd. Tato vrstva bez stavů obsahuje primární výpočetní repliku a volitelně počet sekundárních výpočetních replik, které mohou sloužit jako cíle převzetí služeb při selhání.
- Bezstavová vrstva úložiště tvořená stránkovacími servery. Tato vrstva je modul `sqlservr.exe` distribuovaného úložiště pro procesy spuštěné na výpočetních replikách. Každý stránkový server obsahuje pouze přechodná data a data uložená v mezipaměti, například pokrytí mezipaměti RBPEX na připojeném ssd disporu a datové stránky uložené v paměti. Každý stránkový server má spárovaný stránkový server v konfiguraci aktivní a aktivní, který poskytuje vyrovnávání zatížení, redundanci a vysokou dostupnost.
- Stavová vrstva úložiště protokolu transakcí vytvořená výpočetním uzlem, na kterých běží proces služby Protokol, vstupní zóna transakčního protokolu a dlouhodobé úložiště transakčního protokolu. Vstupní zóna a dlouhodobé úložiště používají Azure Storage, které poskytuje dostupnost a [redundanci](https://docs.microsoft.com/azure/storage/common/storage-redundancy) pro transakční protokol a zajišťuje odolnost dat pro potvrzené transakce.
- Stavová vrstva úložiště dat s databázovými soubory (.mdf/.ndf), které jsou uložené ve službě Azure Storage a jsou aktualizovány servery stránek. Tato vrstva využívá funkce dostupnosti dat a [redundance](https://docs.microsoft.com/azure/storage/common/storage-redundancy) azure storage. Zaručuje, že každá stránka v datovém souboru bude zachována i v případě, že procesy v jiných vrstvách selhání architektury Hyperscale nebo pokud výpočetní uzly selžou.

Výpočetní uzly ve všech vrstvách Hyperscale běží na Azure Service Fabric, který řídí stav každého uzlu a provádí převzetí služeb při selhání na dostupné uzly v pořádku podle potřeby.

Další informace o vysoké dostupnosti v hyperškálování naleznete v [tématu Databáze vysoká dostupnost v hyperškálování](https://docs.microsoft.com/azure/sql-database/sql-database-service-tier-hyperscale#database-high-availability-in-hyperscale).

## <a name="zone-redundant-configuration"></a>Zónová redundantní konfigurace

Ve výchozím nastavení je cluster uzlů pro model dostupnosti premium vytvořen ve stejném datovém centru. Se zavedením [zón dostupnosti Azure](../availability-zones/az-overview.md)může databáze SQL umístit různé repliky databáze Business Critical do různých zón dostupnosti ve stejné oblasti. Chcete-li odstranit jeden bod selhání, ovládací kroužek je také duplikován přes více zón jako tři brány kroužky (GW). Směrování na konkrétní kroužek brány řídí [Azure Traffic Manager](../traffic-manager/traffic-manager-overview.md) (ATM). Vzhledem k tomu, že zónová redundantní konfigurace v úrovních služby Premium nebo Business Critical nevytváří další redundanci databáze, můžete ji povolit bez dalších nákladů. Výběrem redundantní konfigurace zóny můžete nastavit, aby vaše databáze Premium nebo Business Critical byly odolné vůči mnohem větší sadě selhání, včetně katastrofických výpadků datového centra, bez jakýchkoli změn v logice aplikace. Můžete také převést všechny existující databáze premium nebo důležité pro podnikání kritické nebo fondy do zóny redundantní konfigurace.

Vzhledem k tomu, že zóna redundantní databáze mají repliky v různých datových centrech s určitou vzdálenost mezi nimi, zvýšená latence sítě může zvýšit dobu potvrzení a tím ovlivnit výkon některých úloh OLTP. Vždy se můžete vrátit do konfigurace jedné zóny zakázáním nastavení redundance zóny. Tento proces je online operace podobná pravidelné upgradu úrovně služby. Na konci procesu je databáze nebo fond migrován z zóny redundantní kroužek do jedné zóny kruhu nebo naopak.

> [!IMPORTANT]
> Zónově redundantní databáze a elastické fondy jsou aktuálně podporovány pouze v úrovních služeb Premium a Business Critical ve vybraných oblastech. Při použití úrovně Business Critical je redundantní konfigurace zóny dostupná pouze v případě, že je vybrán výpočetní hardware Gen5. Aktuální informace o oblastech, které podporují zónové redundantní databáze, najdete v [tématu Podpora služeb podle oblastí](../availability-zones/az-overview.md#services-support-by-region).  
> Tato funkce není ve spravované instanci dostupná.

Zóna redundantní verze architektury vysoké dostupnosti je znázorněna na následujícím diagramu:

![zóna architektury s vysokou dostupností je redundantní](./media/sql-database-high-availability/zone-redundant-business-critical-service-tier.png)

## <a name="accelerated-database-recovery-adr"></a>Zrychlené obnovení databáze (ADR)

[Zrychlené obnovení databáze (ADR)](sql-database-accelerated-database-recovery.md) je nová funkce databázového stroje SQL, která výrazně zlepšuje dostupnost databáze, zejména v přítomnosti dlouhotrvajících transakcí. ADR je aktuálně k dispozici pro jednotlivé databáze, elastické fondy a Azure SQL Data Warehouse.

## <a name="testing-application-fault-resiliency"></a>Testování odolnosti proti chybám aplikace

Vysoká dostupnost je základní součástí platformy Azure SQL Database, která funguje transparentně pro databázovou aplikaci. Uvědomujeme si však, že můžete chtít otestovat, jak by automatické operace převzetí služeb při selhání zahájené během plánovaných nebo neplánovaných událostí ovlivnily aplikaci před nasazením do produkčního prostředí. Můžete volat speciální rozhraní API restartovat databázi nebo elastický fond, který bude zase aktivovat převzetí služeb při selhání. V případě zóny redundantní databáze nebo elastického fondu by volání rozhraní API mělo za následek přesměrování připojení klientů na nové primární v zóně dostupnosti odlišné od zóny dostupnosti staré primární. Takže kromě testování, jak převzetí služeb při selhání ovlivňuje existující relace databáze, můžete také ověřit, zda změní výkon od konce na konec z důvodu změn latence sítě. Vzhledem k tomu, že operace restartování je rušivá a velký počet z nich může zdůraznit platformu, pouze jedno volání převzetí služeb při selhání je povoleno každých 30 minut pro každou databázi nebo elastický fond. 

Převzetí služeb při selhání lze iniciovat pomocí rozhraní REST API nebo prostředí PowerShell. Rozhraní REST API naleznete v [tématu převzetí služeb při selhání databáze](https://docs.microsoft.com/rest/api/sql/databases(failover)/failover) a převzetí služeb při selhání [elastického fondu](https://docs.microsoft.com/rest/api/sql/elasticpools(failover)/failover). V prostředí PowerShell naleznete v [tématech Invoke-AzSqlDatabaseFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqldatabasefailover) a [Invoke-AzSqlElasticPoolFailover](https://docs.microsoft.com/powershell/module/az.sql/invoke-azsqlelasticpoolfailover). Volání rozhraní API REST lze také z Azure CLI pomocí [příkazu az rest.](https://docs.microsoft.com/cli/azure/reference-index?view=azure-cli-latest#az-rest)

> [!IMPORTANT]
> Příkaz Převzetí služeb při selhání není aktuálně k dispozici ve vrstvě služby Hyperscale a pro spravovanou instanci.

## <a name="conclusion"></a>Závěr

Azure SQL Database obsahuje integrované řešení s vysokou dostupností, které je hluboce integrované s platformou Azure. Je závislá na Service Fabric pro zjišťování a obnovení selhání, na azure blob úložiště pro ochranu dat a na zónách dostupnosti pro vyšší odolnost proti chybám. Kromě toho azure SQL databáze využívá vždy na dostupnost skupiny technologie z SQL Server pro replikaci a převzetí služeb při selhání. Kombinace těchto technologií umožňuje aplikacím plně realizovat výhody modelu smíšeného úložiště a podporovat i ty nejnáročnější služby SLA.

## <a name="next-steps"></a>Další kroky

- Informace o [zónách dostupnosti Azure](../availability-zones/az-overview.md)
- Další informace o [službě Fabric](../service-fabric/service-fabric-overview.md)
- Další informace o [Azure Traffic Manageru](../traffic-manager/traffic-manager-overview.md)
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete [v tématu Kontinuita provozu](sql-database-business-continuity.md)
