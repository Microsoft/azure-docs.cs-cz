---
title: 'Azure Toolkit: Vzdálené ladění aplikací Apache Spark – Azure HDInsight'
description: Naučte se používat nástroje HDInsight v Sadě nástrojů Azure pro IntelliJ ke vzdálenému ladění aplikací Spark, které běží na clusterech HDInsight prostřednictvím sítě VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/28/2017
ms.openlocfilehash: 393356bd8604f6e7622acd778817681aad31f1f9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76935025"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Použití sady Azure Toolkit for IntelliJ k vzdálenému ladění aplikací Apache Spark v HDInsightu prostřednictvím sítě VPN

Doporučujeme ladit aplikace [Apache Spark](https://spark.apache.org/) na dálku přes SSH. Pokyny najdete v [tématu Vzdálené ladění aplikací Apache Spark v clusteru HDInsight pomocí Sady nástrojů Azure pro IntelliJ až SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Tento článek obsahuje podrobné pokyny, jak používat nástroje HDInsight v sadě nástrojů Azure pro IntelliJ k odeslání úlohy Spark v clusteru HDInsight Spark a pak ji vzdáleně ladit z vašeho stolního počítače. Chcete-li tyto úkoly dokončit, je nutné provést následující kroky vysoké úrovně:

1. Vytvořte virtuální síť Azure od lokality k webu nebo na místo. Kroky v tomto dokumentu předpokládají, že používáte síť mezi lokalitami.
1. Vytvořte cluster Spark ve službě HDInsight, který je součástí virtuální sítě mezi lokalitami.
1. Ověřte připojení mezi hlavním uzlem clusteru a plochou.
1. Vytvořte aplikaci Scala v Aplikaci IntelliJ IDEA a pak ji nakonfigurujte pro vzdálené ladění.
1. Spusťte a laděte aplikaci.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Další informace najdete [v tématu Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Cluster Apache Spark v HDInsightu**. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development kit**. Můžete jej nainstalovat z [webu oracle](https://aka.ms/azure-jdks).
* **IntelliJ IDEA**. Tento článek používá verzi 2017.1. Můžete jej nainstalovat z [webových stránek JetBrains](https://www.jetbrains.com/idea/download/).
* **Nástroje HDInsight v sadě nástrojů Azure pro IntelliJ**. Nástroje HDInsight pro IntelliJ jsou k dispozici jako součást sady Nástrojů Azure pro IntelliJ. Pokyny k instalaci sady Azure Toolkit najdete [v tématu Instalace sady Nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Přihlaste se ke svému předplatnému Azure z IntelliJ IDEA**. Postupujte podle pokynů v [části Azure Toolkit pro IntelliJ k vytvoření aplikací Apache Spark pro cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Řešení výjimky**. Při spuštění aplikace Spark Scala pro vzdálené ladění v počítači se systémem Windows se může stát výjimka. Tato výjimka je vysvětlena v [SPARK-2356](https://issues.apache.org/jira/browse/SPARK-2356) a dochází z důvodu chybějícího souboru WinUtils.exe v systému Windows. Chcete-li tuto chybu vyřešit, je nutné stáhnout soubor [Winutils.exe](https://github.com/steveloughran/winutils) do umístění, například **C:\WinUtils\bin**. Přidejte **proměnnou prostředí HADOOP_HOME** a nastavte hodnotu proměnné na **hodnotu C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: Vytvoření virtuální sítě Azure

Podle pokynů z následujících odkazů vytvořte virtuální síť Azure a ověřte připojení mezi stolním počítačem a virtuální sítí:

* [Vytvoření virtuální sítě s připojením VPN mezi lokalitami pomocí portálu Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Vytvoření virtuální sítě s připojením VPN mezi lokalitami pomocí Prostředí PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurace připojení z bodu na místo k virtuální síti pomocí prostředí PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Vytvoření clusteru HDInsight Spark

Doporučujeme také vytvořit cluster Apache Spark v Azure HDInsight, který je součástí virtuální sítě Azure, kterou jste vytvořili. Použijte informace dostupné v [clusterech založených na Linuxu v HDInsightu](../hdinsight-hadoop-provision-linux-clusters.md). Jako součást volitelné konfigurace vyberte virtuální síť Azure, kterou jste vytvořili v předchozím kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: Ověření připojení mezi hlavním uzlem clusteru a plochou

1. Získejte IP adresu hlavního uzlu. Otevřete umulovní hostova. V okně clusteru vyberte **řídicí panel**.

    ![Vybrat řídicí panel v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. V uzu Ambari vyberte **možnost Hostitelé**.

    ![Vybrat hostitele v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Zobrazí se seznam uzly vedoucí, uzly pracovníka a zookeeper uzly. Hlavní uzly mají předponu **hn***. Vyberte první hlavní uzel.

    ![Najít hlavní uzel v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. Z podokna **Souhrn** v dolní části stránky, která se otevře, zkopírujte **adresu IP** hlavního uzlu a **název hostitele**.

    ![Najít IP adresu v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Přidejte IP adresu a název hostitele hlavního uzlu do souboru **hosts** v počítači, kde chcete spustit, a vzdáleně laděte úlohu Spark. To umožňuje komunikovat s hlavním uzlem pomocí IP adresy, stejně jako název hostitele.

   a. Otevřete soubor poznámkového bloku se zvýšenými oprávněními. V nabídce **Soubor** vyberte **Otevřít**a najděte umístění souboru hosts. V počítači se systémem Windows je umístění **C:\Windows\System32\Drivers\etc\hosts**.

   b. Do souboru **hosts** přidejte následující informace:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Z počítače, který jste připojili k virtuální síti Azure, která používá cluster HDInsight, ověřte, zda můžete ping hlavní uzly pomocí IP adresy, stejně jako název hostitele.

1. Pomocí SSH se můžete připojit k hlavnímu uzlu clusteru podle pokynů v části [Připojit ke clusteru HDInsight pomocí SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Z hlavního uzlu clusteru příkazem ping na ip adresu stolního počítače. Otestujte připojení k oběma adresám IP přiřazeným počítači:

    - Jeden pro připojení k síti
    - Jeden pro virtuální síť Azure

1. Opakujte kroky pro druhý hlavní uzel.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Vytvoření aplikace Apache Spark Scala pomocí nástrojů HDInsight v Azure Toolkit pro IntelliJ a nakonfigurujte ji pro vzdálené ladění

1. Otevřete IntelliJ IDEA a vytvořte nový projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky:

    ![Vyberte novou šablonu projektu v aplikaci IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Vyberte **HDInsight** > **Spark na HDInsight (Scala)**.

    b. Vyberte **další**.
1. V dalším dialogovém okně **Nový projekt** postupujte takto a pak vyberte **Dokončit**:

    - Zadejte název a umístění projektu.

    - V rozevíracím seznamu **Project SDK** (SDK projektu) vyberte **Java 1.8** pro cluster Spark 2.x, nebo vyberte **Java 1.7** pro cluster Spark 1.x.

    - V rozevíracím seznamu **verzí Spark** integruje Průvodce vytvořením projektu Scala správnou verzi pro Spark SDK a Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Výběr verze sady SDK a Spark projektu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Projekt Spark automaticky vytvoří artefakt pro vás. Chcete-li artefakt zobrazit, postupujte takto:

    a. V nabídce **File** (Soubor) vyberte **Project Structure** (Struktura projektu).

    b. V dialogovém okně **Struktura projektu** vyberte **artefakty,** chcete-li zobrazit výchozí artefakt, který je vytvořen. Můžete také vytvořit svůj vlastní artefakt výběrem**+** znaménko plus ( ).

   ![IntelliJ IDEA artefakty vytvořit jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Přidejte do projektu knihovny. Chcete-li přidat knihovnu, postupujte takto:

    a. Klepněte pravým tlačítkem myši na název projektu ve stromu projektu a potom vyberte **příkaz Otevřít nastavení modulu**.

    b. V dialogovém okně **Struktura projektu** vyberte**+** **Knihovny**, vyberte symbol ( ) a pak vyberte Z **Maven**.

    ![Knihovna ke stažení IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. V dialogovém **okně Stáhnout knihovnu z úložiště Maven** vyhledejte a přidejte následující knihovny:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. `yarn-site.xml` Zkopírujte `core-site.xml` a z hlavního uzlu clusteru a přidejte je do projektu. Ke kopírování souborů použijte následující příkazy. [Cygwin](https://cygwin.com/install.html) můžete použít ke `scp` spuštění následujících příkazů ke kopírování souborů z hlavníu uzlu clusteru:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Vzhledem k tomu, že jsme již přidali IP adresu hlavního uzlu `scp` clusteru a názvy hostitelů pro soubor hosts na ploše, můžeme použít příkazy následujícím způsobem:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Chcete-li tyto soubory přidat do projektu, zkopírujte je do složky **/src** ve stromu projektu, například `<your project directory>\src`.

1. Chcete-li provést následující změny, `core-site.xml` aktualizujte soubor:

   a. Nahraďte šifrovaný klíč. Soubor `core-site.xml` obsahuje šifrovaný klíč k účtu úložiště přidruženému ke clusteru. V `core-site.xml` souboru, který jste přidali do projektu, nahraďte šifrovaný klíč skutečným klíčem úložiště přidruženým k výchozímu účtu úložiště. Další informace naleznete v [tématu Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Odeberte z `core-site.xml`následujících položek následující položky z :

    ```xml
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
    ```

   c. Uložte soubor.

1. Přidejte hlavní třídu pro vaši aplikaci. V **Průzkumníku projektu**klepněte pravým tlačítkem myši na **src**, přejděte na **Nový**a vyberte **třídu Scala**.

    ![IntelliJ IDEA Vyberte hlavní třídu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. V dialogovém okně **Vytvořit novou třídu Scaly** zadejte název, vpoli **Typ** vyberte **Objekt** a pak vyberte **OK**.

    ![IntelliJ IDEA Vytvoření nové třídy Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. Do `MyClusterAppMain.scala` souboru vložte následující kód. Tento kód vytvoří kontext Spark `executeJob` a `SparkSample` otevře metodu z objektu.

    ```scala
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
    ```

1. Opakováním kroků 8 a 9 přidejte `*SparkSample`nový objekt Scala s názvem . Do této třídy přidejte následující kód. Tento kód čte data z HVAC.csv (k dispozici ve všech clusterech HDInsight Spark). Načte řádky, které mají pouze jednu číslici v sedmém sloupci v souboru CSV a pak zapíše výstup **/HVACOut** pod výchozí kontejner úložiště pro cluster.

    ```scala
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
    ```

1. Opakováním kroků 8 a 9 `RemoteClusterDebugging`přidejte novou třídu nazvanou . Tato třída implementuje rozhraní testování Spark, který se používá k ladění aplikací. Do `RemoteClusterDebugging` třídy přidejte následující kód:

    ```scala
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
    ```

     Existuje několik důležitých věcí, které je třeba poznamenat:

      * Pro `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, ujistěte se, že sestavení Spark JAR je k dispozici v úložišti clusteru na zadané cestě.
      * V `setJars`aplikaci zadejte umístění, kde je vytvořen artefakt JAR. Obvykle je `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.

1. Ve`*RemoteClusterDebugging` třídě klepněte pravým `test` tlačítkem myši na klíčové slovo a vyberte **příkaz Vytvořit konfiguraci ladění vzdáleného clusteru**.

    ![IntelliJ IDEA Vytvoření vzdálené konfigurace](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. V dialogovém okně **Vytvořit konfiguraci** vzdáleného clusteru Ladění zadejte název konfigurace a pak vyberte **typ test** jako název **testu**. Všechny ostatní hodnoty ponechejte jako výchozí nastavení. Vyberte **Apply** (Použít) a pak vyberte **OK**.

    ![Vytvořit konfiguraci vzdáleného clusteru ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. Nyní byste měli vidět v rozevíracím seznamu Konfigurace **vzdáleného spuštění** v řádku nabídek.

    ![IntelliJ Rozevírací seznam vzdáleného spuštění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5: Spuštění aplikace v režimu ladění

1. V projektu IntelliJ IDEA `SparkSample.scala` otevřete a vytvořte zarážku vedle . `val rdd1` V místní nabídce **Vytvořit zarážku vyberte** **řádek ve funkci executeJob**.

    ![IntelliJ IDEA Přidání zarážky](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Chcete-li aplikaci spustit, vyberte tlačítko **Ladění spustit** vedle rozevíracího seznamu Konfigurace **vzdáleného spuštění.**

    ![IntelliJ IDEA Vyberte tlačítko Ladění spustit](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Když spuštění programu dosáhne zarážky, zobrazí se karta **Debugger** v dolním podokně.

    ![IntelliJ IDEA Zobrazení karty Debugger](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Chcete-li přidat hodinky, vyberte ikonu (**+**).

    ![IntelliJ ladění-add-watch-proměnná](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    V tomto příkladu se aplikace `rdd1` přerušila před vytvořením proměnné. Pomocí těchto hodinek můžeme vidět prvních pět `rdd`řádků v proměnné . Vyberte **Enter**.

    ![IntelliJ Spuštění programu v režimu ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Co vidíte v předchozí bitové kopii je, že za běhu, můžete dotaz terabajty dat a ladění, jak vaše aplikace postupuje. Například ve výstupu zobrazeném na předchozím obrázku můžete vidět, že první řádek výstupu je záhlaví. Na základě tohoto výstupu můžete upravit kód aplikace a v případě potřeby přeskočit řádek záhlaví.

1. Nyní můžete vybrat ikonu **Pokračovat v programu,** abyste mohli pokračovat ve spuštění aplikace.

    ![IntelliJ IDEA Vybrat pokračovat v programu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Pokud aplikace dokončí úspěšně, měli byste vidět výstup jako následující:

    ![Výstup ladicího konzoly IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka

* Vytvoření projektu Scala (video): [Vytvoření aplikací Apache Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [Pomocí sady Azure Toolkit pro IntelliJ můžete vzdáleně ladit aplikace Apache Spark v clusteru HDInsight.](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: Provádění interaktivní analýzy dat pomocí Spark v HDInsightu s nástroji BI](apache-spark-use-bi-tools.md)
* [Apache Spark se strojovým učením: Pomocí Spark v HDInsightu můžete analyzovat teplotu budov pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s machine learningem: Využijte Spark v HDInsightu k předvídání výsledků kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webových stránek pomocí Apache Spark v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Spouštění úloh na dálku v clusteru Apache Spark pomocí Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití sady Nástrojů Azure pro IntelliJ k vytváření aplikací Apache Spark pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit for IntelliJ k vzdálenému ladění aplikací Apache Spark prostřednictvím SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Vytváření aplikací Apache Spark pomocí nástrojů HDInsight v sadě nástrojů Azure pro Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Používejte notebooky Apache Zeppelin s clusterem Apache Spark v HDInsightu](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použijte externí balíčky s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh, které běží v clusteru Apache Spark v HDInsightu](apache-spark-job-debugging.md)
