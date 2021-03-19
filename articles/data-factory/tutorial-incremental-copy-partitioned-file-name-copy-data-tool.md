---
title: Přírůstkové kopírování nových souborů na základě času názvu souboru rozděleného na oddíly
description: Vytvořte datovou továrnu Azure a pak použijte nástroj Kopírování dat pro přírůstkové načtení nových souborů jenom na základě názvu souboru s oddíly.
author: dearandyxu
ms.author: yexu
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 26703920fda8746badf085f96686f922ee250513
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104606629"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nových souborů na základě času názvu souboru rozděleného do oddílů pomocí nástroje Kopírování dat

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Pak použijete nástroj Kopírování dat k vytvoření kanálu, který postupně kopíruje nové soubory na základě času názvu souboru děleného z Azure Blob Storage do úložiště objektů BLOB v Azure.

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Předpoklady

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: jako _zdroj_  dat a úložiště dat _jímky_ použijte úložiště objektů BLOB. Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů BLOB

Provedením těchto kroků Připravte úložiště objektů BLOB pro tento kurz.

1. Vytvořte kontejner s názvem **source**.  Vytvořte v kontejneru cestu ke složce jako **2020/03/17/03** . Vytvořte prázdný textový soubor a pojmenujte ho jako **file1.txt**. Nahrajte file1.txt do složky Path **source/2020/03/17/03** v účtu úložiště.  K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

    ![nahrání souborů](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Upravte prosím název složky časem UTC.  Pokud je například aktuální čas UTC 3:38 17. března 2020, můžete vytvořit cestu ke složce jako **zdroj/2020/03/17/03/** podle pravidla **zdroje/{Year}/{Month}/{Day}/{Hour}/**.

2. Vytvořte kontejner s názvem **Destination**. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  :

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

    Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:

   :::image type="content" source="./media/doc-common-process/name-not-available-error.png" alt-text="Nová chybová zpráva objektu pro vytváření dat pro duplicitní název.":::

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit.
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **vytvořit novou** a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

5. V části **Verze** vyberte **V2**.
6. V části **umístění** vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Vyberte **Vytvořit**.
8. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
9. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**.

    :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::

## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte **kopírování dat** název pro spuštění nástroje kopírování dat.

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)

2. Na stránce **vlastnosti** proveďte následující kroky:

    a. V části **název úlohy** zadejte **DeltaCopyFromBlobPipeline**.

    b. V části **úkol tempo nebo plán úlohy** vyberte možnost **spouštět pravidelně podle plánu**.

    c. V části **typ triggeru** vyberte **okno bubnu**.

    d. V části **opakování** zadejte **1 hodina (e)**.

    e. Vyberte **Další**.

    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy.

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.
    
    b. Z Galerie vyberte Azure Blob Storage a pak vyberte pokračovat.
    
    c. Na stránce **Nová propojená služba (Azure Blob Storage)** zadejte název propojené služby. Vyberte své předplatné Azure a v seznamu **název účtu úložiště** vyberte svůj účet úložiště. Otestujte připojení a pak vyberte **vytvořit**.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Na stránce **zdrojové úložiště dat** vyberte nově vytvořenou propojenou službu a potom klikněte na tlačítko **Další**.

4. Na stránce **Zvolte vstupní soubor nebo složku** proveďte následující kroky:

    a. Procházejte a vyberte **zdrojový** kontejner a pak vyberte **Vybrat.**

    ![Snímek obrazovky se zobrazí v dialogovém okně zvolit vstupní soubor nebo složku.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. V části **chování při načítání souborů** vyberte **přírůstkové načtení: čas-dělené složky nebo názvy souborů**.

    c. Napište cestu k dynamické složce jako **zdroj/{Year}/{Month}/{Day}/{Hour}/** a změňte formát, jak je znázorněno na následujícím snímku obrazovky. Ověřte **binární kopii** a klikněte na tlačítko **Další**.

    ![Snímek obrazovky se zobrazí dialogové okno zvolit vstupní soubor nebo složku s vybraným adresářem.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Na stránce **cílové úložiště dat** vyberte **AzureBlobStorage**, což je stejný účet úložiště jako úložiště zdroje dat, a potom klikněte na **Další**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na stránce **zvolit výstupní soubor nebo složku** proveďte následující kroky:

    a. Vyhledejte a vyberte **cílovou** složku a pak klikněte na **zvolit**.

    b. Zapište dynamickou cestu ke složce jako **cíl/{Year}/{Month}/{Day}/{Hour}/** a změňte formát následujícím způsobem:

    ![Snímek obrazovky se zobrazí v dialogovém okně zvolit výstupní soubor nebo složku.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klikněte na **Next** (Další).

    ![Snímek obrazovky se zobrazí dialogové okno zvolit výstupní soubor nebo složku s vybraným polem další.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).

8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).
    ![Stránka Nasazení](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**.  Musíte počkat na spuštění kanálu, když se aktivuje automaticky (přibližně po jedné hodině). Po spuštění klikněte na odkaz název kanálu **DeltaCopyFromBlobPipeline** a zobrazte podrobnosti o spuštění aktivity nebo spusťte kanál znovu. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

    ![Snímek obrazovky se zobrazí v podokně spuštění kanálu.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs-1.png)
11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Upravte šířku sloupce **zdrojových** a **cílových** sloupců (Pokud je potřeba), aby se zobrazily další podrobnosti, můžete vidět, že zdrojový soubor (file1.txt) byl zkopírován ze  *zdroje/2020/03/17/03/* *cíl/2020/03/17/03/* se stejným názvem souboru. 

    ![Snímek obrazovky ukazuje podrobnosti spuštění kanálu.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Stejný postup můžete také ověřit pomocí Průzkumník služby Azure Storage ( https://storageexplorer.com/) k prohledání souborů.

    ![Snímek obrazovky ukazuje podrobnosti spuštění kanálu pro cíl.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Vytvořte další prázdný textový soubor s novým názvem jako **file2.txt**. Nahrajte soubor file2.txt do složky Path **source/2020/03/17/04** v účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

    > [!NOTE]
    > Možná víte, že je potřeba vytvořit novou cestu ke složce. Upravte prosím název složky časem UTC.  Pokud je například aktuální čas UTC 4:20 na březen. 17, 2020, můžete vytvořit cestu ke složce jako **zdroj/2020/03/17/04/** podle pravidla **{Year}/{Month}/{Day}/{Hour}/**.

13. Pokud se chcete vrátit do zobrazení **spuštění kanálu** , vyberte **všechny spuštěné kanály** a počkejte, až se znovu aktivuje stejný kanál, a to za jinou hodinu.  

    ![Snímek obrazovky se zobrazí odkaz všechny spuštění kanálu, který se vrátí na tuto stránku.](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Vyberte nový odkaz **DeltaCopyFromBlobPipeline** pro druhý běh kanálu, pokud je k dispozici, a proveďte stejnou kontrolu podrobností. Uvidíte, že zdrojový soubor (file2.txt) se zkopíroval ze  **zdroje/2020/03/17/04/**  do **cílového umístění/2020/03/17/04/** se stejným názvem souboru. Stejný postup můžete také ověřit pomocí Průzkumník služby Azure Storage ( https://storageexplorer.com/) k prohledání souborů v **cílovém** kontejneru).


## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-portal.md)
