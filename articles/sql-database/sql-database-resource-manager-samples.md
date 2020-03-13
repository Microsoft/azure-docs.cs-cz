---
title: Šablony Azure Resource Manageru
description: K vytvoření a konfiguraci Azure SQL Database použijte šablony Azure Resource Manager.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: b254621cc414fb9b2b76263957adc80da6e9c22d
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79214009"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Šablony Azure Resource Manager pro Azure SQL Database

Šablony Azure Resource Manager umožňují definovat infrastrukturu jako kód a nasadit vaše řešení do cloudu Azure.

## <a name="single-database--elastic-pool"></a>[Elastický fond pro izolovanou databázi &](#tab/single-database)

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro Azure SQL Database.

| |  |
|---|---|
| [Samostatná databáze](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Tato šablona Azure Resource Manager vytvoří jeden Azure SQL Database s logickým serverem a nakonfiguruje pravidla brány firewall. |
| [Logický Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Tato šablona Azure Resource Manager vytvoří logický Server pro Azure SQL Database. |
| [Elastický fond](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Tato šablona vám umožní nasadit nový elastický fond s novým přidruženým SQL Server a novými databázemi SQL, které se k němu přiřadí. |
| [Skupiny převzetí služeb při selhání](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Tato šablona vytvoří dva logické servery Azure SQL, databázi SQL a skupinu převzetí služeb při selhání.|
| [Detekce hrozeb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Tato šablona vám umožní nasadit logický Server Azure SQL a sadu databází Azure SQL s povoleným detekcí hrozeb s použitím e-mailové adresy pro výstrahy pro každou databázi. Detekce hrozeb je součástí nabídky SQL Advanced Threat Protection (ATP) a poskytuje vrstvu zabezpečení, která reaguje na potenciální hrozby přes servery a databáze SQL.|
| [Auditování Blob Storage Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Tato šablona vám umožní nasadit logický Server Azure SQL s povoleným auditováním pro zápis protokolů auditu do úložiště objektů BLOB. Auditování pro Azure SQL Database sleduje události databáze a zapisuje je do protokolu auditu, který lze umístit do svého účtu služby Azure Storage, pracovního prostoru OMS nebo Event Hubs.|
| [Auditování do centra událostí Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Tato šablona vám umožní nasadit Azure SQL Server s povoleným auditováním pro zápis protokolů auditu do ukončení centra událostí. Aby bylo možné odesílat události auditu do centra událostí, nastavte nastavení auditování pomocí `Enabled` `State` a nastavte `IsAzureMonitorTargetEnabled` jako `true`. Také nakonfigurujte nastavení diagnostiky pomocí kategorie protokolu `SQLSecurityAuditEvents` v databázi `master` (pro auditování na úrovni systému). Auditování pro Azure SQL Database a SQL Data Warehouse sleduje události databáze a zapisuje je do protokolu auditu, který lze umístit do svého účtu služby Azure Storage, pracovního prostoru OMS nebo Event Hubs.|
| [Webová aplikace Azure s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Tato ukázka vytvoří bezplatnou webovou aplikaci Azure a SQL Database na úrovni "Basic".|
| [Webová aplikace Azure a Redis Cache s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Tato šablona vytvoří webovou aplikaci, Redis Cache a SQL Database ve stejné skupině prostředků a vytvoří dva připojovací řetězce ve webové aplikaci pro SQL Database a Redis Cache.|
| [Import dat z úložiště objektů BLOB pomocí ADF v2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Tato šablona Azure Resource Manager vytvoří Azure Data Factory v2, která kopíruje data z Azure Blob Storage do SQL Database.|
| [Cluster HDInsight s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Tato šablona vám umožní vytvořit cluster HDInsight, SQL Database Server, SQL Database a dvě tabulky. Tato šablona se používá v [článku použití Sqoop se systémem Hadoop v HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) . |
| [Aplikace logiky Azure, která spouští uloženou proceduru SQL podle plánu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Tato šablona vám umožní vytvořit aplikaci logiky, která spustí uloženou proceduru SQL podle plánu. Jakékoli argumenty pro proceduru lze umístit do části text v šabloně.|

## <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro Azure SQL Database spravovanou instanci.

| |  |
|---|---|
| [Spravovaná instance v nové virtuální síti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Tato šablona Azure Resource Manager ve virtuální síti vytvoří novou nakonfigurovanou virtuální síť Azure a spravovanou instanci. |
| [Síťové prostředí pro spravovanou instanci](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Toto nasazení vytvoří nakonfigurovanou Virtual Network Azure se dvěma podsítěmi – jednu, která bude vyhrazená pro vaše spravované instance, a další, kde můžete umístit další prostředky (například virtuální počítače, App Service prostředí atd.). Tato šablona vytvoří správně nakonfigurované síťové prostředí, ve kterém můžete nasadit spravované instance. |
| [Spravovaná instance s připojením P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Pomocí tohoto nasazení se vytvoří Virtual Network Azure se dvěma podsítěmi `ManagedInstance` a `GatewaySubnet`. Spravovaná instance bude nasazena v ManagedInstance podsíti. Brána virtuální sítě se vytvoří v `GatewaySubnet` podsíti a nakonfiguruje pro připojení VPN typu Point-to-site. |
| [Spravovaná instance s virtuálním počítačem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Pomocí tohoto nasazení se vytvoří Virtual Network Azure se dvěma podsítěmi `ManagedInstance` a `Management`. Spravovaná instance bude nasazena v `ManagedInstance` podsíti. Virtuální počítač s nejnovější verzí SQL Server Management Studio (SSMS) bude nasazený v `Management` podsíti. |

---
