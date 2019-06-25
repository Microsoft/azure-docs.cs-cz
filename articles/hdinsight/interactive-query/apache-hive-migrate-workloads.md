---
title: Migrace úloh Hive ke službě Azure HDInsight 3.6 do HDInsight 4.0
description: Zjistěte, jak migrovat úlohy Apache Hive v HDInsight 3.6 do HDInsight 4.0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: howto
ms.date: 04/24/2019
ms.openlocfilehash: b39279e560cb1738ff9b33ec587562efd2ed4e8d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65800948"
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

1. Vytvořte novou kopii externí metaúložiště. HDInsight 3.6 a HDInsight 4.0 vyžadují různé metastore schémata a nemůžete sdílet jeden metastore. Zobrazit [použití externích úložišť metadat v Azure HDInsight](../hdinsight-use-external-metadata-stores.md) Další informace o připojení ke clusteru služby HDInsight externí metaúložiště. 
2. Spusťte akci skriptu pro váš cluster HDI 3.6, s "Hlavní uzly" jako typ uzlu pro provádění. Vložte následující identifikátor URI do textového pole označená "URI skriptu Bash": https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh. Do textového pole označená "Argumenty", zadejte servername, databáze, uživatelské jméno a heslo **zkopírovat** Hive metastore, oddělené mezerami. Nezahrnují ". database.windows.net" při zadávání servername.

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

Tato komprimace totiž HDInsight 3.6 a HDInsight 4.0 kyseliny tabulky porozumět rozdílům v modelu ACID odlišně. Komprimace vynucuje čisté kontejner, který záruky konzistence. Oddíl 4 [Hive dokumentace k migraci](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) obsahuje pokyny pro hromadné komprimační tabulky kyseliny HDInsight 3.6.

Po dokončení kroků migrace a komprimace metastore můžete migrovat skutečný datový sklad. Po dokončení migrace sklad Hive, datový sklad HDInsight 4.0 bude mít následující vlastnosti:

* Externí tabulky v HDInsight 3.6 bude externích tabulek v HDInsight 4.0
* Non transakční spravované tabulek v HDInsight 3.6 bude externích tabulek v HDInsight 4.0
* Transakční spravované tabulek v HDInsight 3.6 bude spravované tabulek v HDInsight 4.0

Budete muset upravit vlastnosti služby warehouse před provedením migrace. Například pokud očekáváte, že některé tabulky budou mít přístup třetích stran (jako je cluster HDInsight 3.6), tabulky musí být externím po dokončení migrace. Všechny spravované tabulek v HDInsight 4.0, je transakční. Proto spravované tabulek v HDInsight 4.0 by měl k měla přístup pouze clustery HDInsight 4.0.

Jakmile jsou správně nastaveny vlastnosti vaší tabulky, spusťte nástroj pro migraci sklad Hive z jednoho z hlavním uzlům clusteru pomocí SSH prostředí:

1. Připojte se k vaší hlavního uzlu clusteru pomocí SSH. Pokyny najdete v tématu [připojení k HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md)
1. Otevřete prostředí přihlášení jako uživatel Hive spuštěním `sudo su - hive`
1. Určení verze zásobníku datovou platformou Hortonworks spuštěním `ls /usr/hdp`. Bude se zobrazovat verze řetězec, který byste měli použít následující příkaz.
1. Spusťte následující příkaz z prostředí. Nahraďte `${{STACK_VERSION}}` s řetězce verze z předchozího kroku:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Po dokončení nástroj pro migraci, bude váš sklad Hive připraven pro HDInsight 4.0. 

> [!Important]
> Spravované tabulek v HDInsight 4.0 (včetně tabulek se migroval z 3.6) by neměl přistupují jiné služby nebo aplikace, včetně clusterů HDInsight 3.6.

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

Spusťte skript akce u vašeho clusteru, s "Hlavní uzly" jako typ uzlu pro provádění. Vložte následující identifikátor URI do textového pole označená "URI skriptu Bash": https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Studio analýzy dat může být spuštěn s adresou URL: https://<clustername>.azurehdinsight.net/das/



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
