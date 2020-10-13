---
title: Replikace dat – Azure Database for MySQL
description: Přečtěte si informace o použití replikace dat k synchronizaci z externího serveru do služby Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 8/7/2020
ms.openlocfilehash: 9212142ff6f43a84b141b0781fbe9828eebcbd40
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91537153"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikovat data do Azure Database for MySQL

Replikace vstupních dat umožňuje synchronizovat data z externího serveru MySQL do služby Azure Database for MySQL. Externí server může být místní, virtuální počítače nebo databázová služba, jejímž hostitelem jsou jiní poskytovatelé cloudu. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MySQL. Další informace o replikaci binlog najdete v tématu [Přehled replikace MySQL binlog](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kdy použít Replikace vstupních dat
V hlavních scénářích, které je potřeba zvážit, je použití Replikace vstupních dat:

- **Synchronizace hybridních dat:** Pomocí Replikace vstupních dat můžete udržovat data synchronizovaná mezi místními servery a Azure Database for MySQL. Tato synchronizace je užitečná pro vytváření hybridních aplikací. Tato metoda je odvolání, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Synchronizace více cloudů:** Pro komplexní cloudová řešení použijte Replikace vstupních dat k synchronizaci dat mezi Azure Database for MySQL a různými poskytovateli cloudu, včetně virtuálních počítačů a databázových služeb hostovaných v těchto cloudech.
 
Pro scénáře migrace použijte [Azure Database Migration Service](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Nereplikovaná data
[*Systémová databáze MySQL*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) na zdrojovém serveru se nereplikuje. Změny účtů a oprávnění na zdrojovém serveru se nereplikují. Pokud vytvoříte účet na zdrojovém serveru a tento účet potřebuje přístup k serveru repliky, ručně vytvořte stejný účet na straně serveru repliky. Informace o tom, jaké tabulky jsou obsaženy v systémové databázi, najdete v [příručce k MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="filtering"></a>Filtrování
Pokud chcete přeskočit replikaci tabulek ze zdrojového serveru (hostovaných místně, na virtuálních počítačích nebo v databázové službě hostované jinými poskytovateli cloudu), `replicate_wild_ignore_table` je tento parametr podporovaný. Volitelně můžete tento parametr aktualizovat na serveru repliky hostovaném v Azure pomocí [Azure Portal](howto-server-parameters.md) nebo [Azure CLI](howto-configure-server-parameters-using-cli.md).

Další informace o tomto parametru najdete v [dokumentaci k MySQL](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) .

### <a name="requirements"></a>Požadavky
- Verze zdrojového serveru musí být aspoň MySQL verze 5,6. 
- Verze serveru pro zdroj a replika musí být stejné. Musí být například MySQL verze 5,6 nebo musí být MySQL verze 5,7.
- Každá tabulka musí mít primární klíč.
- Zdrojový server by měl používat modul MySQL InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binárního protokolování a vytváření nových uživatelů na zdrojovém serveru.
- Pokud je na zdrojovém serveru povolený protokol SSL, ujistěte se, že je v uložené proceduře zahrnutý certifikát certifikační autority SSL zadaný pro tuto doménu `mysql.az_replication_change_master` . Podívejte se na následující [Příklady](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) a `master_ssl_ca` parametr.
- Zajistěte, aby byla IP adresa zdrojového serveru přidaná do pravidel brány firewall serveru repliky Azure Database for MySQL. Pomocí webu [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) nebo [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) aktualizujte pravidla brány firewall.
- Ujistěte se, že počítač, který hostuje zdrojový server, umožňuje příchozí i odchozí provoz na portu 3306.
- Ujistěte se, že zdrojový server má **veřejnou IP adresu**, služba DNS je veřejně přístupná nebo má plně kvalifikovaný název domény (FQDN).

### <a name="other"></a>Jiné
- Replikace dat je podporovaná jenom v Pro obecné účely a paměťově optimalizované cenové úrovně.
- Identifikátory globálních transakcí (GTID) se nepodporují.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [nastavit replikaci dat](howto-data-in-replication.md) .
- Další informace o [replikaci v Azure s replikami pro čtení](concepts-read-replicas.md)
- Přečtěte si, jak [migrovat data s minimálními výpadky pomocí DMS](howto-migrate-online.md) .