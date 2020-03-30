---
title: Replikace dat – databáze Azure pro MariaDB
description: Další informace o použití replikace datového připojení k synchronizaci z externího serveru do služby Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 1fbcc1fb27d5e6df4641f79c0d634580f74000b8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532056"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikace dat do databáze Azure pro MariaDB

Replikace vstupních dat umožňuje synchronizovat data ze serveru MariaDB spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MariaDB. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MariaDB. Další informace o replikaci přihrádek najdete v [přehledu replikace přihrádek](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikaci datového data
Hlavní scénáře, které je třeba zvážit použití replikace datového data, jsou:

- **Hybridní synchronizace dat:** Pomocí služby Data-in Replication můžete udržovat data synchronizovaná mezi místními servery a databází Azure pro MariaDB. Tato synchronizace je užitečná pro vytváření hybridních aplikací. Tato metoda je přitažlivá, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Synchronizace více cloudů:** U komplexních cloudových řešení použijte službu Data-in Replication k synchronizaci dat mezi databází Azure pro MariaDB a různými poskytovateli cloudu, včetně virtuálních počítačů a databázových služeb hostovaných v těchto cloudech.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Nereplikovaná data
[*Databáze systému mysql*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na hlavním serveru není replikována. Změny účtů a oprávnění na hlavním serveru nejsou replikovány. Pokud vytvoříte účet na hlavním serveru a tento účet potřebuje přístup k replikovému serveru, vytvořte ručně stejný účet na straně serveru repliky. Chcete-li zjistit, jaké tabulky jsou obsaženy v systémové databázi, naleznete v [dokumentaci MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Požadavky
- Verze hlavního serveru musí být alespoň MariaDB verze 10.2.
- Verze hlavního a replikového serveru musí být stejné. Například oba musí být MariaDB verze 10.2.
- Každá tabulka musí mít primární klíč.
- Hlavní server by měl používat modul InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binárního protokolování a vytváření nových uživatelů na hlavním serveru.
- Pokud je na hlavním serveru povolenprotokol SSL, ujistěte se, že certifikát `mariadb.az_replication_change_master` SSL CA poskytnutý pro doménu byl zahrnut do uložené procedury. Podívejte se na následující `master_ssl_ca` [příklady](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) a parametr.
- Ujistěte se, že se IP adresa hlavního serveru přidala do pravidel brány firewall serveru repliky Azure Database for MariaDB. Pomocí webu [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) nebo [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli) aktualizujte pravidla brány firewall.
- Ujistěte se, že počítač, který je hostitelem hlavního serveru, umožňuje příchozí i odchozí provoz na portu 3306.
- Ujistěte se, že hlavní server má **veřejnou IP adresu**, DNS je veřejně přístupný nebo má plně kvalifikovaný název domény (Plně kvalifikovaný název domény).

### <a name="other"></a>Ostatní
- Replikace dat je podporována pouze v cenových úrovních s optimalizací pro obecné účely a paměť.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [nastavit replikaci dat](howto-data-in-replication.md).
