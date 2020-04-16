---
title: Kopírování dat z Azure Data Lake Storage Gen1 do Gen2
description: Kopírování dat z Azure Data Lake Storage Gen1 do Gen2 pomocí Azure Data Factory
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
ms.openlocfilehash: 6655510a4cfdb88e98319c7fc26c7ae83255bb6f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415827"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopírování dat z Azure Data Lake Storage Gen1 do Gen2 pomocí Azure Data Factory

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Azure Data Lake Storage Gen2 je sada funkcí určených pro analýzu velkých objemů dat, která je integrovaná do [úložiště objektů blob Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ji použít k rozhraní s daty pomocí paradigmat systému souborů a úložiště objektů.

Pokud aktuálně používáte Azure Data Lake Storage Gen1, můžete vyhodnotit Azure Data Lake Storage Gen2 zkopírováním dat z Data Lake Storage Gen1 do Gen2 pomocí Azure Data Factory.

Azure Data Factory je plně spravovaná cloudová služba pro integraci dat. Pomocí této služby můžete naplnit jezero daty z bohaté sady místních a cloudových úložišť dat a ušetřit čas při vytváření analytických řešení. Seznam podporovaných konektorů naleznete v tabulce [Podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí škálovatkapacitní řešení pro přesun y spravovaných dat. Z důvodu architektury horizontálnínavýšení kapacity data factory, může ingestovat data s vysokou propustností. Další informace naleznete v [tématu Kopírování výkonu aktivity](copy-activity-performance.md).

Tento článek ukazuje, jak pomocí nástroje kopírování dat data data data zkopírovat data z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2. Můžete postupovat podle podobných kroků ke kopírování dat z jiných typů úložišť dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/) než začnete.
* Účet Azure Data Lake Storage Gen1 s daty v něm.
* Účet Azure Storage s povoleným úložištěm Datového jezera Gen2. Pokud nemáte účet úložiště, [vytvořte si účet](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V levé nabídce vyberte **Vytvořit data o prostředku** > **+ Analytics** > **Data Factory**.
   
   ![Výběr datové továrny v podokně Nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na stránce **Nová továrna dat** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Nová stránka Factory dat](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Název:** Zadejte globálně jedinečný název pro vaši datovou továrnu Azure. Pokud se zobrazí chyba "Název \"datové továrny LoadADLSDemo\" není k dispozici", zadejte jiný název pro továrnu dat. Použijte například název _**vaše_jméno**_**ADFTutorialDataFactory**. Vytvořte znovu datovou továrnu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné:** Vyberte předplatné Azure, ve kterém chcete vytvořit datové továrny. 
    * **Skupina prostředků**: V rozevíracím seznamu vyberte existující skupinu prostředků. Můžete také vybrat možnost **Vytvořit nový** a zadat název skupiny prostředků. Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/management/overview.md). 
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění pro datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat používaná datovou továrnou se můžou nacházet v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Po dokončení vytváření přejděte do datové továrny. Zobrazí se domovská stránka **Data Factory,** jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka objektu pro vytváření dat](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Vyberte dlaždici **Author & Monitor** a spusťte aplikaci Integrace dat na samostatné kartě.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. Na stránce **Začínáme** vyberte dlaždici **Kopírovat data** a spusťte nástroj pro kopírování dat. 

   ![Kopírovat dlaždici datového nástroje](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na stránce **Vlastnosti** zadejte pro pole **Název úkolu** **příkaz CopyFromADLSGen1ToGen2.** Vyberte **Další**.

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na stránce **Zdrojové úložiště dat** vyberte **+ Vytvořit nové připojení**.

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. V galerii konektorů vyberte **Azure Data Lake Storage Gen1** a vyberte **Pokračovat**.
    
    ![Stránka Gen1 úložiště zdrojových dat Azure Data Lake Storage](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na stránce **Určení připojení Azure Data Lake Storage Gen1** postupujte takto:

   a. Vyberte gen1 úložiště datového jezera pro název účtu a zadejte nebo ověřte **tenanta**.
  
   b. Chcete-li ověřit nastavení, vyberte **možnost Testovat připojení.** Pak vyberte **Dokončit**.
  
   c. Uvidíte, že bylo vytvořeno nové připojení. Vyberte **Další**.
   
   > [!IMPORTANT]
   > V tomto návodu použijete spravovanou identitu pro prostředky Azure k ověření vašeho Azure Data Lake Storage Gen1. Chcete-li udělit spravované identitě správná oprávnění v Azure Data Lake Storage Gen1, postupujte [podle těchto pokynů](connector-azure-data-lake-store.md#managed-identity).
   
   ![Určení účtu Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na stránce **Vybrat vstupní soubor nebo složku** přejděte do složky a souboru, přes který chcete kopírovat. Vyberte složku nebo soubor a vyberte **Zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Určete chování kopírování výběrem možností **Rekurzivně kopírovat soubory** a **Binární kopírování.** Vyberte **Další**.

    ![Určení výstupní složky](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na stránce **Cílové úložiště dat** vyberte + Vytvořit nové **připojení** > **Azure Data Lake Storage Gen2** > **Continue**.

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na stránce Určení připojení Azure **Data Lake Storage Gen2** postupujte takto:

   a. V rozevíracím seznamu **Název účtu úložiště vyberte** svůj účet S.l.E.2.
   
   b. Vytvořte připojení výběrem možnosti **Dokončit**. Pak vyberte **Další**.
   
   ![Určení účtu Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na stránce **Vybrat výstupní soubor nebo složku** zadejte **copyfromadlsgen1** jako název výstupní složky a vyberte **Další**. Data Factory vytvoří odpovídající Azure Data Lake Storage Gen2 systém souborů a podsložky během kopírování, pokud neexistují.

    ![Určení výstupní složky](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na stránce **Nastavení** vyberte **Další**, aby se použilo výchozí nastavení.

12. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **Další**.

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **stránce Nasazení**vyberte **sledovat** kanál.

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu.

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Chcete-li zobrazit spuštění aktivity, které jsou přidruženy ke spuštění kanálu, vyberte odkaz **Zobrazit spuštění aktivity** ve sloupci **Akce.** Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Chcete-li přepnout zpět do zobrazení spuštění kanálu, vyberte odkaz **Potrubí** v horní části. Seznam můžete aktualizovat kliknutím na **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Chcete-li sledovat podrobnosti spuštění pro každou aktivitu kopírování, vyberte odkaz **Podrobnosti** (obrázek brýlí) v části **Akce** v zobrazení sledování aktivity. Můžete sledovat podrobnosti, jako je objem dat zkopírovaných ze zdroje do jímky, propustnost dat, kroky spuštění s odpovídající dobou trvání a použité konfigurace.

    ![Podrobnosti spuštění aktivity monitoru](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Ověřte, že se data zkopírují do vašeho účtu Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Osvědčené postupy

Pokud chcete vyhodnotit upgrady z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2 obecně, najdete v [tématu Upgrade řešení pro analýzu velkých objemů dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-migrate-gen1-to-gen2.md). Následující části představují osvědčené postupy pro použití data factory pro upgrade dat z Data Lake Storage Gen1 na Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Datový oddíl pro kopírování historických dat

- Pokud je celková velikost dat v gen1 úložiště datového jezera menší než 30 TB a počet souborů je menší než 1 milion, můžete všechna data zkopírovat v jednom spuštění aktivity kopírování.
- Pokud máte větší množství dat ke kopírování nebo chcete flexibilitu pro správu migrace dat v dávkách a provést každý z nich dokončit v určitém časovém rámci, rozdělení dat. Dělení také snižuje riziko neočekávaného problému.

Použijte ověření konceptu k ověření komplexního řešení a testování propustnosti kopírování ve vašem prostředí. Hlavní kroky proof-of-concept: 

1. Vytvořte jeden kanál data factory s jednou aktivitou kopírování pro kopírování několika objektů TB dat z Data Lake Storage Gen1 do Data Lake Storage Gen2, abyste získali směrný plán výkonu kopírování. Začněte s [jednotkami integrace dat (DIU)](copy-activity-performance-features.md#data-integration-units) jako 128. 
2. Na základě propustnosti kopírování, kterou získáte v kroku 1, vypočítejte odhadovaný čas, který je nutný pro celou migraci dat. 
3. (Nepovinné) Vytvořte řídicí tabulku a definujte filtr souborů pro rozdělení souborů, které mají být přeneseny. Způsob, jak rozdělit soubory je: 

    - Oddíl podle názvu složky nebo názvu složky pomocí filtru se zástupnými symboly. Doporučujeme tuto metodu.
    - Oddíl podle posledního upraveného času souboru.

### <a name="network-bandwidth-and-storage-io"></a>Šířka pásma sítě a vstupně-to služby 

Můžete řídit souběžnost úloh kopírování data factory, které čtou data z Data Lake Storage Gen1 a zapisují data do Data Lake Storage Gen2. Tímto způsobem můžete spravovat použití na tomto vstupně-v.o, aby se zabránilo ovlivnění běžné obchodní práce na Datové jezero Storage Gen1 během migrace.

### <a name="permissions"></a>Oprávnění 

V datové továrně [konektor Gen1 úložiště datového jezera](connector-azure-data-lake-store.md) podporuje instanční objekt a spravovanou identitu pro ověřování prostředků Azure. Konektor [Data Lake Storage Gen2](connector-azure-data-lake-storage.md) podporuje klíč účtu, instanční objekt a spravovanou identitu pro ověřování prostředků Azure. Chcete-li, aby aplikace Data Factory mohla procházet a kopírovat všechny soubory nebo seznamy řízení přístupu (ACL), které potřebujete, udělte dostatečně vysoká oprávnění pro účet, který poskytujete pro přístup, čtení nebo zápis všech souborů, a nastavte seznamy ACL, pokud se tak rozhodnete. Udělte roli superuživatele nebo vlastníka během období migrace. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachovat akly z úložiště datového jezera Gen1

Pokud chcete replikovat seznamy ACL spolu s datovými soubory při upgradu z Data Lake Storage Gen1 na Data Lake Storage Gen2, přečtěte si informace [o zachování seznamů ACL z gen1 úložiště datového jezera](connector-azure-data-lake-storage.md#preserve-acls). 

### <a name="incremental-copy"></a>Přírůstková kopie 

K načtení pouze nových nebo aktualizovaných souborů z aplikace Data Lake Storage Gen1 můžete použít několik přístupů:

- Načtěte nové nebo aktualizované soubory podle času, kdy byla složka nebo název souboru rozdělena. Příkladem je /2019/05/13/*.
- Načtení nových nebo aktualizovaných souborů podle lastmodifieddate.
- Identifikujte nové nebo aktualizované soubory jakýmkoli nástrojem nebo řešením jiného výrobce. Potom předejte název souboru nebo složky kanálu Data Factory prostřednictvím parametru nebo tabulky nebo souboru. 

Správná frekvence přírůstkového načítání závisí na celkovém počtu souborů v Azure Data Lake Storage Gen1 a na objemu nových nebo aktualizovaných souborů, které se mají načíst pokaždé. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Kopírování přehledu aktivit Azure](copy-activity-overview.md)
> [Data Lake Storage Gen1 konektor](connector-azure-data-lake-store.md)
> [u konektoru Azure Data Lake Storage Gen2 konektor](connector-azure-data-lake-storage.md)