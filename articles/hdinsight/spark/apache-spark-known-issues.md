---
title: Řešení problémů s cluster Apache Spark v Azure HDInsight | Microsoft Docs
description: Další informace o problémech souvisejících s clustery Apache Spark v Azure HDInsight a postup ty obejít.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 610c4103-ffc8-4ec0-ad06-fdaf3c4d7c10
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: nitinme
ms.openlocfilehash: 664c97117de793209007843fa23c98f52c2b079d
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31519235"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Známé problémy pro cluster Apache Spark v HDInsight

Tento dokument uchovává informace o všechny známé problémy pro verzi public preview HDInsight Spark.  

## <a name="livy-leaks-interactive-session"></a>Livy nevracení interaktivní relace.
Když Livy restartuje (z Ambari nebo z důvodu restartování virtuálního počítače headnode 0) k interaktivní relaci stále aktivní, došlo k úniku relaci interaktivní úlohy. V důsledku toho může být nové úlohy zasekla v automatickém platných stavu.

**Omezení rizik:**

Chcete-li tento problém obejít, postupujte takto:

1. SSH do headnode. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte následující příkaz k vyhledání ID aplikací interaktivní úlohy spuštěné prostřednictvím Livy. 
   
        yarn application –list
   
    Název úlohy výchozí bude Livy, pokud úloh bylo zahájeno pomocí Livy interaktivní relace se žádné explicitní názvy zadané. Pro relaci Livy spustí Poznámkový blok Jupyter název úlohy začíná remotesparkmagics_ *. 
3. Spusťte následující příkaz k ukončení těchto úloh. 
   
        yarn application –kill <Application ID>

Nové úlohy spustit. 

## <a name="spark-history-server-not-started"></a>Spark historie Server není spuštěn
Spark historie Server není spuštěn automaticky po vytvoření clusteru.  

**Omezení rizik:** 

Historie serveru spusťte ručně z Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problém s oprávněním v adresáři protokolu Spark
hdiuser získá k následující chybě při odesílání úlohy pomocí spark odeslat:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
A je zapsán protokolu žádné ovladače. 

**Omezení rizik:**

1. Přidejte hdiuser ke skupině Hadoop. 
2. Zadejte 777 oprávnění na /var/log/spark až po vytvoření clusteru. 
3. Aktualizujte umístění protokolu spark pomocí nástroje Ambari být adresář s 777 oprávnění.  
4. Spustit spark odeslat jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Spark Phoenix konektor není podporovaný.

Clustery HDInsight Spark Spark Phoenix konektor nepodporují.

**Omezení rizik:**

Místo toho musíte použít konektor Spark HBase. Pokyny najdete v tématu [postupy k používání konektoru Spark HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problémy související s poznámkové bloky Jupyter
Toto jsou některé známé problémy související s poznámkové bloky Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Poznámkové bloky s jiné znaky než ASCII v názvech souborů
V názvech souborů poznámkového bloku Jupyter nepoužívejte ani jiné znaky než ASCII. Pokud se pokusíte odeslat soubor prostřednictvím uživatelského rozhraní Jupyter, který má název souboru jiné sady než ASCII, selže bez jakékoli chybová zpráva. Jupyter neumožňuje nahrát soubor, ale viditelná chyba nevyvolá výjimku buď.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Chyba při načítání poznámkové bloky o větší velikosti
Může dojít k chybě **`Error loading notebook`** při načtení poznámkových bloků, které jsou větší velikost.  

**Omezení rizik:**

K této chybě dojde, neznamená, že vaše data jsou poškozené nebo ztraceny.  Poznámkové bloky jsou stále na disku v `/var/lib/jupyter`, a můžete SSH do clusteru, aby k nim přístup. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Jakmile se připojíte ke clusteru pomocí SSH, můžete zkopírovat poznámkové bloky z clusteru do místního počítače (pomocí spojovací bod služby nebo WinSCP) jako zálohu nedošlo ke ztrátě všech důležitých dat v poznámkovém bloku. Pak můžete tunelového propojení SSH do vaší headnode na portu 8001 pro přístup k Jupyter bez průchodu přes bránu.  Odtud můžete vymazat výstup Poznámkový blok a znovu jej na minimální velikost poznámkového bloku uložte.

Chcete-li zabránit v budoucnu nedošlo k této chybě, je třeba provést některé z osvědčených postupů:

* Je důležité udržet co nejmenší velikost poznámkového bloku. Všechny výstupy z úloh Spark, která budou odeslána zpět do Jupyter je jako trvalý v poznámkovém bloku.  Obecně je osvědčeným postupem s Jupyter předejdete spuštěná `.collect()` na velké RDD společnosti nebo dataframes; místo toho, pokud chcete prohlížet obsah RDD, zvažte spuštění `.take()` nebo `.sample()` tak, aby výstupu není získat příliš velký.
* Navíc při ukládání Poznámkový blok, zrušte všechny výstupní buněk ke snížení velikosti.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Poznámkový blok počáteční spuštění trvá déle, než se očekávalo
První příkaz kódu do poznámkového bloku Jupyter pomocí Spark magic může trvat déle než minutu.  

**Vysvětlení:**

K tomu dochází, protože při spuštění první buňky kódu. Na pozadí to zahájí konfiguraci relace a Spark, SQL a jsou nastavené kontexty Hive. Po tyto kontexty jsou nastaveny, se spustí první příkaz a díky dojem, který příkaz trvalo dlouhou dobu pro dokončení.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Časový limit Poznámkový blok Jupyter v vytvoření relace
Pokud Spark cluster nemá dostatek prostředků, jádrech Spark a PySpark v poznámkovém bloku Jupyter bude časový limit pokusu o vytvoření relace. 

**Způsoby zmírnění rizik:** 

1. Uvolněte některé prostředky v clusteru Spark pomocí:
   
   * Zastavování jiných poznámkových bloků Spark přechodem do nabídky zavřete a zastavení nebo kliknutím na vypnutí počítače v Průzkumníku poznámkového bloku.
   * Zastavování dalších aplikací Spark z YARN.
2. Restartujte poznámkového bloku, který chcete spustit. Musí být pro vytvoření relace nyní k dispozici dostatek prostředků.

## <a name="see-also"></a>Další informace najdete v tématech
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

