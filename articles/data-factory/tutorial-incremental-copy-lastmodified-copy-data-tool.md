---
title: Nástroj data pro přírůstkové kopírování nových a aktualizovaných souborů
description: Vytvořte datovou továrnu Azure a pak pomocí nástroje Kopírování dat postupně načtěte nové soubory založené na LastModifiedDate.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 25e39d3fc9c56a282714bafb630fee65421fb5ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104606663"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nových a změněných souborů na základě LastModifiedDate pomocí nástroje Kopírování dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu použijete Azure Portal k vytvoření datové továrny. Pak použijete nástroj Kopírování dat k vytvoření kanálu, který přírůstkově kopíruje jenom nové a změněné soubory z Azure Blob Storage do úložiště objektů BLOB v Azure. Používá `LastModifiedDate` k určení, které soubory se mají zkopírovat.

Po dokončení tohoto postupu Azure Data Factory vyhledá všechny soubory ve zdrojovém úložišti, použije filtr souborů podle `LastModifiedDate` a zkopíruje do cílového úložiště pouze soubory, které jsou nové nebo byly od posledního okamžiku aktualizované. Všimněte si, že pokud Data Factory prohledává velký počet souborů, měli byste očekávat i dlouhou dobu trvání. Kontrola souborů je časově náročná, i když se zmenší objem kopírovaných dat.

> [!NOTE]
> Pokud se službou Data Factory teprve začínáte, přečtěte si téma [Úvod do Azure Data Factory](introduction.md).

