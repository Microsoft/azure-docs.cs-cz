---
title: Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0
description: Naučte se migrovat Apache Hive úlohy ve službě HDInsight 3,6 do HDInsight 4,0.
ms.service: hdinsight
author: msft-tacox
ms.author: tacox
ms.reviewer: jasonh
ms.topic: conceptual
ms.date: 04/24/2019
ms.openlocfilehash: b9bf3b41bcd0a79027c5dd9a4f3df979fb0bd9f0
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250154"
---
# <a name="migrate-azure-hdinsight-36-hive-workloads-to-hdinsight-40"></a>Migrace úloh podregistru Azure HDInsight 3,6 do HDInsight 4,0

V tomto dokumentu se dozvíte, jak migrovat úlohy Apache Hive a LLAP v HDInsight 3,6 do HDInsight 4,0. HDInsight 4,0 poskytuje novější podregistr a LLAP funkce, jako například materializovaná zobrazení a ukládání výsledků dotazu do mezipaměti. Při migraci úloh do HDInsight 4,0 můžete použít spoustu nových funkcí podregistru 3, které nejsou k dispozici ve službě HDInsight 3,6.

Tento článek se zabývá následujícími tématy:

* Migrace metadat podregistru do HDInsight 4,0
* Bezpečná migrace tabulek kyselin a non-KYSELých
* Zachování zásad zabezpečení podregistru napříč verzemi HDInsight
* Provádění dotazů a ladění z HDInsight 3,6 na HDInsight 4,0

## <a name="migrate-apache-hive-metadata-to-hdinsight-40"></a>Migrace metadat Apache Hive do HDInsight 4,0

Jednou z výhod podregistru je schopnost exportovat metadata do externí databáze (označované jako metastore podregistru). **Podregistr metastore** zodpovídá za ukládání statistik tabulky, včetně umístění úložiště tabulky, názvů sloupců a informací o indexu tabulky. Schéma databáze metastore se mezi verzemi podregistru liší. Proveďte následující postup k upgradu metastore podregistru HDInsight 3,6 tak, aby byl kompatibilní se službou HDInsight 4,0.

1. Vytvořte novou kopii vašich externích metastore. HDInsight 3,6 a HDInsight 4,0 vyžadují různá schémata metastore a nemůžou sdílet jeden metastore. Další informace o připojení externích metastore ke clusteru HDInsight najdete v tématu [použití externích úložišť metadat ve službě Azure HDInsight](../hdinsight-use-external-metadata-stores.md) . 
2. Spusťte akci skriptu pro cluster HDI 3,6 s "hlavními uzly" jako typ uzlu pro provedení. Vložte následující identifikátor URI do textového pole označeného "bash skriptu URI" https://hdiconfigactions.blob.core.windows.net/hivemetastoreschemaupgrade/launch-schema-upgrade.sh:. Do textového pole označeného "arguments" zadejte servername, databázi, uživatelské jméno a heslo pro **zkopírované** metastore Hive, oddělené mezerami. Při zadávání servername nezahrnujte ". database.windows.net".

> [!Warning]
> Upgrade, který převede schéma metadat HDInsight 3,6 na schéma HDInsight 4,0, nejde vrátit zpět.

## <a name="migrate-hive-tables-to-hdinsight-40"></a>Migrace tabulek podregistru do HDInsight 4,0

