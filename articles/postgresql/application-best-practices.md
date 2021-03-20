---
title: Osvědčené postupy pro vývoj aplikací – Azure Database for PostgreSQL
description: Seznamte se s osvědčenými postupy pro vytváření aplikací pomocí Azure Database for PostgreSQL.
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/10/2020
ms.openlocfilehash: 6463f30bc79d937bd5a51a5c8c78fbdd72954b1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97364573"
---
# <a name="best-practices-for-building-an-application-with-azure-database-for-postgresql"></a>Osvědčené postupy pro sestavování aplikace pomocí Azure Database for PostgreSQL

Tady jsou některé osvědčené postupy, které vám pomůžou vytvořit aplikaci připravenou pro cloud pomocí Azure Database for PostgreSQL. Tyto osvědčené postupy mohou zkrátit dobu vývoje aplikace.

## <a name="configuration-of-application-and-database-resources"></a>Konfigurace prostředků aplikace a databáze

### <a name="keep-the-application-and-database-in-the-same-region"></a>Zachovat aplikaci a databázi ve stejné oblasti
Když nasadíte aplikaci v Azure, ujistěte se, že jsou všechny závislosti ve stejné oblasti. Rozprostření instancí mezi oblasti nebo zóny dostupnosti vytváří latenci sítě, což může mít vliv na celkový výkon aplikace.

### <a name="keep-your-postgresql-server-secure"></a>Zajištění zabezpečení serveru PostgreSQL
Nakonfigurujte server PostgreSQL tak, aby byl [zabezpečený](./concepts-security.md) a není veřejně přístupný. K zabezpečení serveru použijte jednu z těchto možností:
- [Pravidla brány firewall](./concepts-firewall-rules.md)
- [Virtuální sítě](./concepts-data-access-and-security-vnet.md)
- [Azure Private Link](./concepts-data-access-and-security-private-link.md)

Z bezpečnostních důvodů se musíte ke svému serveru PostgreSQL připojit přes SSL a nakonfigurovat server PostgreSQL a svoji aplikaci tak, aby používala TLS 1,2. Přečtěte si téma [Konfigurace protokolu SSL/TLS](./concepts-ssl-connection-security.md).

### <a name="tune-your-server-parameters"></a>Vyladění parametrů serveru
Pro úlohy s vyladěním pro čtení a zvýšení `tmp_table_size` výkonu, které `max_heap_table_size` mohou optimalizovat. Chcete-li vypočítat hodnoty požadované pro tyto proměnné, podívejte se na celkový počet hodnot paměti pro připojení a základní paměť. Součet parametrů paměti pro připojení s výjimkou v `tmp_table_size` kombinaci se základními účty paměti pro celkovou paměť serveru.

