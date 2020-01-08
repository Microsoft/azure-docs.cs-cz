---
title: Načtení dat do Azure Data Lake Storage Gen2
description: Použití Azure Data Factory ke kopírování dat do Azure Data Lake Storage Gen2
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
ms.openlocfilehash: 90573f77c77d614923f882053145d2f84598953d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75440238"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Načtení dat do Azure Data Lake Storage Gen2 s využitím Azure Data Factory

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je integrovaná do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md). Umožňuje uživateli rozhraní s vašimi daty použitím obou paradigmat souboru systému a objektu úložiště.

Azure Data Factory (ADF) je plně spravovaná cloudová služba pro integraci dat. Službu můžete využít k naplnění dat z bohatých sad místních i cloudových úložišť dat a ušetřit čas při vytváření analytických řešení. Podrobný seznam podporovaných konektorů najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí řešení pro přesun spravovaných dat se škálováním na více instancí. Z důvodu architektury automatického navýšení na více instancí (ADF) může ingestovat data při vysoké propustnosti. Podrobnosti najdete v tématu o [výkonu aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak pomocí nástroje Data Factory Kopírování dat načíst data ze _služby Amazon Web Services S3_ do _Azure Data Lake Storage Gen2_. Můžete postupovat podle podobných kroků a kopírovat data z jiných typů úložišť dat.

>[!TIP]
>Chcete-li kopírovat data z Azure Data Lake Storage Gen1 do Gen2, přečtěte si [Tento konkrétní návod](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet Azure Storage s povoleným Data Lake Storage Gen2em: Pokud nemáte účet úložiště, [vytvořte účet](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Účet AWS s kontejnerem S3, který obsahuje data: Tento článek popisuje, jak kopírovat data z Amazon S3. Pomocí podobných kroků můžete použít další úložiště dat.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **data a analýzy** > **Data Factory**:
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-azure-data-lake-storage-gen2//new-azure-data-factory.png)
 
    * **Název**: zadejte globálně jedinečný název pro objekt pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadADLSDemo\" není k dispozici," zadejte jiný název pro objekt pro vytváření dat. Můžete například použít název _**Your**_ **ADFTutorialDataFactory**. Zkuste vytvořit datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. 
    * **Skupina prostředků**: v rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: vyberte **v2**.
    * **Umístění**: vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, která služba Data Factory používá, můžou být v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Až se vytváření dokončí, přejdete do vaší datové továrny. Zobrazí se Domovská stránka **Data Factory** , jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-data-lake-storage-gen2/data-factory-home-page.png)

   Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. Na stránce **Začínáme** vyberte dlaždici **kopírování dat** pro spuštění nástroje kopírování dat: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-data-lake-storage-gen2/copy-data-tool-tile.png)
2. Na stránce **vlastnosti** zadejte **CopyFromAmazonS3ToADLS** pro pole **název úlohy** a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na stránce **zdrojové úložiště dat** klikněte na **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2/source-data-store-page.png)
    
    Z Galerie konektorů vyberte **Amazon S3** a vyberte **pokračovat** .
    
    ![Stránka S3 zdrojového úložiště dat](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na stránce **zadání připojení k Amazon S3** proveďte následující kroky:

   1. Zadejte hodnotu **ID přístupového klíče** .
   2. Zadejte hodnotu **tajného přístupového klíče** .
   3. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **Dokončit**.
   4. Uvidíte, že se vytvoří nové připojení. Vyberte **Next** (Další).
   
      ![Zadat účet Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
      
5. Na stránce pro **volbu vstupního souboru nebo složky** přejděte ke složce a souboru, který chcete zkopírovat. Vyberte složku nebo soubor, vyberte **zvolit**:

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Zadejte chování při kopírování zaškrtnutím možností **Copy files recursively** (Kopírovat soubory rekurzivně) a **Binary copy** (Binární kopie). Vyberte **Další**.

    ![Zadat výstupní složku](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na stránce **cílové úložiště dat** klikněte na **+ vytvořit nové připojení**a pak vyberte **Azure Data Lake Storage Gen2**a **pokračovat**:

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na stránce **zadat Azure Data Lake Storage připojení** proveďte následující kroky:

   1. V rozevíracím seznamu název účtu úložiště vyberte svůj účet s možností Data Lake Storage Gen2.
   2. Vytvořte připojení výběrem možnosti **Dokončit**. Pak vyberte **Další**.
   
   ![Zadat Azure Data Lake Storage Gen2 účet](./media/load-azure-data-lake-storage-gen2/specify-adls.png)

9. Na stránce **zvolte výstupní soubor nebo složku** zadejte **copyfroms3** jako název výstupní složky a vyberte **Další**. ADF vytvoří během kopírování odpovídající systém souborů ADLS Gen2 a podsložky, pokud neexistuje.

    ![Zadat výstupní složku](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na stránce **Nastavení** vyberte možnost **Další** a použijte výchozí nastavení:

    ![Stránka Nastavení](./media/load-azure-data-lake-storage-gen2/copy-settings.png)
11. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2/copy-summary.png)
12. Na **stránce nasazení**vyberte **monitorovat** a Sledujte kanál:

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Actions (akce** ) obsahuje odkazy na zobrazení podrobností o spuštění aktivit a opětovné spuštění kanálu:

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přejít zpátky k zobrazení spuštění kanálu, vyberte odkaz **kanály** v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)

15. Chcete-li monitorovat podrobnosti o spuštění každé aktivity kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) v části **Akce** v zobrazení monitorování aktivit. Můžete monitorovat podrobnosti, jako je objem dat zkopírovaných ze zdroje, do jímky, propustnosti dat, kroky provádění s odpovídající dobou trvání a používané konfigurace:

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

16. Ověřte, že se data zkopírují do účtu Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky

* [Přehled aktivit kopírování](copy-activity-overview.md)
* [Konektor Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
