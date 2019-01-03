---
title: Přidejte knihovny Apache Hive při vytváření clusteru HDInsight – Azure
description: Zjistěte, jak přidat Apache Hive knihovny (soubory jar) do clusteru během vytváření clusteru HDInsight.
services: hdinsight
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 02/27/2018
ms.author: hrasheed
ms.custom: H1Hack27Feb2017,hdinsightactive
ms.openlocfilehash: 4eb4db9a4057d072f348de48bee2f746f77cbb84
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53715337"
---
# <a name="add-custom-apache-hive-libraries-when-creating-your-hdinsight-cluster"></a>Přidat vlastní knihovny Apache Hive při vytváření clusteru HDInsight

Zjistěte, jak předběžné načtení [Apache Hive](https://hive.apache.org/) knihovny v HDInsight. Tento dokument obsahuje informace o použití akce skriptu k předběžné načtení knihovny během vytváření clusteru. Knihovny přidané pomocí kroků v tomto dokumentu jsou globálně k dispozici v podregistru – není nutné používat [přidat JAR](https://cwiki.apache.org/confluence/display/Hive/LanguageManual+Cli) je načíst.

## <a name="how-it-works"></a>Jak to funguje

Při vytváření clusteru, můžete použít akci skriptu k úpravě uzlech clusteru při jejich vytvoření. Skript v tomto dokumentu přijímá jeden parametr, který je umístěním knihoven. Tímto umístěním musí být v účtu služby Azure Storage a knihoven musí být uložena jako soubory jar.

Při vytváření clusteru, skript vytvoří výčet souborů, zkopíruje je do `/usr/lib/customhivelibs/` přidá je do adresáře na hlavní a pracovní uzly, pak `hive.aux.jars.path` vlastnost v `core-site.xml` souboru. Na clusterech založených na Linuxu, aktualizuje také `hive-env.sh` soubor s umístění souborů.

> [!NOTE]  
> Pomocí akcí skriptů v tomto článku zpřístupní knihoven v následujících scénářích:
>
> * **HDInsight se systémem Linux** – při použití Hive klienta **WebHCat**, a **HiveServer2**.
> * **HDInsight se systémem Windows** – při používání klienta Hive a **WebHCat**.

## <a name="the-script"></a>Skript

**Umístění skriptu**

Pro **linuxových clusterech**: [https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh)

Pro **clusterů se systémem Windows**: [https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1](https://hdiconfigactions.blob.core.windows.net/setupcustomhivelibsv01/setup-customhivelibs-v01.ps1)

> [!IMPORTANT]  
> HDInsight od verze 3.4 výše používá výhradně operační systém Linux. Další informace najdete v tématu [Vyřazení prostředí HDInsight ve Windows](hdinsight-component-versioning.md#hdinsight-windows-retirement).

**Požadavky**

* Skripty se musí použít pro obě **hlavním uzlům** a **pracovní uzly**.

* JAR, kterou chcete nainstalovat, musí být uložen v úložišti objektů Blob v Azure ve **jedním kontejnerem**.

* Účet úložiště obsahující knihovnu soubory jar **musí** propojit při vytváření clusteru HDInsight. Musí být buď výchozí účet úložiště, nebo prostřednictvím přidán účet __volitelná konfigurace__.

* Jako parametr pro skript akce musí být zadána cesta WASB do kontejneru. Například, pokud kromě souborů JAR jsou uloženy v kontejneru nazvaném **knihovny** v účtu úložiště s názvem **mystorage**, bude parametr **wasb://libs@mystorage.blob.core.windows.net/**.

  > [!NOTE]  
  > Tento dokument předpokládá, že jste už vytvořili účet úložiště, kontejner objektů blob a odeslat soubory do něj.
  >
  > Pokud jste ještě nevytvořili účet úložiště, můžete udělat, tak prostřednictvím [webu Azure portal](https://portal.azure.com). Potom můžete pomocí nástroje, jako [Průzkumníka služby Azure Storage](https://storageexplorer.com/) k vytvoření kontejneru v účtu a do něj nahrát soubory.

## <a name="create-a-cluster-using-the-script"></a>Vytvoření clusteru pomocí skriptu

> [!NOTE]  
> Následujícím postupem se vytvoří cluster HDInsight se systémem Linux. K vytvoření clusteru se systémem Windows, vyberte **Windows** jako cluster operační systém při vytváření clusteru a pomocí skriptu Windows (PowerShell) namísto skriptu bash.
>
> Prostředí Azure PowerShell nebo sady HDInsight .NET SDK můžete také použít k vytvoření clusteru pomocí tohoto skriptu. Další informace o použití těchto metod najdete v tématu [HDInsight přizpůsobit clustery pomocí skriptových akcí](hdinsight-hadoop-customize-cluster-linux.md).

1. Zahajte zřizování clusteru pomocí kroků v [zřídit HDInsight clustery v Linuxu](hdinsight-hadoop-provision-linux-clusters.md), ale nedokončí zřizování.

2. Na **volitelná konfigurace** vyberte **akcí skriptů**a zadejte následující informace:

   * **NÁZEV**: Zadejte popisný název akce skriptu.

   * **IDENTIFIKÁTOR URI SKRIPTU**: https://hdiconfigactions.blob.core.windows.net/linuxsetupcustomhivelibsv01/setup-customhivelibs-v01.sh.

   * **HLAVNÍ**: Zaškrtněte tuto možnost.

   * **PRACOVNÍK**: Zaškrtněte tuto možnost.

   * **ZOOKEEPER**: Ponechte toto nastavení prázdné.

   * **PARAMETRY**: Zadejte adresu WASB kontejner a účet úložiště, který obsahuje kromě souborů JAR. Příklad: **wasb://libs@mystorage.blob.core.windows.net/**.

3. V dolní části **akcí skriptů**, použijte **vyberte** tlačítko, čímž konfiguraci uložíte.

4. Na **volitelná konfigurace** vyberte **propojených účtech Storage** a vyberte **přidat klíč úložiště** odkaz. Vyberte účet úložiště, který obsahuje kromě souborů JAR. Potom použijte **vyberte** tlačítka pro uložení nastavení a návrat **volitelná konfigurace**.

5. Chcete-li uložit volitelná konfigurace, použijte **vyberte** tlačítko v dolní části **volitelná konfigurace** oddílu.

6. Pokračovat zřizování clusteru, jak je popsáno v [zřídit HDInsight clustery v Linuxu](hdinsight-hadoop-provision-linux-clusters.md).

Po dokončení vytváření clusteru, budete moct použít JAR přidané pomocí tohoto skriptu z podregistru bez nutnosti použití `ADD JAR` příkazu.

## <a name="next-steps"></a>Další postup

Další informace o práci s Hive najdete v tématu [použití Apache Hivu se službou HDInsight](hadoop/hdinsight-use-hive.md)
