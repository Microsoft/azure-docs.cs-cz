---
title: Replikace dat – Azure Database for MariaDB
description: Přečtěte si informace o použití replikace dat k synchronizaci z externího serveru do služby Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 331e064bcf11af31a778cb8dd06c463712421b7c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98662623"
---
# <a name="replicate-data-into-azure-database-for-mariadb"></a>Replikovat data do Azure Database for MariaDB

Replikace vstupních dat umožňuje synchronizovat data ze serveru MariaDB spuštěného v místním prostředí, na virtuálních počítačích nebo v databázových službách hostovaných jinými poskytovateli cloudových služeb do služby Azure Database for MariaDB. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MariaDB. Další informace o replikaci binlog najdete v tématu [Přehled replikace binlog](https://mariadb.com/kb/en/library/replication-overview/).

## <a name="when-to-use-data-in-replication"></a>Kdy použít Replikace vstupních dat
V hlavních scénářích, které je potřeba zvážit, je použití Replikace vstupních dat:

- **Synchronizace hybridních dat:** Pomocí Replikace vstupních dat můžete udržovat data synchronizovaná mezi místními servery a Azure Database for MariaDB. Tato synchronizace je užitečná pro vytváření hybridních aplikací. Tato metoda je odvolání, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Synchronizace více cloudů:** Pro komplexní cloudová řešení použijte Replikace vstupních dat k synchronizaci dat mezi Azure Database for MariaDB a různými poskytovateli cloudu, včetně virtuálních počítačů a databázových služeb hostovaných v těchto cloudech.

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Nereplikovaná data
[*Systémová databáze MySQL*](https://mariadb.com/kb/en/library/the-mysql-database-tables/) na zdrojovém serveru není replikovaná. Změny účtů a oprávnění na zdrojovém serveru se nereplikují. Pokud vytvoříte účet na zdrojovém serveru a tento účet potřebuje přístup k serveru repliky, pak na straně serveru repliky ručně vytvořte stejný účet. Informace o tom, jaké tabulky jsou obsaženy v systémové databázi, najdete v [dokumentaci k MariaDB](https://mariadb.com/kb/en/library/the-mysql-database-tables/).

### <a name="requirements"></a>Požadavky
- Verze zdrojového serveru musí být minimálně MariaDB verze 10,2.
- Verze serveru pro zdroj a replika musí být stejné. Oba typy musí být například MariaDB verze 10,2.
- Každá tabulka musí mít primární klíč.
- Zdrojový server by měl používat modul InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binárního protokolování a vytváření nových uživatelů na zdrojovém serveru.
- Pokud je na zdrojovém serveru povolený protokol SSL, ujistěte se, že je v uložené proceduře zahrnutý certifikát certifikační autority SSL zadaný pro tuto doménu `mariadb.az_replication_change_master` . Podívejte se na následující [Příklady](howto-data-in-replication.md#link-the-source-and-replica-servers-to-start-data-in-replication) a `master_ssl_ca` parametr.
- Zajistěte, aby byla IP adresa zdrojového serveru přidaná do pravidel brány firewall serveru repliky Azure Database for MariaDB. Pomocí webu [Azure Portal](howto-manage-firewall-portal.md) nebo [Azure CLI](howto-manage-firewall-cli.md) aktualizujte pravidla brány firewall.
- Ujistěte se, že počítač, který hostuje zdrojový server, umožňuje příchozí i odchozí provoz na portu 3306.
- Ujistěte se, že zdrojový server má **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN).

### <a name="other"></a>Jiné
- Replikace dat je podporovaná jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [nastavit replikaci dat](howto-data-in-replication.md).
