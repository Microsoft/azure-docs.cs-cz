---
title: Incrementally copy new files based on time partitioned file name
description: Create an Azure data factory and then use the Copy Data tool to incrementally load new files only based on time partitioned file name.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: 746b5cbcc58f6c722623446227417e6c94dd0a80
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74217451"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Incrementally copy new files based on time partitioned file name by using the Copy Data tool

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Then, you use the Copy Data tool to create a pipeline that incrementally copies new files based on time partitioned file name from Azure Blob storage to Azure Blob storage. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Azure storage account**: Use Blob storage as the _source_  and _sink_ data store. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Create two containers in Blob storage

Prepare your Blob storage for the tutorial by performing these steps.

1. Create a container named **source**.  Create a folder path as **2019/02/26/14** in your container. Create an empty text file, and name it as **file1.txt**. Upload the file1.txt to the folder path **source/2019/02/26/14** in your storage account.  K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).
    
    ![upload files](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Please adjust the folder name with your UTC time.  For example, if the current UTC time is 2:03 PM on Feb 26th, 2019, you can create the folder path as **source/2019/02/26/14/** by the rule of **source/{Year}/{Month}/{Day}/{Hour}/** .

2. Create a container named **destination**. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. On the left menu, select **Create a resource** > **Data + Analytics** > **Data Factory**: 
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
    
    Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/doc-common-process/name-not-available-error.png)
   
   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_ **ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).

5. V části **Verze** vyberte **V2**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Create** (Vytvořit).
9. Na řídicím panelu se na dlaždici **Nasazování datové továrny** zobrazí stav zpracování.

    ![Dlaždice Nasazování datové továrny](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka datové továrny](./media/doc-common-process/data-factory-home-page.png)
11. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. On the **Let's get started** page, select the **Copy Data** title to launch the Copy Data tool. 

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)
   
2. On the **Properties** page, take the following steps:

    a. Under **Task name**, enter **DeltaCopyFromBlobPipeline**.

    b. Under **Task cadence or Task schedule**, select **Run regularly on schedule**.

    c. Under **Trigger type**, select **Tumbling Window**.
    
    d. Under **Recurrence**, enter **1 Hour(s)** . 
    
    e. Vyberte **Další**. 
    
    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Click  **+ Create new connection**, to add a connection.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Select **Azure Blob Storage** from the gallery, and then click **Continue**.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. On the **New Linked Service** page, select your storage account from the **Storage account name** list, and then click **Finish**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Select the newly created linked service, then click **Next**. 
    
   ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Na stránce **Zvolte vstupní soubor nebo složku** proveďte následující kroky:
    
    a. Browse and select the **source** container, then select **Choose**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. Under **File loading behavior**, select **Incremental load: time-partitioned folder/file names**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Write the dynamic folder path as **source/{year}/{month}/{day}/{hour}/** , and change the format as followings:
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Check **Binary copy** and click **Next**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. On the **Destination data store** page, select the **AzureBlobStorage**, which is the same storage account as data source store, and then click **Next**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. On the **Choose the output file or folder** page, do the following steps:
    
    a. Browse and select the **destination** folder, then click **Choose**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Write the dynamic folder path as **source/{year}/{month}/{day}/{hour}/** , and change the format as followings:
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Klikněte na **Další**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).
    ![Deployment page](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**.  You need wait for the pipeline run when it is triggered automatically (about after one hour).  When it runs, the **Actions** column includes links to view activity run details and to rerun the pipeline. Select **Refresh** to refresh the list, and select the **View Activity Runs** link in the **Actions** column. 

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. You can see the source file (file1.txt) has been copied from  **source/2019/02/26/14/**  to **destination/2019/02/26/14/** with the same file name.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    You can also verify the same by using Azure Storage Explorer (https://storageexplorer.com/) to scan the files.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Create another empty text file with the new name as **file2.txt**. Upload the file2.txt file to the folder path **source/2019/02/26/15** in your storage account.   K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).   
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > You might be aware that a new folder path is required to be created. Please adjust the folder name with your UTC time.  For example, if the current UTC time is 3:20 PM on Feb 26th, 2019, you can create the folder path as **source/2019/02/26/15/** by the rule of **{Year}/{Month}/{Day}/{Hour}/** .
    
13. To go back to the **Pipeline Runs** view, select **All Pipelines Runs**, and wait for the same pipeline being triggered again automatically after another one hour.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Select **View Activity Run** for the second pipeline run when it comes, and do the same to review details.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    You can see the source file (file2.txt) has been copied from  **source/2019/02/26/15/**  to **destination/2019/02/26/15/** with the same file name.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    You can also verify the same by using Azure Storage Explorer (https://storageexplorer.com/) to scan the files in **destination** container
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-portal.md)