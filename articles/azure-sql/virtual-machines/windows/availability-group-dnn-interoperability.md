---
title: Interoperabilita funkcí se skupinami dostupnosti a naslouchací proces DNN
description: 'Přečtěte si o dalších ohledech při práci s některými SQL Server funkcemi a s modulem pro naslouchání DNN (Distributed Network Name) se skupinou dostupnosti Always On v SQL Server na virtuálních počítačích Azure. '
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
ms.date: 10/08/2020
ms.author: mathoma
ms.openlocfilehash: 19b4b7407468b19419e2f85193b1f8fb6ace39c3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97359400"
---
# <a name="feature-interoperability-with-ag-and-dnn-listener"></a>Interoperabilita funkcí pomocí naslouchacího procesu AG a DNN 
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

K dispozici jsou některé SQL Server funkce, které spoléhají na pevně zakódovaný název virtuální sítě (VNN). V takovém případě při použití naslouchacího procesu DNN (Distributed Network Name) se skupinou dostupnosti Always On a SQL Server na virtuálních počítačích Azure může docházet k nějakým dalším hlediskům. 

Tento článek podrobně popisuje SQL Server funkce a interoperabilitu se službou DNN Listener skupiny dostupnosti. 


## <a name="client-drivers"></a>Klientské ovladače

Pro ovladače ODBC, OLEDB, ADO.NET, JDBC, PHP a Node.js musí uživatelé explicitně zadat název a port naslouchacího procesu DNN jako název serveru v připojovacím řetězci. Pokud chcete zajistit rychlé připojení při převzetí služeb při selhání, přidejte `MultiSubnetFailover=True` do připojovacího řetězce, pokud ho klient SQL podporuje. 

## <a name="tools"></a>nástroje

Uživatelé nástrojů [SQL Server Management Studio](/sql/ssms/sql-server-management-studio-ssms), [Sqlcmd](/sql/tools/sqlcmd-utility), [Azure Data Studio](/sql/azure-data-studio/what-is)a [SQL Server Data Tools](/sql/ssdt/sql-server-data-tools) musí explicitně zadat název a port naslouchacího procesu DNN jako název serveru v připojovacím řetězci pro připojení k naslouchacímu procesu. 

Vytváření naslouchacího procesu DNN prostřednictvím grafického uživatelského rozhraní SQL Server Management Studio (SSMS) se momentálně nepodporuje. 


## <a name="availability-groups-and-fci"></a>Skupiny dostupnosti a FCI

Jako jednu z replik můžete nakonfigurovat skupinu dostupnosti Always On pomocí instance clusteru s podporou převzetí služeb při selhání (FCI). Aby tato konfigurace fungovala s naslouchací proces DNN, [musí instance clusteru s podporou převzetí služeb při selhání také používat DNN](failover-cluster-instance-distributed-network-name-dnn-configure.md) , protože neexistuje způsob, jak umístit virtuální IP adresu FCI do seznamu IP adres AG DNN. 

V této konfiguraci musí adresa URL koncového bodu zrcadlení pro repliku FCI používat FCI DNN. Podobně platí, že pokud je FCI použit jako replika jen pro čtení, musí směrování pouze pro čtení do repliky FCI používat FCI DNN. 

Formát koncového bodu zrcadlení je: `ENDPOINT_URL = 'TCP://<FCI DNN DNS name>:<mirroring endpoint port>'` . 

Pokud je třeba název DNS FCI DNN `dnnlsnr` a `5022` je to port koncového bodu zrcadlení FCI, fragment kódu jazyka Transact-SQL (T-SQL), který vytvoří adresu URL koncového bodu, vypadá takto: 

```sql
ENDPOINT_URL = 'TCP://dnnlsnr:5022'
```

Podobně platí, že formát adresy URL směrování jen pro čtení je: `TCP://<FCI DNN DNS name>:<SQL Server instance port>` . 

Pokud je třeba název DNS DNN `dnnlsnr` a `1444` je port používaný cílovým serverem jen pro čtení SQL Server FCI, fragment kódu T-SQL k vytvoření adresy URL směrování jen pro čtení vypadá takto: 

```sql
READ_ONLY_ROUTING_URL = 'TCP://dnnlsnr:1444'
```

Port v adrese URL můžete vynechat, pokud je to výchozí port 1433. U pojmenované instance nakonfigurujte statický port pro pojmenovanou instanci a zadejte ho do adresy URL směrování jen pro čtení.  

