---
title: Spouštění úloh Apache Sqoop s Azure HDInsight (Apache Hadoop)
description: Zjistěte, jak pomocí Azure PowerShellu z pracovní stanice spouštět import a export Sqoop mezi clusterem Hadoop a databází Azure SQL.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 12/06/2019
ms.openlocfilehash: 8353c0fba034022a79570d09b320b7b5c4c3e60a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74951849"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Použití Apache Sqoopu s Hadoopem ve službě HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Přečtěte si, jak pomocí Apache Sqoop v HDInsightu importovat a exportovat data mezi clusterem HDInsight a databází Azure SQL.

Přestože Apache Hadoop je přirozenou volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory, může být také potřeba zpracovat strukturovaná data uložená v relačních databázích.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) je nástroj určený k přenosu dat mezi clustery Hadoop a relačními databázemi. Můžete ji použít k importu dat ze systému správy relačních databází (RDBMS), jako je SQL Server, MySQL nebo Oracle do distribuovaného souborového systému Hadoop (HDFS), transformovat data v Hadoopu pomocí MapReduce nebo Apache Hive a pak exportovat data zpět do RDBMS . V tomto článku používáte databázi serveru SQL Server pro relační databázi.

> [!IMPORTANT]  
> Tento článek nastaví testovací prostředí k provedení přenosu dat. Potom zvolíte metodu přenosu dat pro toto prostředí z jedné z metod v části [Spustit úlohy Sqoop](#run-sqoop-jobs), dále níže.

Verze Sqoop, které jsou podporované v clusterech HDInsight, najdete [v tématu Co je nového ve verzích clusteru poskytovaných hdinsightem?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Vysvětlení scénáře

Cluster HDInsight je dodáván s některými ukázkovými daty. Použijete následující dva vzorky:

* Soubor protokolu Apache Log4j, který `/example/data/sample.log`je umístěn na adrese . Ze souboru jsou extrahovány následující protokoly:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Tabulka Hive `hivesampletable`s názvem , která odkazuje `/hive/warehouse/hivesampletable`na datový soubor umístěný na adrese . Tabulka obsahuje některá data mobilního zařízení.
  
  | Pole | Datový typ |
  | --- | --- |
  | Clientid |řetězec |
  | dotazovací čas |řetězec |
  | Trhu |řetězec |
  | platforma zařízení |řetězec |
  | zařízenímake |řetězec |
  | model zařízení |řetězec |
  | state |řetězec |
  | country |řetězec |
  | querydwelltime |double |
  | Sessionid |bigint |
  | sessionpagevieworder |bigint |

V tomto článku použijete tyto dvě datové sady k testování importu a exportu Sqoop.

## <a name="set-up-test-environment"></a><a name="create-cluster-and-sql-database"></a>Nastavení testovacího prostředí

Cluster, databáze SQL a další objekty se vytvářejí prostřednictvím portálu Azure pomocí šablony Azure Resource Manager. Šablonu najdete v [šablonách azure quickstart](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Šablona Správce prostředků volá balíček bacpac k nasazení schémat tabulky do databáze SQL.  Balíček bacpac se nachází ve veřejné https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpackontejneru objektů blob . Pokud chcete pro soubory bacpac použít soukromý kontejner, použijte v šabloně následující hodnoty:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Import pomocí šablony nebo portálu Azure podporuje jenom import souboru BACPAC z úložiště objektů blob Azure.

1. Kliknutím na následující bitovou kopii otevřete šablonu Správce prostředků na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Zadejte tyto vlastnosti:

    |Pole |Hodnota |
    |---|---|
    |Předplatné |V rozevíracím seznamu vyberte předplatné Azure.|
    |Skupina prostředků |Vyberte skupinu prostředků z rozevíracího seznamu nebo vytvořte novou.|
    |Umístění |V rozevíracím seznamu vyberte oblast.|
    |Název clusteru |Zadejte název clusteru Hadoop. Používejte pouze malá písmena.|
    |Uživatelské jméno přihlášení clusteru |Ponechte předem vyplněnou `admin`hodnotu .|
    |Heslo přihlášení clusteru |Zadejte heslo.|
    |Uživatelské jméno SSH |Ponechte předem vyplněnou `sshuser`hodnotu .|
    |Ssh heslo |Zadejte heslo.|
    |Přihlášení správce SQL |Ponechte předem vyplněnou `sqluser`hodnotu .|
    |Heslo správce SQL |Zadejte heslo.|
    |_artifacts Umístění | Výchozí hodnotu použijte, pokud nechcete použít vlastní soubor bacpac v jiném umístění.|
    |_artifacts umístění Sas Token |Ponechte prázdné.|
    |Název souboru Bacpac |Výchozí hodnotu použijte, pokud nechcete použít vlastní soubor bacpac.|
    |Umístění |Použijte výchozí hodnotu.|

    Název serveru Azure SQL `<ClusterName>dbserver`Server bude . Název databáze bude `<ClusterName>db`. Výchozí název účtu úložiště `e6qhezrh2pdqu`bude .

3. Vyberte **Souhlasím s výše uvedenými podmínkami**.

4. Vyberte **Koupit**. Zobrazí se nová dlaždice s názvem Odeslání nasazení pro nasazení šablony. Vytvoření clusteru a databáze SQL trvá přibližně 20 minut.

## <a name="run-sqoop-jobs"></a>Spuštění úloh Sqoop

HDInsight můžete spustit úlohy Sqoop pomocí různých metod. V následující tabulce se můžete rozhodnout, která metoda je pro vás vhodná, a potom postupujte podle odkazu na návod.

| **Použijte to,** pokud chcete... | ... **interaktivní** prostředí | ... **dávkové** zpracování | ... z tohoto **klientského operačního systému** |
|:--- |:---:|:---:|:--- |:--- |
| [Ssh](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X nebo Windows |
| [Sada .NET SDK pro systém Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (prozatím) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Omezení

* Hromadný export – S Linuxem založené HDInsight konektor Sqoop slouží k exportu dat na Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje hromadné vložení.
* Dávkování - S Linux-založené HDInsight, `-batch` Při použití přepínače při provádění vložení, Sqoop provádí více vloží namísto dávkování operace vložení.

## <a name="next-steps"></a>Další kroky

Teď jste se naučili používat Sqoop. Další informace naleznete v tématu:

* [Použití Apache Hive s HDInsight](../hdinsight-use-hive.md)
* [Nahrávání dat do HDInsightu](../hdinsight-upload-data.md): Najděte další metody nahrávání dat do úložiště objektů blob HDInsight/Azure.
* [Import a export dat mezi Apache Hadoopem ve službě HDInsight a SQL Database pomocí Apache Sqoopu](./apache-hadoop-use-sqoop-mac-linux.md)