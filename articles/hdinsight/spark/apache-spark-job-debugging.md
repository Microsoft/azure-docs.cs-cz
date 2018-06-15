---
title: Ladění Apache Spark úlohy spuštěné v Azure HDInsight | Microsoft Docs
description: Použít uživatelském rozhraní YARN, Spark uživatelského rozhraní a Spark historie serveru ke sledování a ladění úloh spuštěných na clusteru Spark v Azure HDInsight
services: hdinsight
documentationcenter: ''
author: mumian
manager: cgronlun
editor: cgronlun
tags: azure-portal
ms.assetid: 59af05a7-2bd9-44b0-b55f-2438d294198b
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: conceptual
ms.date: 12/20/2017
ms.author: jgao
ms.openlocfilehash: 1ddf8cd38b4d62dc206a9f27e0620f8c7b232ec3
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
ms.locfileid: "31519378"
---
# <a name="debug-apache-spark-jobs-running-on-azure-hdinsight"></a>Ladění Apache Spark úlohy spuštěné v Azure HDInsight

V tomto článku zjistěte, jak sledovat a ladit spuštěné v clusterech prostředí HDInsight pomocí uživatelského rozhraní YARN, Spark uživatelského rozhraní a serveru Spark historie úlohy Spark. Spustit úlohu Spark pomocí poznámkového bloku dostupné s clusterem Spark **strojového učení: prediktivní analýzy dat kontroly potravin pomocí MLLib**. Následující postup můžete použít ke sledování aplikace, která jste odeslali pomocí jakékoli jiné přístup také, například **odeslání spark**.

## <a name="prerequisites"></a>Požadavky
Musíte mít následující:

* Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Cluster Apache Spark v HDInsight. Pokyny najdete v tématu [clusterů vytvořit Apache Spark v Azure HDInsight](apache-spark-jupyter-spark-sql.md).
* Můžete by měl mít spuštění poznámkového bloku,  **[strojového učení: prediktivní analýzy dat kontroly potravin pomocí MLLib](apache-spark-machine-learning-mllib-ipython.md)**. Návod, jak spustit tento poznámkový blok pomocí následujícího odkazu.  

## <a name="track-an-application-in-the-yarn-ui"></a>Sledování aplikace v uživatelském rozhraní YARN
1. Spuštění uživatelského rozhraní YARN. Klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na **YARN**.
   
    ![Spustit uživatelské rozhraní YARN](./media/apache-spark-job-debugging/launch-yarn-ui.png)
   
   > [!TIP]
   > Alternativně můžete také spustit uživatelské rozhraní YARN z uživatelského rozhraní Ambari. Chcete-li spustit uživatelské rozhraní Ambari, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na **řídicí panel clusteru HDInsight**. V uživatelském rozhraní Ambari, klikněte na **YARN**, klikněte na tlačítko **rychlé odkazy**, klikněte na tlačítko active Resource Manager a pak klikněte na **uživatelského rozhraní správce prostředků**.    
   > 
   > 
2. Protože jste spustili úlohy Spark pomocí Jupyter notebooks, aplikace, má název **remotesparkmagics** (to je název pro všechny aplikace, které jsou spuštěné z poznámkových bloků). Klikněte na tlačítko ID aplikace proti název aplikace, chcete-li získat další informace o úloze. Spustí zobrazení aplikací.
   
    ![Vyhledání ID aplikací Spark](./media/apache-spark-job-debugging/find-application-id.png)
   
    Takové aplikace, které jsou spouštěny z Jupyter notebooks, stav je vždy **systémem** do ukončení poznámkového bloku.
3. Z pohledu aplikace podrobnostem dál zjistěte kontejnery přidružené aplikace a protokoly (stdout/stderr). Můžete také spustit uživatelské rozhraní Spark kliknutím serveru linking odpovídající **sledování adresy URL**, jak je uvedeno níže. 
   
    ![Stažení protokolů o kontejneru](./media/apache-spark-job-debugging/download-container-logs.png)

## <a name="track-an-application-in-the-spark-ui"></a>Sledování aplikace v uživatelském rozhraní Spark
V uživatelském rozhraní Spark podrobnostem do úlohy Spark, které jsou v aplikaci, kterou jste spustili dříve vytvořený.

1. Spuštění rozhraní Spark ze zobrazení aplikací, klikněte na odkaz proti **sledování adresy URL**, jak je uvedeno výše uvedený snímek obrazovky. Zobrazí se všechny úlohy Spark, které jsou v aplikaci běžící v poznámkového bloku Jupyter spouštěny.
   
    ![Zobrazit úlohy Spark](./media/apache-spark-job-debugging/view-spark-jobs.png)
2. Klikněte **vykonavatelů** karty zobrazíte informace o zpracování a úložiště pro každý prováděcího modulu. Můžete také kliknutím na načíst zásobníku volání **vláken Dump** odkaz.
   
    ![Zobrazit vykonavatelů Spark](./media/apache-spark-job-debugging/view-spark-executors.png)
3. Klikněte na tlačítko **fázích** karty zobrazíte fázích přidružené k aplikaci.
   
    ![Zobrazit Spark fáze](./media/apache-spark-job-debugging/view-spark-stages.png)
   
    Každá fáze může mít více úloh, pro které je možné zobrazit provádění statistiky, jako vidíte níže.
   
    ![Zobrazit Spark fáze](./media/apache-spark-job-debugging/view-spark-stages-details.png) 
