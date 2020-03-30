---
title: Přidání dalších účtů Azure Storage do HDInsightu
description: Přečtěte si, jak přidat další účty Azure Storage do existujícího clusteru HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 01/21/2020
ms.openlocfilehash: 87eb04b7323186175195babf6a602fa12d25176f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206703"
---
# <a name="add-additional-storage-accounts-to-hdinsight"></a>Přidání dalších účtů úložiště do HDInsightu

Přečtěte si, jak pomocí akcí skriptů přidat do HDInsightu další *účty* Azure Storage. Kroky v tomto dokumentu přidávají *účet* úložiště do existujícího clusteru HDInsight. Tento článek se vztahuje na *účty* úložiště (není výchozí účet úložiště clusteru) a ne další úložiště, jako je [Azure Data Lake Storage Gen1](hdinsight-hadoop-use-data-lake-store.md) a [Azure Data Lake Storage Gen2](hdinsight-hadoop-use-data-lake-storage-gen2.md).

> [!IMPORTANT]  
> Informace v tomto dokumentu jsou o přidání dalších účtů úložiště do clusteru po jeho vytvoření. Informace o přidávání účtů úložiště během vytváření clusteru najdete [v tématu Nastavení clusterů v HDInsightu pomocí Apache Hadoop, Apache Spark, Apache Kafka a dalších](hdinsight-hadoop-provision-linux-clusters.md).

## <a name="prerequisites"></a>Požadavky

