---
title: Knihovny Apache Hive během vytváření clusteru – Azure HDInsight
description: Naučte se přidávat knihovny Apache Hive (soubory jar) do clusteru HDInsight během vytváření clusteru.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 51a93aaec4abdb2dd9d8fad042c079a48d4ea7a3
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494839"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Při vytváření clusteru HDInsight přidat vlastní knihovny Apache Hive

Naučte se, jak předem načíst knihovny [Apache Hive](https://hive.apache.org/) v HDInsight. Tento dokument obsahuje informace o použití akce skriptu pro předběžné načtení knihoven během vytváření clusteru. Knihovny přidané pomocí kroků v tomto dokumentu jsou v podregistru globálně dostupné – k jejich načtení není nutné použít příkaz [Přidat jar](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) .

## <a name="how-it-works"></a>Jak to funguje

Při vytváření clusteru můžete použít akci skriptu ke změně uzlů clusteru při jejich vytváření. Skript v tomto dokumentu přijímá jeden parametr, což je umístění knihoven. Toto umístění musí být v účtu Azure Storage a knihovny musí být uloženy jako soubory jar.

Při vytváření clusteru skript vytvoří výčet souborů, zkopíruje je do adresáře `/usr/lib/customhivelibs/` v uzlech a pracovní uzly a pak je přidá do vlastnosti `hive.aux.jars.path` v souboru `core-site.xml`. U clusterů se systémem Linux aktualizuje také soubor `hive-env.sh` s umístěním souborů.

> [!NOTE]  
> Použití akcí skriptů v tomto článku zpřístupňuje knihovny v následujících scénářích:
>
> * **HDInsight se systémem Linux** – při použití klienta podregistru, **WebHCat**a **HiveServer2**.
> * **HDInsight se systémem Windows** – při použití klienta podregistru a **WebHCat**.

## <a name="the-script"></a>Skript

**Umístění skriptu**

**Clustery se systémem Linux**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pro **clustery založené na systému Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

**Požadavky**

* Skripty musí být aplikovány na **hlavní uzly** i **pracovní uzly**.

* JAR, který chcete nainstalovat, musí být uložený v Azure Blob Storage v **jediném kontejneru**.

* Účet úložiště, který obsahuje knihovnu souborů JAR, se **musí** během vytváření propojit s clusterem HDInsight. Musí být buď výchozí účet úložiště, nebo účet přidaný prostřednictvím __Volitelné Konfigurace__.

* Cesta WASB k kontejneru musí být zadána jako parametr pro akci skriptu. Pokud je například jar uložen v kontejneru s názvem **knihovny** na účtu úložiště s názvem **mystorage**, parametr by byl **wasb://libs\@mystorage.blob.Core.Windows.NET/** .

  > [!NOTE]  
  > V tomto dokumentu se předpokládá, že jste už vytvořili účet úložiště, kontejner objektů BLOB a nahráli do něj soubory.
  >
  > Pokud jste účet úložiště nevytvořili, můžete to provést prostřednictvím [Azure Portal](https://portal.azure.com). Pak můžete použít nástroj, jako je například [Průzkumník služby Azure Storage](https://storageexplorer.com/) , k vytvoření kontejneru v účtu a nahrání souboru do něj.

## <a name="create-a-cluster-using-the-script"></a>Vytvoření clusteru pomocí skriptu

> [!NOTE]  
> Následující kroky vytvoří cluster HDInsight se systémem Linux. Pokud chcete vytvořit cluster se systémem Windows, při vytváření clusteru vyberte **Windows** jako operační systém clusteru a místo skriptu bash použijte skript Windows (PowerShell).
>
> Pomocí tohoto skriptu můžete také pomocí Azure PowerShell nebo sady HDInsight .NET SDK vytvořit cluster. Další informace o použití těchto metod najdete v tématu [Přizpůsobení clusterů HDInsight pomocí akcí skriptů](hdinsight-hadoop-customize-cluster-linux.md).

1. Spusťte zřizování clusteru pomocí postupu v části [zřízení clusterů HDInsight v systému Linux](hdinsight-hadoop-provision-linux-clusters.md), ale Nedokončujte zřizování.

2. V části **volitelná konfigurace** vyberte **akce skriptu**a zadejte následující informace:

   * **Název**: zadejte popisný název akce skriptu.

   * **Identifikátor URI skriptu**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **Head**: tuto možnost Ověřte.

   * **Pracovní proces**: tuto možnost Ověřte.

   * **ZOOKEEPER**: ponechte toto pole prázdné.

   * **Parametry**: zadejte adresu WASB kontejneru a účtu úložiště, který obsahuje jar. Například **wasb://libs\@mystorage.blob.Core.Windows.NET/** .

3. V dolní části **akcí skriptu**uložte konfiguraci pomocí tlačítka pro **Výběr** .

4. V části **volitelná konfigurace** vyberte **propojené účty úložiště** a pak vyberte odkaz **Přidat klíč úložiště** . Vyberte účet úložiště, který obsahuje jar. Pak pomocí tlačítek **Vybrat** uložte nastavení a vraťte **volitelnou konfiguraci**.

5. Volitelnou konfiguraci uložíte tak, že použijete tlačítko **Vybrat** v dolní části **volitelného oddílu konfigurace** .

6. Pokračujte ve zřizování clusteru, jak je popsáno v tématu [zřizování clusterů HDInsight v systému Linux](hdinsight-hadoop-provision-linux-clusters.md).

Po dokončení vytváření clusteru budete moct použít jar přidaný prostřednictvím tohoto skriptu z podregistru bez nutnosti použít příkaz `ADD JAR`.

## <a name="next-steps"></a>Další kroky

Další informace o práci s podregistrem najdete v tématu [použití Apache Hive se službou HDInsight](hadoop/hdinsight-use-hive.md) .
