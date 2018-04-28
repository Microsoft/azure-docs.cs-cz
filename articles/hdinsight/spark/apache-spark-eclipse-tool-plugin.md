---
title: 'Azure nástrojů pro Eclipse: vytvoření Scala aplikací pro HDInsight Spark | Microsoft Docs'
description: Pomocí nástrojů HDInsight v Azure nástrojů pro Eclipse k vývoji aplikací Spark napsané v jazyce Scala a odesílat je na clusteru HDInsight Spark, přímo z integrovaného vývojového prostředí Eclipse.
services: hdinsight
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: f6c79550-5803-4e13-b541-e86c4abb420b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: nitinme
ms.openlocfilehash: 4e3edc74350bb31e73e21455a221baf9c8b87015
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Vytvoření aplikací Spark pro cluster služby HDInsight pomocí nástrojů Azure pro Eclipse

Pomocí nástrojů HDInsight v Azure nástrojů pro Eclipse k vývoji aplikací Spark napsané v jazyce Scala a odesílat je na clusteru Azure HDInsight Spark, přímo z integrovaného vývojového prostředí Eclipse. Můžete použít nástroje HDInsight modulu plug-in několika různými způsoby:

* K vývoji a odesílání aplikací Scala Spark na clusteru HDInsight Spark
* K přístupu k prostředkům clusteru Azure HDInsight Spark
* K vývoji a místní spuštění aplikace Scala Spark

