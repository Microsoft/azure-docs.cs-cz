---
title: Datový nástroj pro postupné kopírování nových a aktualizovaných souborů
description: Vytvořte azure data factory a pak pomocí nástroje Kopírovat data postupně načíst nové soubory na základě LastModifiedDate.
services: data-factory
author: dearandyxu
ms.author: yexu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.devlang: na
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 3/18/2020
ms.openlocfilehash: 3098ca0d3d5e41c298d3058ffa84fcf129648281
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399485"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Postupně kopírovat nové a změněné soubory založené na LastModifiedDate pomocí nástroje Kopírovat data

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto kurzu použijete portál Azure k vytvoření datové továrny. Pak použijete nástroj Kopírovat data k vytvoření kanálu, který postupně kopíruje pouze nové a změněné soubory, z úložiště objektů Blob Azure do úložiště objektů Blob Azure. Používá `LastModifiedDate` se k určení, které soubory kopírovat.

Po dokončení kroků zde Azure Data Factory prohledá všechny soubory ve zdrojovém `LastModifiedDate`úložišti, použije filtr souborů podle a zkopíruje do cílového úložiště pouze soubory, které jsou nové nebo byly aktualizovány od poslednídoby. Všimněte si, že pokud Data Factory prohledá velký počet souborů, měli byste stále očekávat dlouhé trvání. Prohledávání souborů je časově náročné, i když se sníží množství zkopírovaných dat.

> [!NOTE]
> Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md).

