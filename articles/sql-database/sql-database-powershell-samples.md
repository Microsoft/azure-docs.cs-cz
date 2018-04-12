---
title: Ukázkové skripty Azure PowerShellu pro službu SQL Database | Microsoft Docs
description: Ukázkové skripty Azure PowerShellu, které vám pomůžou vytvářet a spravovat servery služby Azure SQL Database, elastické fondy, databáze a brány firewall.
services: sql-database
documentationcenter: sql-database
author: CarlRabeler
manager: craigg
editor: tysonn
tags: azure-service-management
ms.service: sql-database
ms.custom: overview-samples, mvc
ms.devlang: PowerShell
ms.topic: sample
ms.date: 04/01/2018
ms.author: carlrab
ms.openlocfilehash: 2245964cab3361e0f298cf35d26e8b0a1b8456f0
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="azure-powershell-samples-for-azure-sql-database"></a>Ukázky v Azure PowerShellu pro službu Azure SQL Database

Následující tabulka obsahuje odkazy na ukázkové skripty Azure PowerShellu pro službu Azure SQL Database.

| |  |
|---|---|
|**Vytvoření izolované databáze a elastického fondu**||
| [Vytvoření izolované databáze a konfigurace pravidla brány firewall](scripts/sql-database-create-and-configure-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří izolovanou databázi SQL Azure a nakonfiguruje pravidlo brány firewall na úrovni serveru. |
| [Vytváření elastických fondů a přesun databází ve fondu](scripts/sql-database-move-database-between-pools-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu vytvoří elastické fondy Azure SQL Database, přesune databáze ve fondu a změní úrovně výkonu.|
|**Konfigurace geografické replikace a převzetí služeb při selhání**||
| [Konfigurace a převzetí služeb při selhání izolované databáze s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro izolovanou databázi SQL Azure a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání databáze ve fondu s využitím aktivní geografické replikace](scripts/sql-database-setup-geodr-and-failover-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje aktivní geografickou replikaci pro databázi SQL Azure v elastickém fondu SQL a převezme její služby při selhání do sekundární repliky. |
| [Konfigurace a převzetí služeb při selhání skupiny převzetí služeb při selhání pro izolovanou databázi (Preview)](scripts/sql-database-setup-geodr-failover-database-failover-group-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje skupinu převzetí služeb při selhání pro instanci serveru služby Azure SQL Database, do této skupiny převzetí služeb při selhání přidá databázi a převezme její služby při selhání na sekundární server. |
|**Škálování izolované databáze a elastického fondu**||
| [Škálování izolované databáze](scripts/sql-database-monitor-and-scale-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu monitoruje metriky výkonu databáze SQL Azure, škáluje ji na vyšší úroveň výkonu a vytvoří pravidlo upozornění na jednu z metrik výkonu. |
| [Škálování elastického fondu](scripts/sql-database-monitor-and-scale-pool-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu monitoruje metriky výkonu elastického fondu Azure SQL Database, škáluje ho na vyšší úroveň výkonu a vytvoří pravidlo upozornění na jednu z metrik výkonu.  |
| **Auditování a detekce hrozeb** |
| [Konfigurace auditování a detekce hrozeb](scripts/sql-database-auditing-and-threat-detection-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu nakonfiguruje zásady auditování a detekce hrozeb pro databázi SQL Azure. |
| **Obnovení, kopírování a import databáze**||
| [Obnovení databáze](scripts/sql-database-restore-database-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu obnoví databázi SQL Azure z geograficky redundantní zálohy a odstraněnou databázi SQL Azure z poslední zálohy. |
| [Kopírování databáze na nový server](scripts/sql-database-copy-database-to-new-server-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu vytvoří kopii stávající databáze SQL Azure na novém serveru SQL Azure. |
| [Import databáze ze souboru bacpac](scripts/sql-database-import-from-bacpac-powershell.md?toc=%2fpowershell%2fmodule%2ftoc.json)| Tento skript PowerShellu importuje databázi na server SQL Azure ze souboru bacpac. |
| **Synchronizace dat mezi databázemi**||
| [Synchronizace dat mezi databázemi SQL](scripts/sql-database-sync-data-between-sql-databases.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje Synchronizaci dat pro synchronizaci mezi několika databázemi SQL Azure. |
| [Synchronizace dat mezi službou SQL Database a místním SQL Serverem](scripts/sql-database-sync-data-between-azure-onprem.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu nakonfiguruje Synchronizaci dat pro synchronizaci mezi databází SQL Azure a místní databází SQL Serveru. |
| [Aktualizace schématu synchronizace pro Synchronizaci dat SQL](scripts/sql-database-sync-update-schema.md?toc=%2fpowershell%2fmodule%2ftoc.json) | Tento skript PowerShellu přidá položky do schématu synchronizace pro Synchronizaci dat nebo je z něj odebere. |
|||
