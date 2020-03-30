---
title: Knihovny Apache Hive během vytváření clusteru – Azure HDInsight
description: Přečtěte si, jak přidat knihovny Apache Hive (jar soubory) do clusteru HDInsight během vytváření clusteru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: 0b746963cea5a950ba47d8b4dfeb074cb0910436
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77471019"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Přidání vlastních knihoven Apache Hive při vytváření clusteru HDInsight

Přečtěte si, jak předběžně načíst knihovny [Apache Hive](https://hive.apache.org/) na HDInsightu. Tento dokument obsahuje informace o použití akce skriptu k předběžnému načtení knihoven během vytváření clusteru. Knihovny přidané pomocí kroků v tomto dokumentu jsou globálně dostupné v Úlu – k jejich načtení není nutné používat [ADD JAR.](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli)

## <a name="how-it-works"></a>Jak to funguje

Při vytváření clusteru můžete pomocí akce skriptu upravit uzly clusteru při jejich vytváření. Skript v tomto dokumentu přijímá jeden parametr, což je umístění knihoven. Toto umístění musí být v účtu úložiště Azure a knihovny musí být uloženy jako jar soubory.

Během vytváření clusteru skript vyjmenovává soubory, `/usr/lib/customhivelibs/` zkopíruje je do adresáře na `hive.aux.jars.path` hlavových `core-site.xml` a pracovních uzlech a přidá je do vlastnosti v souboru. V clusterech založených na Linuxu `hive-env.sh` také aktualizuje soubor s umístěním souborů.

Pomocí akce skriptv tomto článku zpřístupníte knihovny při použití klienta Hive pro **WebHCat**a **HiveServer2**.

## <a name="the-script"></a>Skript

**Umístění skriptu**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Požadavky

* Skripty musí být použity jak **uzly Vedoucí,** tak **uzly pracovníka**.

* Nádoby, které chcete nainstalovat, musí být uloženy v azure blob storage v **jednom kontejneru**.

* Účet úložiště obsahující knihovnu souborů jar **musí** být během vytváření propojen s clusterem HDInsight. Musí se jednat buď o výchozí účet úložiště, nebo o účet přidaný prostřednictvím __nastavení účtu úložiště__.

* Cesta WASB ke kontejneru musí být zadána jako parametr akce skriptu. Například pokud jsou jars uloženy v kontejneru s názvem **libs** na `wasbs://libs@mystorage.blob.core.windows.net/`účtu úložiště s názvem **mystorage**, parametr by .

  > [!NOTE]  
  > Tento dokument předpokládá, že jste již vytvořili účet úložiště, kontejner objektů blob a nahráli do něj soubory.
  >
  > Pokud jste nevytvořili účet úložiště, můžete tak učinit prostřednictvím [portálu Azure](https://portal.azure.com). Potom můžete použít nástroj, jako je [Například Azure Storage Explorer](https://storageexplorer.com/) k vytvoření kontejneru v účtu a nahrát soubory do něj.

## <a name="create-a-cluster-using-the-script"></a>Vytvoření clusteru pomocí skriptu

1. Začněte zřizování clusteru pomocí kroků v [provision HDInsight clusterů na Linuxu](hdinsight-hadoop-provision-linux-clusters.md), ale nedokončíte zřizování. K vytvoření clusteru pomocí tohoto skriptu můžete taky použít Azure PowerShell nebo HDInsight .NET SDK. Další informace o použití těchto metod naleznete v [tématu Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md). Na webu Azure Portal vyberte na kartě **Konfigurace + ceny** akci + Přidat **skript**.

1. Pokud **se**účet úložiště obsahující knihovnu souborů jar bude lišit od účtu použitého pro cluster, vyplňte další **účty úložiště**.

1. V **části Akce skriptu**zadejte následující informace:

    |Vlastnost |Hodnota |
    |---|---|
    |Typ skriptu|- Vlastní|
    |Name (Název)|Knihovny |
    |Bash skript URI|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Typ uzlu|Hlava, Pracovník|
    |Parametry|Zadejte adresu WASB do kontejneru a účtu úložiště, který obsahuje sklenice. Například, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Pro Apache Spark 2.1 použijte tento `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh`bash skript URI: .

1. Pokračujte ve zřizování clusteru, jak je popsáno v [clusterech Provision HDInsight v Linuxu](hdinsight-hadoop-provision-linux-clusters.md).

Po dokončení vytváření clusteru, budete moci použít sklenice přidané prostřednictvím tohoto skriptu `ADD JAR` z Hive bez nutnosti používat příkaz.

## <a name="next-steps"></a>Další kroky

Další informace o práci s Hive najdete v [tématu Použití Apache Hive s HDInsight](hadoop/hdinsight-use-hive.md)
