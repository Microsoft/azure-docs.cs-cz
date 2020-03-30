---
title: Přírůstkové kopírování nových souborů na základě názvu souboru rozděleného časem
description: Vytvořte Azure factory dat a pak pomocí nástroje Kopírovat data postupně načíst nové soubory pouze na základě názvu souboru rozděleného časem.
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
ms.date: 3/17/2020
ms.openlocfilehash: 50e82362f39da2d3f3ecfad2de1ed07b68f8a6a1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79501659"
---
# <a name="incrementally-copy-new-files-based-on-time-partitioned-file-name-by-using-the-copy-data-tool"></a>Přírůstkové kopírování nových souborů na základě názvu souboru rozděleného časem pomocí nástroje Kopírovat data

V tomto kurzu pomocí webu Azure Portal vytvoříte datovou továrnu. Potom použijete nástroj Kopírovat data k vytvoření kanálu, který postupně zkopíruje nové soubory na základě názvu souboru s časovým rozdělením z úložiště objektů Blob Azure do úložiště objektů Blob Azure.

> [!NOTE]
> Pokud se službou Azure Data Factory začínáte, přečtěte si téma [Seznámení se službou Azure Data Factory](introduction.md).

V tomto kurzu budete provádět následující kroky:

> [!div class="checklist"]
> * Vytvoření datové továrny
> * Vytvoření kanálu pomocí nástroje pro kopírování dat
> * Monitorování spuštění aktivit a kanálu

## <a name="prerequisites"></a>Požadavky

* **Předplatné Azure:** Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* **Účet úložiště Azure**: Použijte úložiště objektů blob jako _zdroj_ ové úložiště a úložiště dat _jímky._ Pokud účet úložiště Azure nemáte, přečtěte si pokyny v tématu [Vytvoření účtu úložiště](../storage/common/storage-account-create.md).

### <a name="create-two-containers-in-blob-storage"></a>Vytvoření dvou kontejnerů v úložišti objektů Blob

Připravte úložiště objektů Blob pro kurz provedením těchto kroků.