> [!IMPORTANT]
> Tento nástroj slouží k vytvoření a odeslání aplikací pouze pro cluster služby HDInsight Spark na systému Linux.
> 
> 

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit verze 8, který se používá pro modul runtime Eclipse IDE. Si můžete stáhnout z [Oracle webu](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Integrované vývojové prostředí Eclipse. Tento článek používá Neónová Eclipse. Můžete nainstalovat z [Eclipse webu](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalace nástrojů HDInsight v Azure nástrojů Eclipse a Scala modulu plug-in
### <a name="install-azure-toolkit-for-eclipse"></a>Nainstalovat Azure Toolkit pro Eclipse
Nástroje HDInsight pro Eclipse je k dispozici jako součást nástrojů Azure pro prostředí Eclipse. Pokyny k instalaci naleznete v tématu [instalace nástrojů Azure pro Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).
### <a name="install-the-scala-plug-in"></a>Instalace modulu plug-in Scala
Otevřete prostředí Eclipse, HDInsight nástroj automaticky zjišťuje, zda jste nainstalovali Scala modulu plug-in. Vyberte **OK** chcete pokračovat a potom postupujte podle pokynů k instalaci modulu plug-in z Eclipse Marketplace.

![Automatická instalace modulu plug-in Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Uživatel může buď [Přihlaste se k předplatnému Azure](#Sign-in-to-your-Azure-subscription), nebo [odkaz HDInsight cluster](#Link-a-cluster) pomocí nástroje Ambari uživatelského jména a hesla nebo domény připojeno přihlašovací údaje pro spuštění. 

## <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure.
1. Spusťte Eclipse IDE a otevřete Průzkumník Azure. Na **okno** nabídce vyberte možnost **zobrazit zobrazení**a potom vyberte **jiných**. V dialogovém okně, které se otevře, rozbalte položku **Azure**, vyberte **Azure Explorer**a potom vyberte **OK**.

   ![Zobrazit dialogové okno zobrazení](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
2. Klikněte pravým tlačítkem myši **Azure** uzel a potom vyberte **přihlášení**.
3. V **přihlásit k Azure** dialogové okno, vyberte metodu ověřování, vyberte **přihlášení**a zadejte přihlašovací údaje Azure.
   
   ![Azure přihlašovací dialogové okno](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
4. Poté, co jste přihlášení, **vyberte odběry** dialogové okno zobrazí všechna předplatná Azure přidružená pověření. Klikněte na tlačítko **vyberte** zavřete dialogové okno.

   ![Odběry dialogové okno Vybrat](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
5. Na **Průzkumník Azure** rozbalte **HDInsight** zobrazíte clusterů HDInsight Spark v rámci svého předplatného.
   
   ![Clustery HDInsight Spark v Azure Explorer](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
6. Název uzlu clusteru zobrazíte prostředky (například účty úložiště) přidružen ke clusteru můžete dále rozšířit.
   
   ![Rozšiřování název clusteru, který najdete v části prostředky](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

<h2 id="linkcluster">Odkaz clusteru</h2>
Můžete propojit normální clusteru pomocí Ambari spravované uživatelské jméno, také propojení clusteru hadoop zabezpečení pomocí uživatelského jména domény (například: user1@contoso.com).
1. Klikněte na tlačítko **odkaz cluster** z **Azure Explorer**.

   ![odkaz clusteru kontextové nabídky](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

2. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**, pak klikněte na tlačítko OK propojení clusteru. Volitelně můžete zadat účet úložiště, klíč úložiště a pak vyberte kontejner úložiště pro Průzkumník úložišť pro práci v levém stromové zobrazení
   
   ![Dialogové okno odkaz clusteru](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené cluster používáme klíč propojené úložiště, uživatelské jméno a heslo.
   > ![Průzkumník úložišť v prostředí Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

3. Můžete zobrazit v clusteru s podporou propojené **HDInsight** uzlu po kliknutí na tlačítko OK, pokud vstupní informace jsou správná. Teď můžete odeslat žádost této propojené clusteru.

   ![propojené clusteru](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

4. Také můžete zrušit propojení clusteru z **Azure Explorer**.
   
   ![Odpojit clusteru](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Nastavení projektu pro cluster služby HDInsight Spark Spark Scala

1. V pracovním prostoru Eclipse IDE vyberte **soubor**, vyberte **nový**a potom vyberte **projektu**. 
2. V průvodci Nový projekt rozbalte **HDInsight**, vyberte **Spark v HDInsight (Scala)** a potom vyberte **Další**.

   ![Výběr Spark v HDInsight (Scala) projektu](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
3. Průvodce vytvořením projektu Scala automaticky zjistí, zda jste nainstalovali Scala modulu plug-in. Vyberte **OK** pokračovat ve stahování Scala modul plug-in, a pak postupujte podle pokynů k restartování prostředí Eclipse.

   ![Kontrola scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
4. V **nový projekt Scala HDInsight** dialogové okno, zadejte následující hodnoty a potom vyberte **Další**:
   * Zadejte název projektu.
   * V **prostředí JRE** oblasti, ujistěte se, že **používání spuštění prostředí JRE** je nastaven na **JavaSE 1.7** nebo novější.
   * V **Spark knihovny** oblasti, můžete zvolit **Maven použijte ke konfiguraci Spark SDK** možnost.  Naše nástroj integruje správnou verzi Spark SDK a Scala SDK. Můžete také **ručně přidat Spark SDK** možnost, stáhněte a přidat sadu SDK Spark pomocí ručně.

   ![Dialogové okno Nový projekt HDInsight Scala](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
5. V dialogovém okně Další vyberte **Dokončit**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvořit aplikaci pro cluster služby HDInsight Spark Scala

1. V integrovaném vývojovém prostředí Eclipse, z Průzkumníka balíčku, rozbalte projekt, který jste předtím vytvořili, klikněte pravým tlačítkem na **src**, přejděte na příkaz **nový**a potom vyberte **jiných**.
2. V **vyberte Průvodce** dialogové okno, rozbalte seznam **Scala průvodců**, vyberte **Scala objekt**a potom vyberte **Další**.
   
   ![Vyberte dialogové okno Průvodce](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
3. V **vytvořit nový soubor** dialogové okno, zadejte název pro objekt a potom vyberte **Dokončit**.
   
   ![Vytvořit nový soubor – dialogové okno](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
4. V textovém editoru, vložte následující kód:
   
        import org.apache.spark.SparkConf
        import org.apache.spark.SparkContext
   
        object MyClusterApp{
          def main (arg: Array[String]): Unit = {
            val conf = new SparkConf().setAppName("MyClusterApp")
            val sc = new SparkContext(conf)
   
            val rdd = sc.textFile("wasb:///HdiSamples/HdiSamples/SensorSampleData/hvac/HVAC.csv")
   
            //find the rows that have only one digit in the seventh column in the CSV
            val rdd1 =  rdd.filter(s => s.split(",")(6).length() == 1)
   
            rdd1.saveAsTextFile("wasb:///HVACOut")
          }        
        }
5. Spuštění aplikace na clusteru HDInsight Spark:
   
   a. V Průzkumníku balíčku, klikněte pravým tlačítkem na název projektu a potom vyberte **odesílání aplikací Spark na HDInsight**.        
   b. V **Spark odeslání** dialogové okno, zadejte následující hodnoty a potom vyberte **odeslání**:
      
      * Pro **název clusteru**, vyberte cluster HDInsight Spark, na kterém chcete aplikaci spustit.
      * Artefakt z projektu Eclipse, nebo vyberte jednu z pevného disku. Výchozí hodnota závisí na položku, klikněte pravým tlačítkem z Průzkumníka balíčku.
      * V **hlavní název třídy** rozevíracího seznamu, odeslání Průvodce zobrazí všechny názvy objektů ze svého projektu. Vyberte nebo zadejte takový, který chcete spustit. Pokud jste vybrali artefakt z pevného disku, musíte zadat název hlavní třídy ručně. 
      * Protože kód aplikace v tomto příkladu nevyžaduje žádných argumentů příkazového řádku ani odkazovat JAR nebo soubory, můžete zbývající textová pole ponechat prázdné.
        
      ![Dialogové okno Spark odeslání](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
6. **Spark odeslání** karta by se měl spustit zobrazení průběhu. Můžete zastavit výběrem červené tlačítko v aplikaci **Spark odeslání** okno. Můžete také zobrazit protokoly pro tuto konkrétní aplikaci spustit výběrem Ikona glóbu (označen pole blue obrázek).
      
   ![Okno Spark odeslání](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Přístup a spravovat clustery HDInsight Spark pomocí nástrojů HDInsight v Azure nástrojů pro Eclipse
Můžete provádět různé operace pomocí nástroje HDInsight, včetně přístupu k výstupu úlohy.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh
1. V Průzkumníku Azure rozbalte **HDInsight**, rozbalte název clusteru Spark a pak vyberte **úlohy**. 

   ![Úlohy zobrazení uzlu](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

2. Vyberte **úlohy** uzlu. Pokud je nižší než verze Java **1.8**, nástroje HDInsight automaticky připomenutí nainstalujete **E (fx) clipse** modulu plug-in. Vyberte **OK** chcete pokračovat a pak postupujte podle pokynů průvodce a nainstalujte ji z Eclipse Marketplace restartování prostředí Eclipse. 

   ![Nainstalujte clipse E (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

3. Otevřete zobrazení úlohy **úlohy** uzlu. V pravém podokně klikněte **zobrazení úloh Spark** karta zobrazuje všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro který chcete zobrazit další podrobnosti.

   ![Podrobnosti aplikace](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Potom můžete provést některou z těchto akcí:

   * Najeďte na graf úlohy. Zobrazuje základní informace o probíhající úloze. Vyberte graf úlohy a uvidíte fázích a informace, které generuje každých úlohy.

     ![Fáze podrobnosti úlohy](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Vyberte **protokolu** zobrazíte často používají protokoly, včetně **ovladač Stderr**, **ovladač Stdout**, a **informací o adresáři**.

     ![Podrobnosti protokolu](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Výběrem hypertextové odkazy v horní části okna otevřete historie Spark uživatelského rozhraní a uživatelském rozhraní YARN (na úrovni aplikace).

### <a name="access-the-storage-container-for-the-cluster"></a>Přístup k kontejner úložiště pro cluster
1. V Průzkumníku Azure, rozbalte **HDInsight** kořenový uzel zobrazíte seznam clustery HDInsight Spark, které jsou k dispozici.
2. Rozbalte název clusteru účet úložiště a výchozí kontejner úložiště pro cluster.
   
   ![Kontejner úložiště účet a výchozí úložiště](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
3. Vyberte název kontejneru úložiště, který je přidružen ke clusteru. V pravém podokně dvakrát klikněte na **HVACOut** složky. Otevřete jednu z **část -** soubory, které chcete zobrazit výstup aplikace.

### <a name="access-the-spark-history-server"></a>Přístup k serveru Spark historie
1. V Průzkumníku Azure, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřete uživatelské rozhraní historie Spark**. Když se zobrazí výzva, zadejte přihlašovací údaje Správce clusteru. Zadali jste tyto při zřizování clusteru.
2. V řídicím panelu Spark historie serveru použijte tento název aplikace a Hledat aplikace právě dokončila spuštění. V předchozím kódu nastavit název aplikace pomocí `val conf = new SparkConf().setAppName("MyClusterApp")`. Ano, název aplikace Spark byl **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Spuštění portálu Ambari
1. V Průzkumníku Azure, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřete portál pro správu clusteru (Ambari)**. 
2. Když se zobrazí výzva, zadejte přihlašovací údaje Správce clusteru. Zadali jste tyto při zřizování clusteru.

### <a name="manage-azure-subscriptions"></a>Spravovat předplatná Azure
Ve výchozím nastavení zobrazí nástroj HDInsight v Azure nástrojů pro Eclipse clustery Spark ze všech předplatných Azure. V případě potřeby můžete zadat předplatné, pro které chcete pro přístup ke clusteru. 

1. V Průzkumníku Azure, klikněte pravým tlačítkem myši **Azure** kořenový uzel a potom vyberte **Spravovat odběry**. 
2. V dialogovém okně, zrušte zaškrtnutí políčka pro předplatné, které nechcete, aby pro přístup a potom vyberte **Zavřít**. Můžete také vybrat **Odhlásit** Pokud se chcete odhlásit z vašeho předplatného Azure.

## <a name="run-a-spark-scala-application-locally"></a>Místní spuštění aplikace Spark Scala
Nástroje HDInsight v Azure nástrojů pro Eclipse slouží ke spouštění aplikací Spark Scala místně na pracovní stanici. Obvykle se tyto aplikace nepotřebují přístup k prostředkům clusteru, například kontejner úložiště a můžete spustit a otestovat je místně.

### <a name="prerequisite"></a>Požadavek
Při místní aplikací Spark Scala spouštíte na počítači se systémem Windows, může získat výjimku, jak je popsáno v [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). K této výjimce, protože **WinUtils.exe** chybí v systému Windows. 

Chcete-li tuto chybu vyřešit, je třeba [stáhnout spustitelný soubor](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako je **C:\WinUtils\bin**a poté přidejte proměnnou prostředí **HADOOP_HOME** a nastavit hodnotu proměnnou **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Spustit místních aplikací Spark Scala
1. Spusťte Eclipse a vytvořte projekt. V **nový projekt** dialogové okno, vyberte následující možnosti a pak vyberte **Další**.
   
   * V levém podokně vyberte **HDInsight**.
   * V pravém podokně vyberte **Spark v ukázkové spustit HDInsight místní (Scala)**.

   ![Dialogové okno Nový projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
2. K zadání podrobností projektu, postupujte podle kroků 3 až 6 z části starší [Spark Scala projekt pro cluster služby HDInsight Spark instalace](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

3. Ukázkový kód přidá šablona (**LogQuery**) v části **src** složky, kterou můžete spustit místně v počítači.
   
   ![Umístění LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
4. Klikněte pravým tlačítkem myši **LogQuery** aplikace, přejděte na příkaz **spustit jako**a potom vyberte **aplikace: 1 Scala**. Výstup jako se zobrazuje na **konzoly** karty:
   
   ![Místní aplikace Spark, výsledek spuštění](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Známé problémy
Chcete-li odeslat aplikace do Azure Data Lake Store, vyberte **interaktivní** režimu během procesu Azure přihlášení. Pokud vyberete **automatizovaná** režimu, může dojde k chybě.

![Interaktivní přihlášení](./media/apache-spark-eclipse-tool-plugin/interactive-authentication.png)

Můžete odeslat vaší aplikace s libovolnou metodu přihlášení clusteru služby Azure Data Lake.

V současné době Spark výstupů zobrazení přímo není podporováno.

## <a name="feedback"></a>Váš názor
Pokud máte jakékoli zpětné vazby, nebo pokud narazíte na jiné problémy při použití tohoto nástroje, pošlete nám e-mail na hdivstool@microsoft.com.

## <a name="seealso"></a>Viz také
* [Přehled: Apache Spark v Azure HDInsight](apache-spark-overview.md)

### <a name="scenarios"></a>Scénáře
* [Spark s BI: Provádějte interaktivní analýzy dat pomocí Sparku v HDInsight pomocí nástrojů BI](apache-spark-use-bi-tools.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)

### <a name="creating-and-running-applications"></a>Vytváření a spouštění aplikací
* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)

### <a name="tools-and-extensions"></a>Nástroje a rozšíření
* [Použití nástrojů Azure pro IntelliJ k vytvoření a odeslání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně prostřednictvím sítě VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití nástrojů Azure pro IntelliJ k ladění aplikací Spark vzdáleně přes SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástroje HDInsight pro IntelliJ s Hortonworks karanténě](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