## <a name="distributed-availability-group"></a>Distribuovaná skupina dostupnosti

Distribuované skupiny dostupnosti se v současnosti u naslouchacího procesu DNN nepodporují. 

## <a name="replication"></a>Replikace

Veškerá podpora transakcí, sloučení a snímku pro veškerou replikaci podporuje nahrazení naslouchacího procesu VNN pomocí naslouchacího procesu DNN a portu v replikačních objektech, které se připojují k naslouchacího procesu. 

Další informace o tom, jak používat replikaci se skupinami dostupnosti, najdete v tématech [Vydavatel a AG](/sql/database-engine/availability-groups/windows/configure-replication-for-always-on-availability-groups-sql-server), [předplatitelé a](/sql/database-engine/availability-groups/windows/replication-subscribers-and-always-on-availability-groups-sql-server)AG a [distributor a AG](/sql/relational-databases/replication/configure-distribution-availability-group).

## <a name="msdtc"></a>NÁSTROJE

Místní i clusterované MSDTC jsou podporovány, ale Služba MSDTC používá dynamický port, který ke konfiguraci portu HA vyžaduje standardní Azure Load Balancer. V takovém případě musí virtuální počítač použít standardní rezervaci IP adresy nebo nemůže být vystavený pro Internet. 

Definujte dvě pravidla, jednu pro port Mapovač koncových bodů RPC 135 a jednu pro skutečný port MSDTC. Po převzetí služeb při selhání změňte pravidlo pro vyrovnávání zatížení na nový port MSDTC po jeho změně na novém uzlu. 

Pokud je MSDTC místní, ujistěte se, že je povolená odchozí komunikace. 

## <a name="distributed-query"></a>Distribuovaný dotaz 

Distribuovaný dotaz spoléhá na odkazovaný server, který se dá nakonfigurovat pomocí naslouchacího procesu DNN AG a portu. Pokud port není 1433, při konfiguraci propojeného serveru vyberte v SQL Server Management Studio (SSMS) možnost **použít jiný zdroj dat** . 

## <a name="filestream"></a>Souborem

FileStream se podporuje, ale ne pro scénáře, kdy uživatelé přistupují k vymezené sdílené složce pomocí souborového rozhraní Windows API. 

## <a name="filetable"></a>Tabulka souborů

Soubor FileTable se podporuje, ale ne pro scénáře, kdy uživatelé přistupují k vymezené sdílené složce pomocí souborového rozhraní Windows API. 

## <a name="linked-servers"></a>Propojené servery

Nakonfigurujte odkazovaný server pomocí názvu a portu naslouchacího procesu AG DNN. Pokud port není 1433, při konfiguraci propojeného serveru vyberte v SQL Server Management Studio (SSMS) možnost **použít jiný zdroj dat** . 


## <a name="frequently-asked-questions"></a>Nejčastější dotazy


- Kterou SQL Server verze přináší podporu naslouchacího procesu AG DNN? 

   SQL Server 2019 CU8 a novější.

- Jaký je očekávaný čas převzetí služeb při selhání při použití naslouchacího procesu DNN?

   V případě naslouchacího procesu DNN bude doba převzetí služeb při selhání jenom časem převzetí služeb AG při selhání, a to bez jakékoli další doby (například čas sondy při použití Azure Load Balancer).

- Je k dispozici žádný požadavek na verzi pro klienty SQL pro podporu DNN pomocí OLEDB a ODBC?

   `MultiSubnetFailover=True`Pro naslouchací proces DNN doporučujeme podporu připojovacích řetězců. Je k dispozici od SQL Server 2012 (11. x).

- Jsou nějaké změny konfigurace SQL Server nutné pro mě, aby mohl používat naslouchací proces DNN? 

   SQL Server nevyžaduje pro použití DNN žádnou změnu konfigurace, ale některé SQL Server funkce můžou vyžadovat další pozornost. 

- Podporuje DNN clustery s více podsítěmi?

   Ano. Cluster váže DNN ve službě DNS s fyzickými IP adresami všech replik v dostupnosti bez ohledu na podsíť. Klient SQL se pokusí všechny IP adresy názvu DNS bez ohledu na podsíť. 



## <a name="next-steps"></a>Další kroky

Další informace naleznete v tématu: 

- [Technologie clusterů Windows](/windows-server/failover-clustering/failover-clustering-overview)   
- [Skupina dostupnosti Always On](/sql/database-engine/availability-groups/windows/overview-of-always-on-availability-groups-sql-server)