4. Na stránce podrobností fázi můžete spustit DAG vizualizace. Rozbalte **DAG vizualizace** odkaz v horní části stránky, jak je uvedeno níže.
   
    ![Zobrazit vizualizace Spark fázích DAG](./media/apache-spark-job-debugging/view-spark-stages-dag-visualization.png)
   
    DAG nebo přímé Aclyic grafu představuje různých fázích v aplikaci. Každé pole blue v grafu představuje Spark operace, vyvolané z aplikace.
5. Na stránce podrobností fázi můžete také spustit zobrazení časové osy aplikace. Rozbalte **časová osa událostí** odkaz v horní části stránky, jak je uvedeno níže.
   
    ![Zpracuje událost časová osa zobrazení Spark](./media/apache-spark-job-debugging/view-spark-stages-event-timeline.png)
   
    Zobrazí se události Spark ve formě časové osy. Zobrazení časové osy je k dispozici ve třech úrovních mezi úlohami v rámci úlohy a v rámci úsek. Na obrázku výše zaznamená zobrazení časové osy pro danou fázi.
   
   > [!TIP]
   > Pokud jste vybrali **zapnout zvětšování** zaškrtávací políčko můžete posunete doleva a doprava napříč zobrazení časové osy.
   > 
   > 
6. Ostatní karty v uživatelském rozhraní Spark poskytují užitečné informace o instanci Spark také.
   
   * Karta úložiště – Pokud vaše aplikace vytvoří RDDs, může najít informace o těch, na kartě úložiště.
   * Karta prostředí – tato karta obsahuje mnoho užitečných informací o instanci Spark, jako 
     * Verze Scala
     * Adresář protokolu událostí související s clusterem
     * Počet jader vykonavatele pro aplikaci
     * Atd.

## <a name="find-information-about-completed-jobs-using-the-spark-history-server"></a>Najít informace o používání serveru historie Spark dokončené úlohy
Po dokončení úlohy je v serveru Spark historie jako trvalý informace o úloze.

1. Ke spuštění serveru Spark historie, v okně clusteru, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na **Spark historie serveru**.
   
    ![Spusťte Server historie Spark](./media/apache-spark-job-debugging/launch-spark-history-server.png)
   
   > [!TIP]
   > Alternativně můžete také spustit rozhraní Spark historie serveru z uživatelského rozhraní Ambari. Chcete-li spustit uživatelské rozhraní Ambari, v okně clusteru, klikněte na tlačítko **řídicí panel clusteru**a potom klikněte na **řídicí panel clusteru HDInsight**. V uživatelském rozhraní Ambari, klikněte na **Spark**, klikněte na tlačítko **rychlé odkazy**a potom klikněte na **uživatelské rozhraní serveru Spark historie**.
   > 
   > 
2. Zobrazí všechny dokončené aplikace uvedené. Klikněte na tlačítko ID aplikací k podrobnostem aplikace pro další informace.
   
    ![Spusťte Server historie Spark](./media/apache-spark-job-debugging/view-completed-applications.png)

## <a name="see-also"></a>Další informace najdete v tématech
*  [Správa prostředků v clusteru Apache Spark v Azure HDInsight](apache-spark-resource-manager.md)

### <a name="for-data-analysts"></a>Pro analytiky dat

* [Spark s Machine Learning: Používejte Spark v HDInsight pro analýzu teploty v budově pomocí dat HVAC](apache-spark-ipython-notebook-machine-learning.md)
* [Spark s Machine Learning: Používejte Spark v HDInsight k předpovědím výsledků kontrol potravin](apache-spark-machine-learning-mllib-ipython.md)
* [Analýza protokolu webu pomocí Sparku v HDInsight](apache-spark-custom-library-website-log-analysis.md)
* [Analýza dat telemetrie Application Insights pomocí Sparku v HDInsight](apache-spark-analyze-application-insight-logs.md)
* [Použití Caffe v Azure HDInsight Spark pro distribuované hloubkové learning](apache-spark-deep-learning-caffe.md)

### <a name="for-spark-developers"></a>Pro vývojáře, Spark

* [Vytvoření samostatné aplikace pomocí Scala](apache-spark-create-standalone-application.md)
* [Vzdálené spouštění úloh na clusteru Sparku pomocí Livy](apache-spark-livy-rest-interface.md)
* [Modul plug-in nástroje HDInsight pro IntelliJ IDEA pro vytvoření a odesílání aplikací Spark Scala](apache-spark-intellij-tool-plugin.md)
* [Použití modulu plug-in nástroje HDInsight pro IntelliJ IDEA pro vzdálené ladění aplikací Spark](apache-spark-intellij-tool-plugin-debug-jobs-remotely.md)
* [Použití poznámkových bloků Zeppelin s clusterem Sparku v HDInsight](apache-spark-zeppelin-notebook.md)
* [Jádra dostupná pro poznámkový blok Jupyter v clusteru Sparku pro HDInsight](apache-spark-jupyter-notebook-kernels.md)
* [Použití externích balíčků s poznámkovými bloky Jupyter](apache-spark-jupyter-notebook-use-external-packages.md)
* [Instalace Jupyteru do počítače a připojení ke clusteru HDInsight Spark](apache-spark-jupyter-notebook-install-locally.md)


