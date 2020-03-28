---
title: Šablony Azure Resource Manageru
description: K vytvoření a konfiguraci Azure SQL Database použijte šablony Azure Resource Manageru.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79214009"
---
# <a name="azure-resource-manager-templates-for-azure-sql-database"></a>Šablony Azure Resource Manageru pro Azure SQL Database

Šablony Azure Resource Manageru umožňují definovat infrastrukturu jako kód a nasadit svá řešení do cloudu Azure.

## <a name="single-database--elastic-pool"></a>[Jeden databázový & elastický fond](#tab/single-database)

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro Azure SQL Database.

| |  |
|---|---|
| [Izolovaná databáze](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-database-transparent-encryption-create) | Tato šablona Azure Resource Manager vytvoří jednu databázi Azure SQL s logickým serverem a nakonfiguruje pravidla brány firewall. |
| [Logický server](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-logical-server) | Tato šablona Azure Resource Manager vytvoří logický server pro Azure SQL Database. |
| [Elastický bazén](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-elastic-pool-create) | Tato šablona umožňuje nasadit nový elastický fond s novým přidruženým SQL Serverem a novými databázemi SQL, které mu budou přiřazeny. |
| [Skupiny převzetí služeb při selhání](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-with-failover-group) | Tato šablona vytvoří dva logické servery Azure SQL, databázi SQL a skupinu převzetí služeb při selhání.|
| [Detekce hrozeb](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-threat-detection-db-policy-multiple-databases) | Tato šablona umožňuje nasadit logický server Azure SQL a sadu databází Azure SQL s povolenou detekcí hrozeb s e-mailovou adresou pro výstrahy pro každou databázi. Detekce hrozeb je součástí nabídky SQL Advanced Threat Protection (ATP) a poskytuje vrstvu zabezpečení, která reaguje na potenciální hrozby přes sql servery a databáze.|
| [Auditování úložiště objektů blob Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-blob-storage) | Tato šablona umožňuje nasadit logický server Azure SQL s povolenou auditování pro zápis protokolů auditování do úložiště objektů blob. Auditování pro Azure SQL Database sleduje události databáze a zapisuje je do protokolu auditování, který se dá umístit do vašeho účtu úložiště Azure, pracovního prostoru OMS nebo centra událostí.|
| [Auditování do Centra událostí Azure](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sql-auditing-server-policy-to-eventhub) | Tato šablona umožňuje nasadit server Azure SQL s povolenou auditování pro zápis protokolů auditování do ukončujícího centra událostí. Chcete-li odesílat události auditu do centra `Enabled` `State` událostí, nastavte nastavení auditování pomocí aplikace a nastavte `IsAzureMonitorTargetEnabled` je jako `true`. Nakonfigurujte `SQLSecurityAuditEvents` také diagnostická `master` nastavení s kategorií protokolu v databázi (pro auditování úrovně obsluhy). Auditování pro Azure SQL Database a SQL Data Warehouse sleduje události databáze a zapisuje je do protokolu auditování, který se dá umístit do vašeho účtu úložiště Azure, pracovního prostoru OMS nebo centra událostí.|
| [Azure Web App s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-sql-database) | Tato ukázka vytvoří bezplatnou Azure Web App a SQL Database na úrovni služby "Základní".|
| [Azure Web App a redis cache s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-redis-cache-sql-database) | Tato šablona vytvoří webovou aplikaci, mezipaměť Redis a databázi SQL ve stejné skupině prostředků a vytvoří dva připojovací řetězce ve webové aplikaci pro databázi SQL a mezipaměť Redis.|
| [Import dat z úložiště objektů blob pomocí ADF V2](https://github.com/Azure/azure-quickstart-templates/tree/master/101-data-factory-v2-blob-to-sql-copy) | Tato šablona Azure Resource Manager vytvoří Azure Data Factory V2, který kopíruje data z Azure Blob Storage do SQL Database.|
| [Cluster HDInsight s databází SQL](https://github.com/Azure/azure-quickstart-templates/tree/master/101-hdinsight-linux-with-sql-database) | Tato šablona umožňuje vytvořit cluster HDInsight, databázový server SQL, databázi SQL a dvě tabulky. Tato šablona je používána [článek Use Sqoop with Hadoop in HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/hdinsight-use-sqoop) |
| [Aplikace Azure Logic, která spouští proceduru uloženou sql podle plánu](https://github.com/Azure/azure-quickstart-templates/tree/master/101-logic-app-sql-proc) | Tato šablona umožňuje vytvořit aplikaci logiky, která spustí proceduru uloženou sql podle plánu. Všechny argumenty pro postup lze umístit do části textu šablony.|

## <a name="managed-instance"></a>[Spravovaná instance](#tab/managed-instance)

Následující tabulka obsahuje odkazy na šablony Azure Resource Manageru pro Azure SQL Database – spravovaná instance.

| |  |
|---|---|
| [Spravovaná instance v nové virtuální síti](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sqlmi-new-vnet) | Tato šablona Azure Resource Manager vytvoří novou nakonfigurovanou virtuální síť Azure a spravovanou instanci ve virtuální síti. |
| [Síťové prostředí pro spravovanou instanci](https://github.com/Azure/azure-quickstart-templates/tree/master/101-sql-managed-instance-azure-environment) | Toto nasazení vytvoří nakonfigurovanou virtuální síť Azure se dvěma podsítěmi – jednu, která bude vyhrazena pro spravované instance, a další, do které můžete umístit další prostředky (například virtuální počítače, prostředí služby App Service atd.). Tato šablona vytvoří správně nakonfigurované síťové prostředí, ve kterém můžete nasadit spravované instance. |
| [Spravovaná instance s připojením P2S](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-point-to-site-vpn) | Toto nasazení vytvoří virtuální síť Azure `ManagedInstance` se `GatewaySubnet`dvěma podsítěmi a . Spravovaná instance se nasadí v podsíti Spravované instance. Brána virtuální sítě `GatewaySubnet` bude vytvořena v podsíti a nakonfigurována pro připojení VPN bodu k lokalitě. |
| [Spravovaná instance s virtuálním počítačem](https://github.com/Azure/azure-quickstart-templates/tree/master/201-sqlmi-new-vnet-w-jumpbox) | Toto nasazení vytvoří virtuální síť Azure `ManagedInstance` se `Management`dvěma podsítěmi a . Spravovaná instance `ManagedInstance` bude nasazena v podsíti. Virtuální počítač s nejnovější verzí SQL Server Management Studio (SSMS) se nasadí v `Management` podsíti. |

---
