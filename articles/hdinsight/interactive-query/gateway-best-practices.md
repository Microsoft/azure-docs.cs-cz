---
title: Podrobně a osvědčené postupy brány pro Apache Hive ve službě Azure HDInsight
description: Naučte se, jak přejít k osvědčeným postupům pro spouštění dotazů na podregistry přes bránu Azure HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 63484d882d8ccd387257c6f246c2048a09c77bc8
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2021
ms.locfileid: "98933116"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Podrobně a osvědčené postupy brány pro Apache Hive ve službě Azure HDInsight

Brána Azure HDInsight (brána) je front-end HTTPS pro clustery HDInsight. Brána zodpovídá za: ověřování, rozlišení hostitele, zjišťování služeb a další užitečné funkce, které jsou nezbytné pro moderní distribuované systémy. U funkcí poskytovaných bránou dojde k nějaké režii, za kterou tento dokument popisuje osvědčené postupy pro navigaci. Jsou zde také popsány techniky řešení potíží s bránou.

## <a name="the-hdinsight-gateway"></a>Brána HDInsight

Brána HDInsight je jediná část clusteru HDInsight, která je veřejně přístupná prostřednictvím Internetu. Služba brány je k dispozici bez nutnosti přecházet přes veřejný Internet pomocí `clustername-int.azurehdinsight.net` koncového bodu interní brány. Koncový bod interní brány umožňuje navázat připojení ke službě brány bez opuštění virtuální sítě clusteru. Brána zpracovává všechny požadavky, které se odesílají do clusteru, a předává tyto požadavky na správné komponenty a hostitele clusteru.

Následující diagram poskytuje hrubou ilustraci, jak brána poskytuje abstrakci před všemi různými možnostmi rozlišení hostitele v rámci služby HDInsight.

![Diagram rozlišení hostitele](./media/gateway-best-practices/host-resolution-diagram.png "Diagram rozlišení hostitele")

## <a name="motivation"></a>Motivace

Motivace pro vložení brány do clusterů HDInsight je poskytnout rozhraní pro zjišťování služeb a ověřování uživatelů. Mechanismy ověřování poskytované bránou jsou obzvláště důležité pro clustery s podporou protokolu ESP.

Pro zjišťování služeb je výhodou brány, že každá součást v clusteru je přístupná jako jiný koncový bod na webu brány ( `clustername.azurehdinsight.net/hive2` ), a to na rozdíl od velkého množství `host:port` párů.

Pro ověřování brána umožňuje uživatelům ověřování pomocí `username:password` páru přihlašovacích údajů. U clusterů s podporou protokolu ESP by toto pověření představovalo uživatelské jméno a heslo v doméně uživatele. Ověřování clusterů HDInsight přes bránu nevyžaduje, aby klient získal lístek protokolu Kerberos. Vzhledem k tomu, že brána přijímá `username:password` přihlašovací údaje a získá v zastoupení uživatele lístek protokolu Kerberos, lze zabezpečená připojení k bráně z libovolného hostitele klienta, včetně klientů připojených k různým doménám AA-DDS než cluster (ESP).

## <a name="best-practices"></a>Osvědčené postupy

Brána je jediná služba (s vyrovnáváním zatížení mezi dvěma hostiteli), která je odpovědná za předávání a ověřování žádostí. Brána se může stát kritickým bodem propustnosti pro dotazy na podregistry překračující určitou velikost. Když se v bráně přes rozhraní ODBC nebo JDBC spouští velmi velké dotazy **výběru** , může se pozorovat snížení výkonu dotazů. Velmi velké znamená dotazy, které tvoří více než 5 GB dat napříč řádky nebo sloupci. Tento dotaz by mohl obsahovat dlouhý seznam řádků a nebo velký počet sloupců.

Snížení výkonu brány proti dotazům velké velikosti je způsobeno tím, že data musí být přenesena z podkladového úložiště dat (ADLS Gen2) na: Server pro podregistr HDInsight, bránu a nakonec prostřednictvím ovladače JDBC nebo ODBC na hostitele klienta.

Následující diagram znázorňuje kroky zahrnuté v dotazu SELECT.

![Diagram výsledků](./media/gateway-best-practices/result-retrieval-diagram.png "Diagram výsledků")

Apache Hive je relační abstrakce nad systémem souborů, který je kompatibilní s HDFS. Tato abstrakce znamená, že příkazy **Select** v podregistru odpovídají operacím **čtení** v systému souborů. Operace **čtení** jsou přeloženy do příslušného schématu před Nahlášením uživateli. Latence tohoto procesu se zvyšuje o velikost dat a celkový počet směrování potřebných k dosažení koncového uživatele.

