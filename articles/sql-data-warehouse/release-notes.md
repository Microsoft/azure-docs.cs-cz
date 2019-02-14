---
title: Zpráva k vydání verze služby Azure SQL Data Warehouse | Dokumentace Microsoftu
description: Zpráva k vydání verze pro Azure SQL Data Warehouse.
services: sql-data-warehouse
author: mlee3gsd
ms.author: anumjs
ms.reviewer: jrasnick
ms.service: sql-data-warehouse
ms.topic: conceptual
ms.subservice: manage
ms.date: 02/09/2019
ms.openlocfilehash: 2ac60287c9d92ab6230e1dd6777504036e54492d
ms.sourcegitcommit: b3d74ce0a4acea922eadd96abfb7710ae79356e0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56244670"
---
# <a name="azure-sql-data-warehouse-release-notes-and-documentation-updates"></a>Poznámky k verzi Azure SQL Data Warehouse a aktualizace dokumentace

Azure SQL Data Warehouse (datový Sklad SQL) je cloudové podnikového datového skladu, který využívá masivně paralelní zpracování (MPP) k rychlému spouštění složitých dotazů nad petabajty dat. Využijte službu SQL Data Warehouse jako klíčovou součást řešení pro velké objemy dat. Importujte do služby SQL Data Warehouse velké objemy dat pomocí dotazů PolyBase T-SQL a následně využijte sílu architektury MPP ke spouštění vysoce výkonných analýz. Postupným integrováním a analyzováním se datový sklad stane pro vaši firmu jediným spolehlivým zdrojem pravdivých informací.

Kliknutím na odkazy níže a zjistěte další informace o nové funkce a vylepšení, které můžete očekávat v nejnovější verzi služby Azure SQL Data Warehouse. Můžete očekávat pro příjem těchto aktualizací služby během vašeho plánu údržby identifikovaní.

- [SQL Data Warehouse verze 10.0.10106.0 (leden)](./release-notes-10-0-10106-0.md)
- [Prosince 2018](./release-notes-december-2018.md)
- [. Října 2018](./release-notes-october-2018.md)
- [. Září 2018](./release-notes-september-2018.md)
- [. Srpna 2018](./release-notes-august-2018.md)
- [. Července 2018](./release-notes-july-2018.md)
- [. Června 2018](./release-notes-june-2018.md)
- [. Května 2018.](./release-notes-may-2018.md)

## <a name="checking-the-code-version-that-has-been-applied-to-your-data-warehouse"></a>Kontrola verze kódu, který byl použit ke svému datovému skladu

Pro potvrzení, které vydání bylo podle byla použita k vašemu datovému skladu. Připojte se ke svému datovému skladu pomocí SSMS a spusťte následující syntaxi vrátí aktuální verzi služby SQL Data Warehouse.

```sql
SELECT @@VERSION AS 'SQL Data Warehouse';
```

Příklad výstupu: ![Verze SQL Data Warehouse](./media/release-notes/dw-version.png)

Použijte prosím verzi identifikovat pro potvrzení, které verze se nastavily pro Azure SQL Data Warehouse. 


## <a name="next-steps"></a>Další postup
- [Další informace](https://docs.microsoft.com/azure/sql-data-warehouse/viewing-maintenance-schedule) o zobrazení plánu údržby. 
- [Další informace](https://docs.microsoft.com/azure/sql-data-warehouse/changing-maintenance-schedule) o změně plánu údržby.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/alert-metric) o vytváření, zobrazení a Správa výstrah pomocí Azure monitoru.
- [Další informace](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitor-alerts-unified-log-webhook) akce webhooku pro pravidla upozornění protokolů.
- [Další informace](https://docs.microsoft.com/azure/service-health/service-health-overview) o Azure Service Health