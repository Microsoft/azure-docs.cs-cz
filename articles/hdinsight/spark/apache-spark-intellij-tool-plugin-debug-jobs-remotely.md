---
title: 'Sada Azure Toolkit pro IntelliJ: ladění aplikací vzdáleně v HDInsight Spark '
description: Další informace o použití nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ pro vzdálené ladění aplikací Spark, které poběží na clusterech HDInsight prostřednictvím sítě VPN.
services: hdinsight
author: jasonwhowell
ms.author: jasonh
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 1a7fcc11bf1b98d5feaeae07264e556acce2b14b
ms.sourcegitcommit: 1b561b77aa080416b094b6f41fce5b6a4721e7d5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2018
ms.locfileid: "45736069"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Ladění aplikací Spark v HDInsight pomocí sítě VPN vzdáleně pomocí sady Azure Toolkit pro IntelliJ

Doporučujeme, abyste ladění aplikací spark vzdáleně přes SSH. Pokyny najdete v tématu [vzdáleně ladit aplikace Spark v clusteru služby HDInsight pomocí sady Azure Toolkit pro IntelliJ přes SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Tento článek obsahuje podrobné pokyny o tom, jak pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ odeslání úlohy Spark na cluster HDInsight Spark a pak ho vzdáleně ladit ze stolního počítače. K dokončení těchto úloh, je třeba provést následující postup vysoké úrovně:

1. Vytvořte site-to-site nebo point-to-site virtuální síť Azure. Kroky v tomto dokumentu předpokládají, že používáte síť site-to-site.
1. Vytvoření clusteru Spark v HDInsight, který je součástí virtuální sítě site-to-site.
1. Zkontrolujte připojení k hlavnímu uzlu clusteru a pracovní plochy.
1. Vytvoření aplikace v jazyce Scala v IntelliJ IDEA a potom ho nakonfigurovat pro vzdálené ladění.
1. Spuštění a ladění aplikace.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Další informace najdete v tématu [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Cluster Apache Spark v HDInsight**. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development kit**. Můžete ho nainstalovat [webu Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **IntelliJ IDEA**. Tento článek používá verzi 2017.1. Můžete ho nainstalovat [JetBrains webu](https://www.jetbrains.com/idea/download/).
* **Nástroje HDInsight v sadě Azure Toolkit pro IntelliJ**. Nástroje HDInsight pro IntelliJ jsou k dispozici jako součást sady Azure Toolkit pro IntelliJ. Pokyny k instalaci sady Azure Toolkit najdete v tématu [instalace sady Azure Toolkit pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Přihlaste se ke svému předplatnému Azure z IntelliJ IDEA**. Postupujte podle pokynů v [použití sady Azure Toolkit pro IntelliJ umožňující vytváření aplikací pro HDInsight cluster Spark](apache-spark-intellij-tool-plugin.md).
* **Alternativní řešení výjimek**. Při spouštění aplikace Spark Scala pro vzdálené ladění na počítači Windows, může se zobrazit výjimka. Tato výjimka je podrobně [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) a dochází kvůli chybějící soubor WinUtils.exe ve Windows. Chcete-li tuto chybu vyřešit, je třeba [stáhnout spustitelný soubor](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako **C:\WinUtils\bin**. Přidat **HADOOP_HOME** proměnné prostředí a pak nastavte hodnotu proměnné, která **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: Vytvoření virtuální sítě Azure
Postupujte podle pokynů z následujících odkazů můžete vytvořit virtuální síť Azure a pak ověřte připojení mezi počítači a virtuální sítě:

* [Vytvoření virtuální sítě s připojením VPN site-to-site pomocí webu Azure portal](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Vytvoření virtuální sítě s připojením VPN site-to-site pomocí Powershellu](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurace připojení typu point-to-site k virtuální síti pomocí Powershellu](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Vytvoření clusteru HDInsight Spark
Doporučujeme také vytvořit cluster Apache Spark v Azure HDInsight, který je součástí virtuální síť Azure, kterou jste vytvořili. Informace k dispozici v [založených na Linuxu Vytvoření clusterů HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Jako součást volitelné konfigurace vyberte virtuální síť Azure, kterou jste vytvořili v předchozím kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: Ověření připojení mezi hlavního uzlu clusteru a pracovní plochy
1. Získejte IP adresu hlavního uzlu. Otevřete uživatelské rozhraní Ambari clusteru. Z okna clusteru vyberte **řídicí panel**.

    ![Vyberte řídicí panel v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
1. Z uživatelského rozhraní Ambari, vyberte **hostitele**.

    ![Vyberte hostitele v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
1. Zobrazí seznam hlavní uzly, pracovní uzly a uzly zookeeper. Hlavní uzly mají **hn*** předponu. Vyberte první hlavního uzlu.

    ![Vyhledání hlavního uzlu v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
1. Z **Souhrn** panelu v dolní části stránky, které se otevře, kopie **IP adresu** hlavního uzlu a **Hostname**.

    ![Zjistit IP adresu v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
1. Přidat IP adresu a název hostitele hlavního uzlu **hostitele** soubor na počítači, ve kterém chcete spustit a vzdáleně ladit úloha Sparku. To umožňuje komunikaci s hlavní uzel pomocí IP adresy, stejně jako název hostitele.

   a. Otevřete soubor poznámkového bloku se zvýšenými oprávněními. Z **souboru** nabídce vyberte možnost **otevřít**a pak vyhledejte umístění souboru hosts. Na počítači s Windows je umístění **C:\Windows\System32\Drivers\etc\hosts**.

   b. Přidejte následující informace, které **hostitele** souboru:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
1. Z počítače, který je připojený k virtuální síť Azure, který se používá cluster HDInsight ověřte, že příkazem ping hlavní uzly pomocí IP adresy, stejně jako název hostitele.
1. Pomocí SSH se připojte k hlavnímu uzlu clusteru, postupujte podle pokynů v [připojení ke clusteru HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Z hlavního uzlu clusteru odešlete zprávu ping IP adresu aplikace stolním počítači. Test připojení na obě IP adresy přiřazené k počítači:

    - Jeden pro připojení k síti
    - Jeden pro virtuální síť Azure

1. Opakujte kroky pro hlavní uzel.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Vytvoření aplikace Spark Scala pomocí nástrojů HDInsight v sadě Azure Toolkit pro IntelliJ a konfigurace pro vzdálené ladění
1. Otevřete IntelliJ IDEA a vytvořte nový projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky:

    ![Vyberte novou šablonu projekt v IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Vyberte **HDInsight** > **Spark on HDInsight (Scala)** (Spark v HDInsight (Scala)).

    b. Vyberte **Další**.
1. V dalším **nový projekt** dialogové okno, proveďte následující a potom vyberte **Dokončit**:

    - Zadejte název a umístění projektu.

    - V rozevíracím seznamu **Project SDK** (SDK projektu) vyberte **Java 1.8** pro cluster Spark 2.x, nebo vyberte **Java 1.7** pro cluster Spark 1.x.

    - V **verzi Sparku** rozevíracího seznamu, v Průvodci vytvořením projektu Scala integruje správná verze pro sadu SDK Spark a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Vyberte verzi sady SDK a Spark projektu.](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Artefakt Spark project automaticky vytvoří za vás. Chcete-li zobrazit artefakt, postupujte takto:

    a. V nabídce **File** (Soubor) vyberte **Project Structure** (Struktura projektu).

    b. V **strukturu projektu** dialogu **artefakty** Chcete-li zobrazit výchozí artefakt, který je vytvořen. Můžete také vytvořit vlastní artefakt vyberte znaménko plus (**+**).

   ![Vytvoření souboru JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


1. Přidání knihovny do projektu. Pokud chcete přidat do knihovny, postupujte takto:

    a. Klikněte pravým tlačítkem na název projektu ve stromové struktuře projektu a pak vyberte **otevřít modul nastavení**. 

    b. V **strukturu projektu** dialogu **knihovny**, vyberte (**+**) symbolů a pak vyberte **z Mavenu** .

    ![Přidat knihovnu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. V **knihovny stáhnout z úložiště Maven** dialogového okna, vyhledejte a přidejte následující knihovny:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
1. Kopírování `yarn-site.xml` a `core-site.xml` z clusteru hlavního uzlu a přidejte do projektu. Použijte následující příkazy pro kopírování souborů. Můžete použít [Cygwin](https://cygwin.com/install.html) ke spuštění následujících `scp` příkazy pro kopírování souborů z clusteru hlavním uzlům:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Protože jsme už přidali IP adresu hlavního uzlu clusteru a názvy hostitelů pro soubor hostitelů v klientských počítačích, můžeme použít `scp` příkazy následujícím způsobem:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Chcete-li přidat tyto soubory do projektu, zkopírujte je v části **/src** složky vašeho projektu stromu, například `<your project directory>\src`.
1. Aktualizace `core-site.xml` souboru provést následující změny:

   a. Nahraďte šifrovaný klíč. `core-site.xml` Soubor obsahuje šifrovaný klíč k účtu úložiště přidruženého clusteru. V `core-site.xml` soubor, který jste přidali do projektu, nahraďte šifrovaný klíč skutečného úložiště klíč přidružený k výchozí účet úložiště. Další informace najdete v tématu [Správa přístupových klíčů úložiště](../../storage/common/storage-account-manage.md#access-keys).

           <property>
                 <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
                 <value>access-key-associated-with-the-account</value>
           </property>
   b. Odebrat následující položky z `core-site.xml`:

           <property>
                 <name>fs.azure.account.keyprovider.hdistoragecentral.blob.core.windows.net</name>
                 <value>org.apache.hadoop.fs.azure.ShellDecryptionKeyProvider</value>
           </property>

           <property>
                 <name>fs.azure.shellkeyprovider.script</name>
                 <value>/usr/lib/python2.7/dist-packages/hdinsight_common/decrypt.sh</value>
           </property>

           <property>
                 <name>net.topology.script.file.name</name>
                 <value>/etc/hadoop/conf/topology_script.py</value>
           </property>
   c. Uložte soubor.
1. Přidejte hlavní třídu pro vaši aplikaci. Z **Project Exploreru**, klikněte pravým tlačítkem na **src**, přejděte na **nový**a pak vyberte **Scala třídy**.

    ![Vyberte hlavní třída](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
1. V **vytvořit novou třídu Scala** dialogové okno, zadejte název, vyberte **objekt** v **druh** a potom vyberte **OK**.

    ![Vytvořit novou třídu Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
1. V `MyClusterAppMain.scala` soubor, vložte následující kód. Tento kód vytvoří Sparku kontextu a otevře se `executeJob` metodu z `SparkSample` objektu.

        import org.apache.spark.{SparkConf, SparkContext}

        object SparkSampleMain {
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("SparkSample")
                                      .set("spark.hadoop.validateOutputSpecs", "false")
            val sc = new SparkContext(conf)

            SparkSample.executeJob(sc,
                                   "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
                                   "wasb:///HVACOut")
          }
        }

1. Opakujte kroky 8 a 9 k přidání nového objektu Scala volá `*SparkSample`. Přidejte následující kód do této třídy. Tento kód čte data z HVAC.csv (k dispozici na všech clusterech HDInsight Spark). Načte řádky, které mají pouze jednu číslici ve sloupci sedmého v souboru CSV a pak zapíše výstup do **/HVACOut** pod výchozí kontejner úložiště pro cluster.

        import org.apache.spark.SparkContext

        object SparkSample {
         def executeJob (sc: SparkContext, input: String, output: String): Unit = {
           val rdd = sc.textFile(input)

           //find the rows which have only one digit in the 7th column in the CSV
           val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)

           val s = sc.parallelize(rdd.take(5)).cartesian(rdd).count()
           println(s)

           rdd1.saveAsTextFile(output)
           //rdd1.collect().foreach(println)
         }
        }
1. Opakujte kroky 8 a 9 přidejte novou třídu s názvem `RemoteClusterDebugging`. Tato třída implementuje rozhraní pro testování Spark, který slouží k ladění aplikace. Přidejte následující kód, který `RemoteClusterDebugging` třídy:

        import org.apache.spark.{SparkConf, SparkContext}
        import org.scalatest.FunSuite

        class RemoteClusterDebugging extends FunSuite {

         test("Remote run") {
           val conf = new SparkConf().setAppName("SparkSample")
                                     .setMaster("yarn-client")
                                     .set("spark.yarn.am.extraJavaOptions", "-Dhdp.version=2.4")
                                     .set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")
                                     .setJars(Seq("""C:\workspace\IdeaProjects\MyClusterApp\out\artifacts\MyClusterApp_DefaultArtifact\default_artifact.jar"""))
                                     .set("spark.hadoop.validateOutputSpecs", "false")
           val sc = new SparkContext(conf)

           SparkSample.executeJob(sc,
             "wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv",
             "wasb:///HVACOut")
         }
        }

     Existuje několik důležitých věcí, mějte na paměti, které:

      * Pro `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, ujistěte se, že sestavení Spark JAR je k dispozici v úložišti clusteru v zadané cestě.
      * Pro `setJars`, zadejte umístění, ve kterém je vytvořený soubor JAR artefaktů. Obvykle je `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
1. V`*RemoteClusterDebugging` třídy, klepněte pravým tlačítkem myši `test` – klíčové slovo a pak vyberte **vytvořit konfiguraci RemoteClusterDebugging**.

    ![Vytvoření konfigurace vzdáleného](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. V **vytvořit konfiguraci RemoteClusterDebugging** dialogové okno, zadejte název pro konfiguraci a potom vyberte **testování druh** jako **název testu**. Všechny ostatní hodnoty ponechte výchozí nastavení. Vyberte **Apply** (Použít) a pak vyberte **OK**.

    ![Přidat podrobnosti o konfiguraci](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
1. Teď byste měli vidět **vzdálenému běhu** konfigurace rozevíracího seznamu v řádku nabídek.

    ![Vzdálené spuštění rozevíracího seznamu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5: Spusťte aplikaci v režimu ladění
1. V projektu pro IntelliJ IDEA, otevřete `SparkSample.scala` a vytvořila zarážku vedle `val rdd1`. V **vytvořit zarážky pro** rozbalovací nabídce vyberte možnost **řádek ve funkci executeJob**.

    ![Přidání zarážky](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
1. Chcete-li spustit aplikaci, vyberte **ladění spusťte** vedle **vzdálené spuštění** rozevíracího seznamu konfigurace.

    ![Klikněte na tlačítko Spustit ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
1. Při provádění programu dosáhne zarážky, uvidíte **ladicí program** karta v dolní části okna.

    ![Zobrazit kartu ladicího programu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
1. Chcete-li přidat sledování, (**+**) ikonu.

    ![Vyberte ikonu +](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    V tomto příkladu aplikace se podařilo přerušit před proměnnou `rdd1` byl vytvořen. Pomocí tohoto sledování, můžeme vidět prvních pět řádků v proměnné `rdd`. Stiskněte **Enter**.

    ![Spusťte program v režimu ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Viz předchozí obrázek je, že za běhu, může dotazovat terabajty dat a ladění jak postupuje vaší aplikace. Ve výstupu vidíte na předchozím obrázku například vidíte, že první řádek výstupu je záhlaví. Založené na tento výstup, můžete upravit kód pro přeskočení řádku záhlaví, v případě potřeby vaší aplikace.
1. Teď můžete vybrat **programu pokračovat** ikonu a pokračujte ve vaší aplikaci spustit.

    ![Vyberte Program pokračovat](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
1. Pokud se aplikace dokončí úspěšně, byste měli vidět výstup podobný tomuto:

    ![Výstup konzoly](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvořit projekt Scala (video): [vytvoření aplikací Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně v clusteru služby HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Vytvoření aplikací Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse k vytvoření aplikací Spark](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh, které běží v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)
