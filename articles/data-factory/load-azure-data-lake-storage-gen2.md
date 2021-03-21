---
title: Načtení dat do Azure Data Lake Storage Gen2
description: Použití Azure Data Factory ke kopírování dat do Azure Data Lake Storage Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 075778e2650d3b1f67447078eb6cb130849bbb35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593743"
---
# <a name="load-data-into-azure-data-lake-storage-gen2-with-azure-data-factory"></a>Načtení dat do Azure Data Lake Storage Gen2 s využitím Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je integrovaná do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md). Umožňuje vám přidružit data pomocí systémů souborů i úložišť objektů.

Azure Data Factory (ADF) je plně spravovaná cloudová služba pro integraci dat. Službu můžete využít k naplnění dat z bohatých sad místních i cloudových úložišť dat a ušetřit čas při vytváření analytických řešení. Podrobný seznam podporovaných konektorů najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí řešení pro přesun spravovaných dat se škálováním na více instancí. Z důvodu architektury automatického navýšení na více instancí (ADF) může ingestovat data při vysoké propustnosti. Podrobnosti najdete v tématu o [výkonu aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak pomocí nástroje Data Factory Kopírování dat načíst data ze _služby Amazon Web Services S3_ do _Azure Data Lake Storage Gen2_. Můžete postupovat podle podobných kroků a kopírovat data z jiných typů úložišť dat.

>[!TIP]
>Chcete-li kopírovat data z Azure Data Lake Storage Gen1 do Gen2, přečtěte si [Tento konkrétní návod](load-azure-data-lake-storage-gen2-from-gen1.md).

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet Azure Storage s povoleným Data Lake Storage Gen2em: Pokud nemáte účet úložiště, [vytvořte účet](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).
* Účet AWS s kontejnerem S3, který obsahuje data: Tento článek popisuje, jak kopírovat data z Amazon S3. Pomocí podobných kroků můžete použít další úložiště dat.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte vytvořit data Factory **pro**  >  **integraci** prostředků  >  :
   
   ![Výběr datové továrny v podokně Nový](./media/doc-common-process/new-azure-data-factory-menu.png)

2. Na stránce **Nová datová továrna** zadejte hodnoty pro následující pole:
 
    * **Název**: zadejte globálně jedinečný název pro objekt pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat *YourDataFactoryName* není k dispozici", zadejte jiný název pro datovou továrnu. Můžete například použít název _**Your**_**ADFTutorialDataFactory**. Zkuste vytvořit datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. 
    * **Skupina prostředků**: v rozevíracím seznamu vyberte existující skupinu prostředků nebo vyberte možnost **vytvořit novou** a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, která služba Data Factory používá, můžou být v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.

4. Až se vytváření dokončí, přejdete do vaší datové továrny. Zobrazí se Domovská stránka **Data Factory** , jak je znázorněno na následujícím obrázku: 
   
   :::image type="content" source="./media/doc-common-process/data-factory-home-page.png" alt-text="Domovská stránka Azure Data Factory s dlaždicí pro monitorování autora &.":::

   Výběrem dlaždice **Author & Monitor** (Vytvořit a monitorovat) otevřete na samostatné kartě aplikaci pro integraci dat.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data**. Spustí se nástroj pro kopírování dat.

2. Na stránce **vlastnosti** zadejte **CopyFromAmazonS3ToADLS** pro pole **název úlohy** a vyberte **Další**.

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2/copy-data-tool-properties-page.png)
3. Na stránce pro **zdrojové úložiště dat** klikněte na **+ Vytvořit nové připojení**. Z Galerie konektorů vyberte **Amazon S3** a vyberte **pokračovat**.
    
    ![Stránka S3 zdrojového úložiště dat](./media/load-azure-data-lake-storage-gen2/source-data-store-page-s3.png)
    
