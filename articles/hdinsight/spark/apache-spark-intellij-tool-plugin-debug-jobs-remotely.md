---
title: 'Sada Azure Toolkit: Vzdálená aplikace pro Apache Spark ladění – Azure HDInsight'
description: Naučte se používat nástroje HDInsight v Azure Toolkit for IntelliJ pro vzdálené ladění aplikací Spark, které běží na clusterech HDInsight prostřednictvím sítě VPN.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 11/28/2017
ms.openlocfilehash: 63d46298de2f480d97d25de2ec7e1ec78fbebd8d
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/26/2021
ms.locfileid: "98789459"
---
# <a name="use-azure-toolkit-for-intellij-to-debug-apache-spark-applications-remotely-in-hdinsight-through-vpn"></a>Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně ve službě HDInsight prostřednictvím sítě VPN

Doporučujeme ladit [Apache Spark](https://spark.apache.org/) aplikace vzdáleně prostřednictvím SSH. Pokyny najdete v tématu [vzdálené ladění Apache Spark aplikací v clusteru HDInsight s Azure Toolkit for IntelliJ prostřednictvím SSH](./apache-spark-intellij-tool-debug-remotely-through-ssh.md).

Tento článek poskytuje podrobné pokyny k používání nástrojů HDInsight v Azure Toolkit for IntelliJ k odeslání úlohy Sparku do clusteru HDInsight Spark a jejich vzdálené ladění z stolního počítače. K dokončení těchto úloh je nutné provést následující kroky vysoké úrovně:

1. Vytvořte virtuální síť Azure typu Site-to-site nebo Point-to-site. U kroků v tomto dokumentu se předpokládá, že používáte síť Site-to-site.
1. Vytvořte v HDInsight cluster Spark, který je součástí virtuální sítě Site-to-site.
1. Ověřte připojení mezi hlavním uzlem clusteru a vaší plochou.
1. Vytvořte v IntelliJ nápad aplikaci Scala a nakonfigurujte ji pro vzdálené ladění.
1. Spusťte a ladění aplikace.

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**. Další informace najdete v tématu [získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Cluster Apache Spark v HDInsight**. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Sada Oracle Java Development Kit**. Můžete ji nainstalovat z [webu Oracle](/azure/developer/java/fundamentals/java-jdk-long-term-support).
* **INTELLIJ nápad**. Tento článek používá verzi 2017,1. Můžete ji nainstalovat z [webu JetBrains](https://www.jetbrains.com/idea/download/).
* **Nástroje HDInsight v Azure Toolkit for IntelliJ**. Nástroje HDInsight pro IntelliJ jsou k dispozici jako součást Azure Toolkit for IntelliJ. Pokyny k instalaci sady Azure Toolkit najdete v tématu [install Azure Toolkit for IntelliJ](/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Přihlaste se k předplatnému Azure z INTELLIJ nápadu**. Postupujte podle pokynů v tématu [použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](apache-spark-intellij-tool-plugin.md).
* **Alternativní řešení výjimky**. Při spuštění aplikace Spark Scala pro vzdálené ladění v počítači se systémem Windows může dojít k výjimce. Tato výjimka je vysvětlena v [Spark-2356](https://issues.apache.org/jira/browse/SPARK-2356) a nastane v důsledku chybějícího WinUtils.exe souboru v systému Windows. Chcete-li tuto chybu vyřešit, je třeba stáhnout [Winutils.exe](https://github.com/steveloughran/winutils) do umístění, jako je například **C:\WinUtils\bin**. Přidejte **HADOOP_HOME** proměnnou prostředí a pak nastavte hodnotu proměnné na **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: vytvoření virtuální sítě Azure

Podle pokynů z následujících odkazů vytvořte virtuální síť Azure a pak ověřte připojení mezi stolním počítačem a virtuální sítí:

* [Vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí Azure Portal](../../vpn-gateway/tutorial-site-to-site-portal.md)
* [Vytvoření virtuální sítě s připojením VPN typu Site-to-site pomocí PowerShellu](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurace připojení typu Point-to-site k virtuální síti pomocí PowerShellu](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Vytvoření clusteru HDInsight Spark

Doporučujeme také vytvořit cluster Apache Spark ve službě Azure HDInsight, který je součástí virtuální sítě Azure, kterou jste vytvořili. Použijte informace dostupné v části [vytváření clusterů se systémem Linux v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Jako součást volitelné konfigurace vyberte virtuální síť Azure, kterou jste vytvořili v předchozím kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: ověření připojení mezi hlavním uzlem clusteru a vaší plochou

1. Získejte IP adresu hlavního uzlu. Otevřete uživatelské rozhraní Ambari pro cluster. V okně clusteru vyberte **řídicí panel**.

    ![Výběr řídicího panelu v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-apache-ambari.png)

1. V uživatelském rozhraní Ambari vyberte **hostitelé**.

    ![Výběr hostitelů v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/apache-ambari-hosts1.png)

1. Zobrazí se seznam hlavních uzlů, pracovních uzlů a uzlů Zookeeper. Hlavní uzly mají předponu **HN** _. Vyberte první hlavní uzel.

    ![Najděte hlavní uzel v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-cluster-headnodes.png)

1. V podokně *Souhrn* _ * v dolní části stránky, která se otevře, zkopírujte **IP adresu** hlavního uzlu a **názvu hostitele**.

    ![Vyhledání IP adresy v Apache Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address1.png)

1. Přidejte IP adresu a název hlavního uzlu do souboru **hosts** v počítači, na kterém chcete spustit, a vzdáleně ladit úlohu Spark. To vám umožní komunikovat s hlavním uzlem pomocí IP adresy a názvu hostitele.

   a. Otevřete soubor poznámkového bloku se zvýšenými oprávněními. V nabídce **soubor** vyberte možnost **otevřít** a poté vyhledejte umístění souboru Hosts. V počítači s Windows se umístění **C:\Windows\System32\Drivers\etc\hosts**.

   b. Do souboru **hostitelů** přidejte následující informace:

    ```
    # For headnode0
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    
    # For headnode1
    192.xxx.xx.xx nitinp
    192.xxx.xx.xx nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
    ```

1. Z počítače, ke kterému jste připojeni ke službě Azure Virtual Network, kterou používá cluster HDInsight, ověřte, že je možné pomocí adresy IP a názvu hostitele odeslat příkaz k otestování hlavního uzlu.

1. Pomocí SSH se připojte k hlavnímu uzlu clusteru podle pokynů v tématu [připojení ke clusteru HDInsight pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Z hlavního uzlu clusteru otestujte IP adresu stolního počítače. Otestujte připojení ke všem IP adresám, které jsou přiřazeny k počítači:

    - Jedno pro síťové připojení
    - Jedna pro virtuální síť Azure

1. Opakujte kroky pro druhý hlavní uzel.

## <a name="step-4-create-an-apache-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Vytvoření aplikace Apache Spark Scala pomocí nástrojů služby HDInsight v Azure Toolkit for IntelliJ a její konfigurace pro vzdálené ladění

1. Otevřete IntelliJ nápad a vytvořte nový projekt. V dialogovém okně **New Project** (Nový projekt) proveďte následující kroky:

    ![Výběr nové šablony projektu v IntelliJ NÁPADu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Vyberte **HDInsight**  >  **Spark v HDInsight (Scala)**.

    b. Vyberte **Další**.
1. V dalším dialogovém okně **Nový projekt** proveďte následující a potom vyberte **Dokončit**:

    - Zadejte název a umístění projektu.

    - V rozevíracím seznamu **Project SDK** (SDK projektu) vyberte **Java 1.8** pro cluster Spark 2.x, nebo vyberte **Java 1.7** pro cluster Spark 1.x.

    - V rozevíracím seznamu **verze Sparku** Průvodce vytvořením projektu Scala integruje správnou verzi sady Spark SDK a sady Scala SDK. Pokud je verze clusteru Spark nižší než 2.0, vyberte **Spark 1.x**. V opačném případě vyberte **Spark 2.x**. V tomto příkladu se používá **Spark 2.0.2 (Scala 2.11.8)**.
  
   ![Výběr projektové sady SDK a verze Sparku](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
1. Projekt Spark automaticky vytvoří artefakt za vás. Chcete-li zobrazit artefakt, postupujte následovně:

    a. V nabídce **File** (Soubor) vyberte **Project Structure** (Struktura projektu).

    b. V dialogovém okně **struktura projektu** vyberte **artefakty** a zobrazte tak výchozí artefakt, který se vytvoří. Svůj vlastní artefakt můžete vytvořit také tak, že vyberete znaménko plus ( **+** ).

   ![IntelliJy nápadů vytvoření jar](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-default-artifact.png)

1. Přidejte knihovny do projektu. Chcete-li přidat knihovnu, postupujte následovně:

    a. Ve stromové struktuře projektu klikněte pravým tlačítkem myši na název projektu a pak vyberte **Otevřít nastavení modulu**.

    b. V dialogovém okně **struktura projektu** vyberte možnost **knihovny**, vyberte **+** symbol () a pak vyberte možnost **z Maven**.

    ![Knihovna stažení NÁPADu pro IntelliJ](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-add-library.png)

    c. V dialogovém okně **stáhnout knihovnu z úložiště Maven** vyhledejte a přidejte následující knihovny:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`

1. Zkopírujte `yarn-site.xml` a `core-site.xml` z hlavního uzlu clusteru a přidejte je do projektu. K kopírování souborů použijte následující příkazy. Pomocí [Cygwin](https://cygwin.com/install.html) můžete spustit následující `scp` příkazy ke zkopírování souborů z hlavních uzlů clusteru:

    ```bash
    scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .
    ```

    Vzhledem k tomu, že jsme už přidali IP adresu hlavního uzlu clusteru a názvy hostitelů pro soubor hostitelů na ploše, můžeme `scp` tyto příkazy použít následujícím způsobem:

    ```bash
    scp sshuser@nitinp:/etc/hadoop/conf/core-site.xml .
    scp sshuser@nitinp:/etc/hadoop/conf/yarn-site.xml .
    ```

    Chcete-li přidat tyto soubory do projektu, zkopírujte je do složky **/Src** ve stromu projektu, například `<your project directory>\src` .

1. Aktualizujte `core-site.xml` soubor a proveďte následující změny:

   a. Nahraďte zašifrovaný klíč. Tento `core-site.xml` soubor obsahuje zašifrovaný klíč k účtu úložiště přidruženému ke clusteru. V `core-site.xml` souboru, který jste přidali do projektu, nahraďte zašifrovaný klíč skutečným klíčem úložiště přidruženým k výchozímu účtu úložiště. Další informace najdete v tématu [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md).

    ```xml
    <property>
            <name>fs.azure.account.key.hdistoragecentral.blob.core.windows.net</name>
            <value>access-key-associated-with-the-account</value>
    </property>
    ```

   b. Odebrat následující položky z `core-site.xml` :

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

   c. Soubor uložte.

1. Přidejte do své aplikace hlavní třídu. V **Průzkumníku projektu** klikněte pravým tlačítkem myši **na src**, přejděte na **Nový** a pak vyberte **Třída Scala**.

    ![IntelliJ nápad vybrat hlavní třídu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)

1. V dialogovém okně **vytvořit novou třídu Scala** zadejte název, vyberte **objekt** v poli **druh** a pak vyberte **OK**.

    ![IntelliJ nápad vytvořit novou třídu Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)

1. Do `MyClusterAppMain.scala` souboru vložte následující kód. Tento kód vytvoří kontext Spark a otevře `executeJob` metodu z `SparkSample` objektu.

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

1. Opakujte kroky 8 a 9 pro přidání nového objektu Scala s názvem `*SparkSample` . Do této třídy přidejte následující kód. Tento kód načte data z HVAC.csv (k dispozici ve všech clusterech HDInsight Spark). Načte řádky, které mají jenom jednu číslici v sedmé sloupci v souboru CSV, a pak zapíše výstup do **/HVACOut** do výchozího kontejneru úložiště pro cluster.

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

1. Zopakováním kroků 8 a 9 přidejte novou třídu s názvem `RemoteClusterDebugging` . Tato třída implementuje testovací rozhraní Spark, které se používá k ladění aplikací. Do třídy přidejte následující kód `RemoteClusterDebugging` :

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

     Je potřeba si uvědomit několik důležitých věcí:

      * V případě nástroje se `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")` ujistěte, že je v úložišti clusteru v zadané cestě k dispozici JAR pro sestavení Spark.
      * V `setJars` poli zadejte umístění, kde se vytvoří jar artefaktů. Obvykle je to `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar` .

1. Ve `*RemoteClusterDebugging` třídě klikněte pravým tlačítkem na `test` klíčové slovo a pak vyberte **vytvořit konfiguraci RemoteClusterDebugging**.

    ![IntelliJ nápad vytvořit vzdálenou konfiguraci](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

1. V dialogovém okně **vytvořit konfiguraci RemoteClusterDebugging** zadejte název konfigurace a pak jako **název testu** vyberte **Typ testu** . U všech ostatních hodnot ponechte výchozí nastavení. Vyberte **Apply** (Použít) a pak vyberte **OK**.

    ![Vytvořit konfiguraci RemoteClusterDebugging](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)

1. V řádku nabídek by se teď měl zobrazit rozevírací seznam konfigurace **vzdáleného spuštění** .

    ![IntelliJ rozevírací seznam vzdáleného spuštění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-config-remote-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5: spuštění aplikace v režimu ladění

1. V projektu NÁPADu IntelliJ otevřete `SparkSample.scala` a vytvořte zarážku vedle `val rdd1` . V místní nabídce **vytvořit zarážku pro** vyberte **řádek ve funkci executeJob**.

    ![IntelliJ nápad přidat zarážku](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-create-breakpoint.png)

1. Chcete-li spustit aplikaci, vyberte tlačítko **Spustit ladění** vedle rozevíracího seznamu konfigurace **vzdáleného spuštění** .

    ![IntelliJ nápad vyberte tlačítko Spustit ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode-button.png)

1. Když se program dorazí na zarážku, v dolním podokně se zobrazí karta **ladicí program** .

    ![IntelliJ nápad zobrazit kartu ladicího programu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/intellij-debugger-tab.png)

1. Chcete-li přidat kukátko, vyberte **+** ikonu ().

    ![IntelliJ Debug-Add-Watch-Variable](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    V tomto příkladu aplikace podařilo přerušit před `rdd1` vytvořením proměnné. Pomocí tohoto kukátka můžete v proměnné zobrazit prvních pět řádků `rdd` . Stiskněte klávesu **Enter**.

    ![IntelliJ spustit program v režimu ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    To, co vidíte na předchozím obrázku, je, že za běhu můžete zadávat dotazy na terabajty dat a ladit, jak vaše aplikace pokračuje. Například ve výstupu zobrazeném na předchozím obrázku vidíte, že první řádek výstupu je záhlaví. Na základě tohoto výstupu můžete upravit kód aplikace, aby v případě potřeby přeskočil řádek záhlaví.

1. Nyní můžete vybrat ikonu **pokračovat v programu** a pokračovat ve spuštění aplikace.

    ![IntelliJ nápad vybrat pokračovat v programu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-remote-run.png)

1. Pokud se aplikace úspěšně dokončí, měl by se zobrazit výstup podobný následujícímu:

    ![Výstup konzoly IntelliJ NÁPADu pro ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete-window.png)

## <a name="next-steps"></a><a name="seealso"></a>Další kroky

* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka

* Vytvořit projekt Scala (video): [vytváření Apache Sparkch Scala aplikací](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně na clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře

* [Apache Spark s BI: provádějte interaktivní analýzy dat pomocí Sparku ve službě HDInsight s nástroji BI.](apache-spark-use-bi-tools.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight můžete analyzovat teplotu budovy pomocí dat TVK.](apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark s Machine Learning: pomocí Sparku v HDInsight předpovídat výsledky kontroly potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza webového protokolu pomocí Apache Spark ve službě HDInsight](./apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací

* [Vytvoření samostatné aplikace pomocí Scala](./apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Apache Spark s využitím Apache Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření

* [Použití Azure Toolkit for IntelliJ k vytvoření Apache Spark aplikací pro cluster HDInsight](apache-spark-intellij-tool-plugin.md)
* [Použití Azure Toolkit for IntelliJ k ladění Apache Spark aplikací vzdáleně prostřednictvím SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight v Azure Toolkit for Eclipse k vytváření Apache Sparkch aplikací](./apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Apache Zeppelin s clusterem Apache Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro Jupyter Notebook v clusteru Apache Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s Jupyter poznámkovým blokem](apache-spark-jupyter-notebook-use-external-packages.md)
* [Nainstalujte do počítače Jupyter a připojte ho ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků

* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh, které běží na clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)