* Cluster Hadoop na HDInsight. Viz [Začínáme s HDInsight na Linuxu](./hadoop/apache-hadoop-linux-tutorial-get-started.md).
* Název účtu úložiště a klíč. Viz [Správa přístupových klíčů účtu úložiště](../storage/common/storage-account-keys-manage.md).
* Pokud používáte PowerShell, budete potřebovat modul AZ.  Podívejte se [na přehled Azure PowerShellu](https://docs.microsoft.com/powershell/azure/overview).

## <a name="how-it-works"></a>Jak to funguje

Během zpracování skript provede následující akce:

* Pokud účet úložiště již existuje v konfiguraci core-site.xml pro cluster, skript se ukončí a nebudou provedeny žádné další akce.

* Ověří, zda účet úložiště existuje a lze přistupovat pomocí klíče.

* Šifruje klíč pomocí pověření clusteru.

* Přidá účet úložiště do souboru core-site.xml.

* Zastaví a restartuje [služby Apache Oozie](https://oozie.apache.org/), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Hadoop MapReduce2](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html)a [Apache Hadoop HDFS.](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) Zastavení a spuštění těchto služeb jim umožňuje používat nový účet úložiště.

> [!WARNING]  
> Použití účtu úložiště v jiném umístění než cluster HDInsight není podporováno.

## <a name="add-storage-account"></a>Přidání účtu úložiště

Pomocí [akce skriptu](hdinsight-hadoop-customize-cluster-linux.md#script-action-to-a-running-cluster) použijte změny s následujícími poznámkami:

|Vlastnost | Hodnota |
|---|---|
|Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/linuxaddstorageaccountv01/add-storage-account-v01.sh`|
|Typ uzlu|Head|
|Parametry|`ACCOUNTNAME``ACCOUNTKEY` `-p` (nepovinné)|

* `ACCOUNTNAME`je název účtu úložiště, který chcete přidat do clusteru HDInsight.
* `ACCOUNTKEY`je přístupový `ACCOUNTNAME`klíč pro aplikaci .
* Parametr `-p` je volitelný. Pokud je zadán, klíč není zašifrován a je uložen v souboru core-site.xml jako prostý text.

## <a name="verification"></a>Ověření

Při zobrazení clusteru HDInsight na webu Azure Portal se výběrem __položky Účty úložiště__ v části __Vlastnosti__ nezobrazí účty úložiště přidané prostřednictvím této akce skriptu. Azure PowerShell a Azure CLI nezobrazují další účet úložiště. Informace o úložišti se nezobrazí, protože skript `core-site.xml` pouze upravuje konfiguraci clusteru. Tyto informace se nepoužívají při načítání informací o clusteru pomocí řešení API pro správu Azure.

Chcete-li ověřit další úložiště, použijte jednu z níže uvedených metod:

### <a name="powershell"></a>PowerShell

Skript vrátí název účtu úložiště přidružený k danému clusteru. Nahraďte `CLUSTERNAME` skutečný název clusteru a spusťte skript.

```powershell
# Update values
$clusterName = "CLUSTERNAME"

$creds = Get-Credential -UserName "admin" -Message "Enter the cluster login credentials"

$clusterName = $clusterName.ToLower();

# getting service_config_version
$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName`?fields=Clusters/desired_service_config_versions/HDFS" `
    -Credential $creds -UseBasicParsing
$respObj = ConvertFrom-Json $resp.Content

$configVersion=$respObj.Clusters.desired_service_config_versions.HDFS.service_config_version

$resp = Invoke-WebRequest -Uri "https://$clusterName.azurehdinsight.net/api/v1/clusters/$clusterName/configurations/service_config_versions?service_name=HDFS&service_config_version=$configVersion" `
    -Credential $creds
$respObj = ConvertFrom-Json $resp.Content

# extract account names
$value = ($respObj.items.configurations | Where type -EQ "core-site").properties | Get-Member -membertype properties | Where Name -Like "fs.azure.account.key.*"
foreach ($name in $value ) { $name.Name.Split(".")[4]}
```

### <a name="apache-ambari"></a>Apache Ambari

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Přejděte na **rozhraní HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Dodržujte klávesy, které začínají na `fs.azure.account.key`. Název účtu bude součástí klíče, jak je vidět v tomto ukázkovém obrázku:

   ![ověření prostřednictvím Apache Ambari](./media/hdinsight-hadoop-add-storage/apache-ambari-verification.png)

## <a name="remove-storage-account"></a>Odebrání účtu úložiště

1. Z webového prohlížeče `https://CLUSTERNAME.azurehdinsight.net`přejděte `CLUSTERNAME` na , kde je název clusteru.

1. Přejděte na **rozhraní HDFS** > **Configs** > **Advanced** > Custom**core-site**.

1. Odeberte následující klávesy:
    * `fs.azure.account.key.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`
    * `fs.azure.account.keyprovider.<STORAGE_ACCOUNT_NAME>.blob.core.windows.net`

Po odebrání těchto klíčů a uložení konfigurace je třeba restartovat Oozie, Přízi, MapReduce2, HDFS a Hive jeden po druhém.

## <a name="known-issues"></a>Známé problémy

### <a name="storage-firewall"></a>Brána firewall úložiště

Pokud se rozhodnete zabezpečit svůj účet úložiště pomocí **omezení brány firewall a virtuálních sítí** ve vybraných **sítích**, nezapomeňte povolit výjimku **Povolit důvěryhodné služby Microsoftu...** aby hdinsight měl přístup k vašemu účtu úložiště.

### <a name="unable-to-access-storage-after-changing-key"></a>Po změně klíče nelze získat přístup k úložišti.

Pokud změníte klíč pro účet úložiště, HDInsight už nemůže přistupovat k účtu úložiště. HDInsight používá kopii klíče uloženou v mezipaměti v souboru core-site.xml pro cluster. Tato kopie uložená v mezipaměti musí být aktualizována tak, aby odpovídala novému klíči.

Znovu spuštění akce skriptu __neaktualizuje__ klíč, protože skript zkontroluje, zda položka pro účet úložiště již existuje. Pokud položka již existuje, neprovede žádné změny.

Chcete-li tento problém vyřešit:  
1. Odeberte účet úložiště.
1. Přidejte účet úložiště.

> [!IMPORTANT]  
> Otočení klíče úložiště pro účet primárního úložiště připojeného ke clusteru není podporováno.

### <a name="poor-performance"></a>Nízký výkon

Pokud je účet úložiště v jiné oblasti než cluster HDInsight, může dojít ke snížení výkonu. Přístup k datům v jiné oblasti odesílá síťový provoz mimo regionální datové centrum Azure a přes veřejný internet, což může zavést latenci.

### <a name="additional-charges"></a>Příplatky

Pokud se účet úložiště nachází v jiné oblasti než cluster HDInsight, můžete si při fakturaci Azure všimnout dalších poplatků za odchozí přenos. Poplatek za odchozí přenos se použije, když data opustí regionální datové centrum. Tento poplatek se použije i v případě, že provoz je určený pro jiné datové centrum Azure v jiné oblasti.

## <a name="next-steps"></a>Další kroky

Zjistili jste, jak přidat další účty úložiště do existujícího clusteru HDInsight. Další informace o akcích skriptů najdete [v tématu Přizpůsobení clusterů HDInsight založených na Linuxu pomocí akce skriptu.](hdinsight-hadoop-customize-cluster-linux.md)
