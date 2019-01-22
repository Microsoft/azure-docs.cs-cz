---
title: Transformace dat pomocí Sparku v Azure Data Factory | Microsoft Docs
description: Tento kurz obsahuje podrobné pokyny pro transformaci dat pomocí aktivity Sparku ve službě Azure Data Factory.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/10/2018
ms.author: douglasl
ms.openlocfilehash: be9ed1d840eab5478dee4d212705211c0967bdbd
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54427963"
---
# <a name="transform-data-in-the-cloud-by-using-a-spark-activity-in-azure-data-factory"></a>Transformace dat v cloudu pomocí aktivity Sparku ve službě Azure Data Factory
V tomto kurzu vytvoříte pomocí webu Azure Portal kanál služby Azure Data Factory. Tento kanál transformuje data pomocí aktivity Sparku a propojené služby Azure HDInsight na vyžádáni. 

V tomto kurzu provedete následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření kanálu využívajícího aktivitu Sparku
> * Aktivace spuštění kanálu
> * Monitorování spuštění kanálu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* **Účet služby Azure Storage**. Vytvoříte skript Pythonu a vstupní soubor a nahrajete je do Azure Storage. V tomto účtu úložiště se ukládá výstup z programu Sparku. Cluster Spark na vyžádání používá stejný účet úložiště jako primární úložiště.  

> [!NOTE]
> HdInsight podporuje jenom účty úložiště pro obecné účely s úrovní Standard. Ujistěte se, že účet úložiště nemá úroveň Premium nebo není určený jenom pro objekty blob.

* **Azure PowerShell**. Postupujte podle pokynů v tématu [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/azurerm/install-azurerm-ps).


### <a name="upload-the-python-script-to-your-blob-storage-account"></a>Nahrání skriptu Pythonu do účtu služby Blob Storage
1. Vytvořte soubor Pythonu s názvem **WordCount_Spark.py** s následujícím obsahem: 

    ```python
    import sys
    from operator import add
    
    from pyspark.sql import SparkSession
    
    def main():
        spark = SparkSession\
            .builder\
            .appName("PythonWordCount")\
            .getOrCreate()
            
        lines = spark.read.text("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/inputfiles/minecraftstory.txt").rdd.map(lambda r: r[0])
        counts = lines.flatMap(lambda x: x.split(' ')) \
            .map(lambda x: (x, 1)) \
            .reduceByKey(add)
        counts.saveAsTextFile("wasbs://adftutorial@<storageaccountname>.blob.core.windows.net/spark/outputfiles/wordcount")
        
        spark.stop()
    
    if __name__ == "__main__":
        main()
    ```
1. Položku *&lt;storageAccountName&gt;* nahraďte názvem svého účtu služby Azure Storage. Pak soubor uložte. 
1. Ve službě Azure Blob Storage vytvořte kontejner **adftutorial**, pokud ještě neexistuje. 
1. Vytvořte složku s názvem **spark**.
1. Ve složce **spark** vytvořte dílčí složku s názvem **script**. 
1. Do podsložky **script** uložte soubor **WordCount_Spark.py**. 


### <a name="upload-the-input-file"></a>Nahrání vstupního souboru
1. Vytvořte soubor s názvem **minecraftstory.txt** a nějakým textem. Program Sparku spočítá slova v tomto textu. 
1. Ve složce **spark** vytvořte dílčí složku s názvem **inputfiles**. 
1. Nahrajte do dílčí složky **inputfiles** soubor **minecraftstory.txt**. 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. Spusťte webový prohlížeč **Microsoft Edge** nebo **Google Chrome**. Uživatelské rozhraní služby Data Factory podporují v současnosti jenom webové prohlížeče Microsoft Edge a Google Chrome.
1. Vyberte **Nový** v nabídce vlevo, vyberte **Data a analýzy** a pak vyberte **Datová továrna**. 
   
   ![Výběr datové továrny v podokně Nový](./media/tutorial-transform-data-spark-portal/new-azure-data-factory-menu.png)
1. V podokně **Nová datová továrna** zadejte do pole **Název** text **ADFTutorialDataFactory**. 
      
   ![Podokno Nová datová továrna](./media/tutorial-transform-data-spark-portal/new-azure-data-factory.png)
 
   Název datové továrny Azure musí být *globálně jedinečný*. Pokud se zobrazí následující chyba, změňte název datové továrny. (Použijte třeba název **&lt;vaše_jméno&gt;ADFTutorialDataFactory**). Pravidla pojmenování artefaktů služby Data Factory najdete v článku [Data Factory – pravidla pojmenování](naming-rules.md).
  
   ![Chyba: název není k dispozici](./media/tutorial-transform-data-spark-portal/name-not-available-error.png)
1. Jako **Předplatné** vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
1. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
   - Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků. 
   - Vyberte **Vytvořit novou** a zadejte název skupiny prostředků.   
         
   Některé kroky v tomto rychlém startu vychází z předpokladu, že pro skupinu prostředků použijete název **ADFTutorialResourceGroup**. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
