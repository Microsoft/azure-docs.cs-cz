---
title: Migrace úloh Hive ke službě Azure HDInsight 3.6 do HDInsight 4.0
description: Zjistěte, jak migrovat úlohy Apache Hive v HDInsight 3.6 do HDInsight 4.0.
services: hdinsight
ms.service: hdinsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/15/2019
ms.openlocfilehash: 708df64802ace17fa77b4e0a695c9f1c3bd18a77
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2019
ms.locfileid: "59610202"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrace úloh Hive ke službě Azure HDInsight 3.6 do HDInsight 4.0

Tento dokument ukazuje, jak migrovat úlohy Apache Hive a LLAP na HDInsight 3.6 do HDInsight 4.0. HDInsight 4.0 poskytuje novější funkce Hive a LLAP, jako je materializované zobrazení a ukládání do mezipaměti výsledek dotazu. Při migraci vašich úloh do HDInsight 4.0, můžete použít mnoho novější funkce Hive 3, které nejsou k dispozici na HDInsight 3.6.

Tento článek se zabývá následujícími tématy:

* Migrace metadat Hive do HDInsight 4.0
* Bezpečné migraci kyseliny a jiných kyseliny tabulky
* Zachování zabezpečení zásad Hivu ve verzích HDInsight
* Provádění dotazů a ladění z HDInsight 3.6 do HDInsight 4.0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrace Apache Hive metadata do HDInsight 4.0

Jednou z výhod Hive je možnost exportu metadat k externí databázi (označované jako Hive Metastore). **Hive Metastore** zodpovídá za ukládání statistiky tabulky, včetně umístění úložiště tabulky, názvy sloupců a informace o tabulce indexu. Schéma databáze pro metastore se liší mezi verzemi Hive. Následujícím postupem upgradujte Metastore Hive HDInsight 3.6, aby byl kompatibilní s HDInsight 4.0.

1. Vytvořte novou kopii externí metaúložiště. HDInsight 3.6 a HDInsight 4.0 vyžadují různé metastore schémata a nemůžete sdílet jeden metastore.
1. Připojte k a) existujícího clusteru HDInsight 4.0 nebo (b) do clusteru, který vytváříte první novou kopii metastore. Zobrazit [použití externích úložišť metadat v Azure HDInsight](../hdinsight-use-external-metadata-stores.md) Další informace o připojení ke clusteru služby HDInsight externí metaúložiště. Po připojení Metastore bude automaticky převést na 4.0 kompatibilní metastore.

> [!Warning]
> Upgrade, který převede metadata schématu HDInsight 3.6 na schéma HDInsight 4.0, je nevratná.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrace na HDInsight 4.0 tabulek Hive

