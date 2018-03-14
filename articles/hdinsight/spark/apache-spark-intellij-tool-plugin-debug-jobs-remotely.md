---
title: "Azure nástrojů pro IntelliJ: ladění aplikace vzdáleně v HDInsight Spark | Microsoft Docs"
description: "Další informace o použití nástrojů HDInsight v Azure nástrojů pro IntelliJ pro vzdálené ladění aplikací Spark, které běží na clustery HDInsight prostřednictvím sítě VPN."
services: hdinsight
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 55fb454f-c7dc-46de-a978-e242e9a94f4c
ms.service: hdinsight
ms.custom: hdinsightactive
ms.workload: big-data
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/28/2017
ms.author: nitinme
ms.openlocfilehash: 315a879368e522cc17eeff7be766f0c8a38d470b
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2018
---
# <a name="use-azure-toolkit-for-intellij-to-debug-spark-applications-remotely-in-hdinsight-through-vpn"></a>Ladění aplikací Spark v HDInsight prostřednictvím sítě VPN vzdáleně pomocí nástrojů Azure pro IntelliJ

Doporučujeme, abyste ladění aplikací spark vzdáleně přes SSH. Pokyny najdete v tématu [vzdálené ladění aplikací Spark v clusteru HDInsight pomocí sady Azure Toolkit pro IntelliJ prostřednictvím SSH](https://docs.microsoft.com/azure/hdinsight/hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh).

Tento článek obsahuje podrobné pokyny o tom, jak používat nástroje HDInsight v Azure nástrojů pro IntelliJ odeslat úlohu Spark v HDInsight Spark clusteru a pak ho vzdáleně ladit ze stolního počítače. K dokončení těchto úloh, je nutné provést následující postup vysoké úrovně:

1. Vytvořte site-to-site nebo point-to-site virtuální síť Azure. Kroky v tomto dokumentu předpokládají, že používáte síť site-to-site.
2. Vytvořte Spark cluster v HDInsight, který je součástí virtuální sítě site-to-site.
3. Zkontrolujte připojení mezi hlavního uzlu clusteru a pracovní ploše.
4. Vytvoření aplikace Scala v IntelliJ IDEA a pak ho nakonfigurovat pro vzdálené ladění.
5. Spuštění a ladění aplikace.

## <a name="prerequisites"></a>Požadavky
* **Předplatné Azure**. Další informace najdete v tématu [získat bezplatnou zkušební verzi Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Cluster Apache Spark v HDInsight**. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* **Oracle Java development kit**. Můžete nainstalovat z [Oracle webu](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* **IntelliJ IDEA**. Tento článek používá verzi 2017.1. Můžete nainstalovat z [JetBrains webu](https://www.jetbrains.com/idea/download/).
* **Nástroje HDInsight v Azure nástrojů pro IntelliJ**. Nástroje HDInsight pro IntelliJ jsou k dispozici jako součást nástrojů Azure pro IntelliJ. Pokyny k instalaci nástrojů Azure najdete v tématu [instalaci nástrojů Azure pro IntelliJ](https://docs.microsoft.com/java/azure/intellij/azure-toolkit-for-intellij-installation).
* **Přihlaste se k předplatnému Azure z IntelliJ IDEA**. Postupujte podle pokynů v [pomocí nástrojů Azure pro IntelliJ k vytvoření aplikací Spark pro cluster služby HDInsight](apache-spark-intellij-tool-plugin.md).
* **Alternativní řešení výjimka**. Při spouštění aplikací Spark Scala pro vzdálené ladění na počítači se systémem Windows, může získat výjimku. Tato výjimka je vysvětleno v [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356) a dochází z důvodu chybějící WinUtils.exe souboru v systému Windows. Chcete-li tuto chybu vyřešit, je potřeba [stáhnout spustitelný soubor](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako **C:\WinUtils\bin**. Přidat **HADOOP_HOME** proměnné prostředí a pak nastavte hodnotu proměnné na **C\WinUtils**.

## <a name="step-1-create-an-azure-virtual-network"></a>Krok 1: Vytvoření virtuální sítě Azure
Postupujte podle pokynů z následujících odkazů můžete vytvořit virtuální síť Azure a pak ověřte připojení mezi počítač a virtuální sítě:

* [Vytvoření virtuální sítě s připojením VPN typu site-to-site pomocí portálu Azure](../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)
* [Vytvoření virtuální sítě s připojením VPN typu site-to-site pomocí prostředí PowerShell](../../vpn-gateway/vpn-gateway-create-site-to-site-rm-powershell.md)
* [Konfigurace připojení typu point-to-site k virtuální síti pomocí prostředí PowerShell](../../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md)

## <a name="step-2-create-an-hdinsight-spark-cluster"></a>Krok 2: Vytvoření clusteru HDInsight Spark
Doporučujeme také vytvářet cluster Apache Spark v Azure HDInsight, který je součástí Azure virtuální síť, kterou jste vytvořili. Použijte informace k dispozici v [vytvořit systémem Linux clusterů v HDInsight](../hdinsight-hadoop-provision-linux-clusters.md). Jako součást volitelné konfigurace vyberte virtuální síť Azure, kterou jste vytvořili v předchozím kroku.

## <a name="step-3-verify-the-connectivity-between-the-cluster-head-node-and-your-desktop"></a>Krok 3: Ověření připojení mezi hlavního uzlu clusteru a pracovní plochy
1. Získáte IP adresu hlavního uzlu. Otevřete uživatelské rozhraní Ambari pro cluster. V okně clusteru vyberte **řídicí panel**.

    ![Vyberte řídicí panel v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/launch-ambari-ui.png)
2. Uživatelské rozhraní Ambari, vyberte **hostitele**.

    ![Vyberte hostitele v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/ambari-hosts.png)
3. Zobrazí seznam hlavních uzlech, pracovní uzly a uzly zookeeper. Hlavních uzlech mají **hn*** předponu. Vyberte první hlavního uzlu.

    ![Najít hlavního uzlu v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/cluster-headnodes.png)
4. Z **Souhrn** panelu v dolní části stránky, které se otevře, kopie **IP adresu** hlavního uzlu a **Hostname**.

    ![Najít IP adresu v Ambari](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/headnode-ip-address.png)
5. Přidat IP adresu a název hostitele k hlavního uzlu **hostitele** soubor na počítači, ve které chcete spustit a vzdáleně ladit úlohy Spark. To vám umožňuje komunikovat s hlavního uzlu pomocí IP adresu, stejně jako název hostitele.

   a. Otevřete soubor poznámkového bloku se zvýšenými oprávněními. Z **soubor** nabídce vyberte možnost **otevřete**a vyhledejte umístění souboru hostitelů. Na počítači s Windows, je umístění **C:\Windows\System32\Drivers\etc\hosts**.

   b. Přidejte následující informace, které **hostitele** souboru:

           # For headnode0
           192.xxx.xx.xx hn0-nitinp
           192.xxx.xx.xx hn0-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net

           # For headnode1
           192.xxx.xx.xx hn1-nitinp
           192.xxx.xx.xx hn1-nitinp.lhwwghjkpqejawpqbwcdyp3.gx.internal.cloudapp.net
6. Z počítače, který jste připojeni k Azure virtuální síť, která se používá v clusteru HDInsight ověřte, že může odeslat hlavních uzlech příkaz ping pomocí IP adresu, stejně jako název hostitele.
7. Použití SSH se připojit k hlavnímu uzlu clusteru podle pokynů v [připojit ke clusteru HDInsight pomocí protokolu SSH](../hdinsight-hadoop-linux-use-ssh-unix.md). Z hlavního uzlu clusteru pomocí příkazu ping adresu IP stolní počítač. Test připojení na obě IP adresy přiřazené k počítači:

    - Jeden pro síťové připojení
    - Jeden pro virtuální síť Azure

8. Opakujte kroky pro hlavní uzel.

## <a name="step-4-create-a-spark-scala-application-by-using-hdinsight-tools-in-azure-toolkit-for-intellij-and-configure-it-for-remote-debugging"></a>Krok 4: Vytvoření aplikací Spark Scala pomocí nástrojů HDInsight pro IntelliJ v nástrojů Azure a nakonfigurovat ji pro vzdálené ladění
1. Otevřete IntelliJ IDEA a vytvořte nový projekt. V **nový projekt** dialogové okno pole, postupujte takto:

    ![Vyberte novou šablonu, projektu v IntelliJ IDEA](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-hdi-scala-app.png)

    a. Vyberte **HDInsight** > **Spark v HDInsight (Scala)**.

    b. Vyberte **Next** (Další).
2. V dalším **nový projekt** dialogové okno, proveďte následující a potom vyberte **Dokončit**:

    - Zadejte název projektu a umístění.

    - V **SDK projektu** rozevíracího seznamu vyberte **Java 1.8** pro Spark 2.x cluster nebo vyberte **Java 1.7** 1.x clusteru Spark.

    - V **Spark verze** rozevíracího seznamu, Průvodce vytvořením projektu Scala integruje správnou verzi sady SDK Spark a Scala SDK. Pokud verze clusteru Spark je starší než 2.0, vyberte **Spark 1.x**. Jinak vyberte možnost **Spark2.x**. Tento příklad používá **Spark bodu 2.0.2 (Scala 2.11.8)**.
  
   ![Vyberte verzi sady SDK a Spark projektu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-scala-project-details.png)
  
3. Projekt Spark pro vás automaticky vytvoří artefakt. Chcete-li zobrazit artefaktu, postupujte takto:

    a. Z **soubor** nabídce vyberte možnost **strukturu projektu**.

    b. V **strukturu projektu** dialogové okno, vyberte **artefakty** zobrazíte výchozí artefaktu, který je vytvořen. Můžete také vytvořit vlastní artefaktů výběrem na symbol plus (**+**).

   ![Vytvoření JAR](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/default-artifact.png)


4. Do projektu přidejte knihovny. Pokud chcete přidat do knihovny, postupujte takto:

    a. Klikněte pravým tlačítkem na název projektu ve stromové struktuře projektu a potom vyberte **otevřete nastavení modulu**. 

    b. V **strukturu projektu** dialogové okno, vyberte **knihovny**, vyberte (**+**) symbolů a potom vyberte **z Maven** .

    ![Přidání knihovny](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/add-library.png)

    c. V **knihovny stáhnout z úložiště Maven** dialogové okno pole, vyhledejte a přidejte následující knihovny:

   * `org.scalatest:scalatest_2.10:2.2.1`
   * `org.apache.hadoop:hadoop-azure:2.7.1`
5. Kopírování `yarn-site.xml` a `core-site.xml` z clusteru hlavního uzlu a přidat je do projektu. Použijte následující příkazy pro kopírování souborů. Můžete použít [emulaci](https://cygwin.com/install.html) spustit následující `scp` příkazy pro kopírování souborů z clusteru hlavní uzly:

        scp <ssh user name>@<headnode IP address or host name>://etc/hadoop/conf/core-site.xml .

    Protože jsme již přidali, IP adresu hlavního uzlu clusteru a názvy hostitelů pro souboru hostitelů na ploše, můžeme použít `scp` příkazy následujícím způsobem:

        scp sshuser@hn0-nitinp:/etc/hadoop/conf/core-site.xml .
        scp sshuser@hn0-nitinp:/etc/hadoop/conf/yarn-site.xml .

    Chcete-li do projektu přidejte tyto soubory, zkopírujte je v části **/src** složky ve stromu vašeho projektu, například `<your project directory>\src`.
6. Aktualizace `core-site.xml` souboru provést následující změny:

   a. Nahraďte šifrovaný klíč. `core-site.xml` Soubor obsahuje šifrovaný klíč k účtu úložiště, který je přidružen ke clusteru. V `core-site.xml` soubor, který jste přidali do projektu zašifrovaný klíč nahraďte skutečným úložiště klíč přidružený výchozí účet úložiště. Další informace najdete v tématu [Správa přístupových klíčů úložiště](../../storage/common/storage-create-storage-account.md#manage-your-storage-account).

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
7. Přidejte hlavní třídy pro vaši aplikaci. Z **Project Exploreru**, klikněte pravým tlačítkem na **src**, přejděte na **nový**a potom vyberte **Scala třída**.

    ![Vyberte hlavní – třída](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code.png)
8. V **vytvořte novou třídu Scala** dialogové okno, zadejte název, vyberte **objekt** v **druhu** a pak vyberte **OK**.

    ![Vytvořte novou třídu Scala](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/hdi-spark-scala-code-object.png)
9. V `MyClusterAppMain.scala` souboru, vložte následující kód. Tento kód vytvoří Spark kontextu a otevře se okno `executeJob` metoda z `SparkSample` objektu.

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

10. Opakujte kroky 8 a 9 k přidání nového objektu Scala názvem `*SparkSample`. Přidejte následující kód do této třídy. Tento kód čte data z HVAC.csv (k dispozici v na všech clusterech HDInsight Spark). Načte řádky, které mají ve sloupci sedmého v souboru CSV pouze jednu číslici a pak zapíše výstup do **/HVACOut** pod výchozí kontejner úložiště pro cluster.

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
11. Opakujte kroky 8 a 9 přidat novou třídu s názvem `RemoteClusterDebugging`. Tato třída implementuje rozhraní Spark test, který slouží k ladění aplikací. Přidejte následující kód, který `RemoteClusterDebugging` třídy:

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

     Existuje několik důležitých věcí Všimněte si, které:

      * Pro `.set("spark.yarn.jar", "wasb:///hdp/apps/2.4.2.0-258/spark-assembly-1.6.1.2.4.2.0-258-hadoop2.7.1.2.4.2.0-258.jar")`, zkontrolujte, zda je sestavení Spark JAR k dispozici v úložišti clusteru v zadané cestě.
      * Pro `setJars`, zadejte umístění, kde se má vytvořit artefakt JAR. Obvykle je `<Your IntelliJ project directory>\out\<project name>_DefaultArtifact\default_artifact.jar`.
12. V`*RemoteClusterDebugging` třídy, klikněte pravým tlačítkem myši `test` – klíčové slovo a potom vyberte **vytvořit konfigurace RemoteClusterDebugging**.

    ![Vytvořit vzdálenou konfiguraci](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-remote-config.png)

13. V **vytvořit konfigurace RemoteClusterDebugging** dialogové okno, zadejte název pro konfiguraci a potom vyberte **testovací druh** jako **název testovacího**. Všechny ostatní hodnoty ponechte jako výchozí nastavení. Vyberte **použít**a potom vyberte **OK**.

    ![Přidání podrobností konfigurace](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/provide-config-value.png)
14. Teď byste měli vidět **vzdálené spuštění** rozevíracím seznamu konfigurací v panelu nabídek.

    ![Vzdálené spuštění rozevíracího seznamu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/config-run.png)

## <a name="step-5-run-the-application-in-debug-mode"></a>Krok 5: Spuštění aplikace v režimu ladění
1. Otevřete v projektu IntelliJ IDEA `SparkSample.scala` a vytvořit zarážku vedle `val rdd1`. V **vytvořit zarážek pro** rozbalovací nabídce vyberte možnost **řádku funkce executeJob**.

    ![Přidání zarážky](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/create-breakpoint.png)
2. Ke spuštění aplikace, vyberte **ladění spustit** vedle položky **vzdálené spuštění** konfigurace rozevíracího seznamu.

    ![Kliknutím na tlačítko Spustit ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-run-mode.png)
3. Při spuštění programu dosáhne zarážky, uvidíte **ladicí program** karty v dolním podokně.

    ![Zobrazit kartu ladicí program](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch.png)
4. Chcete-li přidat sledovat, vyberte (**+**) ikona.

    ![Vyberte + ikonu](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable.png)

    V tomto příkladu aplikace překročila před proměnnou `rdd1` byla vytvořena. Pomocí této sledovat uvidíme prvních pět řádky v proměnné `rdd`. Vyberte **zadejte**.

    ![Spusťte program v režimu ladění](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-add-watch-variable-value.png)

    Co se zobrazí v předchozí obrázek je, že v době běhu může dotaz terabajtů dat a ladění jak postupuje vaší aplikace. Ve výstupu viz předchozí obrázek, například uvidíte, že první řádek výstupu je záhlaví. Podle toho, tento výstup, můžete upravit kód aplikace tak, aby přeskočil řádek záhlaví, v případě potřeby.
5. Teď si můžete vybrat **obnovit Program** ikonu pokračujte aplikace spustit.

    ![Vyberte Program, obnovení](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-continue-run.png)
6. Pokud aplikace skončí úspěšně, byste měli vidět výstup takto:

    ![Výstup konzoly](./media/apache-spark-intellij-tool-plugin-debug-jobs-remotely/debug-complete.png)

## <a name="seealso"></a>Další kroky
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="demo"></a>Ukázka
* Vytvoření projektu Scala (video): [vytvoření aplikací Spark Scala](https://channel9.msdn.com/Series/AzureDataLake/Create-Spark-Applications-with-the-Azure-Toolkit-for-IntelliJ)
* Vzdálené ladění (video): [pomocí nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně v clusteru HDInsight](https://channel9.msdn.com/Series/AzureDataLake/Debug-HDInsight-Spark-Applications-with-Azure-Toolkit-for-IntelliJ)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: provádějte interaktivní analýzy dat pomocí Spark v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: používejte Spark v HDInsight pro analýzu stavební teploty pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](../hdinsight-apache-spark-custom-library-website-log-analysis.md)

### <a name="create-and-run-applications"></a>Vytvoření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](../hdinsight-apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Vytvoření aplikací Spark pro cluster služby HDInsight pomocí nástrojů Azure pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně přes SSH](apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Používat nástroje HDInsight pro vytvoření aplikací Spark v Azure nástrojů pro Eclipse](../hdinsight-apache-spark-eclipse-tool-plugin.md)
* [Použití poznámkových bloků Zeppelin s clusterem Spark v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Spark pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="manage-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh, které běží na cluster Apache Spark v HDInsight](apache-spark-job-debugging.md)
