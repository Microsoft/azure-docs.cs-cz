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
ms.openlocfilehash: 743f9dd8f7998178a75d716f4da22efee2b3bc79
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131125"
---
# <a name="incrementally-copy-new-and-changed-files-based-on-lastmodifieddate-by-using-the-copy-data-tool"></a>Postupně kopírovat nové a změněné soubory založené na LastModifiedDate pomocí nástroje Kopírovat data

V tomto kurzu použijete portál Azure k vytvoření datové továrny. Potom použijete nástroj Kopírovat data k vytvoření kanálu, který postupně kopíruje pouze nové a změněné soubory na základě jejich **LastModifiedDate** z úložiště objektů blob Azure do úložiště objektů Blob Azure.

Tímto způsobem adf prohledá všechny soubory ze zdrojového úložiště, použije filtr souborů podle jejich LastModifiedDate a zkopíruje nový a aktualizovaný soubor pouze od posledního okamžiku do cílového úložiště.  Vezměte prosím na vědomí, že pokud necháte ADF skenovat obrovské množství souborů, ale pouze zkopírovat několik souborů na místo určení, stále byste očekávat, že dlouhá doba trvání v důsledku skenování souborů je časově náročné stejně.   

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu provedete následující úkoly:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: Použijte úložiště objektů blob jako _zdroj_ ové úložiště a úložiště dat _jímky._ Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů Blob

Připravte úložiště objektů Blob pro kurz provedením těchto kroků.

1. Vytvořte kontejner s názvem **zdroj**. K provedení tohoto úkolu můžete použít různé nástroje, například [Průzkumník a údržbu zařízení Azure](https://storageexplorer.com/).

2. Vytvořte kontejner s názvem **cíl**.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte **Vytvořit data o prostředku** > **+ Analytics** > **Data Factory**:

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

   Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:

   ![Nová datová továrna – chybová zpráva](./media/doc-common-process/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, ve kterém vytvoříte novou datovou továrnu.
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    * V rozevíracím seznamu vyberte **Použít existující** a vyberte existující skupinu prostředků.

    * Vyberte **Vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

5. Ve **verzi**vyberte **V2**.
6. V části **umístění**vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například Azure Storage a SQL Database) a výpočetní prostředky (například Azure HDInsight), které používá vaše továrna na data, můžou být v jiných umístěních a oblastech.
8. Vyberte **Vytvořit**.
9. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
10. Pokud chcete otevřít uživatelské rozhraní Azure Data Factory (UI) na samostatné kartě, vyberte dlaždici **Author & Monitor.**

    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte v názvu **Kopírovat data** a otevřete nástroj Kopírovat data.

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)

2. Na stránce **Vlastnosti** postupujte takto:

    a. V **části Název úkolu**zadejte **DeltaCopyFromBlobPipeline**.

    b. V **části Kadence úloh** nebo Plán **úloh**vyberte **pravidelně spouštět podle plánu**.

    c. V části **Typ aktivační události**vyberte možnost **Omílání .**

    d. V části **Opakování**zadejte **15 minut**.

    e. Vyberte **další**.

    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy.

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/copy-data-tool-properties-page.png)

3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Výběrem **možnosti + Vytvořit nové připojení**přidáte připojení.

    b. V galerii vyberte **Azure Blob Storage** a pak vyberte **Pokračovat**.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-blob.png)

    c. Na stránce **New Linked Service (Azure Blob Storage)** vyberte svůj účet úložiště ze seznamu **názvů účtů úložiště.** Otestujte připojení a pak vyberte **Vytvořit**.

    d. Vyberte nově vytvořenou propojenou službu a pak vyberte **Další**.

   ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/source-data-store-page-select-linkedservice.png)

4. Na stránce **Choose the input file or folder** (Zvolit vstupní soubor nebo složku) proveďte následující kroky:

    a. Procházejte a vyberte **zdrojovou** složku a pak vyberte **Zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-input-file-folder.png)

    b. V části **Chování načítání souboru**vyberte **Přírůstkové načtení: LastModifiedDate**.

    c. Zaškrtněte **políčko Binární kopírování** a vyberte **možnost Další**.

     ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/check-binary-copy.png)

