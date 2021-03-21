---
title: Kopírování dat z Azure Data Lake Storage Gen1 do Gen2
description: Použití Azure Data Factory ke kopírování dat z Azure Data Lake Storage Gen1 na Gen2
ms.author: jingwang
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 02/18/2021
ms.openlocfilehash: 4a4bc0b55928fcaa40cd1cb03ca0522e611df476
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101727715"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopírování dat z Azure Data Lake Storage Gen1 do Gen2 pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Lake Storage Gen2 je sada funkcí vyhrazených pro analýzy velkých objemů dat, která je integrovaná do [úložiště objektů BLOB v Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ji použít pro připojení k datům pomocí paradigmat systému souborů a úložiště objektů.

Pokud v tuto chvíli používáte Azure Data Lake Storage Gen1, můžete vyhodnotit Azure Data Lake Storage Gen2 kopírováním dat z Data Lake Storage Gen1 do Gen2 pomocí Azure Data Factory.

Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Službu můžete využít k naplnění dat z bohatých sad místních i cloudových úložišť dat a ušetřit čas při sestavování analytických řešení. Seznam podporovaných konektorů najdete v tabulce [podporovaných úložišť dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí řešení pro přesun spravovaných dat se škálováním na více instancí. Kvůli architektuře Data Factory škálování na více instancí může ingestovat data při vysoké propustnosti. Další informace najdete v tématu [výkon aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak pomocí nástroje pro kopírování dat Data Factory zkopírovat data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2. Můžete postupovat podle podobných kroků a kopírovat data z jiných typů úložišť dat.

## <a name="prerequisites"></a>Předpoklady

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/).
* Azure Data Lake Storage Gen1 účet s daty.
* Azure Storage účet s povoleným Data Lake Storage Gen2em. Pokud nemáte účet úložiště, [vytvořte účet](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek**  >  **data a analýzy**  >  **Data Factory**.
   
   ![Data Factory výběr v novém podokně](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Název**: zadejte globálně jedinečný název pro objekt pro vytváření dat Azure. Pokud se zobrazí chyba "název objektu pro vytváření dat \" LoadADLSDemo \" není k dispozici", zadejte jiný název pro datovou továrnu. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Vytvořte datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: vyberte předplatné Azure, ve kterém chcete vytvořit datovou továrnu. 
    * **Skupina prostředků**: z rozevíracího seznamu vyberte existující skupinu prostředků. Můžete také vybrat možnost **vytvořit novou** a zadat název skupiny prostředků. Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
    * **Verze**: Vyberte **V2**.
    * **Umístění**: vyberte umístění pro datovou továrnu. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat používaná datovou továrnou se můžou nacházet v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Až se vytváření dokončí, přejdete do své datové továrny. Zobrazí se Domovská stránka **Data Factory** , jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Kliknutím na dlaždici **Author & monitor** můžete spustit aplikaci pro integraci dat na samostatné kartě.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. Na stránce **Začínáme** vyberte dlaždici **kopírování dat** pro spuštění nástroje pro kopírování dat. 

   ![Dlaždice nástroje pro kopírování dat](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na stránce **vlastnosti** zadejte **CopyFromADLSGen1ToGen2** pro pole **název úlohy** . Vyberte **Další**.

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na stránce **zdrojové úložiště dat** vyberte **+ vytvořit nové připojení**.

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. V galerii konektorů vyberte **Azure Data Lake Storage Gen1** a vyberte **Pokračovat**.
    
    ![Stránka Azure Data Lake Storage Gen1 zdrojového úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na stránce **zadat Azure Data Lake Storage Gen1 připojení** proveďte následující kroky:

   a. Vyberte Data Lake Storage Gen1 pro název účtu a zadejte nebo ověřte **tenanta**.
  
   b. Vyberte **Test připojení** a ověřte nastavení. Pak vyberte **Dokončit**.
  
   c. Vidíte, že bylo vytvořeno nové připojení. Vyberte **Další**.
   
   > [!IMPORTANT]
   > V tomto návodu použijete pro ověření Azure Data Lake Storage Gen1 spravovanou identitu pro prostředky Azure. Pokud chcete spravované identitě udělit správná oprávnění v Azure Data Lake Storage Gen1, postupujte podle [těchto pokynů](connector-azure-data-lake-store.md#managed-identity).
   
   ![Zadat Azure Data Lake Storage Gen1 účet](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na stránce **zvolit vstupní soubor nebo složku** přejděte do složky a souboru, který chcete zkopírovat. Vyberte složku nebo soubor a vyberte možnost **zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Určete chování při kopírování, a to tak, že vyberete **rekurzivní soubory kopírování** a možnosti **binárního kopírování** . Vyberte **Další**.

    ![Snímek obrazovky se zobrazeným vstupním souborem nebo složkou, kde můžete vybrat rekurzivní kopírování souborů a binární kopii.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na stránce **cílové úložiště dat** vyberte **+ vytvořit nové připojení**  >  **Azure Data Lake Storage Gen2**  >  **pokračovat**.

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na stránce **zadat Azure Data Lake Storage Gen2 připojení** proveďte následující kroky:

   a. V rozevíracím seznamu **název účtu úložiště** vyberte svůj účet s podporou Data Lake Storage Gen2.
   
   b. Vytvořte připojení výběrem možnosti **Dokončit**. Pak vyberte **Další**.
   
   ![Zadat Azure Data Lake Storage Gen2 účet](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na stránce **zvolte výstupní soubor nebo složku** zadejte **copyfromadlsgen1** jako název výstupní složky a vyberte **Další**. Data Factory vytvoří během kopírování odpovídající Azure Data Lake Storage Gen2 systém souborů a podsložek, pokud neexistují.

    ![Snímek obrazovky zobrazující cestu ke složce, kterou zadáte.](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na stránce **Nastavení** vyberte **Další**, aby se použilo výchozí nastavení.

12. Na stránce **Souhrn** zkontrolujte nastavení a klikněte na tlačítko **Další**.

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **stránce nasazení** vyberte **monitorovat** a monitorujte kanál.

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu.

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Pokud chcete zobrazit spuštění aktivit související se spuštěním kanálu, vyberte odkaz **Zobrazit spuštění aktivit** ve sloupci **Akce** . Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přejít zpátky k zobrazení spuštění kanálu, vyberte odkaz **kanály** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Chcete-li monitorovat podrobnosti o spuštění každé aktivity kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) v části **Akce** v zobrazení monitorování aktivit. Můžete monitorovat podrobnosti, jako je objem dat zkopírovaných ze zdroje, do jímky, propustnosti dat, kroky provádění s odpovídající dobou trvání a používané konfigurace.

    ![Podrobnosti o spuštění aktivity monitorování](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Ověřte, že se data zkopírují do účtu Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Osvědčené postupy

Pokud chcete vyhodnotit upgrade z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2 obecně, přečtěte si téma [Upgrade řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). Následující části obsahují osvědčené postupy pro použití Data Factory pro upgrade dat z Data Lake Storage Gen1 na Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Datový oddíl pro historická kopírování dat

- Pokud je celková velikost dat v Data Lake Storage Gen1 menší než 30 TB a počet souborů je menší než 1 000 000, můžete kopírovat všechna data v jednom spuštění aktivity kopírování.
- Pokud máte větší množství dat ke zkopírování nebo chcete, aby tato flexibilita spravovala migraci dat v dávkách a aby každý z nich byla dokončena v určitém časovém rámci, vytvořte oddíly dat. Rozdělení také snižuje riziko neočekávaného problému.

K ověření kompletního řešení a testování propustnosti kopírování ve vašem prostředí použijte zkoušku konceptu. Důležité kroky pro kontrolu konceptu: 

1. Vytvořte jeden Data Factory kanál s jednou aktivitou kopírování a zkopírujte několik služby TBs data z Data Lake Storage Gen1 na Data Lake Storage Gen2, abyste získali směrný plán výkonnosti kopírování. Začněte s [jednotkami Integration data (DIUs)](copy-activity-performance-features.md#data-integration-units) jako 128. 
2. Na základě propustnosti kopírování, kterou získáte v kroku 1, vypočítejte odhadovaný čas potřebný k migraci celé migrace dat. 
3. Volitelné Vytvořte řídicí tabulku a definujte filtr souborů pro rozdělení souborů, které chcete migrovat. Způsob rozdělení souborů na oddíly: 

    - Vytvořte oddíl podle názvu složky nebo složky pomocí filtru se zástupnými znaky. Doporučujeme tuto metodu.
    - Vytvoří oddíl podle času poslední změny souboru.

### <a name="network-bandwidth-and-storage-io"></a>I/O šířka pásma sítě a úložiště 

Můžete řídit souběžnost Data Factory kopírovat úlohy, které čtou data z Data Lake Storage Gen1 a zapisovat data do Data Lake Storage Gen2. Tímto způsobem můžete spravovat použití v rámci tohoto úložiště I/O, aby nedošlo ke vlivu na normální pracovní práci na Data Lake Storage Gen1 během migrace.

### <a name="permissions"></a>Oprávnění 

V Data Factory [konektor Data Lake Storage Gen1](connector-azure-data-lake-store.md) podporuje instanční objekt a spravovanou identitu pro ověřování prostředků Azure. [Konektor Data Lake Storage Gen2](connector-azure-data-lake-storage.md) podporuje klíč účtu, instanční objekt a spravovanou identitu pro ověřování prostředků Azure. Pokud chcete umožnit Data Factory procházení a kopírování všech souborů nebo seznamů řízení přístupu (ACL), které potřebujete, udělte vám dostatečná oprávnění pro účet, který zadáte pro přístup, čtení nebo zápis všech souborů a nastavení seznamů ACL, pokud se rozhodnete. Přidělte mu roli uživatele nebo vlastníka během období migrace. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachovat seznamy ACL z Data Lake Storage Gen1

Pokud chcete replikovat seznamy řízení přístupu spolu s datovými soubory při upgradu z Data Lake Storage Gen1 na Data Lake Storage Gen2, přečtěte si téma [zachování seznamů ACL z Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Přírůstková kopie 

Pomocí několika přístupů můžete načíst jenom nové nebo aktualizované soubory z Data Lake Storage Gen1:

- Načtěte nové nebo aktualizované soubory podle času děleného názvem složky nebo souboru. Příkladem je/2019/05/13/*.
- Načtěte nové nebo aktualizované soubory pomocí LastModifiedDate.
- Identifikujte nové nebo aktualizované soubory jakýmkoli nástrojem nebo řešením třetí strany. Potom předejte název souboru nebo složky do kanálu Data Factory pomocí parametru nebo tabulky nebo souboru. 

Správná frekvence přírůstkového načítání závisí na celkovém počtu souborů v Azure Data Lake Storage Gen1 a na objemu nových nebo aktualizovaných souborů, které mají být pokaždé načteny. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Přehled](copy-activity-overview.md) 
>  aktivit kopírování [Konektor Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md) 
>  [Konektor Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)