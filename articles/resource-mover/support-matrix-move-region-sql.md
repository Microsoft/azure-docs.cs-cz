---
title: Podpora přesunu prostředků SQL Azure mezi oblastmi pomocí Azure Resource stěhovací.
description: Přečtěte si podporu pro přesun prostředků SQL Azure mezi oblastmi pomocí Azure Resource stěhovací.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652805"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Podpora pro přesun prostředků SQL Azure mezi oblastmi Azure

Tento článek shrnuje podporu a požadavky pro přesun prostředků SQL Azure mezi oblastmi Azure pomocí Azure Resource stěhovací.

## <a name="requirements"></a>Požadavky

Požadavky jsou shrnuty v následující tabulce.

**Funkce** | **Podporováno/nepodporováno** | **Podrobnosti**
--- | --- | ---
**Azure SQL Database škálování** | Nepodporováno | Databáze se nedají přesunout do vrstvy služby Azure SQL s využitím prostředků pomocí nástroje Resource stěhovací.
**Zónová redundance** | Podporováno |  Podporované možnosti přesunutí:<br/><br/> – Mezi oblastmi, které podporují redundanci zóny.<br/><br/> -Mezi oblastmi, které nepodporují redundanci zóny.<br/><br/> – Mezi oblastí, která podporuje redundanci zóny, do oblasti, která nepodporuje redundanci zóny.<br/><br/> – Mezi oblastí, které nepodporují redundanci zóny, do oblasti, která podporuje redundanci zóny. 
**Synchronizace dat** | Databáze centra/synchronizace: nepodporováno<br/><br/> Člen synchronizace: podporován. | Pokud je přesunut člen synchronizace, je třeba nastavit synchronizaci dat s novou cílovou databází.
**Existující geografická replikace** | Podporováno | Stávající geografické repliky jsou přemapovány na novou primární oblast v cílové oblasti.<br/><br/> Osazení musí být inicializováno po přesunutí. [Další informace](/azure/sql-database/sql-database-active-geo-replication-portal)
**Transparentní šifrování dat (TDE) s Bring Your Own Key (BYOK)** | Podporováno | [Přečtěte si další informace](../key-vault/general/move-region.md) o přesunu trezorů klíčů napříč oblastmi.
**TDE s klíčem spravovaným službou** | Podporuje se. |  [Přečtěte si další informace](../key-vault/general/move-region.md) o přesunu trezorů klíčů napříč oblastmi.
**Pravidla Maskování dynamických dat** | Podporuje se. | Pravidla se automaticky zkopírují do cílové oblasti v rámci přesunutí. [Přečtěte si další informace](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal).
**Advanced Data Security** | Nepodporováno | Alternativní řešení: nastavte na úrovni SQL Server v cílové oblasti. [Přečtěte si další informace](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).
**Pravidla brány firewall** | Nepodporováno | Alternativní řešení: Nastavte pravidla firewallu pro SQL Server v cílové oblasti. Pravidla brány firewall na úrovni databáze se zkopírují ze zdrojového serveru na cílový server. [Přečtěte si další informace](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule).
**Zásady auditování** | Nepodporováno | Po přesunu se zásady obnoví na výchozí hodnoty. [Přečtěte si](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) , jak resetovat.
**Uchování záloh** | Podporuje se. | Zásady uchovávání záloh zdrojové databáze se přenesou do cílové databáze. [Naučte](/azure/sql-database/sql-database-long-term-backup-retention-configure) se, jak upravit nastavení po přesunutí.
**Automatické ladění** | Nepodporováno | Alternativní řešení: nastavte nastavení automatického ladění po přesunutí. [Přečtěte si další informace](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable).
**Výstrahy databáze** | Nepodporováno | Alternativní řešení: Nastavte výstrahy po přesunutí. [Přečtěte si další informace](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal).
**Azure SQL Server Stretch Database** | Nepodporuje se | Nelze přesunout Stretch databází SQL Server s nástrojem Resource stěhovací.
**Azure Synapse Analytics** | Nepodporuje se | Synapse Analytics se nedá přesunout (dřív Azure SQL Data Warehouse) s nástrojem Resource stěhovací.
## <a name="next-steps"></a>Další kroky

Vyzkoušejte si [prostředky Azure SQL](tutorial-move-region-sql.md) do jiné oblasti s nástrojem Resource stěhovací.