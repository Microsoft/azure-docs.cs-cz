---
title: 'Sada Azure Toolkit pro Eclipse: Scala vytvářet aplikace pro HDInsight Spark '
description: Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse pro vývoj aplikací Spark napsané v jazyce Scala a odesílat je na cluster HDInsight Spark přímo z integrovaného vývojového prostředí Eclipse.
services: hdinsight
author: jasonwhowell
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/30/2017
ms.author: jasonh
ms.openlocfilehash: 836bdccbf3f8887a47da38b47b414722c878be04
ms.sourcegitcommit: 161d268ae63c7ace3082fc4fad732af61c55c949
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/27/2018
ms.locfileid: "43046008"
---
# <a name="use-azure-toolkit-for-eclipse-to-create-spark-applications-for-an-hdinsight-cluster"></a>Vytvoření aplikací Spark pro cluster služby HDInsight pomocí sady Azure Toolkit pro Eclipse

Pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse pro vývoj aplikací Spark napsané v jazyce Scala a odešlete je do clusteru Azure HDInsight Spark, přímo z integrovaného vývojového prostředí Eclipse. Můžete použít několik různých způsobů, jak modul plug-in nástroje HDInsight:

* Pro vývoj a odeslání aplikace Scala Spark na cluster HDInsight Spark
* Pro přístup k vašim prostředkům clusteru Azure HDInsight Spark
* Pro vývoj a spouštění aplikace Scala Spark místně

> [!IMPORTANT]
> Tento nástroj můžete použít k vytvoření a odeslání aplikace pouze pro cluster HDInsight Spark na systému Linux.
> 
> 

## <a name="prerequisites"></a>Požadavky

* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [Vytváření clusterů Apache Spark ve službě Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Oracle Java Development Kit verze 8, který se používá pro modul runtime integrovaného vývojového prostředí Eclipse. Můžete ji stáhnout [webu Oracle](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* Integrované vývojové prostředí Eclipse. Tento článek používá Eclipse Neon. Můžete ho nainstalovat [webu Eclipse](https://www.eclipse.org/downloads/).



## <a name="install-hdinsight-tools-in-azure-toolkit-for-eclipse-and-the-scala-plug-in"></a>Instalace nástrojů HDInsight v sadě Azure Toolkit pro Eclipse a modulu plug-in Scala

### <a name="install-azure-toolkit-for-eclipse"></a>Nainstalujte sadu Azure Toolkit pro Eclipse
Nástroje HDInsight pro Eclipse je k dispozici jako součást sady Azure Toolkit pro Eclipse. Pokyny k instalaci, naleznete v tématu [instalace sady Azure Toolkit pro Eclipse](https://docs.microsoft.com/java/azure/eclipse/azure-toolkit-for-eclipse-installation).

### <a name="install-the-scala-plug-in"></a>Instalace modulu plug-in Scala
Když otevřete Eclipse, nástroje HDInsight automaticky zjišťuje, zda jste nainstalovali modul plug-in Scala. Vyberte **OK** pokračovat, a pak postupujte podle pokynů k instalaci modulu plug-in Eclipse Marketplace.

![Automatická instalace modulu plug-in Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala.png)

Uživatel může buď [Přihlaste se k předplatnému Azure](#Sign-in-to-your-Azure-subscription), nebo [propojit HDInsight cluster](#Link-a-cluster) pomocí nástroje Ambari uživatelského jména a hesla nebo doméně připojené přihlašovací údaje ke spuštění. 

## <a name="sign-in-to-your-azure-subscription"></a>Přihlaste se ke svému předplatnému Azure.
1. Spusťte integrované vývojové prostředí Eclipse a otevřete Průzkumníka služby Azure. Na **okno** nabídce vyberte možnost **zobrazit zobrazení**a pak vyberte **jiných**. V dialogovém okně, které se otevře, rozbalte **Azure**vyberte **Průzkumníka služby Azure**a pak vyberte **OK**.

   ![Zobrazit dialogové okno zobrazení](./media/apache-spark-eclipse-tool-plugin/view-explorer-1.png)
1. Klikněte pravým tlačítkem myši **Azure** uzlu a pak vyberte **přihlášení**.
1. V **Azure Sign In** dialogového okna, vyberte metodu ověřování, vyberte **přihlášení**a zadejte přihlašovací údaje Azure.
   
   ![Azure přihlašovací dialogové okno](./media/apache-spark-eclipse-tool-plugin/view-explorer-2.png)
1. Jakmile jste přihlášení, **vyberte předplatná** dialogové okno obsahuje všechna předplatná Azure přidružená pověření. Klikněte na tlačítko **vyberte** zavřete dialogové okno.

   ![Dialogové okno Vybrat předplatná](./media/apache-spark-eclipse-tool-plugin/Select-Subscriptions.png)
1. Na **Průzkumníka služby Azure** kartu, rozbalte **HDInsight** zobrazíte clustery HDInsight Spark v rámci vašeho předplatného.
   
   ![Clustery HDInsight Spark v Průzkumníku služby Azure](./media/apache-spark-eclipse-tool-plugin/view-explorer-3.png)
1. Název uzlu clusteru a zobrazit prostředky (například účty úložiště) přidružené ke clusteru můžete dále rozšířit.
   
   ![Rozbalení název clusteru a zjistěte prostředky](./media/apache-spark-eclipse-tool-plugin/view-explorer-4.png)

## <a name="link-a-cluster"></a>Propojení clusteru
Normální cluster můžete propojit pomocí Ambari, spravovat uživatelské jméno. Podobně pro cluster HDInsight připojený k doméně můžete propojit pomocí doména a uživatelské jméno, například user1@contoso.com.

1. Vyberte **propojení clusteru** z **Průzkumníka služby Azure**.

   ![propojení clusteru kontextové nabídky](./media/apache-spark-intellij-tool-plugin/link-a-cluster-context-menu.png)

1. Zadejte **název clusteru**, **uživatelské jméno** a **heslo**, pak klikněte na tlačítko OK, chcete-li propojit clusteru. Volitelně můžete zadat účet úložiště, klíč úložiště a pak vyberte kontejner úložiště pro Průzkumníka služby storage pracovat v zobrazení stromu vlevo
   
   ![Dialogové okno clusteru odkaz](./media/apache-spark-eclipse-tool-plugin/link-cluster-dialog.png)
   
   > [!NOTE]
   > Pokud cluster jak zaznamenána v rámci předplatného Azure a propojené clusteru používáme klíč propojené úložiště, uživatelské jméno a heslo.
   > ![Průzkumník služby Storage v Eclipse](./media/apache-spark-eclipse-tool-plugin/storage-explorer-in-Eclipse.png)

1. Můžete zobrazit propojené clusteru v **HDInsight** uzlu po kliknutí na tlačítko OK, pokud vstupní informace jsou správné. Nyní můžete odeslat aplikace do této propojené clusteru.

   ![propojené clusteru](./media/apache-spark-intellij-tool-plugin/linked-cluster.png)

1. Také můžete odpojit clusteru ze **Průzkumníka služby Azure**.
   
   ![Odpojit clusteru](./media/apache-spark-intellij-tool-plugin/unlink.png)


## <a name="set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster"></a>Nastavení projektu Spark Scala pro cluster HDInsight Spark

1. V pracovním prostoru integrovaného vývojového prostředí Eclipse vyberte **souboru**vyberte **nový**a pak vyberte **projektu**. 
1. V průvodci Nový projekt rozbalte **HDInsight**vyberte **Spark v HDInsight (Scala)** a pak vyberte **Další**.

   ![Výběr Spark v HDInsight (Scala) projektu](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-2.png)
1. V Průvodci vytvořením projektu Scala automaticky zjišťuje, zda jste nainstalovali modul plug-in Scala. Vyberte **OK** pokračovat ve stahování Scala modulu plug-in a potom postupujte podle pokynů k restartování prostředí Eclipse.

   ![Kontrola Scala](./media/apache-spark-eclipse-tool-plugin/auto-install-scala-2.png)
1. V **nový projekt Scala HDInsight** dialogové okno, zadejte následující hodnoty a pak vyberte **Další**:
   * Zadejte název projektu.
   * V **prostředí JRE** oblasti, ujistěte se, že **použijte spouštěcí prostředí JRE** je nastavena na **JavaSE 1.7** nebo novější.
   * V **knihovny Spark** oblasti, můžete zvolit **Maven použijte ke konfiguraci Spark SDK** možnost.  Náš nástroj integruje správnou verzi pro Spark SDK a Scala SDK. Můžete také zvolit **ručně přidat Spark SDK** možnost, stahování a ručně přidejte sadu SDK Spark pomocí.

   ![Dialogové okno Nový projekt Scala HDInsight](./media/apache-spark-eclipse-tool-plugin/create-hdi-scala-app-3.png)
1. V dalším dialogovém okně vyberte **Dokončit**. 
   
  
## <a name="create-a-scala-application-for-an-hdinsight-spark-cluster"></a>Vytvoření aplikace pro cluster HDInsight Spark Scala

1. V integrovaném vývojovém prostředí Eclipse, z Průzkumníku balíčků, rozbalte projekt, který jste předtím vytvořili, klikněte pravým tlačítkem na **src**, přejděte na **nový**a pak vyberte **jiných**.
1. V **vyberte Průvodce** dialogového okna rozbalte **Scala průvodců**vyberte **Scala objekt**a pak vyberte **Další**.
   
   ![Vyberte dialogové okno Průvodce](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-1.png)
1. V **vytvořit nový soubor** dialogové okno, zadejte název pro objekt a potom vyberte **Dokončit**.
   
   ![Vytvoření dialogového okna nový soubor](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-2.png)
1. V textovém editoru vložte následující kód:
   
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
1. Spusťte aplikaci v clusteru Spark v HDInsight:
   
   a. V Průzkumníku balíčků klikněte pravým tlačítkem myši na název projektu a vyberte **odeslání aplikace Spark na HDInsight**.        
   b. V **Spark odeslání** dialogové okno, zadejte následující hodnoty a pak vyberte **odeslat**:
      
      * Pro **název clusteru**, vyberte na kterém chcete spustit aplikaci v clusteru HDInsight Spark.
      * Vyberte artefakt z projektu Eclipse nebo vyberte některou z pevného disku. Výchozí hodnota závisí na položce, kliknete pravým tlačítkem myši v Průzkumníku balíčků.
      * V **hlavní název třídy** rozevíracího seznamu, odeslání Průvodce zobrazí všechny názvy objektů z projektu. Vyberte nebo zadejte takový, který chcete spustit. Pokud jste vybrali artefakt z pevného disku, můžete název hlavní třídy zadat ručně. 
      * Protože kód aplikace v tomto příkladu nevyžaduje argumenty příkazového řádku nebo odkazovat na soubory nebo kromě souborů JAR, můžete nechat zbývající textová pole prázdné.
        
      ![Dialogové okno odeslání Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-3.png)
1. **Spark odeslání** karta by měla začínat zobrazení průběhu. Aplikace můžete zastavit tak, že vyberete červené tlačítko v **Spark odeslání** okna. Můžete také zobrazit protokoly pro tuto konkrétní aplikaci spusťte výběrem ikony světě (udávají modrá pole na obrázku).
      
   ![Okno odeslání Spark](./media/apache-spark-eclipse-tool-plugin/create-scala-proj-4.png)


## <a name="access-and-manage-hdinsight-spark-clusters-by-using-hdinsight-tools-in-azure-toolkit-for-eclipse"></a>Přístup a Správa clusterů HDInsight Spark pomocí nástrojů HDInsight v sadě Azure Toolkit pro Eclipse
Pomocí nástrojů HDInsight, včetně přístupu k výstupu úlohy můžete provádět různé operace.

### <a name="access-the-job-view"></a>Přístup k zobrazení úloh
1. V Průzkumníku služby Azure, rozbalte **HDInsight**, rozbalte název clusteru Spark a pak vyberte **úlohy**. 

   ![Úlohy zobrazení uzlu](./media/apache-spark-eclipse-tool-plugin/job-view-node.png)

1. Vyberte **úlohy** uzlu. Pokud je nižší než verze Javy **1.8**, nástrojů HDInsight automaticky připomenutí nainstalujete **E (fx) clipse** modulu plug-in. Vyberte **OK** pokračovat, a pak postupujte podle pokynů průvodce a nainstalujte ji z webu Marketplace Eclipse restartování prostředí Eclipse. 

   ![Nainstalujte clipse E (fx)](./media/apache-spark-eclipse-tool-plugin/auto-install-efxclipse.png)

1. Otevřete zobrazení úloh z **úlohy** uzlu. V pravém podokně klikněte **zobrazení úloh Spark** karta zobrazuje všechny aplikace, které byly spuštěny v clusteru. Vyberte název aplikace, pro kterou chcete zobrazit další podrobnosti.

   ![Podrobnosti aplikace](./media/apache-spark-eclipse-tool-plugin/view-job-logs.png)

   Potom můžete provést některou z těchto akcí:

   * Najeďte myší na graf úlohy. Zobrazí se základní informace o běžící úlohu. Vyberte graf úlohy, a zobrazí se fází a informace, které generují všechny úlohy.

     ![Podrobnosti o úloze fáze](./media/apache-spark-eclipse-tool-plugin/Job-graph-stage-info.png)

   * Vyberte **protokolu** karta zobrazovat často používá protokoly, včetně **ovladač Stderr**, **ovladač Stdout**, a **informací o adresáři**.

     ![Podrobnosti protokolu](./media/apache-spark-eclipse-tool-plugin/Job-log-info.png)

   * Výběrem hypertextové odkazy v horní části okna otevřete historie Sparku uživatelského rozhraní a Uživatelském rozhraní YARN (na úrovni aplikace).

### <a name="access-the-storage-container-for-the-cluster"></a>Přístup ke kontejneru úložiště pro cluster
1. V Průzkumníku služby Azure, rozbalte **HDInsight** kořenový uzel zobrazíte seznam clusterů HDInsight Spark, které jsou k dispozici.
1. Rozbalte název clusteru a podívejte se na účet úložiště a výchozí kontejner úložiště pro cluster.
   
   ![Kontejner úložiště účtu a výchozí úložiště](./media/apache-spark-eclipse-tool-plugin/view-explorer-5.png)
1. Vyberte název kontejneru úložiště přidružené ke clusteru. V pravém podokně klikněte dvakrát na **HVACOut** složky. Otevřete jednu z **část -** soubory, které chcete zobrazit výstup z aplikace.

### <a name="access-the-spark-history-server"></a>Přístup na server historie Sparku
1. V Průzkumníku služby Azure, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít uživatelské rozhraní historie Sparku**. Jakmile budete vyzváni, zadejte přihlašovací údaje Správce clusteru. Zadali jste je při zřizování clusteru.
1. V panelu server historie Sparku použijete název aplikace k vyhledání aplikace dokončení právě běží. V předchozím kódu, nastavte název aplikace s použitím `val conf = new SparkConf().setAppName("MyClusterApp")`. Ano, název vaší aplikace Spark byl **MyClusterApp**.

### <a name="start-the-ambari-portal"></a>Spusťte portál Ambari
1. V Průzkumníku služby Azure, klikněte pravým tlačítkem na název clusteru Spark a pak vyberte **otevřít portál pro správu clusteru (Ambari)**. 
1. Jakmile budete vyzváni, zadejte přihlašovací údaje Správce clusteru. Zadali jste je při zřizování clusteru.

### <a name="manage-azure-subscriptions"></a>Správa předplatných Azure
Ve výchozím nastavení nástroj HDInsight v sadě Azure Toolkit pro Eclipse vypíše clustery Spark ze všech předplatných Azure. V případě potřeby můžete zadat předplatné, pro které chcete pro přístup ke clusteru. 

1. V Průzkumníku služby Azure, klikněte pravým tlačítkem myši **Azure** kořenový uzel a potom vyberte **spravovat předplatná**. 
1. V dialogovém okně, zrušte zaškrtnutí políček pro předplatné, které nechcete, aby pro přístup a pak vyberte **Zavřít**. Můžete také vybrat **Odhlásit** potřeby odhlaste se z vašeho předplatného Azure.

## <a name="run-a-spark-scala-application-locally"></a>Spouštění aplikace Spark Scala místně
Nástroje HDInsight v sadě Azure Toolkit pro Eclipse slouží ke spuštění aplikace Spark Scala místně na pracovní stanici. Obvykle tyto aplikace nepotřebují přístup k prostředkům clusteru, jako je například kontejner úložiště, a můžete spustit a otestovat je místně.

### <a name="prerequisite"></a>Požadavek
Když používáte místní aplikace Spark Scala na počítači Windows, může získat výjimku, jak je vysvětleno v [SPARK 2356](https://issues.apache.org/jira/browse/SPARK-2356). Touto výjimkou způsobeno **WinUtils.exe** chybí ve Windows. 

Chcete-li vyřešit tuto chybu, je třeba [stažení spustitelného souboru](http://public-repo-1.hortonworks.com/hdp-win-alpha/winutils.exe) do umístění, jako je **C:\WinUtils\bin**a pak přidejte proměnnou prostředí **HADOOP_HOME** a nastavte hodnotu vlastnosti proměnnou **C\WinUtils**.

### <a name="run-a-local-spark-scala-application"></a>Spuštění místních aplikací Spark Scala
1. Spusťte Eclipse a vytvořte projekt. V **nový projekt** dialogové okno, vyberte následující možnosti a pak vyberte **Další**.
   
   * V levém podokně vyberte **HDInsight**.
   * V pravém podokně vyberte **Spark ve vzorku pro místní spuštění HDInsight (Scala)**.

   ![Dialogové okno Nový projekt](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run.png)
   
1. Chcete-li poskytnout podrobnosti o projektu, postupujte podle kroků 3 až 6 z předchozí části [nastavit projekt Spark Scala pro cluster HDInsight Spark](#set-up-a-spark-scala-project-for-an-hdinsight-spark-cluster).

1. Šablona přidá ukázkový kód (**LogQuery**) v části **src** složky, kterou můžete spustit místně ve vašem počítači.
   
   ![Umístění LogQuery](./media/apache-spark-eclipse-tool-plugin/local-app.png)
   
1. Klikněte pravým tlačítkem myši **LogQuery** aplikaci, přejděte na příkaz **spustit jako**a pak vyberte **1 aplikace Scala**. Výstup jako tento popis se zobrazuje na **konzoly** kartu:
   
   ![Místní aplikace Spark, výsledek spuštění](./media/apache-spark-eclipse-tool-plugin/hdi-spark-app-local-run-result.png)

## <a name="known-problems"></a>Známé problémy
Při propojení clusteru, můžu Navrhněte poskytnutí přihlašovacích údajů úložiště.

![Interaktivní přihlášení](./media/apache-spark-eclipse-tool-plugin/link-cluster-with-storage-credential-eclipse.png)

Existují dva režimy pro odeslání úlohy. Pokud přihlašovací údaje úložiště v dávkovém režimu se použije k odeslání úlohy. V opačném případě se použije interaktivním režimu. Pokud cluster je zaneprázdněný, může se zobrazit následující chyba.

![Eclipse zobrazí chybová zpráva při clusteru zaneprázdněný](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-upload.png)

![Eclipse zobrazí chybová zpráva při clusteru zaneprázdněný](./media/apache-spark-eclipse-tool-plugin/eclipse-interactive-cluster-busy-submit.png)

## <a name="feedback"></a>Váš názor
Pokud máte nějakou zpětnou vazbu, nebo pokud narazíte na jakékoli potíže při používání tohoto nástroje, pošlete nám e-mail na hdivstool@microsoft.com.

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
* [Vytvoření a odesílání aplikací Spark Scala pomocí sady Azure Toolkit pro IntelliJ](apache-spark-intellij-tool-plugin.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes síť VPN](../hdinsight-apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití sady Azure Toolkit pro IntelliJ pro ladění aplikací Spark vzdáleně přes SSH](../hdinsight-apache-spark-intellij-tool-debug-remotely-through-ssh.md)
* [Použití nástrojů HDInsight pro IntelliJ s Hortonworks Sandbox](../hadoop/hdinsight-tools-for-intellij-with-hortonworks-sandbox.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)

### <a name="managing-resources"></a>Správa prostředků
* [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)
* [Sledování a ladění úloh spuštěných v clusteru Apache Spark v HDInsight](apache-spark-job-debugging.md)

