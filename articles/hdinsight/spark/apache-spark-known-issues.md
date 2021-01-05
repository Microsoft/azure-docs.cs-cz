---
title: Řešení potíží s Apache Sparkm clusterem v Azure HDInsight
description: Přečtěte si o problémech souvisejících s Apache Spark clustery ve službě Azure HDInsight a o tom, jak tyto problémy obejít.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: troubleshooting
ms.date: 08/15/2019
ms.author: hrasheed
ms.openlocfilehash: 65163225853037ac4cb97a02c0bdaf554b509fb1
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/30/2020
ms.locfileid: "97822195"
---
# <a name="known-issues-for-apache-spark-cluster-on-hdinsight"></a>Známé problémy pro cluster Apache Spark v HDInsight

Tento dokument sleduje všechny známé problémy ve verzi Public Preview pro HDInsight Spark.  

## <a name="apache-livy-leaks-interactive-session"></a>Livy nevrácení interaktivní relace Apache
Když se [Apache Livy](https://livy.incubator.apache.org/) restartuje (z [Apache Ambari](https://ambari.apache.org/) nebo z důvodu restartování virtuálního počítače hlavnímu uzlu 0) s interaktivní relací, dojde k úniku interaktivní relace úlohy. V důsledku toho se můžou nové úlohy zablokovat v přijatém stavu.

**Zmírnění**

Problém můžete vyřešit pomocí následujícího postupu:

1. SSH do hlavnímu uzlu. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

2. Spuštěním následujícího příkazu Najděte ID aplikací interaktivních úloh spouštěných prostřednictvím Livy.

   ```bash
   yarn application –list
   ```

    Výchozí názvy úloh budou Livy, pokud byly úlohy spuštěny s relací Livy Interactive bez zadání explicitních názvů. Pro relaci Livy spuštěnou v [Jupyter notebook](https://jupyter.org/)začíná název úlohy `remotesparkmagics_*` .

3. Spuštěním následujícího příkazu tyto úlohy ukončete.

   ```bash
   yarn application –kill <Application ID>
   ```

Nové úlohy začínají běžet.

## <a name="spark-history-server-not-started"></a>Server historie Spark není spuštěný.
Po vytvoření clusteru se server historie Sparku nespustí automaticky.  

**Zmírnění**

Ručně spusťte server historie z Ambari.

## <a name="permission-issue-in-spark-log-directory"></a>Problém s oprávněním v adresáři protokolu Spark
Při odesílání úlohy pomocí Spark-Submit hdiuser získá následující chybu:

```
java.io.FileNotFoundException: /var/log/spark/sparkdriver_hdiuser.log (Permission denied)
```

A nezapisuje se žádný protokol ovladače.

**Zmírnění**

1. Přidejte hdiuser do skupiny Hadoop.
2. Po vytvoření clusteru poskytněte/var/log/Spark oprávnění 777.
3. Aktualizujte umístění protokolu Spark pomocí Ambari, aby bylo v adresáři s oprávněními 777.  
4. Spusťte Spark-Submit jako sudo.  

## <a name="spark-phoenix-connector-is-not-supported"></a>Konektor Spark-Phoenix není podporovaný.

Clustery HDInsight Spark nepodporují konektor Spark-Phoenix.

**Zmírnění**

Místo toho je nutné použít Spark-HBase konektor. Pokyny najdete v tématu [použití konektoru Spark-HBase](https://web.archive.org/web/20190112153146/https://blogs.msdn.microsoft.com/azuredatalake/2016/07/25/hdinsight-how-to-use-spark-hbase-connector/).

## <a name="issues-related-to-jupyter-notebooks"></a>Problémy související s Jupyter poznámkovým blokem

Níže jsou uvedené některé známé problémy související s Jupyter poznámkami.

### <a name="notebooks-with-non-ascii-characters-in-filenames"></a>Poznámkové bloky s jinými znaky než ASCII v názvech souborů

V Jupyter Notebookch názvech souborů nepoužívejte jiné znaky než ASCII. Pokud se pokusíte nahrát soubor prostřednictvím uživatelského rozhraní Jupyter, které obsahuje název souboru, který není ASCII, dojde k chybě bez jakékoli chybové zprávy. Jupyter neumožňuje odeslat soubor, ale nevyvolává viditelnou chybu.

### <a name="error-while-loading-notebooks-of-larger-sizes"></a>Při načítání poznámkových bloků větších velikostí došlo k chybě.

**`Error loading notebook`** Při načítání poznámkových bloků, které mají větší velikost, se může zobrazit chyba.  

**Zmírnění**

Pokud se zobrazí tato chyba, neznamená to, že vaše data jsou poškozená nebo ztracená.  Vaše poznámkové bloky jsou pořád na disku v `/var/lib/jupyter` a k nim můžete přistupovat přes SSH. Další informace najdete v tématu [Použití SSH se službou HDInsight](../hdinsight-hadoop-linux-use-ssh-unix.md).

Po připojení ke clusteru pomocí SSH můžete zkopírovat poznámkové bloky z clusteru do místního počítače (pomocí spojovacího bodu služby nebo WinSCP), aby se zabránilo ztrátě jakýchkoli důležitých dat v poznámkovém bloku. Pak můžete pomocí SSH tunelu do svého hlavnímu uzluu na portu 8001 získat přístup k Jupyter bez průchodu bránou.  Odtud můžete vymazat výstup poznámkového bloku a znovu ho uložit, aby se minimalizovala velikost poznámkového bloku.

Aby nedošlo k této chybě v budoucnu, je nutné dodržovat některé osvědčené postupy:

* Je důležité zachovat malou velikost poznámkového bloku. Veškerý výstup úloh Sparku, který se pošle zpátky do Jupyter, je uložený v poznámkovém bloku.  Osvědčeným postupem je Jupyter obecně, abyste se vyhnuli spouštění `.collect()` ve velkých RDD nebo datasnímcích. místo toho, pokud chcete prohlížet obsah RDD, zvažte spuštění nebo, aby `.take()` `.sample()` váš výstup nezískal příliš velký.
* Když uložíte Poznámkový blok, zrušte zaškrtnutí všech výstupních buněk, abyste snížili velikost.

### <a name="notebook-initial-startup-takes-longer-than-expected"></a>Počáteční spuštění poznámkového bloku trvá déle, než bylo očekáváno

První příkaz kódu v Jupyter Notebook pomocí Spark Magic může trvat déle než minutu.  

**Požadované**

K tomu dochází, protože při spuštění první buňky kódu. Na pozadí se zahájí nastavení konfigurace relace a kontexty Spark, SQL a podregistr. Po nastavení těchto kontextů se spustí první příkaz, který dává dojem, že dokončení příkazu trvalo dlouhou dobu.

### <a name="jupyter-notebook-timeout-in-creating-the-session"></a>Jupyter Notebook časový limit při vytváření relace

Pokud má cluster Spark dostatek prostředků, při pokusu o vytvoření relace vyprší časový limit pro jádra Spark a PySpark v Jupyter Notebook.

**Hrozeb**

1. Uvolněte některé prostředky v clusteru Spark pomocí:

   * Ukončete jiné poznámkové bloky Spark tak, že přejdete do nabídky Zavřít a zastavit nebo v Průzkumníku poznámkového bloku kliknete na tlačítko vypnout.
   * Zastavování jiných aplikací Spark z PŘÍZe.

2. Restartujte Poznámkový blok, který jste se pokusili spustit. K dispozici je dostatek prostředků, aby bylo možné nyní vytvořit relaci.

## <a name="see-also"></a>Viz také

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástrojů HDInsight pro IntelliJ NÁPADu při vzdáleném ladění aplikací Apache Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru serveru Apache Spark v HDInsight](apache-spark-job-debugging.md)
