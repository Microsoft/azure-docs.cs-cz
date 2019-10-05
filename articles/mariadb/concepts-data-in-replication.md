---
title: Replikovat data do Azure Database for MariaDB
description: Přečtěte si informace o použití replikace dat k synchronizaci z externího serveru do služby Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 826a6db289bf8b938e85d270f91836b3d8790206
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2019
ms.locfileid: "71973639"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikovat data do Azure Database for MariaDB

Replikace vstupních dat umožňuje synchronizovat data ze serveru MariaDB spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MariaDB. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MariaDB. Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kdy použít Replikace vstupních dat
V hlavních scénářích, které je potřeba zvážit, je použití Replikace vstupních dat:

- **Synchronizace hybridních dat:** Pomocí Replikace vstupních dat můžete udržovat data synchronizovaná mezi místními servery a Azure Database for MariaDB. Tato synchronizace je užitečná pro vytváření hybridních aplikací. Tato metoda je odvolání, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Synchronizace více cloudů:** Pro komplexní cloudová řešení použijte Replikace vstupních dat k synchronizaci dat mezi Azure Database for MariaDB a různými poskytovateli cloudu, včetně virtuálních počítačů a databázových služeb hostovaných v těchto cloudech.

## <a name="limitations-and-considerations"></a>Omezení a požadavky

### <a name="data-not-replicated"></a>Nereplikovaná data
[*Systémová databáze MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na hlavním serveru není replikovaná. Změny účtů a oprávnění na hlavním serveru se nereplikují. Pokud vytvoříte účet na hlavním serveru a tento účet potřebuje přístup k serveru repliky, pak ručně vytvořte stejný účet na straně serveru repliky. Informace o tom, jaké tabulky jsou obsaženy v systémové databázi, najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Požadavky
- Verze hlavního serveru musí být minimálně MariaDB verze 10,2.
- Verze hlavního serveru a serveru repliky musí být stejné. Oba typy musí být například MariaDB verze 10,2.
- Každá tabulka musí mít primární klíč.
- Hlavní server by měl používat modul InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binárního protokolování a vytváření nových uživatelů na hlavním serveru.
- Pokud má hlavní server povolený protokol SSL, ujistěte se, že certifikát certifikační autority SSL zadaný pro doménu byl zahrnutý v uložené proceduře `mariadb.az_replication_change_master`. Podívejte se na následující [Příklady](https://docs.microsoft.com/azure/mariadb/howto-data-in-replication#link-the-master-and-replica-servers-to-start-data-in-replication) a parametr `master_ssl_ca`.
- Ujistěte se, že se IP adresa hlavního serveru přidala do pravidel brány firewall serveru repliky Azure Database for MariaDB. Pomocí webu [Azure Portal](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-portal) nebo [Azure CLI](https://docs.microsoft.com/azure/mariadb/howto-manage-firewall-cli) aktualizujte pravidla brány firewall.
- Ujistěte se, že počítač, který je hostitelem hlavního serveru, umožňuje příchozí i odchozí provoz na portu 3306.
- Ujistěte se, že hlavní server má **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN).

### <a name="other"></a>Jiné
- Replikace dat je podporovaná jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [nastavit replikaci dat](howto-data-in-replication.md).
