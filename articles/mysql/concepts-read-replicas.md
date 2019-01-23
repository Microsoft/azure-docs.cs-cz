---
title: Čtení replik ve službě Azure Database for MySQL.
description: Tento článek popisuje další repliky pro službu Azure Database for MySQL.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 1/22/2019
ms.openlocfilehash: 6ebbaece66d9055fd2bff68eee873b012b4a6d50
ms.sourcegitcommit: cf88cf2cbe94293b0542714a98833be001471c08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54462416"
---
# <a name="read-replicas-in-azure-database-for-mysql"></a>Repliky pro čtení ve službě Azure Database for MySQL

Funkce repliky pro čtení (public preview) umožňuje replikaci dat ze serveru Azure Database for MySQL server (správce) až k pěti jen pro čtení serverům (repliky) v rámci stejné oblasti Azure. Repliky jen pro čtení se aktualizují asynchronně pomocí technologie replikace na základě pozice souboru nativní binární protokol (binlog) stroje MySQL. Další informace o binlog replikace, najdete v článku [Přehled replikace binlog MySQL](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Repliky vytvořené v Azure Database for MySQL služby jsou nové servery, které je možné spravovat stejným způsobem jako normální nebo samostatné servery MySQL. Pro každou další repliku bude vám Účtovaná zřízených výpočetních jádrech a zřízeného úložiště v GB/měsíc. 


Další informace o funkcích replikace MySQL a problémů, najdete v tématu [MySQL replikace dokumentaci](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html).

## <a name="when-to-use-read-replicas"></a>Kdy použít repliky pro čtení

Aplikace a úlohy náročné na čtení můžou být obsluhovány repliky jen pro čtení. Čtení replik zvýšit množství čtení dostupné kapacity ve srovnání s, pokud byste chtěli stačí použít jeden server pro čtení a zápis. Další úlohy můžou být izolované do replik, při zápisu úlohy mohou být přesměrováni na hlavní server.

Běžný scénář, kdy je, aby BI a analytických úloh pomocí repliky pro čtení jako zdroj dat pro generování sestav.

## <a name="considerations-and-limitations"></a>Požadavky a omezení

### <a name="pricing-tiers"></a>Cenové úrovně

Repliky pro čtení jsou aktuálně dostupné jenom v cenové úrovně pro obecné účely a optimalizované pro paměť.

### <a name="master-server-restart"></a>Hlavní server restartovat

Během tohoto období preview při vytvoření repliky pro hlavní server, který nemá žádnou existující repliku, hlavní nejprve restartuje připraví pro replikaci. Vzít v úvahu a provádění těchto operací během období mimo špičku.

### <a name="stopping-replication"></a>Zastavení replikace

Můžete se na zastavení replikace mezi hlavní a serverem repliky. Zastavení replikace, odebere se vztah replikace mezi serverem pro hlavní a repliky.

Po zastavení replikace serveru repliky stane samostatný server. Data v samostatném serveru jsou data, která byla k dispozici na replice v době, kdy byl spuštěn příkaz "zastavení replikace". Samostatný server nebude zachytávat s hlavním serverem. Tento server nelze je převést na repliku znovu.

### <a name="replicas-are-new-servers"></a>Repliky jsou nové servery

Repliky jsou vytvořeny jako nové – Azure Database for MySQL servery. Nelze je převést existující servery repliky.

### <a name="replica-server-configuration"></a>Konfigurace serveru repliky

Servery repliky jsou vytvořené pomocí stejné konfigurace serveru na hlavní server, který obsahuje následující konfigurace:

- Cenová úroveň
- Generace výpočetních funkcí
- Virtuální jádra
- Storage
- Období uchování zálohy
- Možnosti redundance zálohy
- Verze stroje MySQL
- Pravidla brány firewall

Po vytvoření repliky, můžete změnit cenovou úroveň (s výjimkou do a z Basic), výpočetní generaci, virtuální jádra, úložiště a uchovávání záloh odděleně od hlavního serveru.

### <a name="master-server-configuration"></a>Konfigurace hlavního serveru

Pokud hlavní konfigurační server (např.) virtuálních jader a úložiště) je aktualizovaná, konfigurace repliky také je potřeba aktualizovat na stejné nebo vyšší hodnoty. Server repliky bez toho nemusí být schopné udržovat tempo se změnami provedenými na hlavní server a může dojít k chybě v důsledku.

Nová pravidla brány firewall přidat do hlavního serveru po vytvoření serveru repliky se nereplikují do repliky. Toto nové pravidlo brány firewall a je třeba aktualizovat repliku.

### <a name="deleting-the-master-server"></a>Odstraňuje se hlavní server

Při odstranění je hlavní server, se zastaví replikace na všechny repliky pro čtení. Tyto repliky se stanou samostatné servery. Hlavní server sám se odstraní.

### <a name="user-accounts"></a>Uživatelské účty

Uživatelé na hlavním serveru se replikují do repliky pro čtení. Můžete připojit jenom pro čtení replikou s použitím uživatelské účty, které jsou dostupné na hlavním serveru.

### <a name="other"></a>Ostatní

- Identifikátory globální transakce (GTID) nejsou podporovány.
- Vytváří se replika repliky se nepodporuje.
- Tabulky v paměti může způsobit, že repliky přestane být synchronní. Jedná se omezení technologie replikace MySQL. Další informace najdete v [MySQL referenční dokumentaci](https://dev.mysql.com/doc/refman/5.7/en/replication-features-memory.html) Další informace.
- Ladění [ `innodb_file_per_table` ](https://dev.mysql.com/doc/refman/5.7/en/innodb-multiple-tablespaces.html) parametr na hlavním serveru po vytváření serveru repliky může způsobit, že repliky přestane být synchronní. Server repliky není přehled o různých tabulkové prostory.
- Úplný seznam omezení replikace MySQL [dokumentace ke službě MySQL](https://dev.mysql.com/doc/refman/5.7/en/replication-features.html)


## <a name="next-steps"></a>Další postup

- Zjistěte, jak [vytvářet a spravovat další repliky pomocí webu Azure portal](howto-read-replicas-portal.md)
- Zjistěte, jak [vytvářet a spravovat další repliky pomocí Azure CLI](howto-read-replicas-cli.md)