Po dokončení předchozí sadu kroků migrace Hive Metastore HDInsight 4.0, tabulek a databází zaznamenávají do metastore budou viditelné z v rámci clusteru HDInsight 4.0 spuštěním `show tables` nebo `show databases` z v rámci clusteru . Zobrazit [provádění dotazu napříč verzemi HDInsight](#query-execution-across-hdinsight-versions) informace o provádění dotazů v clusterech HDInsight 4.0.

Skutečná data z tabulek, ale není dostupná, dokud clusteru má přístup k účtům úložiště nezbytné. Pokud chcete mít jistotu, že váš cluster HDInsight 4.0, můžete přístup ke stejným datům jako starý cluster HDInsight 3.6, proveďte následující kroky:

1. Určení účtu úložiště Azure, tabulky nebo databáze pomocí popisují ve formátu.
2. Pokud váš cluster HDInsight 4.0 je již spuštěna, připojte ke clusteru prostřednictvím Ambari účtu úložiště Azure. Pokud jste ještě nevytvořili clusteru HDInsight 4.0, ujistěte se, že jako primární nebo sekundární clusteru účet úložiště se zadal účet úložiště Azure. Další informace o přidání účtů úložiště do clusterů HDInsight, naleznete v tématu [přidání dalších účtů úložiště pro HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabulky jsou v HDInsight 3.6 a HDInsight 4.0 zpracovávat odděleně. Z tohoto důvodu nelze sdílet stejné tabulky pro clustery s různými verzemi. Pokud chcete použít HDInsight 3.6 ve stejnou dobu jako HDInsight 4.0, musí mít samostatnou kopii dat pro každou verzi.

Vaší úlohy Hive může obsahovat kombinaci kyseliny a jiných kyseliny tabulky. Jedním klíčovým rozdílem mezi Hive v HDInsight 3.6 (Hive 2) a Hive v HDInsight 4.0 (Hive 3) je kyseliny dodržování předpisů pro tabulky. V HDInsight 3.6 povolení kyseliny předpisů Hive vyžaduje další konfiguraci, ale v HDInsight 4.0 tabulky jsou kompatibilní s kyseliny ve výchozím nastavení. Před migrace je ke spuštění hlavní komprimace tabulce v modelu ACID 3.6 clusteru musí být jedinou akcí. Od zobrazení Hive nebo Beeline spusťte následující dotaz:

```bash
alter table myacidtable compact 'major';
```

Tato komprimace totiž tabulky HDInsight 3.6 a HDInsight 4.0 kyseliny pochopit odpovídající zásadám ACID rozdíly jiný. Komprimace vynucuje čisté kontejner, který záruky konzistence tabulky. Po dokončení komprimace předchozí kroky pro migraci metastore a tabulka stačit používat žádné tabulky HDInsight 3.6 kyseliny v HDInsight 4.0.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpečte Hive v HDInsight verze

Od verze HDInsight 3.6 HDInsight se integruje s Azure Active Directory pomocí HDInsight Enterprise Security Package (ESP). ESP používá ke správě oprávnění konkrétních prostředků v rámci clusteru pomocí protokolu Kerberos a Apache Ranger. Zásady ranger nasazené proti Hivu ve službě HDInsight 3.6 se dají migrovat do HDInsight 4.0 pomocí následujících kroků:

1. Přejděte na panelu portálu Service Manager Ranger ve vašem clusteru HDInsight 3.6.
2. Přejděte na zásady s názvem **HIVE** a exportovat zásady do souboru json.
3. Ujistěte se, že všichni uživatelé uvedené ve formátu json exportované zásad existují v novém clusteru. Pokud uživatel je uvedené ve formátu json zásady, ale neexistuje v novém clusteru, přidejte uživatele do nového clusteru nebo odeberte odkaz ze zásad.
4. Přejděte **Ranger Service Manageru** panelu ve vašem clusteru HDInsight 4.0.
5. Přejděte na zásady s názvem **HIVE** a importujte json zásad ranger z kroku 2.

## <a name="query-execution-across-hdinsight-versions"></a>Provádění dotazu napříč verzemi HDInsight

Existují dva způsoby, jak spustit a ladit dotazy Hive a LLAP v clusteru služby HDInsight 3.6. HiveCLI poskytuje prostředí příkazového řádku a zobrazení zobrazení/Hive Tez poskytuje pracovní postupy využívající grafické rozhraní.

V HDInsight 4.0 bylo nahrazeno tématem Beeline HiveCLI. HiveCLI klienta thriftu Hiveserver 1 a Beeline je klient JDBC, který poskytuje přístup k Hiveserver 2. Beeline je také možné se připojit k jakékoli jiné JDBC databáze koncového bodu kompatibilního s. Beeline je k dispozici out-of-box na HDInsight 4.0 bez jakékoli instalaci potřeba.

Klient grafického uživatelského rozhraní pro interakci s serveru Hive v HDInsight 3.6 je zobrazení Ambari Hive. HDInsight 4.0 nahradí zobrazení Hive s Hortonworks Data Analytics Studio (DAS). DAS není dodávají spolu s HDInsight clustery out-of-box a není oficiálně podporovaných balíčků. Ale DAS můžete nainstalovat na clusteru takto:

1. Stáhněte si [DAS balíček instalační skript](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-mpack.sh) a spustit ho v obou hlavním uzlům clusteru. Jako akci skriptu není spusťte tento skript.
2. Stáhněte si [DAS služby instalační skript](https://hdiconfigactions.blob.core.windows.net/dasinstaller/install-das-component.sh) a spustíme ji jako akci skriptu. Vyberte **hlavní uzly** jako typ uzlu podle výběru z rozhraní akce skriptu.
3. Po dokončení akce skriptu, přejděte do Ambari a vyberte **Data Analytics Studio** ze seznamu služeb. Všechny služby DAS se zastaví. V pravém horním rohu vyberte **akce** a **Start**. Teď můžete spustit a ladit dotazů s DAS.

Po nainstalování DAS dotazy, které jste spustili v prohlížeči dotazy nevidíte, proveďte následující kroky:

1. Jak je popsáno v nastavení konfigurace pro Hive Tez a DAS [Tato příručka pro řešení potíží s instalací DAS](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Ujistěte se, že následující konfigurace adresáře služby Azure storage jsou objekty BLOB stránky a že jsou uvedeny v části `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. Restartujte HDFS, Hive, Tez a DAS na oba hlavní uzly.

## <a name="next-steps"></a>Další postup

* [HDInsight 4.0 oznámení](../hdinsight-version-release.md)
* [Podrobné informace o HDInsight 4.0](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [3 odpovídající zásadám ACID tabulky Hive](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)