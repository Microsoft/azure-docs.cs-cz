---
title: Interoperabilita funkcí s SQL Server FCI & DNN
description: 'Přečtěte si o dalších ohledech při práci s některými SQL Server funkcemi a prostředku DNN (Distributed Network Name) s instancí clusteru s podporou převzetí služeb při selhání v SQL Server na virtuálních počítačích Azure. '
services: virtual-machines
documentationCenter: na
author: MashaMSFT
editor: monicar
tags: azure-service-management
ms.service: virtual-machines-sql
ms.subservice: hadr
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 06/02/2020
ms.author: mathoma
ms.openlocfilehash: 3c92aa3b35240831fad14919dc73609d803c610a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97358210"
---
# <a name="feature-interoperability-with-sql-server-fci--dnn"></a>Interoperabilita funkcí s SQL Server FCI & DNN
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

K dispozici jsou některé SQL Server funkce, které spoléhají na pevně zakódovaný název virtuální sítě (VNN). V takovém případě se při použití prostředku názvu distribuované sítě (DNN) s instancí clusteru s podporou převzetí služeb při selhání a SQL Server na virtuálních počítačích Azure vyskytly další okolnosti. 

V tomto článku se dozvíte, jak nakonfigurovat alias sítě při použití prostředku DNN a jaké SQL Server funkce vyžadují další aspekty.

## <a name="create-network-alias-fci"></a>Vytvořit alias sítě (FCI)

Některé komponenty na straně serveru jsou závislé na pevně zakódované hodnotě VNN a vyžadují alias sítě, který mapuje VNN na název DNS DNN, aby správně fungoval. Postupujte podle kroků v části [Vytvoření aliasu serveru](/sql/database-engine/configure-windows/create-or-delete-a-server-alias-for-use-by-a-client) a vytvořte alias, který mapuje vnn na název DNS DNN. 

U výchozí instance můžete mapování VNN na název DNS DNN přímo, takže VNN = DNN název DNS.
Například pokud název VNN je `FCI1` , název instance je `MSSQLSERVER` a DNN je `FCI1DNN` (klienti se dřív připojili k `FCI` a teď se k němu připojují `FCI1DNN` ), namapuje vnn `FCI1` na DNN `FCI1DNN` . 

U pojmenované instance by se mělo provést mapování síťových aliasů pro úplnou instanci, třeba `VNN\Instance`  =  `DNN\Instance` . Například pokud název VNN je `FCI1` , název instance je `instA` a DNN je `FCI1DNN` (klienti se dřív připojili k `FCI1\instA` a teď se k němu připojují `FCI1DNN\instaA` ), namapuje vnn `FCI1\instaA` na DNN `FCI1DNN\instaA` . 



## <a name="client-drivers"></a>Klientské ovladače

Pro ovladače ODBC, OLEDB, ADO.NET, JDBC, PHP a Node.js musí uživatelé explicitně zadat název DNS DNN jako název serveru v připojovacím řetězci. Pokud chcete zajistit rychlé připojení při převzetí služeb při selhání, přidejte `MultiSubnetFailover=True` do připojovacího řetězce, pokud ho klient SQL podporuje. 

## <a name="tools"></a>Nástroje

