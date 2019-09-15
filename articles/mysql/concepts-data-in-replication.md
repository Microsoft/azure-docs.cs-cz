---
title: Replikovat data do Azure Database for MySQL.
description: Tento článek popisuje replikaci dat v Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 5ef11e86b85a537a809352325d56ac3ff983c2c1
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993050"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikovat data do Azure Database for MySQL

Replikace dat umožňuje synchronizovat data z externího serveru MySQL do služby Azure Database for MySQL. Externí server může být místní, virtuální počítače nebo databázová služba, jejímž hostitelem jsou jiní poskytovatelé cloudu. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikaci dat
V hlavních scénářích, které je potřeba zvážit při replikaci dat, jsou tyto:

- **Synchronizace hybridních dat:** Díky replikaci dat můžete udržovat data synchronizovaná mezi místními servery a Azure Database for MySQL. Tato synchronizace je užitečná pro vytváření hybridních aplikací. Tato metoda je odvolání, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Synchronizace více cloudů:** V případě komplexních cloudových řešení můžete pomocí replikace dat synchronizovat data mezi Azure Database for MySQL a různými poskytovateli cloudu, včetně virtuálních počítačů a databázových služeb hostovaných v těchto cloudech.
 
Pro scénáře migrace použijte [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Omezení a požadavky

### <a name="data-not-replicated"></a>Nereplikovaná data
[*Systémová databáze MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-database.html) na hlavním serveru se nereplikuje. Změny účtů a oprávnění na hlavním serveru se nereplikují. Pokud vytvoříte účet na hlavním serveru a tento účet potřebuje přístup k serveru repliky, ručně vytvořte stejný účet na straně serveru repliky. Informace o tom, jaké tabulky jsou obsaženy v systémové databázi, najdete v [příručce k MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-database.html).

### <a name="requirements"></a>Požadavky
- Verze hlavního serveru musí být aspoň MySQL verze 5,6. 
- Verze hlavního serveru a serveru repliky musí být stejné. Musí být například MySQL verze 5,6 nebo musí být MySQL verze 5,7.
- Každá tabulka musí obsahovat primární klíč.
- Hlavní server by měl používat modul MySQL InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binárního protokolování a vytváření nových uživatelů na hlavním serveru.
- Pokud má hlavní server povolený protokol SSL, ujistěte se, že je v `mysql.az_replication_change_master` uložené proceduře zahrnutý certifikát certifikační autority SSL zadaný pro tuto doménu. Podívejte se na následující [Příklady](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) a `master_ssl_ca` parametr.
- Ujistěte se, že se IP adresa hlavního serveru přidala do pravidel brány firewall serveru repliky Azure Database for MySQL. Pomocí webu [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) nebo [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) aktualizujte pravidla brány firewall.
- Ujistěte se, že počítač, který je hostitelem hlavního serveru, umožňuje příchozí i odchozí provoz na portu 3306.
- Ujistěte se, že hlavní server má **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN).

### <a name="other"></a>Ostatní
- Replikace dat je podporovaná jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.
- Identifikátory globálních transakcí (GTID) se nepodporují.

## <a name="next-steps"></a>Další postup
- Přečtěte si, jak [nastavit replikaci dat](howto-data-in-replication.md) .
- Další informace o [replikaci v Azure s replikami pro čtení](concepts-read-replicas.md)
- Přečtěte si, jak [migrovat data s minimálními výpadky pomocí DMS](howto-migrate-online.md) .