4. Na stránce **Nová propojená služba (Amazon S3)** proveďte následující kroky:

   1. Zadejte hodnotu **ID přístupového klíče** .
   2. Zadejte hodnotu **tajného přístupového klíče** .
   3. Kliknutím na **Test připojení** ověřte nastavení a pak vyberte **vytvořit**.

      ![Zadat účet Amazon S3](./media/load-azure-data-lake-storage-gen2/specify-amazon-s3-account.png)
   4. Uvidíte, že se vytvořilo nové připojení AmazonS3. Vyberte **Další**. 

5. Na stránce pro **volbu vstupního souboru nebo složky** přejděte ke složce a souboru, který chcete zkopírovat. Vyberte složku nebo soubor a pak vyberte **zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2/choose-input-folder.png)

6. Určete chování kopírování kontrolou možností **rekurzivního** a **binárního kopírování** . Vyberte **Další**.

    ![Snímek obrazovky zobrazuje vstupní soubor nebo složku, kde můžete vybrat binární kopii a rekurzivně.](./media/load-azure-data-lake-storage-gen2/specify-binary-copy.png)
    
7. Na stránce **cílové úložiště dat** klikněte na **+ vytvořit nové připojení** a vyberte **Azure Data Lake Storage Gen2** a pak **pokračovat**.

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2/destination-data-storage-page.png)

8. Na stránce **Nová propojená služba (Azure Data Lake Storage Gen2)** proveďte následující kroky:

   1. V rozevíracím seznamu Název účtu úložiště vyberte účet, který podporuje Data Lake Storage Gen2.
   2. Vyberte **vytvořit** a vytvořte připojení. Pak vyberte **Další**.   

        ![Zadat Azure Data Lake Storage Gen2 účet](./media/load-azure-data-lake-storage-gen2/specify-azure-data-lake-storage.png)

9. Na stránce **zvolte výstupní soubor nebo složku** zadejte **copyfroms3** jako název výstupní složky a vyberte **Další**. ADF vytvoří během kopírování ADLS Gen2 odpovídající soubor systému souborů a podsložek, pokud neexistuje.

    ![Snímek obrazovky zobrazující cestu ke složce, kterou zadáte.](./media/load-azure-data-lake-storage-gen2/specify-adls-path.png)

10. Na stránce **Nastavení** vyberte **Další**, aby se použilo výchozí nastavení.

    ![Stránka Nastavení](./media/load-azure-data-lake-storage-gen2/copy-settings.png)

11. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na tlačítko **Další**.

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2/copy-summary.png)

12. Na stránce **Nasazení** vyberte **Monitorovat** a začněte monitorovat kanál (úlohu). 
 
13. Po úspěšném dokončení kanálu se zobrazí spuštění kanálu, které se aktivuje ruční triggerem. Pomocí odkazů ve sloupci **název kanálu** můžete zobrazit podrobnosti o aktivitách a znovu spustit kanál.

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2/monitor-pipeline-runs.png)

14. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **CopyFromAmazonS3ToADLS** pod SLOUPCEM název kanálu. Pokud chcete zobrazit podrobnosti o operaci kopírování, vyberte odkaz **Podrobnosti** (ikona brýlí) ve SLOUPCI název aktivity. Můžete monitorovat podrobnosti, jako je objem dat zkopírovaných ze zdroje, do jímky, propustnosti dat, postup provádění s odpovídající dobou trvání a použitou konfiguraci.
 
    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2/monitor-activity-runs.png)
    
    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-data-lake-storage-gen2/monitor-activity-run-details.png)

15. Jestliže chcete zobrazení aktualizovat, vyberte Aktualizovat. Výběrem možnosti **všechny spuštěné kanály** v horní části přejdete zpátky k zobrazení spuštění kanálu.

16. Ověřte, že se data zkopírují do účtu Data Lake Storage Gen2.

## <a name="next-steps"></a>Další kroky

* [Přehled aktivit kopírování](copy-activity-overview.md)
* [Konektor Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)
