---
title: Replikaci dat do Azure databáze pro databázi MySQL.
description: Tento článek popisuje data v replikace pro databázi Azure pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 06/20/2018
ms.openlocfilehash: 72f8211ecc0534b15402911de8fc0ec3d541a835
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36294900"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikaci dat do Azure databáze pro databázi MySQL

Replikace dat v umožňuje synchronizovat data z databáze MySQL serveru se systémem na místních počítačích v virtuální počítače, nebo v databázi služby hostované jiných poskytovatelů cloudu do databáze Azure pro službu MySQL. Data v replikace je založena na binární protokol (binlog) na základě pozice replikace souboru nativní pro MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikační Data v
Hlavní scénáře, zvažte možnost použití replikace dat v jsou:

- **Synchronizace dat hybridní:** Data v replikaci, můžete ponechat data synchronizovat mezi místními servery a Azure Database pro databázi MySQL. Tato synchronizace je užitečné pro vytváření hybridní aplikace. Tato metoda je přitažlivými, pokud máte existující místní databázový server, ale chcete přesunout data v oblasti blíže koncovým uživatelům.
- **Synchronizaci s více cloudu:** pro komplexní cloudové řešení pro použití replikace dat pro synchronizaci dat mezi Azure Database pro databázi MySQL a zprostředkovatelů jiný cloud, včetně virtuálních počítačů a databáze služby hostované v tyto cloudy.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Není replikace dat
[ *Systémovou databázi mysql* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) nejsou replikována na primárním serveru. Změny účtů a oprávnění na primárním serveru nejsou replikovány. Pokud vytvoříte účet na primárním serveru a tento účet potřebuje přístup k serveru repliky, pak ručně vytvořte stejný účet na straně serveru repliky. Chcete-li pochopit, jaké tabulky jsou obsaženy v systémové databázi, přečtěte si téma [MySQL ruční](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Požadavky
- Primární server verze musí být minimálně verze 5.6 MySQL. 
- Verze serveru primárním serverem a repliky musí být stejné. Například MySQL verze 5.6 musí být oba nebo MySQL verzi 5.7 musí být oba.
- Každá tabulka musí mít primární klíč.
- Primární server by měl používat modul MySQL InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binární protokolování a vytváření nových uživatelů na primárním serveru.

### <a name="other"></a>Ostatní
- Identifikátory globální transakce (GTID) nejsou podporovány.

## <a name="next-steps"></a>Další postup
- Zjistěte, jak [nastavení replikace dat v](howto-data-in-replication.md)
