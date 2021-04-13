---
title: Vysoká dostupnost
titleSuffix: Azure SQL Database and SQL Managed Instance
description: Seznamte se s funkcemi a funkcemi vysoké dostupnosti služby spravované instance SQL Azure SQL Database a SQL
services: sql-database
ms.service: sql-db-mi
ms.subservice: high-availability
ms.custom: sqldbrb=2
ms.devlang: ''
ms.topic: conceptual
author: emlisa
ms.author: emlisa
ms.reviewer: sstein, emlisa
ms.date: 10/28/2020
ms.openlocfilehash: 7204f32b8fec411fba2afa39b011a8755aea3744
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2021
ms.locfileid: "107309675"
---
# <a name="high-availability-for-azure-sql-database-and-sql-managed-instance"></a>Vysoká dostupnost pro Azure SQL Database a SQL Managed instance
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Cílem architektury vysoké dostupnosti v Azure SQL Database a spravované instance SQL je zaručit, že vaše databáze bude v provozu až 99,99% času (více informací týkajících se konkrétní smlouvy SLA pro různé úrovně, přečtěte si [smlouvu SLA pro Azure SQL Database a SQL Managed instance](https://azure.microsoft.com/support/legal/sla/sql-database/)), aniž byste se museli starat o dopad operací údržby a výpadků. Azure automaticky zpracovává důležité úlohy údržby, jako jsou třeba opravy, zálohování, upgrade Windows a Azure SQL, a také neplánované události, jako je například základní hardware, software nebo selhání sítě.  Pokud je podkladová databáze v Azure SQL Database opravena nebo převezme služby při selhání, nemůžete si všimnout, pokud ve své aplikaci použijete [logiku opakování](develop-overview.md#resiliency) . SQL Database a spravovaná instance SQL se můžou rychle zotavit i v nejdůležitějších případech, které zajistí, že vaše data jsou vždycky k dispozici.

Řešení vysoké dostupnosti je navrženo tak, aby se zajistilo, že potvrzená data nejsou nikdy ztracena z důvodu selhání, že operace údržby neovlivní vaše zatížení a že databáze nebude v rámci softwarové architektury jediným bodem selhání. Neexistují žádná časová období údržby ani výpadky, které by měly vyžadovat, abyste při upgradu nebo údržbě databáze zastavili úlohy.

K dispozici jsou dva modely architektury vysoké dostupnosti:

- **Standardní model dostupnosti** založený na oddělení výpočetních prostředků a úložiště.  Spoléhá se na vysokou dostupnost a spolehlivost vzdálené vrstvy úložiště. Tato architektura cílí na rozpočtově orientované podnikové aplikace, které mohou tolerovat některé snížení výkonu během údržby.
- **Model dostupnosti Premium** , který je založen na clusteru procesů databázového stroje. Spoléhá na to, že je vždy kvorum dostupných uzlů databázového stroje. Tato architektura cílí na kritické aplikace s vysokými nároky na vstupně-výstupní operace, vysokou přenosovou rychlostí a zaručuje minimální dopad na výkon na vaše zatížení během aktivit údržby.

SQL Database a SQL Managed instance jsou spouštěny v nejnovější stabilní verzi databázového stroje SQL Server a operačním systému Windows a většina uživatelů neoznámila průběžné upgradu.

## <a name="basic-standard-and-general-purpose-service-tier-locally-redundant-availability"></a>Místně redundantní dostupnost úrovně služeb Basic, Standard a Pro obecné účely

Úrovně služeb Basic, Standard a Pro obecné účely využívají standardní architekturu dostupnosti pro neserverový i zřízený výpočetní výkon. Následující obrázek znázorňuje čtyři různé uzly s oddělenými výpočetními a úložnými vrstvami.

![Oddělení výpočtů a úložiště](./media/high-availability-sla/general-purpose-service-tier.png)

Standardní model dostupnosti obsahuje dvě vrstvy:

- Bezstavová výpočetní vrstva, která spouští `sqlservr.exe` proces a obsahuje pouze přechodná a data uložená v mezipaměti, jako je například tempdb, modelové databáze na připojené SSD a mezipaměť plánu, fond vyrovnávací paměti a fond columnstore v paměti. Tento bezstavový uzel provozuje služba Azure Service Fabric, která inicializuje `sqlservr.exe` , ovládá stav uzlu a v případě potřeby provede převzetí služeb při selhání jiným uzlem.
- Stavová Datová vrstva s databázovými soubory (. mdf/. ldf), které jsou uložené v úložišti objektů BLOB v Azure. Služba Azure Blob Storage má vestavěnou funkci dostupnosti dat a redundance. Zaručuje, že každý záznam v souboru protokolu nebo na stránce v datovém souboru se zachová i v případě `sqlservr.exe` , že dojde k chybě procesu.

Pokaždé, když je databázový stroj nebo operační systém upgradovaný, nebo dojde k selhání, Azure Service Fabric přesune bezstavový `sqlservr.exe` proces do jiného bezstavového výpočetního uzlu s dostatečnou volnou kapacitou. Data v úložišti objektů BLOB v Azure nejsou ovlivněná přesunem a soubory dat a protokolů jsou připojené k nově inicializovanému `sqlservr.exe` procesu. Tento proces garantuje 99,99% dostupnost, ale u velkého zatížení může dojít k výraznému snížení výkonu během přechodu, protože nový `sqlservr.exe` proces začíná studenou mezipamětí.

## <a name="general-purpose-service-tier-zone-redundant-availability-preview"></a>Pro obecné účely redundantní dostupnosti zóny služby na úrovni služby (Preview)

Redundantní konfigurace zóny služby pro obecné účely se nabízí pro výpočetní prostředky bez serveru i pro zřizování. Tato konfigurace využívá [zóny dostupnosti Azure](../../availability-zones/az-overview.md)   k replikaci databází v různých fyzických umístěních v oblasti Azure.Když vyberete redundanci zóny, můžete vytvořit nové a stávající serverlesss a zřídit samostatné databáze pro obecné účely a elastické fondy odolné vůči mnohem větším chybám, včetně závažných výpadků Datacenter, a to bez jakýchkoli změn v aplikační logice.

Redundantní konfigurace zóny pro vrstvu pro obecné účely má dvě vrstvy:  

- Stavová Datová vrstva s databázovými soubory (. mdf/. ldf), které jsou uložené v ZRS (úložiště redundantní v zóně). Pomocí [ZRS](../../storage/common/storage-redundancy.md) se soubory dat a protokolů synchronně kopírují do tří zón dostupnosti Azure, které jsou fyzicky izolované.
- Bezstavová výpočetní vrstva, která spouští proces sqlservr.exe a obsahuje pouze přechodná a data uložená v mezipaměti, jako je například TempDB, modelové databáze na připojené SSD a mezipaměť plánu, fond vyrovnávací paměti a fond columnstore v paměti. Tento bezstavový uzel je provozován službou Azure Service Fabric, která inicializuje sqlservr.exe, řídí stav uzlu a v případě potřeby provádí převzetí služeb při selhání jiným uzlem. V případě redundantních databází bez serveru a zřízené databáze pro obecné účely jsou uzly s volnou kapacitou snadno dostupné v jiných Zóny dostupnosti pro převzetí služeb při selhání.

Redundantní verze architektury vysoké dostupnosti pro úroveň služby pro obecné účely je znázorněná v následujícím diagramu:

![Redundantní konfigurace zóny pro obecné účely](./media/high-availability-sla/zone-redundant-for-general-purpose.png)

> [!IMPORTANT]
> Redundantní konfigurace zóny je dostupná jenom v případě, že je vybraný Gen5 výpočetní hardware. Tato funkce není k dispozici ve spravované instanci SQL. Redundantní konfigurace zóny pro obecné účely bez serveru je dostupná jenom v těchto oblastech: Východní USA, Východní USA 2, Západní USA 2, Severní Evropa, Západní Evropa, jihovýchodní Asie, Austrálie – východ, Japonsko – východ, Velká Británie – jih a Francie – střed.

> [!NOTE]
> Pro obecné účely databází s velikostí 80 Vcore může dojít ke snížení výkonu s redundantní konfigurací zóny. Kromě toho se může stát, že operace, jako je zálohování, obnovení, kopírování databáze, nastavení vztahů geografického a DR, a downgrade redundantní databáze zóny z Pro důležité obchodní informace na Pro obecné účely můžou mít pomalejší výkon pro všechny izolované databáze větší než 1 TB. Další informace najdete v [dokumentaci k naší latenci týkající se škálování databáze](single-database-scale.md) .
> 
> [!NOTE]
> Verze Preview se v rámci rezervované instance nezabývá.

## <a name="premium-and-business-critical-service-tier-locally-redundant-availability"></a>Úroveň služeb Premium a Pro důležité obchodní informace místně redundantní dostupnost

Úrovně služeb Premium a Pro důležité obchodní informace využívají model dostupnosti Premium, který integruje výpočetní prostředky ( `sqlservr.exe` procesy) a úložiště (místně připojené SSD) na jeden uzel. Vysoká dostupnost se dosahuje replikací výpočetních prostředků i úložiště do dalších uzlů vytvářejících cluster se čtyřmi uzly.

![Cluster uzlů databázového stroje](./media/high-availability-sla/business-critical-service-tier.png)

Podkladové soubory databáze (. mdf/. ldf) jsou umístěné v připojeném úložišti SSD, aby bylo možné zajistit velmi nízkou latenci v/v pro vaše zatížení. Vysoká dostupnost se implementuje pomocí technologie podobné SQL Server [skupinám dostupnosti Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server). Cluster obsahuje jednu primární repliku, která je přístupná pro úlohy zákazníka pro čtení i zápis, a to až ze tří sekundárních replik (výpočetní výkon a úložiště), které obsahují kopie dat. Primární uzel průběžně přenáší změny do sekundárních uzlů v pořadí a před potvrzením každé transakce zajišťuje, aby byla data synchronizována do alespoň jedné sekundární repliky. Tento postup zaručuje, že pokud dojde k selhání primárního uzlu z jakéhokoli důvodu, je vždy plně synchronizovaný uzel, který převezme služby při selhání. Převzetí služeb při selhání iniciuje Service Fabric Azure. Jakmile se sekundární replika pokusí o nový primární uzel, vytvoří se další sekundární replika, která zajistí, že cluster má dostatek uzlů (sada kvora). Po dokončení převzetí služeb při selhání se připojení Azure SQL automaticky přesměrují do nového primárního uzlu.

Model dostupnosti Premium navíc nabízí možnost přesměrovat připojení Azure SQL, která jsou jen pro čtení, na jednu ze sekundárních replik. Tato funkce se nazývá horizontální navýšení [kapacity čtení](read-scale-out.md). Poskytuje 100% dodatečnou výpočetní kapacitu bez dalších poplatků za vypínání operací jen pro čtení, jako jsou analytické úlohy, z primární repliky.

## <a name="premium-and-business-critical-service-tier-zone-redundant-availability"></a>Redundantní dostupnost zóny úrovně služeb Premium a Pro důležité obchodní informace 

Ve výchozím nastavení se cluster uzlů pro model dostupnosti Premium vytvoří ve stejném datovém centru. Při zavedení [zóny dostupnosti Azure](../../availability-zones/az-overview.md)můžou SQL Database umístit různé repliky databáze pro důležité obchodní informace do různých zón dostupnosti ve stejné oblasti. Chcete-li eliminovat jediný bod selhání, je řídicí kanál také duplikován v několika zónách jako tři prstence brány (GS). Směrování na konkrétního okruhu brány se řídí službou [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md) (ATM). Vzhledem k tomu, že zóna redundantní konfigurace v úrovních služby Premium nebo Pro důležité obchodní informace nevytváří další redundanci databáze, můžete ji povolit bez dalších poplatků. Výběrem redundantní konfigurace zóny můžete zajistit, aby vaše databáze Premium nebo Pro důležité obchodní informace odolné vůči mnohem většímu počtu selhání, včetně závažných výpadků Datacenter, a to bez jakýchkoli změn v aplikační logice. Můžete také převést všechny existující databáze nebo fondy Premium nebo Pro důležité obchodní informace na zónu redundantní konfigurace.

Vzhledem k tomu, že redundantní databáze zóny mají v různých datových centrech repliky s určitou vzdáleností, zvýšení latence sítě může zvýšit dobu potvrzení a tím ovlivnit výkon některých OLTP úloh. Do konfigurace s jednou zónou se můžete kdykoli vrátit tím, že zakážete nastavení redundance zóny. Tento proces je online operace podobná standardnímu upgradu vrstvy služeb. Na konci procesu je databáze nebo fond migrován z zóny redundantního vyzvánění do jediného okruhu zóny nebo naopak.

> [!IMPORTANT]
> Při použití Pro důležité obchodní informace úrovně je redundantní konfigurace zóny dostupná jenom v případě, že je vybraný Gen5 výpočetní hardware. Aktuální informace o oblastech, které podporují redundantní databáze zóny, najdete v tématu [Podpora služeb v jednotlivých oblastech](../../availability-zones/az-region.md).

> [!NOTE]
> Tato funkce není k dispozici ve spravované instanci SQL.

Redundantní verze architektury s vysokou dostupností v zóně je znázorněna v následujícím diagramu:

![redundantní zóna architektury vysoké dostupnosti](./media/high-availability-sla/zone-redundant-business-critical-service-tier.png)


## <a name="hyperscale-service-tier-availability"></a>Dostupnost vrstvy služeb s škálovatelným škálováním

Architektura vrstvy služeb s více instancemi je popsaná v tématu [Architektura distribuovaných funkcí](./service-tier-hyperscale.md#distributed-functions-architecture) a je aktuálně dostupná jenom pro SQL Database, nikoli pro SPRAVOVANOU instanci SQL.

![Funkční architektura s škálovatelným škálováním](./media/high-availability-sla/hyperscale-architecture.png)

Model dostupnosti v rámci škálování zahrnuje čtyři vrstvy:

- Bezstavová výpočetní vrstva, která spouští `sqlservr.exe` procesy a obsahuje pouze přechodná data a data uložená v mezipaměti, jako například nepokrývání mezipaměti RBPEX cache, tempdb, modelová databáze atd. na připojené SSD a v paměti plánu, fondu vyrovnávacích pamětí a fondu columnstore. Tato Bezstavová vrstva zahrnuje primární repliku COMPUTE a volitelně také počet sekundárních výpočetních replik, které můžou sloužit jako cíle převzetí služeb při selhání.
- Bezstavová vrstva úložiště vytvořená stránkami serverů. Tato vrstva je distribuovaný modul úložiště pro `sqlservr.exe` procesy spuštěné ve výpočetních replikách. Každý server stránky obsahuje jenom přechodná data a data uložená v mezipaměti, jako je třeba pokrytí RBPEX cache na připojené SSD a datových stránek uložených v paměti. Každý server stránky má spárovaný stránkovací Server v konfiguraci aktivní-aktivní, aby poskytoval vyrovnávání zatížení, redundanci a vysokou dostupnost.
- Vrstva úložiště protokolu stavových transakcí vytvořená výpočetním uzlem, na kterém běží proces služby protokolování, zóna pro vykládku protokolu transakcí a dlouhodobé ukládání transakčního protokolu. Cílová zóna a dlouhodobé úložiště používají Azure Storage, což poskytuje dostupnost a [redundanci](../../storage/common/storage-redundancy.md) transakčního protokolu, což zajišťuje odolnost dat pro potvrzené transakce.
- Stavová vrstva úložiště dat s databázovými soubory (. mdf/. NDF), které jsou uložené v Azure Storage a jsou aktualizovány pomocí stránkových serverů. Tato vrstva používá funkce pro dostupnost a [redundanci](../../storage/common/storage-redundancy.md) dat Azure Storage. Zaručuje, že každá stránka v datovém souboru se zachová i v případě, že procesy v jiných vrstvách selže nebo dojde k selhání výpočetních uzlů.

Výpočetní uzly ve všech vrstvách s škálovatelným škálováním běží na Azure Service Fabric, které řídí stav jednotlivých uzlů a v případě potřeby provádějí převzetí služeb při selhání pro dostupné uzly v pořádku.

Další informace o vysoké dostupnosti v měřítku najdete v tématu [Vysoká dostupnost databáze v měřítku](./service-tier-hyperscale.md#database-high-availability-in-hyperscale).


## <a name="accelerated-database-recovery-adr"></a>Urychlené obnovení databáze (ADR)

[Zrychlené obnovení databáze (ADR)](../accelerated-database-recovery.md) je nová funkce databázového stroje, která významně vylepšuje dostupnost databáze, zejména v případě dlouhotrvajících transakcí. Pravidla automatického nasazení jsou v tuto chvíli k dispozici pro Azure SQL Database, Azure SQL Managed instance a Azure synapse Analytics.

## <a name="testing-application-fault-resiliency"></a>Testování odolnosti proti chybám aplikace

Vysoká dostupnost je základní součástí služby SQL Database a platformy SQL Managed Instance, která funguje pro vaši databázovou aplikaci transparentně. Nicméně chápeme, že možná budete chtít otestovat, jaký budou operace automatického převzetí služeb při selhání iniciované během plánovaných nebo neplánovaných událostí mít vliv na aplikaci předtím, než ji nasadíte do produkčního prostředí. Převzetí služeb při selhání můžete aktivovat ručně voláním speciálního rozhraní API pro restartování databáze, elastického fondu nebo spravované instance. V případě zóny redundantního serveru nebo databáze zřízené Pro obecné účely a elastického fondu by volání rozhraní API mělo za následek přesměrování připojení klientů k nové primární databázi v zóně dostupnosti odlišnou od zóny dostupnosti staré primární služby. Takže kromě testování toho, jak převzetí služeb při selhání ovlivňuje stávající databázové relace, můžete také ověřit, jestli se v důsledku změn v latenci sítě změní na koncový výkon. Vzhledem k tomu, že operace restartování je rušivá a velké množství těchto prostředků by mohlo navýšit platformu, pro každou databázi, elastický fond nebo spravovanou instanci je každých 15 minut povoleno pouze jedno volání převzetí služeb při selhání.

Převzetí služeb při selhání se dá iniciovat pomocí PowerShellu, REST API nebo Azure CLI:

|Typ nasazení|PowerShell|REST API| Azure CLI|
|:---|:---|:---|:---|
|databáze|[Invoke – AzSqlDatabaseFailover](/powershell/module/az.sql/invoke-azsqldatabasefailover)|[Převzetí služeb při selhání databáze](/rest/api/sql/databases/failover)|[AZ REST](/cli/azure/reference-index#az-rest) se dá použít k vyvolání volání REST API z Azure CLI.|
|Elastický fond|[Invoke – AzSqlElasticPoolFailover](/powershell/module/az.sql/invoke-azsqlelasticpoolfailover)|[Převzetí služeb při selhání elastického fondu](/javascript/api/@azure/arm-sql/elasticpools#failover_string__string__string__msRest_RequestOptionsBase)|[AZ REST](/cli/azure/reference-index#az-rest) se dá použít k vyvolání volání REST API z Azure CLI.|
|MI|[Invoke – AzSqlInstanceFailover](/powershell/module/az.sql/Invoke-AzSqlInstanceFailover/)|[Spravované instance – převzetí služeb při selhání](/rest/api/sql/managed%20instances%20-%20failover/failover)|[AZ SQL mi Failover převzetí služeb při selhání](/cli/azure/sql/mi/#az-sql-mi-failover)|

> [!IMPORTANT]
> Příkaz pro převzetí služeb při selhání není k dispozici pro čitelné sekundární repliky databází v rámci škálování.

## <a name="conclusion"></a>Závěr

Azure SQL Database a Azure SQL Managed instance jsou integrované řešení s vysokou dostupností, které je hluboko integrované s platformou Azure. Je závislý na Service Fabric pro detekci selhání a obnovení, v úložišti objektů BLOB v Azure pro ochranu dat a na Zóny dostupnosti pro zajištění vyšší odolnosti proti chybám (jak je uvedeno výše v dokumentu, který zatím není použitelný pro Azure SQL Managed instance). Kromě toho SQL Database a SQL Managed instance využívají technologii skupin dostupnosti Always On z instance SQL Server pro replikaci a převzetí služeb při selhání. Kombinace těchto technologií umožňuje aplikacím plně realizovat výhody modelu kombinovaného úložiště a podporuje nejnáročnější SLA.

## <a name="next-steps"></a>Další kroky

- Informace o [zóny dostupnosti Azure](../../availability-zones/az-overview.md)
- Informace o [Service Fabric](../../service-fabric/service-fabric-overview.md)
- Přečtěte si o [Azure Traffic Manager](../../traffic-manager/traffic-manager-overview.md)
- Naučte [se iniciovat ruční převzetí služeb při selhání na spravované instanci SQL](../managed-instance/user-initiated-failover.md) .
- Další možnosti pro vysokou dostupnost a zotavení po havárii najdete v tématu [Kontinuita podnikových aplikací](business-continuity-high-availability-disaster-recover-hadr-overview.md) .
