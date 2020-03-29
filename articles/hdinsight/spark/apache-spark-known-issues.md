---
title: Řešení problémů s clusterem Apache Spark v Azure HDInsightu
description: Přečtěte si o problémech souvisejících s clustery Apache Spark v Azure HDInsight a jak je obejít.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 2c153d818136c5d8804dae72004dfaf17fd1bf7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73494530"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Známé problémy pro cluster Apache Spark na HDInsightu

Tento dokument sleduje všechny známé problémy pro hdinsight spark public preview.  

## <a name="apache-livy-leaks-interactive-session"></a>Apache Livy úniky interaktivní relace
Když [apache livy](https://livy.incubator.apache.org/) restartuje (z [Apache Ambari](https://ambari.apache.org/) nebo z důvodu headnode 0 restartu virtuálního počítače) s interaktivní relace stále naživu, interaktivní pracovní relace je unikly. V důsledku toho mohou být ve stavu Přijato uvízly nové úlohy.

**Zmírnění:**

Pomocí následujícího postupu problém obejít:

1. Ssh do headnode. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spusťte následující příkaz a najděte ID aplikace interaktivních úloh spuštěných prostřednictvím Livy.

        yarn application –list

    Výchozí názvy úloh budou Livy, pokud byly úlohy zahájeny interaktivní relací Livy bez zadaných explicitních názvů. Pro relaci Livy spuštěnou [poznámkovým blokem Jupyter](https://jupyter.org/)začíná název úlohy na . `remotesparkmagics_*`

3. Spusťte následující příkaz zabít tyto úlohy.

        yarn application –kill <Application ID>

Začnou běžet nové úlohy.

## <a name="spark-history-server-not-started"></a>Server historie spark nebyl spuštěn
Po vytvoření clusteru není server historie spark automaticky spuštěn.  

**Zmírnění:**

Ručně spusťte history server z Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problém s oprávněním v adresáři protokolu Spark
hdiuser dostane následující chybu při odesílání úlohy pomocí spark-submit:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

A není zapsán žádný záznam ovladače.

**Zmírnění:**

1. Přidejte hdiuser do skupiny Hadoop.
2. Po vytvoření clusteru poskytněte 777 oprávnění k parametru /var/log/spark.
3. Aktualizujte umístění protokolu jiskry pomocí ambari, aby se adresář s 777 oprávněními.  
4. Spustit jiskra-předložit jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Konektor Spark-Phoenix není podporován

Clustery HDInsight Spark nepodporují konektor Spark-Phoenix.

**Zmírnění:**

Místo toho je nutné použít konektor Spark-HBase. Pokyny naleznete v tématu [Jak používat konektor Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problémy související s notebooky Jupyter

Níže jsou uvedeny některé známé problémy týkající se Jupyter notebooky.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Poznámkové bloky se znaky, které nejsou znaky ASCII v názvech souborů

Nepoužívejte znaky jiné než ASCII v názvech souborů poznámkového bloku Jupyter. Pokud se pokusíte nahrát soubor prostřednictvím uživatelského rozhraní Jupyter, které má název souboru bez ASCII, selže bez chybové zprávy. Jupyter vám nedovolí nahrát soubor, ale nevyvolá viditelnou chybu.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Při načítání poznámkových bloků větších velikostí došlo k chybě

Při načítání poznámkových bloků, které mají větší velikost, se může zobrazit chyba. **`Error loading notebook`**  

**Zmírnění:**

Pokud se zobrazí tato chyba, neznamená to, že vaše data jsou poškozená nebo ztracená.  Poznámkové bloky jsou stále `/var/lib/jupyter`na disku v aplikaci a můžete ssh do clusteru pro přístup k nim. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení ke clusteru pomocí SSH můžete zkopírovat poznámkové bloky z clusteru do místního počítače (pomocí SCP nebo WinSCP) jako zálohu, abyste zabránili ztrátě důležitých dat v poznámkovém bloku. Poté můžete SSH tunel do vašeho headnode na portu 8001 pro přístup K Jupyter, aniž by přes bránu.  Odtud můžete vymazat výstup poznámkového bloku a znovu jej uložit, abyste minimalizovali velikost poznámkového bloku.

Chcete-li zabránit této chybě v budoucnu, je nutné dodržovat některé doporučené postupy:

* Je důležité, aby velikost notebooku byla malá. Všechny výstupy z úloh Spark, které se odesílají zpět do Jupyteru, se v poznámkovém bloku trvalý.  Je to osvědčený postup s Jupyter `.collect()` obecně, aby se zabránilo spuštění na velkých RDD nebo datových rámců; Místo toho, pokud chcete nahlédnout do obsahu RDD, zvažte spuštění `.take()` nebo `.sample()` tak, aby se výstup příliš nezvinnul.
* Také při uložení poznámkového bloku zrušte všechny výstupní buňky, abyste zmenšili velikost.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Počáteční spuštění notebooku trvá déle, než bylo očekáváno

První kód ový příkaz v jupyterovém notebooku pomocí Spark magic může trvat déle než minutu.  

**Vysvětlení:**

K tomu dochází, protože při spuštění první buňky kódu. Na pozadí to iniciuje konfiguraci relace a Spark, SQL a Hive kontexty jsou nastaveny. Po nastavení těchto kontextů je spuštěn první příkaz a to vyvolává dojem, že prohlášení trvalo dlouhou dobu.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Časový čas poznámkového bloku Jupyter při vytváření relace

Když spark cluster je z prostředků, Spark a PySpark jádra v jupyter notebooku bude časový majestát se snaží vytvořit relaci.

**Skutečnosti snižující závažnost rizika:**

1. Uvolněte některé prostředky v clusteru Spark takto:

   * Zastavení ostatních poznámkových bloků Spark tak, že přejdete do nabídky Zavřít a zastavit nebo kliknete na Vypnout v průzkumníku poznámkových bloků.
   * Zastavení dalších aplikací Spark z YARN.

2. Restartujte poznámkový blok, který jste se pokoušeli spustit. K vytvoření relace by nyní mělo být k dispozici dostatek prostředků.

## <a name="see-also"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Použijte Spark v HDInsightu pro analýzu teploty budovy pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití HDInsight Tools Plugin pro IntelliJ IDEA k ladění aplikací Apache Spark na dálku](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark na HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro notebook Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)