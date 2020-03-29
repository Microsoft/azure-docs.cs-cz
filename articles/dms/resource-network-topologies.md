---
title: Topologie sítě pro migrace spravovaných instancí SQL
titleSuffix: Azure Database Migration Service
description: Naučte se zdrojové a cílové konfigurace pro migrace spravovaných instancí Azure SQL Database pomocí služby Migrace databáze Azure.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254961"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Síťové topologie pro migrace spravovaných instancí Azure SQL DB pomocí služby Migrace databáze Azure

Tento článek popisuje různé topologie sítě, které služba Migrace databáze Azure můžete pracovat s poskytovat komplexní prostředí migrace z místních serverů SQL do Azure SQL Database Spravované instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Spravovaná instance Azure SQL Database nakonfigurovaná pro hybridní úlohy 

Tuto topologii použijte, pokud je vaše instance spravované databází Azure SQL připojená k místní síti. Tento přístup poskytuje nejvíce zjednodušené směrování v síti a poskytuje maximální propustnost dat během migrace.

![Topologie sítě pro hybridní úlohy](media/resource-network-topologies/hybrid-workloads.png)

**Požadavky**

- V tomto scénáři se instance spravované službou Azure SQL Database database a instance Azure Database Migration Service vytvoří ve stejné virtuální síti Microsoft Azure, ale používají různé podsítě.  
- Virtuální síť použitá v tomto scénáři je také připojena k místní síti pomocí [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Správa databáze Azure SQL je izolovaná od místní sítě

Tuto topologii sítě použijte, pokud vaše prostředí vyžaduje jeden nebo více z následujících scénářů:

- Instance spravované službou Azure SQL Database je izolovaná od místního připojení, ale instance služby Migrace databáze Azure je připojená k místní síti.
- Pokud jsou zavedeny zásady řízení přístupu na základě rolí (RBAC) a je třeba omezit uživatele na přístup ke stejnému předplatnému, které je hostitelem spravované instance Azure SQL Database.
- Virtuální sítě používané pro spravovanou instanci Azure SQL Database a službu migrace databáze Azure jsou v různých předplatných.

![Topologie sítě pro spravovanou instanci izolovaná od místní sítě](media/resource-network-topologies/mi-isolated-workload.png)

**Požadavky**

- Virtuální síť, kterou pro tento scénář používá služba Azure Database Migration Service,https://docs.microsoft.com/azure/expressroute/expressroute-introduction) musí být také připojena k místní síti pomocí (nebo [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Nastavte [partnerský vztah v síti virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítí používanou pro spravovanou instanci Azure SQL Database a službou Migrace databáze Azure.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Migrace z cloudu na cloud: Sdílená virtuální síť

Tuto topologii použijte, pokud je zdrojový SQL Server hostovaný ve virtuálním počítači Azure a sdílí stejnou virtuální síť se spravovanou instancí Azure SQL Database a službou Migrace databáze Azure.

![Topologie sítě pro migrace z cloudu na cloud se sdílenou virtuální sítí](media/resource-network-topologies/cloud-to-cloud.png)

**Požadavky**

- Žádné další požadavky.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migrace z cloudu do cloudu: Izolovaná virtuální síť

Tuto topologii sítě použijte, pokud vaše prostředí vyžaduje jeden nebo více z následujících scénářů:

- Spravovaná instance Azure SQL Database se zřápává v izolované virtuální síti.
- Pokud jsou zavedeny zásady řízení přístupu na základě rolí (RBAC) a je třeba omezit uživatele na přístup ke stejnému předplatnému, které je hostitelem spravované instance Azure SQL Database.
- Virtuální sítě používané pro azure sql database spravované instance a azure database migration service jsou v různých předplatných.

![Topologie sítě pro migrace z cloudu na cloud s izolovanou virtuální sítí](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Požadavky**

- Nastavte [partnerský vztah v síti virtuální sítě](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) mezi virtuální sítí používanou pro spravovanou instanci Azure SQL Database a službou Migrace databáze Azure.

## <a name="inbound-security-rules"></a>Příchozí pravidla zabezpečení

| **NÁZEV**   | **Port** | **Protokol** | **Zdroj** | **Cíl** | **Akce** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Všechny      | Všechny          | PODSÍŤ DMS | Všechny             | Povolit      |

## <a name="outbound-security-rules"></a>Odchozí pravidla zabezpečení

| **NÁZEV**                  | **Port**                                              | **Protokol** | **Zdroj** | **Cíl**           | **Akce** | **Důvod pravidla**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| správa                | 443,9354                                              | TCP          | Všechny        | Všechny                       | Povolit      | Komunikace roviny správy prostřednictvím service busu a úložiště objektů blob Azure. <br/>(Pokud je partnerský vztah Microsoftu povolen, možná toto pravidlo nebudete potřebovat.)                                                             |
| Diagnostika               | 12000                                                 | TCP          | Všechny        | Všechny                       | Povolit      | DMS používá toto pravidlo ke shromažďování diagnostických informací pro účely řešení potíží.                                                                                                                      |
| Zdrojový server SQL         | 1433 (nebo port IP protokolu TCP, který server SQL Server naslouchá) | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Připojení zdroje SQL Serveru z DMS <br/>(Pokud máte připojení mezi lokalitami, možná toto pravidlo nebudete potřebovat.)                                                                                       |
| Instance s názvem serveru SQL Server | 1434                                                  | UDP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Připojení zdroje instance SQL Server z DMS <br/>(Pokud máte připojení mezi lokalitami, možná toto pravidlo nebudete potřebovat.)                                                                        |
| Sdílená složky SMB                 | 445                                                   | TCP          | Všechny        | Adresní prostor místního prostředí | Povolit      | Sdílená síť Oválná síť SMB pro DMS pro ukládání záložních souborů databáze pro migrace do Azure SQL Database MI a SQL serverů na virtuálním počítači Azure <br/>(Pokud máte připojení mezi lokalitami, možná toto pravidlo nebudete potřebovat). |
| DMS_subnet                | Všechny                                                   | Všechny          | Všechny        | DMS_Subnet                | Povolit      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Viz také

- [Migrace serveru SQL Server do spravované instance Azure SQL Database](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Přehled předpokladů pro používání služby Migrace databáze Azure](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Vytvoření virtuální sítě pomocí portálu Azure](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Další kroky

- Přehled služby migrace databáze Azure najdete v článku [Co je služba migrace databáze Azure?](dms-overview.md).
- Aktuální informace o místní dostupnosti služby Migrace databáze Azure najdete na stránce [Produkty dostupné podle oblasti.](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration)
