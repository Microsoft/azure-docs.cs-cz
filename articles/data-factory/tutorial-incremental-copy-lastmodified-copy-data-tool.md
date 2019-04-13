---
title: Přírůstkové kopírování nové a změněné soubory, které jsou založené na LastModifiedDate pomocí nástroje pro kopírování dat | Dokumentace Microsoftu
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje kopírování dat pro přírůstkové kopírování nových souborů podle LastModifiedDate.
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
ms.openlocfilehash: 8308190e0e68365343fb50ca33f9bea75c3e4e66
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2019
ms.locfileid: "59544479"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nové a změněné soubory, které jsou založené na LastModifiedDate pomocí nástroje pro kopírování dat

V tomto kurzu použijete Azure portal k vytvoření datové továrny. Pak použijete nástroj pro kopírování dat vytvoříte kanál, který přírůstkově kopíruje nové a změněné soubory, na základě jejich **LastModifiedDate** z úložiště objektů Blob Azure do Azure Blob storage.

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující úlohy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure**: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: Použití služby Blob storage jako _zdroj_ a _jímky_ úložiště. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-quickstart-create-account.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů Blob

Připravte úložiště objektů Blob pro tento kurz provedením následujících kroků.

1. Vytvořte kontejner s názvem **zdroj**. Můžete použít různé nástroje k provedení této úlohy, jako například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

2. Vytvořte kontejner s názvem **cílové**. 

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **Data a analýzy** > **služby Data Factory**: 
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**. 
      
     ![Nová datová továrna](./media/tutorial-copy-data-tool/new-azure-data-factory.png)
 
   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:
   
   ![Nová datová továrna – chybová zpráva](./media/tutorial-copy-data-tool/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte Azure **předplatné** ve kterém vytvoříte nové datové továrny. 
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:
     
    * Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    * Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).

5. V části **verze**vyberte **V2**.
6. V části **Umístění** vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Zaškrtněte **Připnout na řídicí panel**. 
8. Vyberte **Vytvořit**.
9. Na řídicím panelu najdete **nasazování datové továrny** dlaždici zobrazíte stav procesu.

    ![Dlaždice nasazování datové továrny](media/tutorial-copy-data-tool/deploying-data-factory.png)
10. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
   
    ![Domovská stránka datové továrny](./media/tutorial-copy-data-tool/data-factory-home-page.png)
11. Azure Data Factory uživatelské rozhraní (UI) na samostatné kartě otevřít, vyberte **vytvořit a monitorovat** dlaždici. 

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na **pusťme se do práce** stránky, vyberte **kopírování dat** nadpis můžete otevřít nástroj pro kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/tutorial-copy-data-tool/copy-data-tool-tile.png)
   
2. Na **vlastnosti** stránce, proveďte následující kroky:

    a. V části **název úkolu**, zadejte **DeltaCopyFromBlobPipeline**.

    b. V části **tempo úkolu** nebo **plán úlohy**vyberte **pravidelně spouštět podle plánu**.

    c. V části **typ aktivační události**vyberte **Přeskakujícího okna**.
    
    d. V části **opakování**, zadejte **15 minut**. 
    
    e. Vyberte **Další**. 
    
    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy. 

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)
    
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Vyberte **+ vytvořit nové připojení**, můžete přidat připojení.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page.png)

    b. Vyberte **Azure Blob Storage** z galerie a pak vyberte **pokračovat**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na **Nová propojená služba** stránky, vyberte svůj účet úložiště z **název účtu úložiště** seznamu a pak vyberte **Dokončit**.
    
    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-linkedservice.png)
    
    d. Vyberte nově vytvořený propojenou službu a pak vyberte **Další**. 
    
   ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:
    
    a. Procházet a vybrat **zdroj** složku a pak vyberte **zvolit**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)
    
    b. V části **chování načítání souboru**vyberte **přírůstkové načítání: LastModifiedDate**.
    
    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-loading-behavior.png)
    
    c. Zkontrolujte **binární kopie** a vyberte **Další**.
    
     ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)
     
5. Na **cílového úložiště dat** stránce **službě Azure BLOB Storage**. Tento stejný účet úložiště jako zdrojové úložiště dat je. Pak vyberte **Další**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/destination-data-store-page-select-linkedservice.png)
    
6. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) proveďte následující kroky:
    
    a. Procházet a vybrat **cílové** složku a pak vyberte **zvolit**.
    
    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)
    
    b. Vyberte **Další**.
    
     ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/click-next-after-output-folder.png)
    
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další). 

    ![Stránka Nastavení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/settings-page.png)
    
8. Na **Souhrn** stránky, zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)
    
9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)
    
10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu. Vyberte **aktualizovat** aktualizujte seznam a vyberte **zobrazit spuštění aktivit** propojit **akce** sloupec. 

    ![Aktualizujte seznam a vyberte Zobrazit spuštění aktivit](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. Existuje pouze jednu aktivitu (aktivita kopírování) v kanálu, takže se zobrazí pouze jedna položka. Podrobnosti o operaci kopírování zobrazíte výběrem odkazu **Podrobnosti** (ikona brýlí) ve sloupci **Akce**. 

    ![Aktivitu kopírování, která je v kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)
    
    Protože neexistuje žádný soubor v **zdroj** kontejneru v účtu Blob storage, se nezobrazí všechny soubory zkopírovány do **cílové** kontejneru v účtu Blob storage.
    
    ![Žádný soubor v kontejneru zdrojový nebo cílový kontejner](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)
    
12. Vytvořte prázdný textový soubor s názvem **file1.txt**. Tento textový soubor k nahrání **zdroj** kontejneru v účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).   

    ![File1.txt vytvoříte a nahrajete do zdrojového kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)
    
13. Chcete přejít zpátky k **spuštění kanálu** zobrazit, vyberte možnost **všechna spuštění kanálu**a počkejte, až se znovu automaticky spustí stejný kanálu.  

    ![Vyberte všechna spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Vyberte **zobrazení spuštění aktivit** pro druhý kanál spustí, když se zobrazí. Pak zkontrolujte podrobnosti stejným způsobem, který jste to udělali pro první spuštění kanálu.  

    ![Vyberte zobrazení spuštění aktivit zkontrolujte podrobnosti](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs5.png)

    Bude tématu jeden soubor (file1.txt) byl zkopírován z **zdroj** kontejneru **cílové** kontejneru účtu úložiště objektů Blob.
    
    ![File1.txt byl zkopírován z kontejneru zdroje k cílovému kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)
    
15. Vytvořte další prázdný textový soubor s názvem **Soubor2.txt**. Tento textový soubor k nahrání **zdroj** kontejneru v účtu Blob storage.   
    
16. Opakujte kroky 13 a 14 pro tento druhý textový soubor. Uvidíte, že pouze nový soubor (Soubor2.txt) byl zkopírován z **zdroj** kontejneru **cílové** kontejneru účtu úložiště v další spuštění kanálu.  
    
    ![Soubor2.txt byl zkopírován z kontejneru zdroje k cílovému kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs7.png)

    Můžete to taky ověřit pomocí [Průzkumníka služby Azure Storage](https://storageexplorer.com/) kontrolovala soubory.
    
    ![Kontrolovat soubory pomocí Průzkumníka služby Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)

    
## <a name="next-steps"></a>Další postup
Přejděte k následujícímu kurzu se dozvíte o transformaci dat pomocí clusteru Apache Spark v Azure:

> [!div class="nextstepaction"]
>[Transformace dat v cloudu s využitím clusteru Apache Spark](tutorial-transform-data-spark-portal.md)