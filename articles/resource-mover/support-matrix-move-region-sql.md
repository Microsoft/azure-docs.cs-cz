---
title: Podpora přesunu prostředků SQL Azure mezi oblastmi pomocí Azure Resource stěhovací.
description: Přečtěte si podporu pro přesun prostředků SQL Azure mezi oblastmi pomocí Azure Resource stěhovací.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 22a7738c2d4d3cc02c03c233e0821f07b459dd94
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2020
ms.locfileid: "96452082"
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
**Existující geografická replikace** | Podporováno | Stávající geografické repliky jsou přemapovány na novou primární oblast v cílové oblasti.<br/><br/> Osazení musí být inicializováno po přesunutí. [Další informace](../azure-sql/database/active-geo-replication-configure-portal.md)
**Transparentní šifrování dat (TDE) s Bring Your Own Key (BYOK)** | Podporováno | [Přečtěte si další informace](../key-vault/general/move-region.md) o přesunu trezorů klíčů napříč oblastmi.
**TDE s klíčem spravovaným službou** | Podporuje se. |  [Přečtěte si další informace](../key-vault/general/move-region.md) o přesunu trezorů klíčů napříč oblastmi.
**Pravidla Maskování dynamických dat** | Podporuje se. | Pravidla se automaticky zkopírují do cílové oblasti v rámci přesunutí. [Další informace](../azure-sql/database/dynamic-data-masking-configure-portal.md).
**Advanced Data Security** | Nepodporováno | Alternativní řešení: nastavte na úrovni SQL Server v cílové oblasti. [Další informace](../azure-sql/database/azure-defender-for-sql.md).
**Pravidla brány firewall** | Nepodporováno | Alternativní řešení: Nastavte pravidla firewallu pro SQL Server v cílové oblasti. Pravidla brány firewall na úrovni databáze se zkopírují ze zdrojového serveru na cílový server. [Další informace](../azure-sql/database/firewall-create-server-level-portal-quickstart.md).
**Zásady auditování** | Nepodporováno | Po přesunu se zásady obnoví na výchozí hodnoty. [Přečtěte si](../azure-sql/database/auditing-overview.md) , jak resetovat.
**Uchování záloh** | Podporuje se. | Zásady uchovávání záloh zdrojové databáze se přenesou do cílové databáze. [Naučte](../azure-sql/database/long-term-backup-retention-configure.md) se, jak upravit nastavení po přesunutí.
**Automatické ladění** | Nepodporováno | Alternativní řešení: nastavte nastavení automatického ladění po přesunutí. [Další informace](../azure-sql/database/automatic-tuning-enable.md).
**Výstrahy databáze** | Nepodporováno | Alternativní řešení: Nastavte výstrahy po přesunutí. [Další informace](../azure-sql/database/alerts-insights-configure-portal.md).
**Azure SQL Server Stretch Database** | Nepodporuje se | Nelze přesunout Stretch databází SQL Server s nástrojem Resource stěhovací.
**Azure Synapse Analytics** | Nepodporuje se | Nejde přesunout Azure synapse Analytics s nástrojem Resource stěhovací.
## <a name="next-steps"></a>Další kroky

Vyzkoušejte si [prostředky Azure SQL](tutorial-move-region-sql.md) do jiné oblasti s nástrojem Resource stěhovací.