V tomto kurzu dokončíte tyto úkoly:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet Azure Storage**: Použití úložiště objektů blob pro zdrojové úložiště a úložiště jímek. Pokud nemáte účet Azure Storage, postupujte podle pokynů v části [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů Blob

Příprava úložiště objektů Blob na kurz takto:

1. Vytvořte kontejner s názvem **zdroj**. K provedení tohoto úkolu můžete použít různé nástroje, jako je [Průzkumník úložiště Azure](https://storageexplorer.com/).

2. Vytvořte kontejner s názvem **cíl**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levém podokně vyberte **Vytvořit prostředek**. Vyberte **Analytics** > **Data Factory**:

   ![Vybrat datovou továrnu](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být globálně jedinečný. Může se zobrazit tato chybová zpráva:

   ![Název není k dispozici chybová zpráva](./media/doc-common-process/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. V části **Předplatné**vyberte předplatné Azure, ve kterém vytvoříte novou datovou továrnu.
4. V části **Skupina zdrojů**provázte jeden z těchto kroků:

    * Vyberte **Použít existující** a pak vyberte existující skupinu prostředků v seznamu.

    * Vyberte **Vytvořit nový** a zadejte název skupiny prostředků.
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

5. Jako **Verzi** vyberte **V2**.
6. V části **Umístění** vyberte umístění datové továrny. V seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a Azure SQL Database) a výpočetní prostředky (například Azure HDInsight), které používá vaše továrna na data, můžou být v jiných umístěních a oblastech.
8. Vyberte **Vytvořit**.
9. Po vytvoření datové továrny se zobrazí domovská stránka datové továrny.
10. Pokud chcete otevřít uživatelské rozhraní Azure Data Factory (UI) na samostatné kartě, vyberte dlaždici **Author & Monitor:**

    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data** a otevřete nástroj Kopírovat data:

   ![Kopírovat dlaždici Dat](./media/doc-common-process/get-started-page.png)

2. Na stránce **Vlastnosti** postupujte takto:

    a. V **části Název úkolu**zadejte **DeltaCopyFromBlobPipeline**.

    b. V **části Kadence úloh nebo Plán úloh**vyberte pravidelně **spouštět podle plánu**.

    c. V části **Typ aktivační události**vyberte okno **Omílání**.

    d. V části **Opakování**zadejte **15 minut**.

    e. Vyberte **Další**.

    Data Factory vytvoří kanál se zadaným názvem úkolu.

    ![Kopírovat stránku vlastností dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stránce **Zdrojové úložiště dat** proveďte tyto kroky:

    a. Chcete-li přidat připojení, vyberte **Vytvořit nové připojení.**

    b. V galerii vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**:

    ![Vyberte azure blogové úložiště](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na stránce **Nová propojená služba (Azure Blob Storage)** vyberte svůj účet úložiště ze seznamu **názvů účtů úložiště.** Otestujte připojení a pak vyberte **Vytvořit**.

    d. Vyberte novou propojenou službu a pak vyberte **Další**:

   ![Vyberte novou propojenou službu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

    a. Vyhledejte a vyberte **zdrojovou** složku a pak vyberte **Vybrat**.

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. V části **Chování načítání souboru**vyberte **Přírůstkové načtení: LastModifiedDate**.

    c. Vyberte **Binární kopírování** a pak vyberte **Další**:

     ![Výběr vstupního souboru nebo stránky složky](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stránce **Cílové úložiště dat** vyberte službu **AzureBlobStorage,** kterou jste vytvořili. Jedná se o stejný účet úložiště jako zdrojové úložiště dat. Pak vyberte **Další**.

6. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) proveďte následující kroky:

    a. Vyhledejte a vyberte **cílovou** složku a pak vyberte **Vybrat**:

    ![Volba výstupního souboru nebo stránky složky](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Vyberte **Další**.

7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).

8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Aplikace se přepne na kartu **Monitor.** Zobrazí se stav kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. Vyberte odkaz v části **NÁZEV KANÁLU,** chcete-li zobrazit podrobnosti spuštění aktivity nebo znovu spustit kanál.

    ![Aktualizace podrobností o spuštění seznamu a zobrazení aktivity](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. V kanálu je pouze jedna aktivita (aktivita kopírování), takže se zobrazí pouze jedna položka. Podrobnosti o operaci kopírování vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **NÁZEV AKTIVITY.** Podrobnosti o vlastnostech naleznete v tématu [Kopírování přehledu aktivity](copy-activity-overview.md).

    ![Kopírování aktivity v kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Vzhledem k tomu, že ve zdrojovém kontejneru v účtu úložiště objektů Blob nejsou žádné soubory, neuvidíte žádné soubory zkopírované do cílového kontejneru v účtu:

    ![Žádné soubory ve zdrojovém nebo cílovém kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Vytvořte prázdný textový soubor a pojmenujte jej **file1.txt**. Nahrajte tento textový soubor do zdrojového kontejneru ve vašem účtu úložiště. K provádění těchto úloh můžete použít různé nástroje, jako je [Průzkumník úložiště Azure](https://storageexplorer.com/).

    ![Vytvoření souborufile1.txt a jeho nahrání do zdrojového kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Chcete-li se vrátit zpět do zobrazení **spuštění kanálu,** vyberte **možnost Všechny spuštění kanálu**a počkejte, až se stejný kanál znovu automaticky spustí.  

    ![Vybrat všechny spuštění kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Po dokončení druhého spuštění kanálu postupujte podle stejných kroků uvedených výše a zkontrolujte podrobnosti spuštění aktivity.  

    Uvidíte, že jeden soubor (file1.txt) byl zkopírován ze zdrojového kontejneru do cílového kontejneru vašeho účtu úložiště objektů Blob:

    ![soubor file1.txt byl zkopírován ze zdrojového kontejneru do cílového kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Vytvořte další prázdný textový soubor a pojmenujte jej **file2.txt**. Nahrajte tento textový soubor do zdrojového kontejneru v účtu úložiště objektů Blob.

16. Opakujte kroky 13 a 14 pro druhý textový soubor. Uvidíte, že pouze nový soubor (file2.txt) byl zkopírován ze zdrojového kontejneru do cílového kontejneru vašeho účtu úložiště během tohoto kanálu spustit.  

    Můžete také ověřit, že byl zkopírován pouze jeden soubor pomocí [Průzkumníka úložiště Azure](https://storageexplorer.com/) ke skenování souborů:

    ![Prohledává soubory pomocí Průzkumníka úložiště Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Další kroky
Přejděte na následující kurz, kde se dozvíte, jak transformovat data pomocí clusteru Apache Spark v Azure:

> [!div class="nextstepaction"]
>[Transformace dat v cloudu pomocí clusteru Apache Spark](tutorial-transform-data-spark-portal.md)