K podobnému chování může dojít při provádění příkazů **Create** nebo **INSERT** velkých objemů dat, protože tyto příkazy budou odpovídat operacím **zápisu** v podkladovém systému souborů. Zvažte zápis dat, jako je RAW ORC, do systému souborů/datalake, místo aby se načetly pomocí **metody** **INSERT** nebo Load.

V clusterech s podporou sady Enterprise Security Pack můžou dostatečně složité zásady Apache Ranger způsobit zpomalení doby kompilace dotazů, což může vést k vypršení časového limitu brány. Pokud se v clusteru ESP zaznamená časový limit brány, zvažte snížení nebo kombinování počtu zásad Ranger.

## <a name="troubleshooting-techniques"></a>Techniky řešení potíží

Existuje několik míst pro zmírnění rizik a porozumění problémům s výkonem, které jsou splněné v rámci výše uvedeného chování. Použijte následující kontrolní seznam při vykonávání snížení výkonu dotazů prostřednictvím brány HDInsight:

* Při provádění velkých dotazů **Select** použijte klauzuli **limit** . Klauzule **limit** omezuje celkové řádky hlášené na hostitele klienta. Klauzule **limit** ovlivňuje pouze generování výsledků a nemění plán dotazu. Chcete-li použít klauzuli **limit** pro plán dotazu, použijte konfiguraci `hive.limit.optimize.enable` . **Limit** lze kombinovat s posunem pomocí omezení formuláře argumentu **x, y**.

* Pojmenujte sloupce důležité při spouštění dotazů **Select** namísto použití **Select \** _. Výběr méně sloupců sníží množství čtených dat.

_ Zkuste spustit dotaz, který vás zajímá prostřednictvím Apache Beeline. Pokud načítání výsledků prostřednictvím Apache Beeline trvá delší dobu, očekávat prodlevy při načítání stejných výsledků prostřednictvím externích nástrojů.

* Otestujte základní dotaz na podregistr, abyste měli jistotu, že je možné navázat připojení k bráně HDInsight. Zkuste spustit základní dotaz ze dvou nebo více externích nástrojů, abyste se ujistili, že žádný z těchto nástrojů neběží.

* Projděte si všechny výstrahy Apache Ambari a ujistěte se, že jsou služby HDInsight v dobrém stavu. Výstrahy Ambari je možné integrovat s Azure Monitor pro vytváření sestav a analýzy.

* Pokud používáte externí podregistr metastore, ověřte, že metastore úložiště Azure SQL DB pro podregistr nedosáhlo svého limitu. Pokud se DTU blíží ke svému limitu, budete muset zvětšit velikost databáze.

* Zajistěte, aby všechny nástroje třetích stran, jako je PBI nebo Tableau, používaly stránkování k zobrazení tabulek nebo databází. Pokud potřebujete pomoc s stránkováním, obraťte se na partnery podpory pro tyto nástroje. Hlavním nástrojem použitým pro stránkování je `fetchSize` parametr JDBC. Velikost malého načtení může způsobit snížení výkonu brány, ale příliš velká velikost načtení může mít za následek časový limit brány. Ladění velikosti načtení se musí provádět na základě zatížení.

* Pokud váš datový kanál zahrnuje čtení velkého množství dat z podkladového úložiště clusteru HDInsight, zvažte použití nástroje, který rozhraní přímo s Azure Storage, například Azure Data Factory

* Při spouštění interaktivních úloh zvažte použití Apache Hive LLAP, protože LLAP může poskytovat plynulejší prostředí pro rychlé vracení výsledků dotazu.

* Zvažte zvýšení počtu vláken dostupných pro službu metastore podregistru pomocí `hive.server2.thrift.max.worker.threads` . Toto nastavení je obzvláště důležité, když velký počet souběžných uživatelů posílá dotazy do clusteru.

* Snižte počet opakovaných pokusů, které se použily pro přístup k bráně z jakýchkoli externích nástrojů. Je-li použito více opakovaných pokusů, zvažte následující zásady exponenciálního pokusu o opakování.

* Zvažte možnost Povolit podregistr komprese pomocí konfigurací `hive.exec.compress.output` a `hive.exec.compress.intermediate` .

## <a name="next-steps"></a>Další kroky

* [Apache Beeline ve službě HDInsight](../hadoop/apache-hadoop-use-hive-beeline.md)
* [Postup řešení potíží s vypršením časového limitu brány HDInsight](./troubleshoot-gateway-timeout.md)
* [Virtuální sítě pro HDInsight](../hdinsight-plan-virtual-network-deployment.md)
* [HDInsight se službou Express Route](../connect-on-premises-network.md)