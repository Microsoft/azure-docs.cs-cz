---
title: Spouštění úloh Apache Sqoop v Azure HDInsight (Apache Hadoop)
description: Naučte se používat Azure PowerShell z pracovní stanice ke spuštění importu a exportu Sqoop mezi clusterem Hadoop a databází SQL Azure.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/12/2019
ms.openlocfilehash: f2a153b1eef974c8c73df49a6eed53ef5dbf2353
ms.sourcegitcommit: 8ef0a2ddaece5e7b2ac678a73b605b2073b76e88
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71076218"
---
# <a name="use-apache-sqoop-with-hadoop-in-hdinsight"></a>Použití Apache Sqoop se systémem Hadoop ve službě HDInsight

[!INCLUDE [sqoop-selector](../../../includes/hdinsight-selector-use-sqoop.md)]

Naučte se používat Apache Sqoop ve službě HDInsight k importu a exportu dat mezi clusterem HDInsight a databází SQL Azure.

I když je Apache Hadoop přirozenou volbou pro zpracování nestrukturovaných a částečně strukturovaných dat, jako jsou protokoly a soubory, může být potřeba zpracovat strukturovaná data uložená v relačních databázích.

[Apache Sqoop](https://sqoop.apache.org/docs/1.99.7/user.html) je nástroj určený k přenosu dat mezi clustery Hadoop a relačními databázemi. Můžete ji použít k importu dat ze systému pro správu relačních databází (RDBMS), jako je SQL Server, MySQL nebo Oracle, do systému Hadoop Distributed File System (HDFS), transformovat data v Hadoop pomocí MapReduce nebo Apache Hive a pak data exportovat zpátky do RDBMS. . V tomto článku používáte databázi SQL Server pro relační databázi.

> [!IMPORTANT]  
> Tento článek nastaví testovací prostředí pro přenos dat. Pak zvolíte způsob přenosu dat pro toto prostředí z jedné z metod v části [spuštění úloh Sqoop](#run-sqoop-jobs), které jsou níže.

Verze Sqoop podporované v clusterech HDInsight najdete v tématu [co je nového ve verzích clusterů poskytovaných službou HDInsight](../hdinsight-component-versioning.md) .

## <a name="understand-the-scenario"></a>Vysvětlení scénáře

Cluster HDInsight obsahuje ukázková data. Použijete následující dvě ukázky:

* Soubor protokolu Apache log4j, který se nachází na adrese `/example/data/sample.log`. Následující protokoly jsou extrahovány ze souboru:

```text
2012-02-03 18:35:34 SampleClass6 [INFO] everything normal for id 577725851
2012-02-03 18:35:34 SampleClass4 [FATAL] system problem at id 1991281254
2012-02-03 18:35:34 SampleClass3 [DEBUG] detail for id 1304807656
...
```

* Tabulka podregistru s `hivesampletable`názvem, která odkazuje na datový soubor umístěný `/hive/warehouse/hivesampletable`v. Tabulka obsahuje některá data mobilních zařízení.
  
  | Pole | Datový typ |
  | --- | --- |
  | ClientID |řetězec |
  | querytime |řetězec |
  | Uvádět |řetězec |
  | deviceplatform |řetězec |
  | devicemake |řetězec |
  | devicemodel |řetězec |
  | state |řetězec |
  | Krajin |řetězec |
  | querydwelltime |double |
  | SessionID |bigint |
  | sessionpagevieworder |bigint |

V tomto článku pomocí těchto dvou datových sad otestujete Sqoop import a export.

## <a name="create-cluster-and-sql-database"></a>Nastavení testovacího prostředí
Cluster, SQL Database a další objekty se vytvářejí prostřednictvím Azure Portal pomocí Azure Resource Manager šablony. Šablonu najdete v [šablonách rychlého startu Azure](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-sql-database/). Šablona Správce prostředků volá balíček BacPac pro nasazení schémat tabulek do databáze SQL.  Balíček BacPac se nachází ve veřejném kontejneru objektů blob, https://hditutorialdata.blob.core.windows.net/usesqoop/SqoopTutorial-2016-2-23-11-2.bacpac. Pokud chcete použít privátní kontejner pro soubory BacPac, použijte následující hodnoty v šabloně:

```json
"storageKeyType": "Primary",
"storageKey": "<TheAzureStorageAccountKey>",
```

> [!NOTE]  
> Import pomocí šablony nebo Azure Portal podporuje pouze import souboru BACPAC z úložiště objektů BLOB v Azure.

1. Výběrem následujícího obrázku otevřete šablonu Správce prostředků v Azure Portal.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-sql-database%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-use-sqoop/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Zadejte tyto vlastnosti:

    |Pole |Value |
    |---|---|
    |Subscription |V rozevíracím seznamu vyberte své předplatné Azure.|
    |Resource group |Z rozevíracího seznamu vyberte skupinu prostředků nebo vytvořte novou.|
    |Location |V rozevíracím seznamu vyberte oblast.|
    |Název clusteru |Zadejte název clusteru Hadoop. Použijte pouze malé písmeno.|
    |Uživatelské jméno přihlášení clusteru |Nechte předem vyplněnou hodnotu `admin`.|
    |Heslo přihlášení clusteru |Zadejte heslo.|
    |Uživatelské jméno SSH |Nechte předem vyplněnou hodnotu `sshuser`.|
    |Heslo SSH |Zadejte heslo.|
    |Přihlášení správce SQL |Nechte předem vyplněnou hodnotu `sqluser`.|
    |Heslo správce SQL |Zadejte heslo.|
    |Umístění _artifacts | Pokud nechcete použít vlastní soubor BacPac v jiném umístění, použijte výchozí hodnotu.|
    |Token SAS pro umístění _artifacts |Ponechte prázdné.|
    |Název souboru BacPac |Pokud nechcete použít vlastní soubor BacPac, použijte výchozí hodnotu.|
    |Location |Použijte výchozí hodnotu.|

    Název Azure SQL Server bude `<ClusterName>dbserver`. Název databáze bude `<ClusterName>db`. Výchozí název účtu úložiště bude `e6qhezrh2pdqu`.

3. Vyberte Souhlasím **s podmínkami a ujednáními uvedenými nahoře**.

4. Vyberte **Koupit**. Zobrazí se nová dlaždice s názvem odeslání nasazení pro Template deployment. Vytvoření clusteru a databáze SQL trvá přibližně 20 minut.

## <a name="run-sqoop-jobs"></a>Spuštění úloh Sqoop

HDInsight může spouštět úlohy Sqoop pomocí různých metod. Pomocí následující tabulky se rozhodněte, která metoda je pro vás nejvhodnější, a pak použijte odkaz na návod.

| **Použít** Pokud chcete... | ...an **interaktivní** prostředí | ...**batch** zpracování | ...from to **klientský operační systém** |
|:--- |:---:|:---:|:--- |:--- |
| [SSH](apache-hadoop-use-sqoop-mac-linux.md) |? |? |Linux, UNIX, Mac OS X nebo Windows |
| [Sada .NET SDK pro Hadoop](apache-hadoop-use-sqoop-dotnet-sdk.md) |&nbsp; |?  |Windows (pro teď) |
| [Azure PowerShell](apache-hadoop-use-sqoop-powershell.md) |&nbsp; |? |Windows |

## <a name="limitations"></a>Omezení

* Hromadný export – pomocí HDInsight se systémem Linux, konektor Sqoop používaný k exportu dat do Microsoft SQL Server nebo Azure SQL Database v současné době nepodporuje hromadné vložení.
* Dávkování – se systémem Linux HDInsight při použití `-batch` přepínače při provádění operací INSERT Sqoop provede vícenásobné vkládání místo dávkování operací vložení.

## <a name="next-steps"></a>Další postup
Nyní jste se naučili, jak používat Sqoop. Další informace naleznete v tématu:

* [Použití Apache Hive se službou HDInsight](../hdinsight-use-hive.md)
* [Použití Apache prasete se službou HDInsight](../hdinsight-use-pig.md)
* [Nahrát data do HDInsight](../hdinsight-upload-data.md): Najděte další metody pro nahrávání dat do služby HDInsight/Azure Blob Storage.
