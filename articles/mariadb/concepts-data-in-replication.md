---
title: Replikace dat do služby Azure Database pro MariaDB.
description: Tento článek popisuje dat replikace pro službu Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: a6bb6ae45c4240f21b1e0baf4956b053d4b98720
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46948175"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikace dat do služby Azure Database pro MariaDB

Replikace dat umožňuje synchronizovat data ze serveru MariaDB s místně, v databázi služby hostované jiných poskytovatelů cloudových služeb do služby Azure Database pro MariaDB službu nebo virtuálních počítačích. Replikace dat je založená na binární protokol (binlog) na základě pozice replikace souboru nativní MariaDB. Další informace o binlog replikace, najdete v článku [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikace dat
Hlavní scénáře pro zvážení použití replikace dat jsou:

- **Synchronizace dat hybridní:** se Data replikace, abyste mohli data synchronizovat mezi vašimi místními servery a Azure Database pro MariaDB. Tato synchronizace je užitečné pro vytváření hybridních aplikací. Tato metoda je přitažlivými, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Multicloudové synchronizace:** komplexní cloudové řešení, pomocí replikace dat pro synchronizaci dat mezi – Azure Database pro MariaDB a různí poskytovatelé cloudových, včetně virtual machines a databáze služby hostované v tyto cloudy.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Není replikace dat
[ *Systémovou databázi mysql* ](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na hlavním serveru se nereplikuje. Nebudou se replikovat změny v účtech a oprávněních v hlavní server. Pokud vytvoříte účet na hlavním serveru a tento účet musí mít přístup k serveru repliky, pak ručně vytvořte stejný účet na straně serveru repliky. Vysvětlení, co tabulky jsou obsaženy v systémové databázi, najdete v tématu [MariaDB dokumentaci](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Požadavky
- Verze hlavního serveru musí být dlouhý aspoň MariaDB verze 10.2.
- Verze serveru master a repliky se musí shodovat. Například musí být MariaDB verze 10.2.
- Každá tabulka musí obsahovat primární klíč.
- Hlavní server by měl používat modul InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binární protokolování a vytváření nových uživatelů na hlavní server.

### <a name="other"></a>Ostatní
- Replikace dat je jen pro podporované obecné účely a optimalizované pro paměť cenové úrovně.
- Identifikátory globální transakce (GTID) nejsou podporovány.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nastavení replikace dat](howto-data-in-replication.md).
