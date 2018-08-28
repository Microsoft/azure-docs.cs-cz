---
title: Řešení potíží s clusterem Apache Spark v Azure HDInsight
description: Další informace o problémech souvisejících s clustery Apache Spark v Azure HDInsight a jak ty obejít.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 02/21/2018
ms.author: jasonh
ms.openlocfilehash: 92baa28393100abe3d7694920e5ee327966db927
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43048304"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Známé problémy pro cluster Apache Spark v HDInsight

Tento dokument uchovává informace o známé problémy HDInsight Spark verze public Preview.  

## <a name="livy-leaks-interactive-session"></a>Interaktivní relace nevracení Livy
Když Livy restartovat (z Ambari nebo z důvodu restartování virtuálního počítače hlavního uzlu 0) na interaktivní relaci stále aktivní, je úniku relaci interaktivní úlohy. Nové úlohy v důsledku toho mohou zaseknout ve stavu přijato.

**Omezení rizik:**

Chcete-li tento problém obejít, postupujte takto:

1. SSH do hlavního uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte následující příkaz k vyhledání ID aplikací rychlý prostřednictvím Livy interaktivní úlohy. 
   
        yarn application –list
   
    Pokud úlohy, které byly spuštěny s Livy interaktivní relace se žádné explicitní názvy zadané, bude výchozí názvy projektu Livy. Pro relaci Livy tím, že aplikace Jupyter notebook název úlohy začíná remotesparkmagics_ *. 
3. Spusťte následující příkaz k ukončení úlohy. 
   
        yarn application –kill <Application ID>

Nové úlohy začít spouštět. 

## <a name="spark-history-server-not-started"></a>Server historie Sparku nebylo zahájeno
Po vytvoření clusteru se automaticky spustit Server historie Sparku.  

**Omezení rizik:** 

Historie serveru spusťte ručně z Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problému s oprávněními v adresáři protokolu Spark
Při odesílání úlohy pomocí skriptu spark-submit, získá hdiuser následující chybu:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```
A žádný ovladač protokol je zapsán. 

**Omezení rizik:**

1. Přidáte hdiuser do skupiny Hadoop. 
2. Zadejte 777 oprávnění na /var/log/spark po vytvoření clusteru. 
3. Aktualizace umístění protokolu spark pomocí nástroje Ambari bude adresář s 777 oprávnění.  
4. Spuštění skriptu spark-submit jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Konektor Spark Phoenix není podporovaný

Clustery HDInsight Spark konektoru Spark Phoenix nepodporují.

**Omezení rizik:**

Místo toho je nutné použít konektor Spark HBase. Pokyny najdete v tématu [použití konektoru Spark HBase](https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problémy související s poznámkovými bloky Jupyter
Toto jsou některé známé problémy související s poznámkovými bloky Jupyter.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Poznámkové bloky s ne ASCII znaky v názvech souborů
Nepoužívejte ne ASCII znaky v názvech souborů Poznámkový blok Jupyter. Pokud se pokusíte odeslat soubor pomocí uživatelského rozhraní Jupyter, který má název souboru ne ASCII, selže bez jakékoli chybovou zprávu. Jupyter neumožňuje nahrát soubor, ale nevyvolá chybu viditelné buď.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Chyba při načítání poznámkové bloky o větší velikosti
Může se zobrazit chyba **`Error loading notebook`** při načíst poznámkovými bloky, které jsou větší velikost.  

**Omezení rizik:**

K této chybě dojde, neznamená, že vaše data jsou poškozena nebo ztráty.  Poznámkové bloky jsou stále na disku v `/var/lib/jupyter`, a získat přístup přes SSH do clusteru, aby k nim přístup. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Jakmile se připojíte ke clusteru pomocí SSH, můžete zkopírovat vašich poznámkových bloků z clusteru do místního počítače (pomocí spojovacího bodu služby nebo WinSCP) jako zálohování, aby se zabránilo ztrátě veškerá důležitá data v poznámkovém bloku. Pak můžete tunel SSH do vašeho hlavního uzlu na port 8001 pro přístup k Jupyter bez nutnosti kontaktovat bránu.  Odtud můžete vymazat výstupem poznámkového a znovu pro minimalizaci velikosti poznámkového bloku.

K této chybě zabránit v budoucnosti, je třeba provést některé osvědčené postupy:

* Je důležité udržet co nejmenší velikost poznámkového bloku. Všechny výstupy z Sparkových úloh, která budou odeslána zpět do aplikace Jupyter se ukládají v poznámkovém bloku.  Obecně je nejvhodnější s Jupyterem s aby se zabránilo spouštění `.collect()` na velké RDD společnosti nebo datových rámců; místo toho, pokud chcete, a prohlédněte si obsah RDD, zvažte spuštění `.take()` nebo `.sample()` tak, aby výstupu nezíská příliš velký.
* Navíc když uložíte Poznámkový blok, vymazat veškerý výstup buňky, které chcete zmenšit velikost.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Prvním spuštění poznámkového bloku trvá déle, než se čekalo
První příkaz kódu do poznámkového bloku Jupyter pomocí Spark magic může trvat déle než minutu.  

**Vysvětlení:**

K tomu dochází, protože při spuštění první buňky kódu. Na pozadí vyvolá konfigurace relací a Sparku, SQL a Hive kontexty jsou nastavené. Po těchto kontextech jsou nastaveny, první příkaz je spuštěný a díky tomu získá dojem, který příkaz trvalo dlouhou dobu pro dokončení.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Časový limit Poznámkový blok Jupyter v vytvoření relace
Když Spark cluster je nemá dostatek prostředků, Spark a PySpark jádra v poznámkovém bloku Jupyter se časový limit pokusu o vytvoření relace. 

**Omezení rizik:** 

1. Uvolněte některé prostředky ve vašem clusteru Sparku pomocí:
   
   * Přejděte do nabídky zavřít a zastavení nebo kliknutím na vypnutí v Průzkumníku poznámkového bloku se zastavuje jiné Spark poznámkových bloků.
   * Zastavuje se další aplikace Spark z YARN.
2. Restartujte Poznámkový blok, který se spustí. Musí být vytvořit relaci nyní k dispozici dostatek prostředky.

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