### <a name="use-environment-variables-for-connection-information"></a>Pro informace o připojení použít proměnné prostředí
Neukládejte přihlašovací údaje databáze do kódu aplikace. V závislosti na aplikaci front-end postupujte podle pokynů pro nastavení proměnných prostředí. Informace o použití služby App Service najdete v článku[jak nakonfigurovat nastavení aplikace](../app-service/configure-common.md#configure-app-settings) a službu Azure Kuberentes, najdete v tématu [How to use Kubernetes tajných klíčů](https://kubernetes.io/docs/concepts/configuration/secret/).

## <a name="performance-and-resiliency"></a>Výkon a odolnost
Tady je několik nástrojů a postupů, které můžete použít k ladění problémů s výkonem ve vaší aplikaci.

### <a name="use-connection-pooling"></a>Použití sdružování připojení
S sdružováním připojení se v době spuštění a údržbě vytvoří pevná sada připojení. To taky pomáhá snižovat fragmentaci paměti na serveru, který je způsobený dynamickými novými připojeními vytvořenými na databázovém serveru. Sdružování připojení lze nakonfigurovat na straně aplikace, pokud ji rozhraní aplikace nebo ovladač databáze podporuje. Pokud tato možnost není podporovaná, doporučuje se využít Pooler službu proxy připojení, jako je [PgBouncer](https://pgbouncer.github.io/) nebo [pgpool](https://pgpool.net/mediawiki/index.php/Main_Page) spuštěné mimo aplikaci a připojení k databázovému serveru. PgBouncer i pgpool jsou nástroje založené na komunitě, které pracují s Azure Database for PostgreSQL.

### <a name="retry-logic-to-handle-transient-errors"></a>Logika opakování pro zpracování přechodných chyb
V aplikaci může docházet k přechodným chybám, při kterých se připojení k databázi neúčtují nebo ztratí přerušovaně. V takových situacích je server v provozu po jednom až dvou opakovaných pokusech za 5 až 10 sekund. Dobrým postupem je počkat na 5 sekund před prvním opakováním. Pak postupujte podle každého opakování tak, že postupně rozrostete, až 60 sekund. Omezte maximální počet opakovaných pokusů, při kterých aplikace nebere v úvahu operaci, takže se můžete dále prozkoumat. Další informace najdete v tématu [řešení chyb připojení](./concepts-connectivity.md) .

### <a name="enable-read-replication-to-mitigate-failovers"></a>Povolením replikace pro čtení zmírnit převzetí služeb při selhání
Pro scénáře převzetí služeb při selhání můžete použít [replikace vstupních dat](./concepts-read-replicas.md) . Pokud používáte repliky čtení, nedochází k automatizovanému převzetí služeb při selhání mezi zdrojovým serverem a serverem repliky. Všimnete si prodlevy mezi zdrojem a replikou, protože replikace je asynchronní. Prodleva sítě může být ovlivněná mnoha faktory, jako je velikost úlohy spuštěné na zdrojovém serveru a latence mezi datovými centry. Ve většině případů prodleva repliky vychází z několika sekund až do několika minut.


## <a name="database-deployment"></a>Nasazení databáze

### <a name="configure-cicd-deployment-pipeline"></a>Konfigurace kanálu nasazení CI/CD
V některých případech je nutné nasadit změny do vaší databáze. V takových případech můžete použít průběžnou integraci (CI) prostřednictvím [akcí GitHubu](https://github.com/Azure/postgresql/blob/master/README.md) pro server PostgreSQL k aktualizaci databáze spuštěním vlastního skriptu.

### <a name="define-manual-database-deployment-process"></a>Definovat ruční proces nasazení databáze
Při ručním nasazování databáze můžete pomocí těchto kroků minimalizovat prostoje nebo snížit riziko neúspěšného nasazení:

- Vytvořte kopii provozní databáze v nové databázi pomocí pg_dump.
- Aktualizujte novou databázi pomocí nových změn schématu nebo aktualizací potřebných pro vaši databázi.
- Produkční databáze umístěte do stavu jen pro čtení. Dokud se nasazení nedokončí, neměli byste mít v provozní databázi žádné operace zápisu.
- Otestujte aplikaci pomocí nově aktualizované databáze z kroku 1.
- Nasaďte změny aplikace a ujistěte se, že aplikace teď používá novou databázi s nejnovějšími aktualizacemi.
- Ponechte starou provozní databázi, abyste mohli změny vrátit zpět. Pak se můžete vyhodnotit tak, že odstraníte starou provozní databázi nebo ji v případě potřeby vyexportujete do Azure Storage.

>  [!NOTE]
> Pokud je aplikace jako aplikace pro elektronické obchodování a nemůžete ji vložit do stavu jen pro čtení, nasaďte změny přímo do provozní databáze po vytvoření zálohy. Změna následujících by se měla provádět v době mimo špičku s malým provozem do aplikace, aby se minimalizoval dopad, protože někteří uživatelé můžou narazit na neúspěšné požadavky. Ujistěte se, že kód aplikace také zpracovává všechny neúspěšné požadavky.

## <a name="database-schema-and-queries"></a>Schéma databáze a dotazy
Tady je několik tipů, které byste měli mít na paměti při sestavování schématu databáze a vašich dotazů.

### <a name="use-bigint-or-uuid-for-primary-keys"></a>Pro primární klíče použít BIGINT nebo UUID
Při sestavování vlastní aplikace nebo některých rozhraní, která možná používají `INT` místo `BIGINT` pro primární klíče. Při použití nástroje můžete ```INT``` Spustit riziko, že hodnota ve vaší databázi může překročit kapacitu úložiště ```INT``` datového typu. Tato změna v existující produkční aplikaci může být časově náročná s vyššími náklady na čas vývoje. Další možností je použít pro primární klíče [UUID](https://www.postgresql.org/docs/current/datatype-uuid.html) . Tento identifikátor používá automaticky generovaný 128 bitový řetězec, například ```a0eebc99-9c0b-4ef8-bb6d-6bb9bd380a11``` . Přečtěte si další informace o [PostgreSQL datových typech](https://www.postgresql.org/docs/8.1/datatype.html).

### <a name="use-indexes"></a>Použití indexů

V Postgres existuje mnoho typů [indexů](https://www.postgresql.org/docs/9.1/indexes.html) , které lze použít různými způsoby. Použití indexu pomáhá serveru najít a načíst konkrétní řádky mnohem rychleji, než kolik jich mohl dělat bez indexu. Ale indexy také přidávají režii databázovému serveru, takže se vyhnete příliš mnoha indexům.

### <a name="use-autovacuum"></a>Použít autovaku
Můžete optimalizovat Server s využitím autovaku na Azure Database for PostgreSQL serveru. PostgreSQL umožňuje větší souběžnost databáze, ale při každém výsledku aktualizace se vloží a odstraní. Pro odstranění jsou záznamy měkkým označením, které se budou vyprázdnit později. Pro provedení těchto úloh PostgreSQL spustí vakuovou úlohu. Pokud neurčíte čas do doby od času, může docházet k neaktivním řazeným kolekcím, které se shromažďují:

- Dispozici determinističtější dat, jako jsou například větší databáze a tabulky.
- Větší podoptimální indexy.
- Zvýšila se vstupně-výstupní operace.

Přečtěte si další informace o [tom, jak optimalizovat pomocí autovaku](howto-optimize-autovacuum.md).

### <a name="use-pg_stats_statements"></a>Použít pg_stats_statements
Pg_stat_statements je rozšíření PostgreSQL, které je ve výchozím nastavení povolené v Azure Database for PostgreSQL. Rozšíření poskytuje způsob, jak sledovat statistiku spouštění všech příkazů SQL spuštěných serverem. Podívejte [se, jak používat pg_statement](howto-optimize-query-stats-collection.md).


### <a name="use-the-query-store"></a>Použití úložiště dotazů
Funkce [úložiště dotazů](./concepts-query-store.md) v Azure Database for PostgreSQL poskytuje efektivnější způsob, jak sledovat statistiku dotazů. Tuto funkci doporučujeme jako alternativu k používání pg_stats_statements.

### <a name="optimize-bulk-inserts-and-use-transient-data"></a>Optimalizujte hromadné vkládání a používejte přechodná data.
Pokud máte operace s úlohami, které zahrnují přechodná data nebo hromadné vkládání velkých datových sad, zvažte použití neprotokolovaných tabulek. Ve výchozím nastavení zajišťuje nedělitelnost a odolnost. Nedělitelnost, konzistence, izolace a odolnost tvoří vlastnosti KYSELosti. Podívejte [se, jak optimalizovat hromadné vložení](howto-optimize-bulk-inserts.md).

## <a name="next-steps"></a>Další kroky
[Průvodce Postgres](http://postgresguide.com/)