V tomto kurzu dokončíte tyto úlohy:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Azure Storage účet**: použijte úložiště objektů BLOB pro zdrojová úložiště a úložiště dat jímky. Pokud účet Azure Storage nemáte, postupujte podle pokynů v části [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

## <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů BLOB

Připravte úložiště objektů BLOB pro kurz provedením těchto kroků:

1. Vytvořte kontejner s názvem **source**. K provedení této úlohy můžete použít různé nástroje, například [Průzkumník služby Azure Storage](https://storageexplorer.com/).

2. Vytvořte kontejner s názvem **Destination**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levém podokně vyberte **Vytvořit prostředek**. Vyberte **integrační**  >  **Data Factory**:

   ![Vyberte Data Factory](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být globálně jedinečný. Může se zobrazit tato chybová zpráva:

    :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nová chybová zpráva objektu pro vytváření dat pro duplicitní název.":::

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. V části **předplatné** vyberte předplatné Azure, ve kterém vytvoříte novou datovou továrnu.
4. V části **Skupina prostředků** proveďte jeden z následujících kroků:

    * Vyberte **použít existující** a potom v seznamu vyberte existující skupinu prostředků.

    * Vyberte **vytvořit novou** a zadejte název skupiny prostředků.
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

5. Jako **Verzi** vyberte **V2**.
6. V části **Umístění** vyberte umístění datové továrny. V seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a Azure SQL Database) a výpočetní prostředí (například Azure HDInsight), které vaše Datová továrna používá, můžou být v jiných umístěních a oblastech.
8. Vyberte **Vytvořit**.
9. Po vytvoření objektu pro vytváření dat se zobrazí domovská stránka datové továrny.
10. Pokud chcete na samostatné kartě otevřít Azure Data Factory uživatelské rozhraní (UI), vyberte dlaždici pro **monitorování & autora** :

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte dlaždici **kopírování dat** pro otevření nástroje kopírování dat:

   ![Dlaždice Kopírování dat](./media/doc-common-process/get-started-page.png)

2. Na stránce **vlastnosti** proveďte následující kroky:

    a. V části **název úlohy** zadejte **DeltaCopyFromBlobPipeline**.

    b. V části **úkol tempo nebo plán úlohy** vyberte možnost **spouštět pravidelně podle plánu**.

    c. V části **typ triggeru** vyberte **okno bubnu**.

    d. V části **opakování** zadejte **15 minut**.

    e. Vyberte **Další**.

    Data Factory vytvoří kanál se zadaným názvem úlohy.

    ![Stránka vlastností kopírování dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stránce **zdrojové úložiště dat** proveďte tyto kroky:

    a. Vyberte  **vytvořit nové připojení** a přidejte připojení.

    b. Z Galerie vyberte **Azure Blob Storage** a pak vyberte **pokračovat**:

    ![Výběr služby Azure blog Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na stránce **Nová propojená služba (Azure Blob Storage)** vyberte svůj účet úložiště ze seznamu **název účtu úložiště** . Otestujte připojení a pak vyberte **vytvořit**.

    d. Vyberte novou propojenou službu a potom vyberte **Další**:

   ![Výběr nové propojené služby](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

    a. Vyhledejte a vyberte **zdrojovou** složku a pak vyberte **zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. V části **chování načítání souborů** vyberte **přírůstkové načtení: LastModifiedDate**.

    c. Vyberte **binární kopii** a potom vyberte **Další**:

     ![Výběr stránky vstupního souboru nebo složky](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stránce **cílové úložiště dat** vyberte službu **AzureBlobStorage** , kterou jste vytvořili. Toto je stejný účet úložiště jako zdrojové úložiště dat. Pak vyberte **Další**.

6. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) proveďte následující kroky:

    a. Vyhledejte a vyberte **cílovou** složku a pak vyberte **zvolit**:

    ![Vybrat stránku výstupního souboru nebo složky](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Vyberte **Další**.

7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).

8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Aplikace se přepne na kartu **monitorování** . Zobrazí se stav kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. Pokud chcete zobrazit podrobnosti o spuštění aktivit nebo znovu spustit kanál, vyberte odkaz v části **název kanálu** .

    ![Aktualizuje seznam a zobrazí podrobnosti o spuštění aktivit.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs-1.png)

11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **název aktivity** . Podrobnosti o vlastnostech najdete v tématu [Přehled aktivit kopírování](copy-activity-overview.md).

    ![Aktivita kopírování v kanálu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Vzhledem k tomu, že ve zdrojovém kontejneru úložiště objektů BLOB nejsou žádné soubory, nezobrazí se žádné soubory zkopírované do cílového kontejneru v účtu:

    ![Ve zdrojovém kontejneru nebo cílovém kontejneru nejsou žádné soubory.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Vytvořte prázdný textový soubor a pojmenujte ho **file1.txt**. Nahrajte tento textový soubor do zdrojového kontejneru v účtu úložiště. K provedení těchto úloh, například [Průzkumník služby Azure Storage](https://storageexplorer.com/), můžete použít různé nástroje.

    ![Vytvoření file1.txt a nahrání do zdrojového kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Pokud se chcete vrátit do zobrazení **spuštění kanálu** , vyberte **všechna spuštění kanálu** a počkejte, až se znovu automaticky aktivuje stejný kanál.  

14. Po dokončení druhého kanálu postupujte podle výše uvedených kroků a podívejte se na podrobnosti o spuštění aktivit.  

    Uvidíte, že jeden soubor (file1.txt) se zkopíroval ze zdrojového kontejneru do cílového kontejneru vašeho účtu úložiště objektů BLOB:

    ![file1.txt se zkopíroval ze zdrojového kontejneru do cílového kontejneru.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Vytvořte další prázdný textový soubor a pojmenujte ho **file2.txt**. Nahrajte tento textový soubor do zdrojového kontejneru v účtu služby Blob Storage.

16. Opakujte kroky 13 a 14 pro druhý textový soubor. Uvidíte, že během tohoto spuštění kanálu se do cílového kontejneru účtu úložiště zkopíroval jenom nový soubor (file2.txt).  

    Můžete také ověřit, zda byl zkopírován pouze jeden soubor pomocí [Průzkumník služby Azure Storage](https://storageexplorer.com/) k prohledání souborů:

    ![Kontrolovat soubory pomocí Průzkumník služby Azure Storage](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět, jak transformovat data pomocí Apache Spark clusteru v Azure, přečtěte si následující kurz:

> [!div class="nextstepaction"]
>[Transformace dat v cloudu pomocí Apache Sparkho clusteru](tutorial-transform-data-spark-portal.md)
