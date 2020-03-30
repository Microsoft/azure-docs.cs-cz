---
title: Replikace dat – databáze Azure pro MySQL
description: Další informace o použití replikace datového připojení k synchronizaci z externího serveru do služby Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 20be34191355e6ade40e0f3b218818bfa5345a28
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533228"
---
# <a name="replicate-data-into-azure-database-for-mysql"></a>Replikace dat do databáze Azure pro MySQL

Replikace dat umožňuje synchronizovat data z externího serveru MySQL do služby Azure Database for MySQL. Externí server může být místní, ve virtuálních počítačích nebo databázová služba hostovaná jinými poskytovateli cloudu. Replikace vstupních dat je založená na replikaci na základě pozice v souboru binárního protokolu (binlog) nativní pro MySQL. Další informace o replikaci přihrádek najdete v [přehledu replikace binlogu MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html). 

## <a name="when-to-use-data-in-replication"></a>Kdy použít replikaci datového data
Hlavní scénáře, které je třeba zvážit použití replikace datového data, jsou:

- **Hybridní synchronizace dat:** Pomocí služby Data-in Replication můžete udržovat data synchronizovaná mezi místními servery a databází Azure pro MySQL. Tato synchronizace je užitečná pro vytváření hybridních aplikací. Tato metoda je přitažlivá, pokud máte existující místní databázový server, ale chcete přesunout data do oblasti blíže koncovým uživatelům.
- **Synchronizace více cloudů:** U komplexních cloudových řešení použijte službu Data-in Replication k synchronizaci dat mezi databází Azure pro MySQL a různými poskytovateli cloudu, včetně virtuálních počítačů a databázových služeb hostovaných v těchto cloudech.
 
Pro scénáře migrace použijte [službu Migrace databáze Azure](https://azure.microsoft.com/services/database-migration/)(DMS).

## <a name="limitations-and-considerations"></a>Omezení a důležité informace

### <a name="data-not-replicated"></a>Nereplikovaná data
[*Databáze systému mysql*](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html) na hlavním serveru není replikována. Změny účtů a oprávnění na hlavním serveru nejsou replikovány. Pokud vytvoříte účet na hlavním serveru a tento účet potřebuje přístup k serveru repliky, ručně vytvořte stejný účet na straně serveru repliky. Chcete-li pochopit, jaké tabulky jsou obsaženy v systémové databázi, naleznete v [příručce MySQL](https://dev.mysql.com/doc/refman/5.7/en/system-schema.html).

### <a name="requirements"></a>Požadavky
- Verze hlavního serveru musí být alespoň MySQL verze 5.6. 
- Verze hlavního a replikového serveru musí být stejné. Například oba musí být MySQL verze 5.6 nebo obojí musí být MySQL verze 5.7.
- Každá tabulka musí mít primární klíč.
- Hlavní server by měl používat modul MySQL InnoDB.
- Uživatel musí mít oprávnění ke konfiguraci binárního protokolování a vytváření nových uživatelů na hlavním serveru.
- Pokud je na hlavním serveru povolenprotokol SSL, ujistěte se, že certifikát `mysql.az_replication_change_master` SSL CA poskytnutý pro doménu byl zahrnut do uložené procedury. Podívejte se na následující `master_ssl_ca` [příklady](https://docs.microsoft.com/azure/mysql/howto-data-in-replication#link-master-and-replica-servers-to-start-data-in-replication) a parametr.
- Ujistěte se, že se IP adresa hlavního serveru přidala do pravidel brány firewall serveru repliky Azure Database for MySQL. Pomocí webu [Azure Portal](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-portal) nebo [Azure CLI](https://docs.microsoft.com/azure/mysql/howto-manage-firewall-using-cli) aktualizujte pravidla brány firewall.
- Ujistěte se, že počítač, který je hostitelem hlavního serveru, umožňuje příchozí i odchozí provoz na portu 3306.
- Ujistěte se, že hlavní server má **veřejnou IP adresu**, DNS je veřejně přístupný nebo má plně kvalifikovaný název domény (Plně kvalifikovaný název domény).

### <a name="other"></a>Ostatní
- Replikace dat je podporována pouze v cenových úrovních s optimalizací pro obecné účely a paměť.
- Globální identifikátory transakcí (GTID) nejsou podporovány.

## <a name="next-steps"></a>Další kroky
- Přečtěte si, jak [nastavit replikaci dat](howto-data-in-replication.md)
- Informace o [replikaci v Azure pomocí replik pro čtení](concepts-read-replicas.md)
- Informace o [migraci dat s minimálními prostoji pomocí služby DMS](howto-migrate-online.md)