5. Na stránce **cílové úložiště dat** vyberte **AzureBlobStorage,** které jste vytvořili. Jedná se o stejný účet úložiště jako zdrojové úložiště dat. Pak vyberte **Další**.

6. Na stránce **Choose the output file or folder** (Zvolit výstupní soubor nebo složku) proveďte následující kroky:

    a. Procházejte a vyberte **cílovou** složku a pak vyberte **Vybrat**.

    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/choose-output-file-folder.png)

    b. Vyberte **další**.

7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).

8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/summary-page.png)

9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).

    ![Stránka Nasazení](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/deployment-page.png)

10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Aplikace se přepne na kartu **Monitor.** Zobrazí se stav kanálu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. Kliknutím na odkaz v části **NÁZEV KANÁLU** zobrazíte podrobnosti spuštění aktivity nebo znovu spusťte kanál. 

    ![Aktualizovat seznam a vybrat možnost Zobrazit spuštění aktivity](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs1.png)

11. V kanálu je pouze jedna aktivita (aktivita kopírování), takže se zobrazí pouze jedna položka. Podrobnosti o operaci kopírování vyberte odkaz **Podrobnosti** (ikona brýlí) ve sloupci **NÁZEV AKTIVITY.** Podrobnosti o vlastnostech najdete v tématu [Přehled aktivity kopírování](copy-activity-overview.md). 

    ![Aktivita kopírování je v kanálu.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs2.png)

    Vzhledem k tomu, že ve **zdrojovém** kontejneru v účtu úložiště objektů Blob není žádný soubor zkopírován do **cílového** kontejneru v účtu úložiště objektů Blob.

    ![Žádný soubor ve zdrojovém nebo cílovém kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3.png)

12. Vytvořte prázdný textový soubor a pojmenujte jej **file1.txt**. Nahrajte tento textový soubor do **zdrojového** kontejneru ve vašem účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

    ![Vytvoření souboru1.txt a odeslání do zdrojového kontejneru](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs3-1.png)

13. Chcete-li se vrátit zpět do zobrazení **Spuštění kanálu,** vyberte **možnost Všechny spuštění kanálu**a počkejte, až se stejný kanál znovu automaticky spustí.  

    ![Vybrat všechny spuštěné kanály](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs4.png)

14. Po dokončení druhého spuštění kanálu postupujte podle výše uvedených kroků a zkontrolujte podrobnosti spuštění aktivity.  

    Uvidíte, že jeden soubor (file1.txt) byl zkopírován ze **zdrojového** kontejneru do **cílového** kontejneru vašeho účtu úložiště objektů Blob.

    ![Soubor File1.txt byl zkopírován ze zdrojového kontejneru do cílového kontejneru.](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs6.png)

15. Vytvořte další prázdný textový soubor a pojmenujte jej **file2.txt**. Nahrajte tento textový soubor do **zdrojového** kontejneru v účtu úložiště objektů Blob.

16. Opakujte kroky 13 a 14 pro tento druhý textový soubor. Uvidíte, že pouze nový soubor (file2.txt) byl zkopírován ze **zdrojového** kontejneru do **cílového** kontejneru vašeho účtu úložiště při příštím spuštění kanálu.  

    Můžete to také ověřit pomocí [Průzkumníka úložiště Azure](https://storageexplorer.com/) ke skenování souborů.

    ![Prohledává soubory pomocí Průzkumníka úložiště Azure](./media/tutorial-incremental-copy-lastmodified-copy-data-tool/monitor-pipeline-runs8.png)


## <a name="next-steps"></a>Další kroky
Přejdete k následujícímu kurzu, kde se dozvíte o transformaci dat pomocí clusteru Apache Spark v Azure:

> [!div class="nextstepaction"]
>[Transformace dat v cloudu pomocí clusteru Apache Spark](tutorial-transform-data-spark-portal.md)
