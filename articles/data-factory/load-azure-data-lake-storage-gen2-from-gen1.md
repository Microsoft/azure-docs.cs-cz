---
title: Kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory
description: Použití Azure Data Factory pro kopírování dat z Azure Data Lake Storage Gen1 na Gen2
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 02/15/2019
ms.author: jingwang
ms.openlocfilehash: e1dd3953925d4907617295fbc14a1a4e042213e1
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56309603"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory

Azure Data Lake Storage Gen2 je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, integrované do [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md). Umožňuje uživateli rozhraní s vašimi daty použitím obou paradigmat souboru systému a objektu úložiště.

Pokud aktuálně používáte Azure Data Lake Storage Gen1, můžete novou funkci Gen2 vyhodnotit tak, že zkopírujete data z Data Lake Storage Gen1 na Gen2 pomocí Azure Data Factory.

Azure Data Factory je služba pro integraci plně spravovaný cloudový datový. Služby můžete použít k naplnění lake s daty z široké místních a cloudových dat ukládá a ušetřit čas při vytváření vlastních analytických řešení. Podrobný seznam podporovaných konektorů, najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí řešení pro přesun dat horizontální navýšení kapacity, kterou spravuje. Vzhledem k architektuře horizontální navýšení kapacity služby ADF se schopností ingestovat data při vysoké propustnosti. Podrobnosti najdete v tématu [výkonu aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak pomocí nástroje pro kopírování dat Data Factory pro kopírování dat z _Azure Data Lake Storage Gen1_ do _Azure Data Lake Storage Gen2_. Můžete použít podobným způsobem kopírovat data z jiných typů úložišť dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure: Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet Azure Data Lake Storage Gen1 s daty.
* Účet služby Azure Storage s Data Lake Storage Gen2 povoleno: Pokud nemáte účet úložiště, klikněte na tlačítko [tady](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM) k jejímu vytvoření.

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **nový** > **Data a analýzy** > **služby Data Factory**:
   
   ![Vytvoření nové datové továrny](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory-menu.png)
2. V **nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název pro službu Azure data factory. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadADLSDemo\" není k dispozici," Zadejte jiný název datové továrny. Můžete například použít název  _**vaše_jméno**_**ADFTutorialDataFactory**. Zkuste znovu vytvořit datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: Vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
    * **Skupina prostředků**: Z rozevíracího seznamu vyberte existující skupinu prostředků, nebo **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v článku [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md).  
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používané datovou továrnou můžou být v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Po vytvoření se, přejděte do služby data factory. Zobrazí **služby Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka datové továrny](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

   Vyberte **vytvořit a monitorovat** dlaždice aplikace pro integraci dat v na samostatné kartě.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. V **Začínáme** stránky, vyberte **kopírování dat** dlaždice nástroje pro kopírování dat: 

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. V **vlastnosti** určete, **CopyFromADLSGen1ToGen2** pro **název úkolu** pole a vyberte **Další**:

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. V **zdrojového úložiště dat** klikněte na **+ vytvořit nové připojení**:

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
    Vyberte **Azure Data Lake Storage Gen1** z Galerie konektoru a vyberte **pokračovat**
    
    ![Azure Data Lake Storage Gen1 stránka zdrojového úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
4. V **připojení zadejte Azure Data Lake Storage Gen1** stránce, proveďte následující kroky:
   1. Vyberte vaše Data Lake Storage Gen1 pro název účtu a zadat nebo ověřit **Tenanta**.
   2. Klikněte na tlačítko **Test připojení** Pokud chcete ověřit nastavení, pak vyberte **Dokončit**.
   3. Uvidíte, že se vytvoří nové připojení. Vyberte **Další**.
   
   > [!IMPORTANT]
   > V tomto názorném postupu použijete k ověření vaší Data Lake Storage Gen1 spravovanou identitu pro prostředky Azure. Je potřeba udělit příslušná oprávnění v Azure Data Lake Storage Gen1 MSI pomocí následujícího [tyto pokyny](connector-azure-data-lake-store.md#managed-identity).
   
   ![Zadejte účet Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
5. V **zvolte vstupní soubor nebo složku** stránce, přejděte do složky a souboru, který chcete zkopírovat. Vyberte složky nebo souboru, vyberte **zvolit**:

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

6. Zadejte chování kopírování kontrolou **kopírovat soubory rekurzivně** a **binární kopie** možnosti. Vyberte **Další**:

    ![Zadejte výstupní složka](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
7. V **cílového úložiště dat** klikněte na **+ vytvořit nové připojení**a pak vyberte **Azure Data Lake Storage Gen2**a vyberte **pokračovat**:

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

8. V **připojení zadejte Azure Data Lake Storage Gen2** stránce, proveďte následující kroky:

   1. Vyberte vaše Data Lake Storage Gen2 podporující účet z "názvu účtu úložiště" rozevírací seznam.
   2. Vyberte **Dokončit** k vytvoření připojení. Pak vyberte **Další**.
   
   ![Zadejte účet Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

9. V **zvolte výstupní soubor nebo složku** zadejte **copyfromadlsgen1** jako název složky výstupu a vyberte **Další**: 

    ![Zadejte výstupní složka](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

10. V **nastavení** stránce **Další** použít výchozí nastavení.

11. V **Souhrn** stránky, zkontrolujte nastavení a vyberte **Další**:

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
12. V **stránku nasazení**vyberte **monitorování** a začněte monitorovat kanál:

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
13. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. **Akce** sloupec obsahuje odkazy, chcete-li zobrazit podrobnosti o spuštění aktivit a opětovné spuštění kanálu:

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

14. Pokud chcete zobrazit spuštění aktivit, které jsou spojeny se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** odkaz v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

15. Pokud chcete monitorovat spuštění podrobnosti o každé aktivitě kopírování, vyberte **podrobnosti** odkaz (obrázek brýlí) ve skupinovém rámečku **akce** v aktivitě zobrazení monitorování. Podobně jako objem dat zkopírovanou ze zdroje do jímky, propustnost dat, provádění kroků s určitou dobu a použít konfigurace, můžete sledovat informace:

    ![Podrobnosti o spuštění aktivit monitorování](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

16. Ověřte, že se data kopírují do svého účtu Data Lake Storage Gen2.

## <a name="best-practices"></a>Osvědčené postupy

Při kopírování velké objemy dat z úložiště dat založeného na souboru, jsou navrženy pro:

- Rozdělení souborů do 10TB na 30TB použití.
- Nespouštějí příliš mnoho souběžných kopírování používá pro vyhnout omezení šířky pásma z úložišť dat zdrojem nebo jímkou. Můžete začít s jedné kopie spuštění a monitorování propustnost a pak postupně přidat další podle potřeby.

## <a name="next-steps"></a>Další postup

* [Přehled aktivit kopírování](copy-activity-overview.md)
* [Konektor služby Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)