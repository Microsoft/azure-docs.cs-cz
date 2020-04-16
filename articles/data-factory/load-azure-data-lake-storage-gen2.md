---
title: Načtení dat do Azure Data Lake Storage Gen2
description: Kopírování dat do Azure Data Lake Storage Gen2 pomocí Azure Data Factory
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 05/13/2019
ms.openlocfilehash: 96674f059e9cbc21c5c8c64eff8c94c810c4aa32
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417772"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Načtení dat do Azure Data Lake Storage Gen2 s Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 je sada funkcí určených pro analýzu velkých objemů dat, integrované do [úložiště objektů blob Azure](../storage/blobs/storage-blobs-introduction.md). To vám umožní rozhraní s daty pomocí souborového systému a paradigmat pro ukládání objektů.

Azure Data Factory (ADF) je plně spravovaná cloudová služba pro integraci dat. Pomocí této služby můžete naplnit jezero daty z bohaté sady místních a cloudových úložišť dat a ušetřit čas při vytváření analytických řešení. Podrobný seznam podporovaných konektorů naleznete v tabulce [Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí škálovatkapacitní řešení pro přesun y spravovaných dat. Díky škálovatelně navoděné architektuře adf může ingestovat data s vysokou propustností. Podrobnosti naleznete v [tématu Kopírování výkonu aktivity](copy-activity-performance.md).

Tento článek ukazuje, jak pomocí nástroje Data Factory Copy Data tool načíst data ze _služby Amazon Web Services S3_ do _Azure Data Lake Storage Gen2_. Můžete postupovat podle podobných kroků ke kopírování dat z jiných typů úložišť dat.

>[!TIP]
>Kopírování dat z Azure Data Lake Storage Gen1 do Gen2 najdete v [tomto konkrétním návodu](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Účet Azure Storage s povoleným úložištěm Datového jezera Gen2: Pokud nemáte účet úložiště, [vytvořte si účet](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Účet AWS s kontejnerem S3, který obsahuje data: Tento článek ukazuje, jak kopírovat data z Amazon S3. Jiná úložiště dat můžete použít podle podobných kroků.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte **Vytvořit data o prostředku** > **+ Analytics** > **Data Factory**:
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová továrna dat** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Název:** Zadejte globálně jedinečný název pro vaši datovou továrnu Azure. Pokud se zobrazí chyba "Název \"datové továrny LoadADLSDemo\" není k dispozici", zadejte jiný název pro továrnu dat. Můžete například použít název _**yourname**_**ADFTutorialDataFactory**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné:** Vyberte předplatné Azure, ve kterém chcete vytvořit datové továrny. 
    * **Skupina prostředků**: V rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **Vytvořit nový** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění pro datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používány factory může být v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Po dokončení vytváření přejděte do datové továrny. Zobrazí se domovská stránka **Data Factory,** jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data** a spusťte nástroj Kopírovat data: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na stránce **Vlastnosti** zadejte **příkaz CopyFromAmazonS3ToADLS** pro pole **Název úkolu** a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na stránce **Zdrojové úložiště dat** klikněte na **+ Vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    V galerii konektorů vyberte **Amazon S3** a vyberte **Pokračovat**
    
    ![Stránka úložiště zdrojových dat s3](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na stránce **Určení připojení Amazon S3** proveďte následující kroky:

   1. Zadejte hodnotu **ID přístupového klíče.**
   2. Zadejte hodnotu **klíče tajného přístupu.**
   3. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **Dokončit**.
   4. Uvidíte nové připojení dostane vytvořen. Vyberte **Další**.
   
      ![Zadejte účet Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na stránce pro **volbu vstupního souboru nebo složky** přejděte ke složce a souboru, který chcete zkopírovat. Vyberte složku/soubor, vyberte **Zvolit**:

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Zadejte chování při kopírování zaškrtnutím možností **Copy files recursively** (Kopírovat soubory rekurzivně) a **Binary copy** (Binární kopie). Vyberte **další**:

    ![Určení výstupní složky](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na stránce **Cílové úložiště dat** klikněte na + Vytvořit nové **připojení**a pak vyberte Azure Data Lake **Storage Gen2**a vyberte **Pokračovat**:

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na stránce **Určení připojení úložiště datových jezer Azure** postupujte takto:

   1. V rozevíracím seznamu "Název účtu úložiště" vyberte svůj účet S datovým lakem Gen2.
   2. Vytvořte připojení výběrem možnosti **Dokončit**. Pak vyberte **Další**.
   
   ![Určení účtu Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na stránce **Vybrat výstupní soubor nebo složku** zadejte **copyfroms3** jako název výstupní složky a vyberte **Další**. ADF vytvoří odpovídající ADLS Gen2 souborový systém a podsložky během kopírování, pokud neexistuje.

    ![Určení výstupní složky](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na stránce **Nastavení** vyberte **Další,** chcete-li použít výchozí nastavení:

    ![Stránka Nastavení](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **stránce Nasazení**vyberte **Sledovat,** chcete-li sledovat kanál:

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivity a opětovné spuštění kanálu:

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Chcete-li zobrazit spuštění aktivity, které jsou přidruženy ke spuštění kanálu, vyberte odkaz **Zobrazit spuštění aktivity** ve sloupci **Akce.** Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Chcete-li přepnout zpět do zobrazení spuštění kanálu, vyberte odkaz **Potrubí** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Chcete-li sledovat podrobnosti spuštění pro každou aktivitu kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) v části **Akce** v zobrazení sledování aktivity. Můžete sledovat podrobnosti, jako je objem dat zkopírovaných ze zdroje do jímky, propustnost dat, kroky spuštění s odpovídající dobou trvání a použité konfigurace:

    ![Podrobnosti spuštění aktivity monitoru](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Ověřte, zda jsou data zkopírována do vašeho účtu Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky

* [Kopírovat přehled aktivit](copy-activity-overview.md)
* [Konektor Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