1. Vytvořte kontejner s názvem **zdroj**.  Vytvořte cestu ke složce jako **2020/03/17/03** v kontejneru. Vytvořte prázdný textový soubor a pojmenujte jej jako **file1.txt**. Nahrajte soubor1.txt do zdroje cesty ke **složce/2020/03/17/03** ve vašem účtu úložiště.  K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

    ![Vkládat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/upload-file.png)

    > [!NOTE]
    > Upravte název složky podle času UTC.  Pokud je například aktuální čas Času UTC **source/2020/03/17/03/** 17. **source/{Year}/{Month}/{Day}/{Hour}/**

2. Vytvořte kontejner s názvem **cíl**. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte **Vytvořit data o prostředku** > **+ Analytics** > **Data Factory**:

   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Do pole **Název** na stránce **Nová datová továrna** zadejte **ADFTutorialDataFactory**.

    Název datové továrny musí být _globálně jedinečný_. Možná se zobrazí následující chybová zpráva:

   ![Nová datová továrna – chybová zpráva](./media/doc-common-process/name-not-available-error.png)

   Pokud se zobrazí chybová zpráva týkající se hodnoty názvu, zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
3. Vyberte **předplatné** Azure, v rámci kterého se má nová datová továrna vytvořit.
4. U položky **Skupina prostředků** proveďte jeden z následujících kroků:

    a. Vyberte **Použít existující** a z rozevíracího seznamu vyberte existující skupinu prostředků.

    b. Vyberte **Vytvořit nový**a zadejte název skupiny prostředků. 
         
    Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).

5. V části **Verze** vyberte **V2**.
6. V části **umístění**vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat (například služby Azure Storage a SQL Database) a výpočetní prostředí (například Azure HDInsight) používané datovou továrnou můžou být v jiných umístěních a oblastech.
7. Vyberte **Vytvořit**.
8. Po vytvoření se zobrazí domovská stránka **Datová továrna**.
9. Pokud chcete na samostatné kartě otevřít uživatelské rozhraní služby Azure Data Factory, vyberte dlaždici **Vytvořit a monitorovat**.

    ![Domovská stránka objektu pro vytváření dat](./media/doc-common-process/data-factory-home-page.png)


## <a name="use-the-copy-data-tool-to-create-a-pipeline"></a>Vytvoření kanálu pomocí nástroje pro kopírování dat

1. Na stránce **Začínáme** vyberte název **Kopírovat data** a spusťte nástroj Kopírovat data.

   ![Dlaždice nástroje pro kopírování dat](./media/doc-common-process/get-started-page.png)

2. Na stránce **Vlastnosti** postupujte takto:

    a. V **části Název úkolu**zadejte **DeltaCopyFromBlobPipeline**.

    b. V **části Kadence úloh nebo Plán úloh**vyberte pravidelně **spouštět podle plánu**.

    c. V části **Typ aktivační události**vyberte možnost **Omílání .**

    d. V části **Opakování**zadejte **1 hodin .**

    e. Vyberte **další**.

    Uživatelské rozhraní služby Data Factory vytvoří kanál se zadaným názvem úlohy.

    ![Stránka Vlastnosti](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/copy-data-tool-properties-page.png)
3. Na stránce **Source data store** (Zdrojové úložiště dat) proveďte následující kroky:

    a. Kliknutím na **+ Create new connection** (+ Vytvořit nové připojení) přidejte připojení.
    
    b. V galerii vyberte Azure Blob Storage a pak vyberte Pokračovat.
    
    c. Na stránce **Nová propojená služba (Azure Blob Storage)** zadejte název propojené služby. Vyberte předplatné Azure a vyberte účet úložiště ze seznamu **názvů účtů úložiště.** Otestujte připojení a pak vyberte **Vytvořit**.

    ![Stránka Zdrojové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/source-data-store-page-linkedservice.png)

    d. Na stránce **Zdrojové úložiště dat** vyberte nově vytvořenou propojenou službu a klepněte na tlačítko **Další**.

4. Na stránce **Zvolte vstupní soubor nebo složku** proveďte následující kroky:

    a. Procházejte a vyberte **zdrojový** kontejner a pak vyberte **Zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/choose-input-file-folder.png)

    b. V části **Chování načítání souborů**vyberte **Přírůstkové načtení: názvy složek nebo souborů s časovým rozdělením**.

    c. Napište cestu dynamické složky jako **zdroj/{year}/{month}/{day}/{hour}/** a změňte formát, jak je znázorněno na následujícím snímku obrazovky. Zaškrtněte **políčko Binární kopie** a klepněte na tlačítko **Další**.

    ![Zvolte vstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/check-binary-copy.png)     

5. Na stránce **Cílové úložiště dat** vyberte **AzureBlobStorage**, což je stejný účet úložiště jako úložiště zdrojů dat, a klikněte na **Další**.

    ![Stránka Cílové úložiště dat](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/destination-data-store-page-select-linkedservice.png)
6. Na stránce **Vybrat výstupní soubor nebo složku** postupujte takto:

    a. Procházejte a vyberte **cílovou** složku a klepněte na **tlačítko Vybrat**.

    b. Napište cestu dynamické složky jako **cíl/{year}/{month}/{day}/{hour}/** a změňte formát takto:

    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/output-file-name.png)

    c. Klikněte na **Další**.

    ![Zvolte výstupní soubor nebo složku](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/click-next-after-output-folder.png)
7. Na stránce **Settings** (Nastavení) vyberte **Next** (Další).

8. Na stránce **Souhrn** zkontrolujte nastavení a pak vyberte **Další**.

    ![Stránka souhrnu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/summary-page.png)

9. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu).
    ![Stránka Nasazení](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/deployment-page.png)

10. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**.  Potřebujete počkat na spuštění kanálu, když se spustí automaticky (asi po jedné hodině). Při spuštění klikněte na odkaz název kanálu **DeltaCopyFromBlobPipeline** zobrazit podrobnosti spuštění aktivity nebo znovu spustit kanál. Seznam můžete aktualizovat kliknutím na **Aktualizovat**.

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs1.png)
11. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Upravte šířku sloupce **ve zdrojovém** a **cílovém** sloupci (v případě potřeby) tak, aby zobrazoval další podrobnosti, můžete vidět zdrojový soubor (file1.txt) byl zkopírován ze *zdroje/2020/03/17/03/* do *cíle/2020/03/17/03/* se stejným názvem souboru. 

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs2.png)

    Můžete také ověřit stejné pomocí Průzkumníkahttps://storageexplorer.com/) úložiště Azure (proskenování souborů.

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs3.png)

12. Vytvořte další prázdný textový soubor s novým názvem jako **file2.txt**. Nahrajte soubor file2.txt do zdroje cesty ke **složce/2020/03/17/04** v účtu úložiště. K provedení těchto úloh můžete použít různé nástroje, například [Průzkumníka služby Azure Storage](https://storageexplorer.com/).

    > [!NOTE]
    > Možná si uvědomujete, že je nutné vytvořit novou cestu ke složce. Upravte název složky podle času UTC.  Pokud je například aktuální čas Času UTC 4:20 k 17.1.2020, můžete vytvořit cestu ke složce jako **zdroj/2020/03/17/04/** podle pravidla **{Year}/{Month}/{Day}/{Hour}/**.

13. Chcete-li se vrátit zpět do zobrazení **Spuštění kanálu,** vyberte **možnost Všechny kanály spustí**a počkejte na stejný kanál se aktivuje znovu automaticky po další jednu hodinu.  

    ![Monitorování spuštění kanálu](./media/tutorial-incremental-copy-partitioned-file-name-copy-data-tool/monitor-pipeline-runs5.png)

14. Vyberte nové propojení **DeltaCopyFromBlobPipeline** pro druhý kanál spustit, když přijde, a totéž provést ke kontrole podrobnosti. Uvidíte, že zdrojový soubor (file2.txt) byl zkopírován ze **zdroje/2020/03/17/04/** do **cíle/2020/03/17/04/** se stejným názvem souboru. Můžete také ověřit stejné pomocí Průzkumníkahttps://storageexplorer.com/) úložiště Azure (pro skenování souborů v **cílovém** kontejneru.


## <a name="next-steps"></a>Další kroky
Pokud se chcete dozvědět víc o transformaci dat pomocí clusteru Spark v Azure, přejděte k následujícímu kurzu:

> [!div class="nextstepaction"]
>[Transformace dat pomocí clusteru Spark v cloudu](tutorial-transform-data-spark-portal.md)
