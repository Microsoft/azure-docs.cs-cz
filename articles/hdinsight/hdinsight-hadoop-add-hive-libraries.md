---
title: Knihovny Apache Hive během vytváření clusteru – Azure HDInsight
description: Naučte se přidávat knihovny Apache Hive (soubory jar) do clusteru HDInsight během vytváření clusteru.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.date: 02/14/2020
ms.openlocfilehash: c678372fbd54e528a8a16eacc601e815cfd32e58
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86082229"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Při vytváření clusteru HDInsight přidat vlastní knihovny Apache Hive

Naučte se, jak předem načíst knihovny [Apache Hive](https://hive.apache.org/) v HDInsight. Tento dokument obsahuje informace o použití akce skriptu pro předběžné načtení knihoven během vytváření clusteru. Knihovny přidané pomocí kroků v tomto dokumentu jsou v podregistru globálně dostupné – k jejich načtení není potřeba použít příkaz [Přidat jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) .

## <a name="how-it-works"></a>Jak to funguje

Při vytváření clusteru můžete použít akci skriptu ke změně uzlů clusteru při jejich vytváření. Skript v tomto dokumentu přijímá jeden parametr, což je umístění knihoven. Toto umístění musí být v účtu Azure Storage a knihovny musí být uloženy jako soubory jar.

Při vytváření clusteru skript vypíše soubory, zkopíruje je do `/usr/lib/customhivelibs/` adresáře na hlavním a pracovním uzlu a pak je přidá do `hive.aux.jars.path` vlastnosti v `core-site.xml` souboru. U clusterů se systémem Linux aktualizuje také `hive-env.sh` soubor s umístěním souborů.

Použití akce skriptu v tomto článku zpřístupňuje knihovny při použití klientského podregistru pro **WebHCat**a **HiveServer2**.

## <a name="the-script"></a>Skript

**Umístění skriptu**

[https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

### <a name="requirements"></a>Požadavky

* Skripty musí být aplikovány na **hlavní uzly** i **pracovní uzly**.

* JAR, který chcete nainstalovat, musí být uložený v Azure Blob Storage v **jediném kontejneru**.

* Účet úložiště, který obsahuje knihovnu souborů JAR, se **musí** během vytváření propojit s clusterem HDInsight. Musí se jednat buď o výchozí účet úložiště, nebo o účet, který jste přidali prostřednictvím __Nastavení účtu úložiště__.

* Cesta WASB k kontejneru musí být zadána jako parametr pro akci skriptu. Například pokud jsou jar uložené v kontejneru s názvem **knihovny** na účtu úložiště s názvem **mystorage**, parametr by byl `wasbs://libs@mystorage.blob.core.windows.net/` .

  > [!NOTE]  
  > V tomto dokumentu se předpokládá, že jste už vytvořili účet úložiště, kontejner objektů BLOB a nahráli do něj soubory.
  >
  > Pokud jste účet úložiště nevytvořili, můžete to provést prostřednictvím [Azure Portal](https://portal.azure.com). Pak můžete použít nástroj, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/) , k vytvoření kontejneru v účtu a nahrání souboru do něj.

## <a name="create-a-cluster-using-the-script"></a>Vytvoření clusteru pomocí skriptu

1. Spusťte zřizování clusteru pomocí postupu v části [zřízení clusterů HDInsight v systému Linux](hdinsight-hadoop-provision-linux-clusters.md), ale neprovádějte zřízení. Pomocí tohoto skriptu můžete také pomocí Azure PowerShell nebo sady HDInsight .NET SDK vytvořit cluster. Další informace o použití těchto metod najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md). V případě Azure Portal na kartě **Konfigurace + ceny** vyberte **akci + přidat skript**.

1. Pokud se účet **úložiště, který**obsahuje knihovnu souborů JAR, liší od účtu, který se používá pro cluster, dokončete **Další účty úložiště**.

1. V případě **akcí skriptu**zadejte následující informace:

    |Vlastnost |Hodnota |
    |---|---|
    |Typ skriptu|– Vlastní|
    |Name|Knihovny |
    |Identifikátor URI skriptu bash|`https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh`|
    |Typ (typy) uzlů|Vedoucí pracovník|
    |Parametry|Zadejte adresu WASB kontejneru a účtu úložiště, který obsahuje jar. Například, `wasbs://libs@mystorage.blob.core.windows.net/`.|

    > [!NOTE]
    > Pro Apache Spark 2,1 použijte tento identifikátor URI bash skriptu: `https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v00.sh` .

1. Pokračujte ve zřizování clusteru, jak je popsáno v tématu [zřizování clusterů HDInsight v systému Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po dokončení vytváření clusteru můžete použít jar, které jste přidali prostřednictvím tohoto skriptu z podregistru, aniž byste museli `ADD JAR` příkaz použít.

## <a name="next-steps"></a>Další kroky

Další informace o práci s podregistrem najdete v tématu [použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md) .