Po dokončení předchozí sady kroků pro migraci metastore do prostředí HDInsight 4,0 budou tabulky a databáze zaznamenané v metastore viditelné z clusteru HDInsight 4,0 spuštěním `show tables` nebo `show databases` z clusteru. . Informace o spuštění dotazů v clusterech HDInsight 4,0 najdete v tématu [provádění dotazů napříč verzemi HDInsight](#query-execution-across-hdinsight-versions) .

Skutečná data z tabulek však nejsou přístupná, dokud cluster nemá přístup k potřebným účtům úložiště. Abyste se ujistili, že cluster HDInsight 4,0 má přístup ke stejným datům jako starý cluster HDInsight 3,6, proveďte následující kroky:

1. Určete účet úložiště Azure pro tabulku nebo databázi pomocí popisně formátovaného.
2. Pokud je už cluster HDInsight 4,0 spuštěný, připojte účet Azure Storage ke clusteru přes Ambari. Pokud jste ještě nevytvořili cluster HDInsight 4,0, ujistěte se, že je účet úložiště Azure zadaný jako primární nebo sekundární účet úložiště clusteru. Další informace o přidávání účtů úložiště do clusterů HDInsight najdete v tématu [Přidání dalších účtů úložiště do služby HDInsight](../hdinsight-hadoop-add-storage.md).

> [!Note]
> Tabulky jsou v HDInsight 3,6 a HDInsight 4,0 zpracovávané jinak. Z tohoto důvodu nemůžete sdílet stejné tabulky pro clustery s různými verzemi. Pokud chcete použít HDInsight 3,6 ve stejnou dobu jako HDInsight 4,0, musíte mít samostatné kopie dat pro každou verzi.

Zatížení vašeho podregistru může zahrnovat kombinaci KYSELých a nekyselých tabulek. Jeden klíčový rozdíl mezi podregistrem v HDInsight 3,6 (podregistr 2) a podregistr v HDInsight 4,0 (podregistr 3) je dodržování předpisů pro tabulky v KYSELINě. Ve službě HDInsight 3,6, povolení dodržování předpisů v podregistru vyžaduje další konfiguraci, ale ve výchozím nastavení jsou v tabulkách HDInsight 4,0 kompatibilní s KYSELINou. Jediná akce nutná před migrací je spuštění hlavní komprimace proti tabulce kyselin v clusteru 3,6. V zobrazení podregistru nebo v Beeline spusťte následující dotaz:

```bash
alter table myacidtable compact 'major';
```

Tato komprimace je povinná, protože tabulky v HDInsight 3,6 a HDInsight 4,0 porozumět rozdílům v KYSELosti odlišně. Při komprimaci se vynutila čistá břidlica, která zaručuje konzistenci. Část 4 [dokumentace k migraci podregistru](https://docs.hortonworks.com/HDPDocuments/Ambari-2.7.3.0/bk_ambari-upgrade-major/content/prepare_hive_for_upgrade.html) obsahuje pokyny pro hromadné komprimaci tabulek pro kyselinu HDInsight 3,6.

Po dokončení kroků migrace a komprimace metastore můžete aktuální datový sklad migrovat. Po dokončení migrace datového skladu pro podregistr bude mít datový sklad HDInsight 4,0 následující vlastnosti:

* Externí tabulky v HDInsight 3,6 budou externí tabulky ve službě HDInsight 4,0.
* Netransakční spravované tabulky v HDInsight 3,6 budou externími tabulkami ve službě HDInsight 4,0.
* Transakční spravované tabulky v HDInsight 3,6 budou spravované tabulky ve službě HDInsight 4,0.

Před provedením migrace možná budete muset upravit vlastnosti datového skladu. Například pokud očekáváte, že k některé tabulce budou mít přistup třetí strana (například cluster HDInsight 3,6), musí být tato tabulka po dokončení migrace externě externí. Ve službě HDInsight 4,0 jsou všechny spravované tabulky transakční. Proto jsou spravované tabulky v HDInsight 4,0 k dispozici pouze clusterům HDInsight 4,0.

Po správném nastavení vlastností tabulky spusťte nástroj pro migraci datového skladu z jednoho z hlavních clusteru pomocí prostředí SSH:

1. Připojte se ke clusteru hlavnímu uzlu pomocí SSH. Pokyny najdete v tématu [připojení ke službě HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) .
1. Spusťte prostředí přihlášení jako uživatel podregistru spuštěním`sudo su - hive`
1. Pomocí příkazu `ls /usr/hdp`určete verzi zásobníku datové platformy Hortonworks. Tím se zobrazí řetězec verze, který byste měli použít v příkazu Next.
1. Z prostředí spusťte následující příkaz. Nahraďte `${{STACK_VERSION}}` řetězcem verze z předchozího kroku:

```bash
/usr/hdp/${{STACK_VERSION}}/hive/bin/hive --config /etc/hive/conf --service  strictmanagedmigration --hiveconf hive.strict.managed.tables=true  -m automatic  automatic  --modifyManagedTables --oldWarehouseRoot /apps/hive/warehouse
```

Po dokončení nástroje pro migraci bude váš sklad podregistru připravený pro HDInsight 4,0. 

> [!Important]
> Spravované tabulky v HDInsight 4,0 (včetně tabulek migrovaných z 3,6) nemají být dostupné pro jiné služby nebo aplikace, včetně clusterů HDInsight 3,6.

## <a name="secure-hive-across-hdinsight-versions"></a>Zabezpečení podregistru napříč verzemi HDInsight

Od HDInsight 3,6 se HDInsight integruje s Azure Active Directory pomocí HDInsight Balíček zabezpečení podniku (ESP). ESP používá protokol Kerberos a Apache Ranger ke správě oprávnění konkrétních prostředků v rámci clusteru. Zásady Ranger nasazené pro podregistr v HDInsight 3,6 se dají migrovat do HDInsight 4,0 pomocí následujících kroků:

1. V clusteru HDInsight 3,6 přejděte na panel Service Manager Ranger.
2. Přejděte do zásady s názvem **podregistr** a exportujte zásadu do souboru JSON.
3. Zajistěte, aby všichni uživatelé uvedení v seznamu JSON exportovaných zásad existovali v novém clusteru. Pokud se na uživatele odkazuje v zásadách JSON, ale v novém clusteru neexistuje, přidejte ho do nového clusteru nebo odeberte odkaz ze zásady.
4. V clusteru HDInsight 4,0 přejděte na panel **Service Manager Ranger** .
5. Přejděte do části s názvem **podregistr** a importujte JSON zásad Ranger z kroku 2.

## <a name="query-execution-across-hdinsight-versions"></a>Provádění dotazů v rámci verzí HDInsight

Existují dva způsoby, jak spustit a ladit dotazy na podregistry/LLAP v clusteru HDInsight 3,6. HiveCLI poskytuje prostředí příkazového řádku a zobrazení tez/zobrazení podregistru nabízí pracovní postup založený na grafickém uživatelském rozhraní.

V HDInsight 4,0 byl HiveCLI nahrazen Beeline. HiveCLI je klient Thrift pro Hiveserver 1 a Beeline je JDBC klient, který poskytuje přístup k Hiveserver 2. Beeline je možné použít také pro připojení k jakémukoli jinému koncovému bodu databáze kompatibilního s JDBC. Beeline je k dispozici předem v HDInsight 4,0 bez nutnosti instalace.

V HDInsight 3,6 je klient grafického uživatelského rozhraní pro interakci se serverem podregistru Ambari zobrazením podregistru. HDInsight 4,0 nahrazuje zobrazení podregistru pomocí nástroje Hortonworks data Analytics Studio (DAS). DAS se nedodává s předem připravenými clustery HDInsight a nejedná se o oficiálně podporovaný balíček. DAS se ale v clusteru dá nainstalovat takto:

Spustí pro svůj cluster akci skriptu s "hlavními uzly" jako typem uzlu pro provedení. Do textového pole označeného "bash Script URI" vložte následující identifikátor URI: https://hdiconfigactions.blob.core.windows.net/dasinstaller/LaunchDASInstaller.sh

Data Analytics Studio se dá spustit s adresou URL:\<https://název_clusteru >. azurehdinsight. NET/Das/



Pokud se po instalaci DAS nezobrazí dotazy, které jste spustili v prohlížeči dotazů, proveďte následující kroky:

1. Nastavte konfigurace pro podregistr, tez a DAS, jak je popsáno v [této příručce pro řešení potíží s instalací Das](https://docs.hortonworks.com/HDPDocuments/DAS/DAS-1.2.0/troubleshooting/content/das_queries_not_appearing.html).
2. Ujistěte se, že následující konfigurace adresáře úložiště Azure jsou objekty blob stránky a že jsou uvedené v části `fs.azure.page.blob.dirs`:
    * `hive.hook.proto.base-directory`
    * `tez.history.logging.proto-base-dir`
3. V obou hlavních restartujte HDFS, podregistr, tez a DAS.

## <a name="next-steps"></a>Další postup

* [Oznámení HDInsight 4,0](../hdinsight-version-release.md)
* [Podrobně HDInsight 4,0 s hloubkou](https://azure.microsoft.com/blog/deep-dive-into-azure-hdinsight-4-0/)
* [Tabulky s KYSELINou v podregistru 3](https://docs.hortonworks.com/HDPDocuments/HDP3/HDP-3.1.0/using-hiveql/content/hive_3_internals.html)