Uživatelé nástrojů [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [Sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)a [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) musí explicitně zadat název DNS DNN jako název serveru v připojovacím řetězci. 

## <a name="availability-groups-and-fci"></a>Skupiny dostupnosti a FCI

Skupinu dostupnosti Always On můžete nakonfigurovat pomocí instance clusteru s podporou převzetí služeb při selhání jako jednu z replik. V této konfiguraci musí adresa URL koncového bodu zrcadlení pro repliku FCI používat FCI DNN. Podobně platí, že pokud je FCI použit jako replika jen pro čtení, musí směrování pouze pro čtení do repliky FCI používat FCI DNN. 

Formát koncového bodu zrcadlení je: `ENDPOINT_URL = 'TCP://<DNN DNS name>:<mirroring endpoint port>'` . 

Pokud je třeba název DNS DNN `dnnlsnr` a `5022` je portem koncového bodu zrcadlení FCI, bude fragment kódu jazyka Transact-SQL (T-SQL), který vytvoří adresu URL koncového bodu, vypadat takto: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Podobně platí, že formát adresy URL směrování jen pro čtení je: `TCP://<DNN DNS name>:<SQL Server instance port>` . 

Pokud je třeba název DNS DNN `dnnlsnr` a `1444` je port používaný cílovým serverem jen pro čtení SQL Server FCI, fragment kódu T-SQL k vytvoření adresy URL směrování jen pro čtení vypadá takto: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Port v adrese URL můžete vynechat, pokud je to výchozí port 1433. U pojmenované instance nakonfigurujte statický port pro pojmenovanou instanci a zadejte ho do adresy URL směrování jen pro čtení.  

## <a name="replication"></a>Replikace

Replikace má tři komponenty: Vydavatel, distributor, odběratel. Kterákoli z těchto součástí může být instancí clusteru s podporou převzetí služeb při selhání. Vzhledem k tomu, že se FCI VNN v konfiguraci replikace intenzivně používá explicitně i implicitně, může být potřeba, aby replikace mohla být nutná k tomu, aby replikace fungovala v síťovém aliasu, který mapuje VNN na DNN. 

V rámci replikace používejte název VNN jako název FCI, ale *před konfigurací replikace* vytvořte v následujících vzdálených situacích alias sítě:

| **Součást replikace (FCI s DNN)** | **Vzdálená součást** | **Mapa aliasu sítě** | **Server s mapou sítě**| 
|---------|---------|---------|-------- | 
|Publisher | Rozdělovač | VNN vydavatele do vydavatele DNN| Rozdělovač| 
|Rozdělovač|Roli |Distributor VNN k distributorovi DNN| Roli | 
|Rozdělovač|Publisher | Distributor VNN k distributorovi DNN | Publisher| 
|Roli| Rozdělovač| Předplatitelé VNN do předplatitele DNN | Rozdělovač| 

Předpokládejme například, že máte vydavatele, který je nakonfigurovaný jako FCI pomocí DNN v topologii replikace a distributor je vzdálený. V takovém případě vytvořte alias sítě na serveru distributora pro mapování VNN vydavatele na DNN vydavatele: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-in-configuration-manager.png" alt-text="Nakonfigurujte název DNS DNN jako alias sítě pomocí SQL Server Configuration Manager." :::

Použijte úplný název instance pro pojmenovanou instanci, například následující příklad obrázku: 

:::image type="content" source="media/failover-cluster-instance-dnn-interoperability/alias-named-instance-configuration-manager.png" alt-text="Při konfiguraci aliasu sítě pro pojmenovanou instanci použijte úplný název instance." :::

## <a name="database-mirroring"></a>Zrcadlení databáze

Zrcadlení databáze můžete nakonfigurovat pomocí FCI buď jako partner zrcadlení databáze. Nakonfigurujte ji pomocí [jazyka Transact-SQL (T-SQL)](/sql/database-engine/database-mirroring/example-setting-up-database-mirroring-using-windows-authentication-transact-sql) místo SQL Server Management Studio grafického uživatelského rozhraní. Pomocí T-SQL se zajistí, že se koncový bod zrcadlení databáze vytvoří pomocí DNN namísto VNN. 

Pokud je třeba název DNS DNN `dnnlsnr` a koncový bod zrcadlení databáze je 7022, nakonfiguruje se partner zrcadlení databáze následujícím fragmentem kódu T-SQL: 

```sql
ALTER DATABASE AdventureWorks
    SET PARTNER =
    'TCP://dnnlsnr:7022'
GO 
```

Pro klientský přístup může vlastnost **partner převzetí služeb při selhání** zpracovat převzetí služeb při selhání databáze, ale ne FCI převzetí služeb při selhání. 

## <a name="msdtc"></a>NÁSTROJE

FCI se může účastnit distribuovaných transakcí koordinovaných pomocí služby Microsoft DTC (Distributed Transaction Coordinator) (MSDTC). I když se FCI DNN podporuje jak Clusterová služba MSDTC, tak místní MSDTC v Azure, je pro clusterované MSDTC stále potřeba Nástroj pro vyrovnávání zatížení. DNN definovaný v FCI nenahrazuje požadavek Azure Load Balancer pro clusterovaný koordinátor MSDTC v Azure. 

## <a name="filestream"></a>Souborem

I když je pro databázi v FCI podporovaná FileStream, přístup k FileStream nebo FileTable pomocí rozhraní API systému souborů s DNN se nepodporuje. 

## <a name="linked-servers"></a>Propojené servery

Je podporováno použití propojeného serveru s FCI DNN. Buď použijte DNN přímo ke konfiguraci propojeného serveru, nebo použijte alias sítě pro mapování VNN na DNN. 


Pokud například chcete vytvořit propojený server s názvem DNS DNN `dnnlsnr` pro pojmenovanou instanci `insta1` , použijte následující příkaz Transact-SQL (T-SQL):

```sql
USE [master]   
GO   

EXEC master.dbo.sp_addlinkedserver    
    @server = N'dnnlsnr\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 
```

Případně můžete místo toho vytvořit propojený server pomocí názvu virtuální sítě (VNN), ale budete muset definovat síťový alias pro mapování VNN na DNN. 

Například pro název instance `insta1` , název vnn `vnnname` a název DNN `dnnlsnr` použijte následující příkaz Transact-SQL (T-SQL) k vytvoření propojeného serveru pomocí vnn:

```sql
USE [master]
GO   

EXEC master.dbo.sp_addlinkedserver   
    @server = N'vnnname\inst1',    
    @srvproduct=N'SQL Server' ;   
GO 

```

Pak vytvořte alias sítě, na který se má mapovat `vnnname\insta1` `dnnlsnr\insta1` . 



## <a name="frequently-asked-questions"></a>Nejčastější dotazy


- Jakou verzi SQL Server přináší podporu DNN? 

   SQL Server 2019 CU2 a novější.

- Jaká je očekávaná doba převzetí služeb při selhání při použití DNN?

   V případě DNN bude doba převzetí služeb při selhání jenom časem převzetí služeb při selhání FCI, aniž by se musela přidávat (například čas sondy při použití Azure Load Balancer).

- Je k dispozici žádný požadavek na verzi pro klienty SQL pro podporu DNN pomocí OLEDB a ODBC?

   Doporučujeme `MultiSubnetFailover=True` podporu připojovacích řetězců pro DNN. Je k dispozici od SQL Server 2012 (11. x).

- Vyžadují SQL Server změny konfigurace, které by mě mohl používat DNN? 

   SQL Server nevyžaduje pro použití DNN žádnou změnu konfigurace, ale některé SQL Server funkce můžou vyžadovat další pozornost. 

- Podporuje DNN clustery s více podsítěmi?

   Ano. Cluster váže DNN ve službě DNS s fyzickými IP adresami všech uzlů v clusteru bez ohledu na podsíť. Klient SQL se pokusí všechny IP adresy názvu DNS bez ohledu na podsíť. 



## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu: 

- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [SQL Server instancí clusteru s podporou převzetí služeb při selhání](/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server)

