---
title: Přírůstkové kopírování nových a změněných souborů pomocí služby Azure Data Factory pouze podle LastModifiedDate | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure a pak použít nástroj pro kopírování dat k přírůstkovému načtení pouze podle LastModifiedDate nové soubory.
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
ms.date: 1/24/2019
ms.openlocfilehash: d79b44d0123d64d6280939767e5df7b5f64a5fcb
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2019
ms.locfileid: "58445961"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nové a změněné soubory, které jsou založené na LastModifiedDate pomocí nástroje pro kopírování dat

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak použijete nástroj pro kopírování dat vytvoříte kanál, který přírůstkově kopíruje nové a změněné soubory pouze podle jejich "LastModifiedDate" z úložiště objektů Blob Azure do Azure Blob storage. 

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: Použití služby Blob storage jako _zdroj_ a _jímky_ úložiště. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů Blob

Připravte úložiště objektů Blob pro tento kurz provedením následujících kroků.

1. Vytvořte kontejner s názvem **zdroj**. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

2. Vytvořte kontejner s názvem **cílové**. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **Data a analýzy** > **služby Data Factory**: 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
      
     ![Nová datová továrna](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).

5. V části **Verze** vyberte **V2**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Vytvořit**.
9. Na řídicím panelu se na dlaždici **Nasazování datové továrny** zobrazí stav zpracování.

    ![Dlaždice Nasazování datové továrny](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka datové továrny](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na **pusťme se do práce** stránky, vyberte **kopírování dat** title ke spuštění nástroje pro kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Na **vlastnosti** stránce, proveďte následující kroky:

    a. V části **název úkolu**, zadejte **DeltaCopyFromBlobPipeline**.

    b. V části **tempo úkolu nebo plán úloh**vyberte **pravidelně spouštět podle plánu**.

    c. V části **aktivujte typ**vyberte **Přeskakujícího okna**.
    
    d. V části **opakování**, zadejte **15 minut**. 
    
    e. Vyberte **Další**. 
    
    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Klikněte na tlačítko **+ vytvořit nové připojení**, můžete přidat připojení.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Vyberte **Azure Blob Storage** z galerie a pak klikněte na tlačítko **pokračovat**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na **Nová propojená služba** stránky, vyberte svůj účet úložiště z **název účtu úložiště** seznamu a potom klikněte na tlačítko **Dokončit**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Vyberte nově vytvořený propojenou službu a pak klikněte na **Další**. 
    
   ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stránce **Zvolte vstupní soubor nebo složku** proveďte následující kroky:
    
    a. Procházet a vybrat **zdroj** složky, klikněte na **zvolit**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. V části **chování načítání souboru**vyberte **přírůstkové načítání: LastModifiedDate**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Zkontrolujte **binární kopie** a klikněte na tlačítko **Další**.
    
     ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Na **cílového úložiště dat** stránky, vyberte **službě Azure BLOB Storage** tedy stejné úložiště účtu jako úložiště zdroje dat a klikněte na **Další**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na **zvolte výstupní soubor nebo složku** stránce, proveďte následující kroky:
    
    a. Procházet a vybrat **cílové** složky, klikněte na **zvolit**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Klikněte na **Další**.
    
     ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu. Vyberte **aktualizovat** aktualizujte seznam a vyberte **zobrazit spuštění aktivit** propojit **akce** sloupec. 

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. 

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Vzhledem k tomu, že neexistuje žádný soubor v **zdroj** kontejneru v účtu blob storage, takže se nezobrazí jakéhokoli souboru, který byl zkopírován do **cílové** kontejneru v účtu blob storage.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Vytvořte prázdný textový soubor a pojmenujte ji jako file1.txt. Nahrát soubor file1.txt **zdroj** kontejneru v účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).   

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Chcete přejít zpátky k **spuštění kanálu** zobrazit, vyberte možnost **všechna spuštění kanálů**a počkat na stejném kanálu se automaticky znovu aktivuje.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Vyberte **zobrazení spuštění aktivit** IF druhého spuštění kanálu můžete vidět, že pochází a totéž chcete podívat na podrobnosti.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Zobrazí jeden soubor (file1.txt) byl zkopírován z **zdroj** kontejneru **cílové** kontejneru účtu úložiště.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Vytvořte jiný prázdný textový soubor a pojmenujte ji jako Soubor2.txt. Nahrát soubor Soubor2.txt **zdroj** kontejneru v účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).  
    
16. Totéž jako krok 13 a 14 a uvidíte pouze nový soubor (Soubor2.txt) byl zkopírován z **zdroj** kontejneru **cílové** kontejneru účtu úložiště v další spuštění kanálu.  
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Stejné můžete také ověřit pomocí Průzkumníka služby Azure Storage (https://storageexplorer.com/) kontrolovala soubory.
    
    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Další postup
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-portal.md)