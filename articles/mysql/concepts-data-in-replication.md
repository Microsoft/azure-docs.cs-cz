---
title: Replikaci dat do Azure databáze pro databázi MySQL.
description: Tento článek popisuje data v replikace pro databázi Azure pro databázi MySQL.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql-database
ms.topic: article
ms.date: 05/18/2018
ms.openlocfilehash: 6efb034f63f65283911f46bbb251b5eb12d517ec
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654864"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikaci dat do Azure databáze pro databázi MySQL

Replikace dat v umožňuje synchronizovat data z databáze MySQL serveru se systémem na místních počítačích v virtuální počítače, nebo v databázi služby hostované jiných poskytovatelů cloudu do databáze Azure pro službu MySQL. Replikace dat v je založena na pozici, na základě replikace souborů binární protokol (binlog) nativní pro MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikační Data v
Hlavní scénáře, zvažte možnost použití replikace dat v jsou:

- **Synchronizace dat hybridní:** Data v replikaci, můžete ponechat data synchronizovat mezi místními servery a Azure Database pro databázi MySQL. Tato synchronizace je užitečné pro vytváření hybridní aplikace. Tato možnost je přitažlivými, pokud máte existující místní databázový server, ale chcete přesunout data v oblasti blíže koncovým uživatelům.
- **Synchronizaci s více cloudu:** pro komplexní cloudové řešení pro použití replikace dat pro synchronizaci dat mezi Azure Database pro databázi MySQL a zprostředkovatelů jiný cloud, včetně virtuálních počítačů a databáze služby hostované v tyto cloudy.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Není replikace dat
[ *Systémovou databázi mysql* ](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) nejsou replikována na primárním serveru. To zahrnuje změny účty a oprávnění na primárním serveru. Pokud vytvoříte účet na primárním serveru a tento účet potřebuje přístup k serveru repliky, pak ručně vytvořte stejný účet na straně serveru repliky. Abyste pochopili, jaký tabulky jsou obsaženy v systémové databázi, naleznete [MySQL ruční](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

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