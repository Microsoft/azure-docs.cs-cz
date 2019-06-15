---
title: Spouštět úlohy Apache Sqoop s Azure HDInsight (Apache Hadoop)
description: Zjistěte, jak pomocí Azure Powershellu z pracovní stanice Sqoop import a export mezi clusterem Hadoop a službě Azure SQL database.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: 6764d8d812789c9f54fa59e10b2a3e416e583a9c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "62129395"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Použití Apache Sqoop se systémem Hadoop v HDInsight
[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Další informace o použití Apache Sqoop v HDInsight pro import a export dat mezi clusterem HDInsight a Azure SQL database.

I když Apache Hadoop je přirozenou volbou pro zpracování nestrukturovaných nebo semistrukturovaných dat, jako jsou protokoly a soubory, může to mít také potřeba ke zpracování strukturovaných dat, která je uložená v relačních databázích.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) je nástroj určený pro přenos dat mezi clustery Hadoop a relačními databázemi. Slouží k importu dat ze systému pro správu relačních databází (RDBMS), jako jsou SQL Server, MySQL nebo Oracle do distribuovaného systému souborů Hadoop (HDFS) transformace dat v systému Hadoop pomocí MapReduce nebo Apache Hive a pak exportovat data zpět do relační databázový systém . V tomto článku využívají databáze serveru SQL Server pro relační databáze.

> [!IMPORTANT]  
> Tento článek nastaví i testovací prostředí pro samotný přenos dat. Pak zvolte metodu přenosu dat pro toto prostředí z jedné z metod popsaných v části [úlohy spustit Sqoop](#run-sqoop-jobs), níže.

Sqoop verze, které jsou podporovány v clusterech HDInsight najdete v tématu [co je nového ve verzích clusterů poskytovaných službou HDInsight?](../hdinsight-component-versioning.md)

## <a name="understand-the-scenario"></a>Vysvětlení scénáře

HDInsight cluster se dodává s ukázkovými daty. Můžete použít následující dvě ukázky:

* Soubor protokolu Apache Log4j, které se nacházejí v `/example/data/sample.log`. Tyto protokoly jsou extrahovány ze souboru:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Tabulky Hive s názvem `hivesampletable`, které se odkazuje na datový soubor umístěn v `/hive/warehouse/hivesampletable`. Tabulka obsahuje některé data mobilních zařízení.
  
  | Pole | Typ dat |
  | --- | --- |
  | ID klienta |string |
  | querytime |string |
  | na trhu |string |
  | deviceplatform |string |
  | devicemake |string |
  | devicemodel |string |
  | state |string |
  | Země |string |
  | querydwelltime |double |
  | ID relace |bigint |
  | sessionpagevieworder |bigint |

V tomto článku použijete k testování Sqoop import a export těmito dvěma datovými sadami.

## <a name="create-cluster-and-sql-database"></a>Nastavení testovacího prostředí
Cluster, SQL database a dalších objektů jsou vytvořené prostřednictvím webu Azure portal pomocí šablony Azure Resource Manageru. Šablony lze nalézt v [šablony rychlý start Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Šablony Resource Manageru volá bacpac balíčku pro nasazení schémata tabulek do služby SQL database.  Balíček bacpac se nachází v kontejneru objektů blob veřejný, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Pokud chcete použít u souborů bacpac kontejner privátní, použijte následující hodnoty v šabloně:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Import pomocí šablony nebo na webu Azure portal podporuje pouze import souboru BACPAC z úložiště objektů blob v Azure.

1. Vyberte následující obrázek otevřete šablonu Resource Manageru na webu Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Zadejte tyto vlastnosti:

    |Pole |Hodnota |
    |---|---|
    |Předplatné |Z rozevíracího seznamu vyberte své předplatné Azure.|
    |Skupina prostředků |Z rozevíracího seznamu vyberte skupinu prostředků nebo vytvořte novou|
    |Location |Z rozevíracího seznamu vyberte oblast.|
    |Název clusteru |Zadejte název clusteru Hadoop. Použijte pouze malé písmeno.|
    |Uživatelské jméno přihlášení clusteru |Ponechte hodnotu předvyplněný `admin`.|
    |Heslo přihlášení clusteru |Zadejte heslo.|
    |Uživatelské jméno SSH |Ponechte hodnotu předvyplněný `sshuser`.|
    |SSH heslo |Zadejte heslo.|
    |Přihlašovací jméno správce SQL |Ponechte hodnotu předvyplněný `sqluser`.|
    |Heslo správce SQL |Zadejte heslo.|
    |_artifacts umístění | Použijte výchozí hodnotu, pokud chcete použít vlastní soubor bacpac v jiném umístění.|
    |Token Sas _artifacts umístění |Ponechte prázdné.|
    |Název souboru Bacpac |Použijte výchozí hodnotu, pokud chcete použít vlastní soubor bacpac.|
    |Location |Použijte výchozí hodnotu.|

    Název serveru Azure SQL Server bude `<ClusterName>dbserver`. Název databáze bude `<ClusterName>db`. Výchozí název účtu úložiště bude `e6qhezrh2pdqu`.

3. Vyberte **vyjadřuji souhlas s podmínkami a ujednáními uvedenými nahoře**.

4. Vyberte **Koupit**. Zobrazí se nová dlaždice s názvem odeslání nasazení pro šablonu nasazení. Vytvoření clusteru a databáze SQL trvá přibližně 20 minut.

## <a name="run-sqoop-jobs"></a>Spouštění úloh Sqoop

HDInsight můžete spouštět úlohy Sqoop pomocí různých metod. Použijte následující tabulku k rozhodnutí, která metoda je pro vás nejvhodnější a potom na odkaz pro návod.

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, Unix, Mac OS X a Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (prozatím) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Omezení

* Hromadné export - s Linuxovým systémem HDInsight, Sqoop konektor používaný k exportu dat Microsoft SQL Server nebo Azure SQL Database aktuálně nepodporuje operace hromadného vložení.
* Dávkování – s Linuxovým systémem HDInsight při použití `-batch` přepnout při provádění operace vložení, Sqoop provede několik vložení místo dávkování operace vložení.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili, jak použít Sqoop. Další informace naleznete v tématu:

* [Použití Apache Hivu se službou HDInsight](../hdinsight-use-hive.md)
* [Použití Apache Pig s HDInsight](../hdinsight-use-pig.md)
* [Nahrání dat do HDInsight](../hdinsight-upload-data.md): Najdete další metody pro nahrávání dat do HDInsight nebo Azure Blob storage.
