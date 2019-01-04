---
title: Replikace dat do služby Azure Database pro MariaDB.
description: Tento článek popisuje dat replikace pro službu Azure Database pro MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 0a1ead1580f6764fec7d1d18daa38bf093f242f2
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2018
ms.locfileid: "53547600"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikace dat do služby Azure Database pro MariaDB

Replikace dat umožňuje synchronizovat data ze serveru MariaDB s místně, v databázi služby hostované jiných poskytovatelů cloudových služeb do služby Azure Database pro MariaDB službu nebo virtuálních počítačích. Replikace dat je založená na binární protokol (binlog) na základě pozice replikace souboru nativní MariaDB. Další informace o binlog replikace, najdete v článku [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikace dat
Hlavní scénáře pro zvážení použití replikace dat jsou:

- **Hybridní synchronizace dat:** Pomocí Data replikace můžete zachovat data synchronizovat mezi vašimi místními servery a Azure Database pro MariaDB. Tato synchronizace je užitečné pro vytváření hybridních aplikací. Tato metoda je přitažlivými, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Multicloudové synchronizace:** Pro komplexní cloudové řešení pomocí replikace dat pro synchronizaci dat mezi – Azure Database pro MariaDB a různí poskytovatelé cloudových, včetně virtual machines a databáze služby hostované v tyto cloudy.

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
