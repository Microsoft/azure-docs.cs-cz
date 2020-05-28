---
title: Šablony Azure Resource Manageru
titleSuffix: Azure SQL Database & SQL Managed Instance
description: Pomocí Azure Resource Manager šablon můžete vytvořit a nakonfigurovat Azure SQL Database a Azure SQL Managed instance.
services: sql-database
ms.service: sql-database
ms.subservice: service
ms.custom: overview-samples sqldbrb=2
ms.devlang: ''
ms.topic: sample
author: jovanpop-msft
ms.author: jovanpop
ms.reviewer: sstein
ms.date: 02/04/2019
ms.openlocfilehash: 0e3688fdbeabd525e54aad1b9e36c82b9c86751f
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/27/2020
ms.locfileid: "84053289"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database--sql-managed-instance"></a>Šablony Azure Resource Manager pro Azure SQL Database & spravované instance SQL
[!INCLUDE[appliesto-sqldb-sqlmi](../includes/appliesto-sqldb-sqlmi.md)]

Azure Resource Manager šablony vám umožní definovat infrastrukturu jako kód a nasadit vaše řešení do cloudu Azure pro vaše Azure SQL Database a Azure SQL Managed instance.

## <a name="azure-sql-database"></a>[Azure SQL Database](#tab/single-database)

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro Azure SQL Database.

| |  |
|---|---|
| [SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Tato šablona Azure Resource Manager vytvoří v Azure SQL Database jednu databázi a nakonfiguruje pravidla brány firewall protokolu IP na úrovni serveru. |
| [Server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Tato šablona Azure Resource Manager vytvoří server pro Azure SQL Database. |
| [Elastický fond](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Tato šablona vám umožní nasadit elastický fond a přiřadit k němu databáze. |
| [Skupiny převzetí služeb při selhání](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Tato šablona vytvoří v Azure SQL Database dva servery, jednu databázi a skupinu převzetí služeb při selhání.|
| [Detekce hrozeb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Tato šablona umožňuje nasadit server a sadu databází s povoleným detekcí hrozeb s e-mailovou adresou pro výstrahy pro každou databázi. Detekce hrozeb je součástí nabídky SQL Advanced Threat Protection (ATP) a poskytuje vrstvu zabezpečení, která reaguje na potenciální hrozby přes servery a databáze.|
| [Auditování Blob Storage Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Tato šablona vám umožní nasadit server s povoleným auditováním pro zápis protokolů auditu do úložiště objektů BLOB. Auditování pro Azure SQL Database sleduje události databáze a zapisuje je do protokolu auditu, který lze umístit do svého účtu služby Azure Storage, pracovního prostoru OMS nebo Event Hubs.|
| [Auditování do centra událostí Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Tato šablona vám umožní nasadit server s povoleným auditováním pro zápis protokolů auditu do existujícího centra událostí. Aby bylo možné odesílat události auditu do centra událostí, nastavte nastavení auditování pomocí `Enabled` `State` a nastavte `IsAzureMonitorTargetEnabled` jako `true` . Nakonfigurujte také nastavení diagnostiky pomocí `SQLSecurityAuditEvents` kategorie log v `master` databázi (pro auditování na úrovni systému). Auditování sleduje události databáze a zapisuje je do protokolu auditu, který lze umístit do svého účtu služby Azure Storage, pracovního prostoru OMS nebo Event Hubs.|
| [Webová aplikace Azure s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Tato ukázka vytvoří bezplatnou webovou aplikaci Azure a databázi v Azure SQL Database na úrovni základní služby.|
| [Webová aplikace Azure a Redis Cache s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Tato šablona vytvoří webovou aplikaci, Redis Cache a SQL Database ve stejné skupině prostředků a vytvoří dva připojovací řetězce ve webové aplikaci pro SQL Database a Redis Cache.|
| [Import dat z úložiště objektů BLOB pomocí ADF v2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Tato šablona Azure Resource Manager vytvoří Azure Data Factory v2, která kopíruje data z Azure Blob Storage do SQL Database.|
| [Cluster HDInsight s SQL Database](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Tato šablona vám umožní vytvořit cluster HDInsight, logický SQL Server, databázi a dvě tabulky. Tato šablona se používá v [článku použití Sqoop se systémem Hadoop v HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) . |
| [Aplikace logiky Azure, která spouští uloženou proceduru SQL podle plánu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Tato šablona vám umožní vytvořit aplikaci logiky, která spustí uloženou proceduru SQL podle plánu. Jakékoli argumenty pro proceduru lze umístit do části text v šabloně.|

## <a name="azure-sql-managed-instance"></a>[Spravovaná instance Azure SQL](#tab/managed-instance)

Následující tabulka obsahuje odkazy na šablony Azure Resource Manager pro spravovanou instanci Azure SQL.

| |  |
|---|---|
| [Spravovaná instance SQL v nové virtuální síti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Tato šablona Azure Resource Manager vytvoří ve virtuální síti novou nakonfigurovanou virtuální síť Azure a spravovanou instanci SQL. |
| [Síťové prostředí pro spravovanou instanci SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Toto nasazení vytvoří nakonfigurovanou Virtual Network Azure se dvěma podsítěmi – jednu, která bude vyhrazená pro vaše spravované instance SQL, a další, kde můžete umístit další prostředky (například virtuální počítače, App Service prostředí atd.). Tato šablona vytvoří správně nakonfigurované síťové prostředí, kde můžete nasadit spravované instance SQL. |
| [Spravovaná instance SQL s připojením P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Toto nasazení vytvoří Azure Virtual Network se dvěma podsítěmi `ManagedInstance` a `GatewaySubnet` . Spravovaná instance SQL bude nasazena v podsíti ManagedInstance. Brána virtuální sítě se vytvoří v `GatewaySubnet` podsíti a nakonfiguruje pro připojení VPN typu Point-to-site. |
| [Spravovaná instance SQL s virtuálním počítačem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Toto nasazení vytvoří Azure Virtual Network se dvěma podsítěmi `ManagedInstance` a `Management` . Spravovaná instance SQL bude nasazena v `ManagedInstance` podsíti. Virtuální počítač s nejnovější verzí SQL Server Management Studio (SSMS) bude nasazený v `Management` podsíti. |

---
