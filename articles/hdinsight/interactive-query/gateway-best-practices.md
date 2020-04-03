---
title: Podrobné informace o službě Gateway a osvědčené postupy pro Apache Hive v Azure HDInsight
description: Zjistěte, jak se orientovat v doporučených postupech pro spouštění dotazů Hive přes bránu Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/01/2020
ms.openlocfilehash: 924b1132efeb3ee4211593da190f5b7251029ae3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80586974"
---
# <a name="gateway-deep-dive-and-best-practices-for-apache-hive-in-azure-hdinsight"></a>Podrobné informace o službě Gateway a osvědčené postupy pro Apache Hive v Azure HDInsight

Brána Azure HDInsight (Gateway) je front-end HTTPS pro clustery HDInsight. Brána je zodpovědná za: ověřování, rozlišení hostitele, zjišťování služeb a další užitečné funkce nezbytné pro moderní distribuovaný systém. Funkce poskytované bránou mají za následek určitou režii, pro kterou tento dokument popisuje osvědčené postupy pro navigaci. Brány řešení potíží jsou také popsány.

## <a name="the-hdinsight-gateway"></a>Brána HDInsight

Brána HDInsight je jedinou částí clusteru HDInsight, která je veřejně přístupná přes internet. Služba Gateway je přístupná bez nutnosti přecit přes veřejný internet pomocí koncového `clustername-int.azurehdinsight.net` bodu interní brány. Koncový bod interní brány umožňuje navázat připojení ke službě brány bez ukončení virtuální sítě clusteru. Brána zpracovává všechny požadavky, které jsou odesílány do clusteru a předá tyto požadavky správné součásti a hostitelé clusteru.

Následující diagram poskytuje hrubou ilustraci toho, jak brána poskytuje abstrakci před všemi různými možnostmi rozlišení hostitele v rámci HDInsight.

![Diagram řešení hostitele](./media/gateway-best-practices/host-resolution-diagram.png "Diagram řešení hostitele")

## <a name="motivation"></a>Motivace

Motivací pro umístění brány před clustery HDInsight je poskytnout rozhraní pro zjišťování služeb a ověřování uživatelů. Mechanismy ověřování poskytované bránou jsou obzvláště důležité pro clustery s podporou protokolu ESP.

Pro zjišťování služby výhodou brány je, že každá součást v rámci clusteru je `clustername.azurehdinsight.net/hive2`přístupná jako jiný koncový `host:port` bod na webu brány ( ), na rozdíl od velkého počtu párování.

Pro ověřování umožňuje brána uživatelům `username:password` ověření pomocí dvojice pověření. Pro clustery s podporou ESP by toto pověření bylo uživatelské jméno a heslo domény uživatele. Ověřování clusterů HDInsight prostřednictvím brány nevyžaduje, aby klient získal lístek kerberos. Vzhledem k `username:password` tomu, že brána přijímá pověření a získává lístek kerberos uživatele jménem uživatele, lze k bráně vytvořit zabezpečená připojení z libovolného hostitele klienta, včetně klientů spojených s různými doménami AA-DDS než cluster (ESP).

## <a name="best-practices"></a>Osvědčené postupy

Brána je jedna služba (vyrovnávání zatížení mezi dvěma hostiteli) zodpovědná za předávání a ověřování požadavků. Brána se může stát kritickým bodem propustnosti pro dotazy Hive přesahující určitou velikost. Snížení výkonu dotazu může být pozorováno při velmi velké **dotazy SELECT** jsou spouštěny na gateway prostřednictvím ROZHRANÍ ODBC nebo JDBC. "Velmi velké" znamená dotazy, které tvoří více než 5 GB dat napříč řádky nebo sloupci. Tento dotaz může obsahovat dlouhý seznam řádků a nebo široký počet sloupců.

Snížení výkonu brány kolem dotazů velké velikosti je, protože data musí být přenesena z podkladového úložiště dat (ADLS Gen2) na: HDInsight Hive Server, gateway a nakonec prostřednictvím ovladačů JDBC nebo ODBC na hostitele klienta.

Následující diagram znázorňuje kroky související s dotazem SELECT.

![Diagram výsledků](./media/gateway-best-practices/result-retrieval-diagram.png "Diagram výsledků")

Apache Hive je relační abstrakce nad souborovým systémem kompatibilním s HDFS. Tato abstrakce znamená **SELECT** příkazy v Hive odpovídají operacím **čtení** v souborovém systému. Operace **čtení** jsou přeloženy do příslušnéschéma před nahlášeno u uživatele. Latence tohoto procesu se zvyšuje s velikostí dat a celkovým počtem směrování potřebných k oslovení koncového uživatele.

