---
title: Šablony Azure Resource Manageru pro službu SQL Database | Dokumentace Microsoftu
description: Pomocí šablon Azure Resource Manageru k vytvoření a konfigurace Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: ''
manager: craigg
ms.date: 02/04/2019
ms.openlocfilehash: 58513b77315badc52acbc0c5116343461b44fa08
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56232958"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Šablony Azure Resource Manageru pro službu Azure SQL Database

Šablony Azure Resource Manageru umožňují definovat infrastrukturu jako kód a nasazení řešení do cloudu Azure.

## <a name="single-database--elastic-pool"></a>Izolované databáze a elastického fondu

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro službu Azure SQL Database.

| |  |
|---|---|
| [Izolované databáze](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Tuto šablonu Azure Resource Manageru vytvoří izolovanou databázi SQL Azure s logického serveru a nakonfiguruje pravidla brány firewall. |
| [Logický server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Tuto šablonu Azure Resource Manageru vytvoří logický server Azure SQL Database. |
| [Elastický fond](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Tuto šablonu můžete nasadit nový elastický fond se jí přiřadit jeho nové přidružený Server SQL a nové databáze SQL. |
| [Skupiny převzetí služeb při selhání](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Tato šablona vytvoří dvě logické servery Azure SQL, SQL database a převzetí služeb při selhání skupiny.|
| [Advanced Threat Protection](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-advanced-threat-protection-server-policy) | Tato šablona umožňuje nasadit logický server Azure SQL s Advanced Threat Protection povolen a volitelné Azure SQL Database. SQL Advanced Threat Protection je jednotný balíček pokročilých možností zabezpečení SQL.|
| [Detekce hrozeb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Tato šablona umožňuje nasadit logický server Azure SQL a sadu databází Azure SQL Database s detekcí hrozeb služby povolené s e-mailovou adresu pro oznámení pro každou databázi. Detekce hrozeb je součástí nabídky SQL Advanced Threat Protection (ATP) a poskytuje vrstvu zabezpečení, která bude reagovat na potenciální hrozby přes servery a databáze SQL.|
| [Auditování objektů Blob v Azure Storage](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Tato šablona umožňuje nasazení logického serveru Azure SQL pomocí auditování povoleno pro zápis protokolů auditu do úložiště objektů blob. Auditování pro Azure SQL Database sleduje události databáze a zapisuje je do protokolu auditu, které mohou být umístěny v účtu úložiště Azure, pracovní prostor OMS nebo Event Hubs.|
| [Auditování do centra událostí Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Tuto šablonu můžete nasadit server Azure SQL s auditování povoleno pro zápis protokolů auditu do stávající centra událostí. Aby bylo možné odesílat události auditu do centra událostí, nastavte nastavení s auditování `Enabled` `State` a nastavte `IsAzureMonitorTargetEnabled` jako `true`. Nakonfigurujte také nastavení diagnostiky se `SQLSecurityAuditEvents` kategorii diagnostické protokoly na `master` databázi (při poskytování auditování na úrovni). Auditování pro Azure SQL Database a SQL Data Warehouse sleduje události databáze a zapisuje je do protokolu auditu, které mohou být umístěny v účtu úložiště Azure, pracovní prostor OMS nebo Event Hubs.|
| [Webové aplikace Azure s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Tato ukázka vytvoří bezplatná webová aplikace Azure a SQL Database na úrovni "Základní" služby.|
| [Webové aplikace Azure a Redis Cache s využitím SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Tato šablona vytvoří webovou aplikaci, Redis Cache a SQL Database ve stejné skupině prostředků a vytvoří dva připojovací řetězce ve webové aplikaci pro SQL Database a Redis Cache.|
| [Umožňuje importovat data z úložiště objektů blob pomocí ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Azure Data Factory V2, který kopíruje data z Azure Blob Storage do SQL Database vytvoří tato šablona Azure Resource Manageru.|
| [Clusteru HDInsight se službou SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Tato šablona umožňuje vytvořit HDInsight cluster, server služby SQL Database, SQL Database a dvě tabulky. Tato šablona používá [pomocí Sqoop se systémem Hadoop v HDInsight článku](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Azure Logic App, na kterém běží SQL uložená procedura podle plánu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Tato šablona umožňuje vytvořit aplikaci logiky, která uložené procedury SQL se spouštět podle plánu. Všechny argumenty pro proceduru můžou být přepnuté do části textu šablony.|

## <a name="managed-instance"></a>MI

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro službu Azure SQL Database – Managed Instance.

| |  |
|---|---|
| [Spravovaná Instance v nové sítě VNet](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Tuto šablonu Azure Resource Manageru vytvoří novou virtuální síť nakonfigurovanou Azure a spravované Instance ve virtuální síti. |
| [Síťové prostředí pro Managed Instance](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Toto nasazení vytvoří nakonfigurované virtuální sítě Azure se dvěma podsítěmi – ten, který bude vyhrazený pro vaši Managed instance a jiný umístění jiné prostředky (například virtuální počítače, služby App Service Environment, atd.). Tato šablona vytvoří správně nakonfigurované síťové prostředí, kde můžete nasadit spravované instance. |
| [Managed Instance pomocí připojení P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Toto nasazení vytvoří virtuální síť Azure se dvěma podsítěmi `ManagedInstance` a `GatewaySubnet`. V podsíti ManagedInstance se nasadí spravovanou instanci. Brána virtuální sítě se vytvoří v `GatewaySubnet` podsítě a nakonfigurovaný pro připojení Point-to-Site VPN. |
| [Spravovaná Instance s virtuálním počítačem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Toto nasazení vytvoří virtuální síť Azure se dvěma podsítěmi `ManagedInstance` a `Management`. Managed Instance se nasadí v `ManagedInstance` podsítě. Virtuální počítač s nejnovější verzí systému SQL Server Management Studio (SSMS) nasadí v `Management` podsítě. |