1. Jako **Verzi** vyberte **V2**.
1. Jako **Umístění** vyberte umístění datové továrny. 

   Seznam oblastí Azure, ve kterých je momentálně dostupná Data Factory, vyberte oblasti, které vás zajímají na následující stránce a potom rozbalte **Analytics** najít **služby Data Factory**: [Dostupné produkty v jednotlivých oblastech](https://azure.microsoft.com/global-infrastructure/services/). Úložiště dat (třeba Azure Storage a Azure SQL Database) a výpočetní prostředí (jako HDInsight) používaná službou Data Factory můžou být v jiných oblastech.

1. Vyberte **Vytvořit**.

1. Po vytvoření se zobrazí stránka **Datová továrna**. Kliknutím na dlaždici **Vytvořit a monitorovat** spusťte na samostatné kartě aplikaci uživatelského rozhraní služby Data Factory.

    ![Domovská stránka datové továrny s dlaždici Vytvořit a monitorovat](./media/tutorial-transform-data-spark-portal/data-factory-home-page.png)

## <a name="create-linked-services"></a>Vytvoření propojených služeb
V této části vytvoříte tyto dvě propojené služby: 
    
- **Propojená služba Azure Storage**, která propojí účet služby Azure Storage s datovou továrnou. Toto úložiště používá cluster HDInsight na vyžádání. Obsahuje také skript Sparku, který se má spustit. 
- **Propojená služba HDInsight na vyžádání**. Azure Data Factory automaticky vytvoří cluster HDInsight a spustí program Sparku. Až bude cluster HDInsight zadanou dobu nečinný, odstraní ho. 

### <a name="create-an-azure-storage-linked-service"></a>Vytvoření propojené služby Azure Storage

1. Na stránce **Začínáme** přepněte na levém panelu na kartu **Upravit**. 

   ![Stránka Začínáme](./media/tutorial-transform-data-spark-portal/get-started-page.png)

1. Ve spodní části okna vyberte možnost **Připojení** a potom možnost **+ Nové**. 

   ![Tlačítka pro vytvoření nového připojení](./media/tutorial-transform-data-spark-portal/new-connection.png)
1. V okně **Nová propojená služba** vyberte **Data Store** > **Azure Blob Storage** a potom vyberte **Pokračovat**. 

   ![Výběr dlaždice Azure Blob Storage](./media/tutorial-transform-data-spark-portal/select-azure-storage.png)
1. V seznamu v poli **Název účtu úložiště** vyberte název a potom vyberte **Uložit**. 

   ![Pole pro zadání názvu účtu úložiště](./media/tutorial-transform-data-spark-portal/new-azure-storage-linked-service.png)


### <a name="create-an-on-demand-hdinsight-linked-service"></a>Vytvoření propojené služby HDInsight na vyžádání

1. Znovu vyberte tlačítko **+ Nová** a vytvořte další propojenou službu. 
1. V okně **Nová propojená služba** vyberte **Compute** > **Azure HDInsight** a potom vyberte **Pokračovat**. 

   ![Výběr dlaždice „Azure HDInsight“](./media/tutorial-transform-data-spark-portal/select-azure-hdinsight.png)
1. V okně **Nová propojená služba** proveďte následující kroky: 

   a. Do pole **Název** zadejte **AzureHDInsightLinkedService**.
   
   b. Ověřte, že je v poli **Typ** vybraná možnost **HDInsight na vyžádání**.
   
   c. V poli **Propojená služba Azure Storage** vyberte **AzureStorage1**. Tuto propojenou službu jste vytvořili dříve. Pokud jste použili jiný název, zadejte sem správný název. 
   
   d. V poli **Typ clusteru** vyberte **spark**.
   
   e. V poli **ID instančního objektu** zadejte ID instančního objektu s oprávněním k vytvoření clusteru HDInsight. 
   
      Tento instanční objekt musí být členem role přispěvatele předplatného nebo skupiny prostředků, ve které se cluster vytvoří. Další informace najdete v tématu [Vytvoření aplikace Azure Active Directory a instančního objektu](../active-directory/develop/howto-create-service-principal-portal.md).
   
   f. Do pole **Klíč instančního objektu** zadejte klíč. 
   
   g. V poli **Skupina prostředků** vyberte stejnou skupinu prostředků, kterou jste použili při vytváření datové továrny. Cluster Spark se vytvoří v této skupině prostředků. 
   
   h. Rozbalte **Typ operačního systému**.
   
   i. Zadejte **Jméno uživatele clusteru**. 
   
   j. Zadejte **Heslo clusteru** pro tohoto uživatele. 
   
   k. Vyberte **Finish** (Dokončit). 

   ![Nastavení propojené služby HDInsight](./media/tutorial-transform-data-spark-portal/azure-hdinsight-linked-service-settings.png)

> [!NOTE]
> Azure HDInsight omezuje celkový počet jader, která můžete v jednotlivých podporovaných oblastech Azure použít. V případě propojené služby HDInsight na vyžádání se cluster HDInsight vytvoří ve stejném umístění jako služba Azure Storage, kterou používá jako primární úložiště. Ujistěte se, že máte dostatečné kvóty pro jádra, aby bylo možné cluster úspěšně vytvořit. Další informace najdete v tématu [Nastavení clusterů v HDInsight se systémem Hadoop, Spark, Kafka a dalšími](../hdinsight/hdinsight-hadoop-provision-linux-clusters.md). 

## <a name="create-a-pipeline"></a>Vytvoření kanálu

1. Vyberte tlačítko **+** (plus) a potom v nabídce vyberte **Kanál**.

   ![Tlačítka pro vytvoření nového kanálu](./media/tutorial-transform-data-spark-portal/new-pipeline-menu.png)
1. Na panelu nástrojů **Aktivity** rozbalte **HDInsight**. Přetáhněte aktivitu **Spark** z panelu nástrojů **Aktivity** na plochu návrháře kanálu. 

   ![Přetažení aktivity Spark](./media/tutorial-transform-data-spark-portal/drag-drop-spark-activity.png)
1. Ve vlastnostech v dolní části okna aktivity **Spark** proveďte následující kroky: 

   a. Přepněte na kartu **Cluster HDInsight**.
   
   b. Vyberte službu **AzureHDInsightLinkedService**, kterou jste vytvořili v předchozím kroku. 
        
   ![Zadání propojené služby HDInsight](./media/tutorial-transform-data-spark-portal/select-hdinsight-linked-service.png)
1. Přepněte na kartu **Skripty/Jar** a proveďte následující kroky: 

   a. V poli **Propojená služba úloh** vyberte **AzureStorage1**.
   
   b. Klikněte na **Procházet úložiště**.

   ![Určení skriptu Spark na kartě „Skripty/Jar“](./media/tutorial-transform-data-spark-portal/specify-spark-script.png)
   
   c. Přejděte do složky **adftutorial/spark/script**, vyberte soubor **WordCount_Spark.py** a potom vyberte **Dokončit**.      

1. Pokud chcete kanál ověřit, vyberte tlačítko **Ověřit** na panelu nástrojů. Výběrem tlačítka **>>** (šipka doprava) zavřete okno ověřování. 
    
   ![Tlačítko Ověřit](./media/tutorial-transform-data-spark-portal/validate-button.png)
1. Vyberte **Publikovat vše**. Uživatelské rozhraní služby Data Factory publikuje entity (propojené služby a kanál) do služby Azure Data Factory. 
    
   ![Tlačítko Publikovat vše](./media/tutorial-transform-data-spark-portal/publish-button.png)


## <a name="trigger-a-pipeline-run"></a>Aktivace spuštění kanálu
Vyberte na panelu nástrojů **Aktivační událost** a potom vyberte **Aktivovat**. 

![Tlačítka Aktivační událost a Aktivovat](./media/tutorial-transform-data-spark-portal/trigger-now-menu.png)

## <a name="monitor-the-pipeline-run"></a>Monitorování spuštění kanálu

1. Přepněte na kartu **Monitorování**. Ověřte, že se zobrazuje spuštění kanálu. Vytvoření clusteru Spark trvá přibližně 20 minut. 
   
1. Pravidelně klikejte na **Aktualizovat** a kontrolujte stav spuštění kanálu. 

   ![Karta pro monitorování spuštění kanálu s tlačítkem Aktualizovat](./media/tutorial-transform-data-spark-portal/monitor-tab.png)

1. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte možnost **Zobrazit spuštění aktivit** ve sloupci **Akce**.

   ![Stav spuštění kanálu](./media/tutorial-transform-data-spark-portal/pipeline-run-succeeded.png) 

   Zpátky na zobrazení spuštění kanálu můžete přepnout výběrem odkazu **Kanály** v horní části.

   ![Zobrazení Spuštění aktivit](./media/tutorial-transform-data-spark-portal/activity-runs.png)

## <a name="verify-the-output"></a>Ověření výstupu
Ověřte, že se ve složce spark/otuputfiles/wordcount kontejneru adftutorial vytvořil výstupní soubor. 

![Umístění výstupního souboru](./media/tutorial-transform-data-spark-portal/verity-output.png)

Tento soubor by měl obsahovat všechna slova ze vstupního textového souboru a počet výskytů těchto slov v souboru. Příklad: 

```
(u'This', 1)
(u'a', 1)
(u'is', 1)
(u'test', 1)
(u'file', 1)
```

## <a name="next-steps"></a>Další kroky
Kanál v této ukázce transformuje data pomocí aktivity Sparku a propojené služby HDInsight na vyžádáni. Naučili jste se tyto postupy: 

> [!div class="checklist"]
> * Vytvoření datové továrny 
> * Vytvoření kanálu využívajícího aktivitu Sparku
> * Aktivace spuštění kanálu
> * Monitorování spuštění kanálu

Pokud chcete zjistit, jak transformovat data spuštěním skriptu Hivu v clusteru Azure HDInsight ve virtuální síti, přejděte k následujícímu kurzu: 

> [!div class="nextstepaction"]
> [Kurz: Transformace dat pomocí Hivu ve službě Azure Virtual Network](tutorial-transform-data-hive-virtual-network-portal.md).