Podobné chování může dojít při provádění **CREATE** nebo **INSERT** příkazy velkých dat, protože tyto příkazy budou odpovídat **write** operace v základním souborovém systému. Zvažte zápis dat, jako je například nezpracovaný ORC, do souborového systému/datalake namísto načtení pomocí **INSERT** nebo **LOAD**.

V clusterech s podporou sady Enterprise Security Pack může způsobit dostatečně složité zásady Apache Ranger zpomalení doby kompilace dotazů, což může vést k časovému limitu brány. Pokud je v clusteru ESP zaznamenán časový rozsah brány, zvažte snížení nebo kombinování počtu zásad ranger.

## <a name="troubleshooting-techniques"></a>Postupy řešení potíží

Existuje více míst pro zmírnění a pochopení problémů s výkonem splněny jako součást výše uvedeného chování. Při snížení výkonu dotazů nad bránou HDInsight použijte následující kontrolní seznam:

* Klauzuli **LIMIT** použijte při provádění velkých dotazů **SELECT.** Klauzule **LIMIT** sníží celkový počet řádků hlášených hostiteli klienta. Klauzule **LIMIT** ovlivňuje pouze generování výsledků a nemění plán dotazu. Chcete-li použít **klauzuli LIMIT** v `hive.limit.optimize.enable`plánu dotazů, použijte konfiguraci . **LIMIT** lze kombinovat s posunem pomocí formuláře **argumentu LIMIT x,y**.

* Pojmenujte sloupce, které vás zajímají, při spuštění dotazů **SELECT** namísto použití **funkce SELECT \* **. Výběrem menšího počtu sloupců se sníží množství přečtených dat.

* Zkuste spustit dotaz zájmu prostřednictvím Apache Beeline. Pokud načítání výsledků přes Apache Beeline trvá delší dobu, očekávejte zpoždění při načítání stejných výsledků prostřednictvím externích nástrojů.

* Otestujte základní dotaz Hive, abyste zajistili, že lze navázat připojení k bráně HDInsight. Zkuste spustit základní dotaz ze dvou nebo více externích nástrojů a ujistěte se, že žádný jednotlivý nástroj není spuštěn do problémů.

* Zkontrolujte všechny apache ambari výstrahy, abyste se ujistili, že služby HDInsight jsou v pořádku. Ambari Výstrahy lze integrovat s Azure Monitor pro vytváření sestav a analýzy.

* Pokud používáte externí Hive Metastore, zkontrolujte, že Azure SQL DB DTU pro Hive Metastore nedosáhla svého limitu. Pokud DTU se blíží jeho limit, budete muset zvětšit velikost databáze.

* Ujistěte se, že všechny nástroje jiných výrobců, jako je PBI nebo Tableau používají stránkování k zobrazení tabulek nebo databází. Poraďte se se svými partnery podpory pro tyto nástroje pro pomoc při stránkování. Hlavním nástrojem používaným pro stránkování je `fetchSize` parametr JDBC. Malá velikost načtení může mít za následek snížení výkonu brány, ale velikost načítání příliš velká může mít za následek časový čas brány. Optimalizace velikosti načítání musí být provedena na základě zatížení.

* Pokud váš datový kanál zahrnuje čtení velkého množství dat ze základního úložiště clusteru HDInsight, zvažte použití nástroje, který je propojen přímo s Azure Storage, jako je Azure Data Factory.

* Zvažte použití Apache Hive LLAP při spouštění interaktivních úloh, protože LLAP může poskytnout plynulejší prostředí pro rychlé vrácení výsledků dotazu

* Zvažte zvýšení počtu podprocesů, které jsou k `hive.server2.thrift.max.worker.threads`dispozici pro službu Hive Metastore pomocí . Toto nastavení je důležité zejména v případě, že do clusteru odesílá dotazy vysoký počet souběžných uživatelů.

* Snižte počet opakovaných pokusů použitých k přístupu do brány z libovolného externího nástroje. Pokud se používá více opakování, zvažte následující exponenciální zásady zpětného opakování

* Zvažte povolení komprese Hive `hive.exec.compress.output` `hive.exec.compress.intermediate`pomocí konfigurací a .

## <a name="next-steps"></a>Další kroky

* [Apache Beeline na HDInsight](https://docs.microsoft.com/azure/hdinsight/hadoop/apache-hadoop-use-hive-beeline)
* [Postupy řešení potíží s časovým obdobím brány HDInsight](https://docs.microsoft.com/azure/hdinsight/interactive-query/troubleshoot-gateway-timeout)
* [Virtuální sítě pro HDInsight](https://docs.microsoft.com/azure/hdinsight/hdinsight-plan-virtual-network-deployment)
* [HDInsight s expresní trasou](https://docs.microsoft.com/azure/hdinsight/connect-on-premises-network)