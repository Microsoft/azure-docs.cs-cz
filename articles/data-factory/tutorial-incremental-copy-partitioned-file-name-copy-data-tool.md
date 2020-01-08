---
title: Přírůstkové kopírování nových souborů na základě času názvu souboru rozděleného na oddíly
description: Vytvořte datovou továrnu Azure a pak použijte nástroj Kopírování dat pro přírůstkové načtení nových souborů jenom na základě názvu souboru s oddíly.
services: data-factory
documentationcenter: ''
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 1/24/2019
ms.openlocfilehash: b6826fa53e9810bdd30af4faf9fbf6bc3c3492e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75439255"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nových souborů na základě času názvu souboru rozděleného do oddílů pomocí nástroje Kopírování dat

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak použijete nástroj Kopírování dat k vytvoření kanálu, který postupně kopíruje nové soubory na základě času názvu souboru děleného z Azure Blob Storage do úložiště objektů BLOB v Azure. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: jako _zdroj_ dat a úložiště dat _jímky_ použijte úložiště objektů BLOB. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů BLOB

Provedením těchto kroků Připravte úložiště objektů BLOB pro tento kurz.

1. Vytvořte kontejner s názvem **source**.  Vytvořte v kontejneru cestu ke složce jako **2019/02/26/14** . Vytvořte prázdný textový soubor a pojmenujte ho jako **Soubor1. txt**. Nahrajte adresář Soubor1. txt do složky Path **source/2019/02/26/14** do svého účtu úložiště.  K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).
    
    ![nahrání souborů](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)
    
    > [!NOTE]
    > Upravte prosím název složky časem UTC.  Pokud je například aktuální čas UTC 2:03 ODP. 26 2019, můžete vytvořit cestu ke složce jako **source/2019/02/26/14/** podle pravidla **zdroje/{Year}/{Month}/{Day}/{Hour}/** .

2. Vytvořte kontejner s názvem **Destination**. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **data a analýzy** > **Data Factory**: 
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
    
    Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/doc-common-process/name-not-available-error.png)
   
   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_ **ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

5. V části **Verze** vyberte **V2**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Vytvořit**.
9. Na řídicím panelu se na dlaždici **Nasazování datové továrny** zobrazí stav zpracování.

    ![Dlaždice Nasazování datové továrny](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)
11. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte **kopírování dat** název pro spuštění nástroje kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)
   
2. Na stránce **vlastnosti** proveďte následující kroky:

    a. V části **název úlohy**zadejte **DeltaCopyFromBlobPipeline**.

    b. V části **úkol tempo nebo plán úlohy**vyberte možnost **spouštět pravidelně podle plánu**.

    c. V části **typ triggeru**vyberte **okno bubnu**.
    
    d. V části **opakování**zadejte **1 hodina (e)** . 
    
    e. Vyberte **Next** (Další). 
    
    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ vytvořit nové připojení**přidejte připojení.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page.png)
    
    b. Z Galerie vyberte **Azure Blob Storage** a potom klikněte na **pokračovat**.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-blob.png)
    
    c. Na stránce **Nová propojená služba** vyberte v seznamu **název účtu úložiště** svůj účet úložiště a pak klikněte na **Dokončit**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Vyberte nově vytvořenou propojenou službu a pak klikněte na **Další**. 
    
   ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-select-linkedservice.png)
4. Na stránce **Zvolte vstupní soubor nebo složku** proveďte následující kroky:
    
    a. Procházejte a vyberte **zdrojový** kontejner a pak vyberte **Vybrat.**
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)
    
    b. V části **chování při načítání souborů**vyberte **přírůstkové načtení: čas-dělené složky nebo názvy souborů**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-loading-behavior.png)
    
    c. Zapište cestu k dynamické složce jako **zdroj/{Year}/{Month}/{Day}/{Hour}/** a změňte formát následujícím způsobem:
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name.png)
    
    d. Ověřte **binární kopii** a klikněte na tlačítko **Další**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     
5. Na stránce **cílové úložiště dat** vyberte **AzureBlobStorage**, což je stejný účet úložiště jako úložiště zdroje dat, a potom klikněte na **Další**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png) 
6. Na stránce **zvolit výstupní soubor nebo složku** proveďte následující kroky:
    
    a. Vyhledejte a vyberte **cílovou** složku a pak klikněte na **zvolit**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-output-file-folder.png)   
    
    b. Zapište cestu k dynamické složce jako **zdroj/{Year}/{Month}/{Day}/{Hour}/** a změňte formát následujícím způsobem:
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/input-file-name2.png)    
    
    c. Klikněte na **Další**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)  
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/settings-page.png)  
8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)
    
9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).
    Stránka nasazení ![](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)
    
10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**.  Musíte počkat na spuštění kanálu, když se aktivuje automaticky (přibližně po jedné hodině).  Když se spustí, sloupec **Actions (akce** ) obsahuje odkazy na zobrazení podrobností o spuštění aktivit a opětovné spuštění kanálu. Vyberte **aktualizovat** a aktualizujte seznam a vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . 

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Můžete zobrazit zdrojový soubor (Soubor1. txt), který jste zkopírovali ze **zdrojového/2019/02/26/14/** do **cílového/2019/02/26/14/** se stejným názvem souboru.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)
    
    Stejný postup můžete také ověřit pomocí Průzkumník služby Azure Storage (https://storageexplorer.com/) k prohledání souborů.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)
12. Vytvořte další prázdný textový soubor s novým názvem jako **Soubor2. txt**. Nahrajte soubor Soubor2. txt do složky Path **source/2019/02/26/15** ve svém účtu úložiště.   K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).   
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs4.png)
    
    > [!NOTE]
    > Možná víte, že je potřeba vytvořit novou cestu ke složce. Upravte prosím název složky časem UTC.  Pokud je například aktuální čas UTC 3:20 odp. 26 2019, můžete vytvořit cestu ke složce jako **source/2019/02/26/15/** podle pravidla **{Year}/{Month}/{Day}/{Hour}/** .
    
13. Pokud se chcete vrátit do zobrazení **spuštění kanálu** , vyberte **všechny spuštěné kanály**a počkejte, až se znovu aktivuje stejný kanál, a to za jinou hodinu.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Vyberte možnost **Zobrazit spuštění aktivit** pro druhý běh kanálu, pokud je k dispozici, a proveďte stejnou kontrolu podrobností.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs6.png)
    
    Zdrojový soubor (Soubor2. txt) si můžete prohlédnout ze **zdroje/2019/02/26/15/** do **cílového/2019/02/02/15/** se stejným názvem souboru.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs7.png) 
    
    Stejný postup můžete také ověřit pomocí Průzkumník služby Azure Storage (https://storageexplorer.com/) pro skenování souborů v **cílovém** kontejneru
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-portal.md)