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
ms.date: 05/13/2019
ms.author: jingwang
ms.openlocfilehash: d13dad6e87bd6c821b497138ad75d7ae2b9a052d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67068978"
---
# <a name="copy-data-from-azure-data-lake-storage-gen1-to-gen2-with-azure-data-factory"></a>Kopírování dat z Azure Data Lake Storage Gen1 na Gen2 s Azure Data Factory

Azure Data Lake Storage Gen2 je sada funkcí, které jsou vyhrazené pro analýzy velkých objemů dat, která je integrována do [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md). Můžete ho pro rozhraní s daty pomocí obou paradigmat souboru systému a objektu úložiště.

Pokud aktuálně používáte službu Azure Data Lake Storage Gen1, můžete vyhodnotit Azure Data Lake Storage Gen2 zkopírováním dat z Data Lake Storage Gen1 na Gen2 pomocí služby Azure Data Factory.

Azure Data Factory je služba pro integraci plně spravovaný cloudový datový. Služby můžete použít k naplnění lake s daty z široké místních a cloudových dat ukládá a ušetřit čas při vytváření vlastních analytických řešení. Seznam podporovaných konektorů, najdete v tabulce [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).

Azure Data Factory nabízí řešení pro přesun dat horizontální navýšení kapacity, kterou spravuje. Architektura škálování na více instancí služby Data Factory se schopností ingestovat data při vysoké propustnosti. Další informace najdete v tématu [výkonu aktivity kopírování](copy-activity-performance.md).

V tomto článku se dozvíte, jak používat nástroj pro kopírování dat Data Factory pro kopírování dat z Azure Data Lake Storage Gen1 do Azure Data Lake Storage Gen2. Můžete použít podobným způsobem kopírovat data z jiných typů úložišť dat.

## <a name="prerequisites"></a>Požadavky

* Předplatné Azure. Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.
* Účet Azure Data Lake Storage Gen1 s daty.
* Účet služby Azure Storage s Data Lake Storage Gen2 povolena. Pokud nemáte účet úložiště [vytvořit účet](https://ms.portal.azure.com/#create/Microsoft.StorageAccount-ARM).

## <a name="create-a-data-factory"></a>Vytvoření datové továrny

1. V nabídce vlevo vyberte **vytvořit prostředek** > **Data a analýzy** > **služby Data Factory**.
   
   ![Výběr datové továrny v podokně nový](./media/quickstart-create-data-factory-portal/new-azure-data-factory-menu.png)

2. Na **nová datová továrna** zadejte hodnoty pro pole, která jsou zobrazena na následujícím obrázku: 
      
   ![Stránka Nová datová továrna](./media/load-azure-data-lake-storage-gen2-from-gen1/new-azure-data-factory.png)
 
    * **Název**: Zadejte globálně jedinečný název pro službu Azure data factory. Pokud se zobrazí chyba "název objektu pro vytváření dat \"LoadADLSDemo\" není k dispozici," Zadejte jiný název datové továrny. Použijte například název _**vaše_jméno**_ **ADFTutorialDataFactory**. Vytvořte datovou továrnu znovu. Pravidla pojmenování artefaktů služby Data Factory najdete v tématu [Data Factory – pravidla pojmenování](naming-rules.md).
    * **Předplatné**: Vyberte své předplatné Azure, ve kterém chcete datovou továrnu vytvořit. 
    * **Skupina prostředků**: Z rozevíracího seznamu vyberte existující skupinu prostředků. Také můžete vybrat **vytvořit nový** možnost a zadejte název skupiny prostředků. Informace o skupinách prostředků najdete v tématu [Použití skupin prostředků ke správě prostředků Azure](../azure-resource-manager/resource-group-overview.md). 
    * **Verze**: Vyberte **V2**.
    * **Umístění**: Vyberte umístění datové továrny. V rozevíracím seznamu se zobrazí pouze podporovaná umístění. Úložiště dat, které jsou používané datovou továrnou můžou být v jiných umístěních a oblastech. 

3. Vyberte **Vytvořit**.
4. Po vytvoření, přejděte do služby data factory. Zobrazí **služby Data Factory** domovskou stránku, jak je znázorněno na následujícím obrázku: 
   
   ![Domovská stránka datové továrny](./media/load-azure-data-lake-storage-gen2-from-gen1/data-factory-home-page.png)

5. Vyberte **vytvořit a monitorovat** dlaždice aplikace integrace dat na samostatné kartě.

## <a name="load-data-into-azure-data-lake-storage-gen2"></a>Načtení dat do Azure Data Lake Storage Gen2

1. Na **Začínáme** stránky, vyberte **kopírování dat** dlaždice nástroje pro kopírování dat. 

   ![Dlaždice nástroj pro kopírování dat](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-tile.png)
2. Na **vlastnosti** určete, **CopyFromADLSGen1ToGen2** pro **název úkolu** pole. Vyberte **Další**.

    ![Stránka Vlastnosti](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-data-tool-properties-page.png)
3. Na **zdrojového úložiště dat** stránce **+ vytvořit nové připojení**.

    ![Stránka Zdrojové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page.png)
    
4. Vyberte **Azure Data Lake Storage Gen1** z Galerie konektoru a vyberte **pokračovat**.
    
    ![Azure Data Lake Storage Gen1 stránka zdrojového úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/source-data-store-page-adls-gen1.png)
    
5. Na **připojení zadejte Azure Data Lake Storage Gen1** stránce, postupujte podle těchto kroků:

   a. Vyberte vaše Data Lake Storage Gen1 pro název účtu a zadat nebo ověřit **Tenanta**.
  
   b. Vyberte **Test připojení** chcete ověřit nastavení. Pak vyberte **Dokončit**.
  
   c. Uvidíte, že bylo vytvořeno nové připojení. Vyberte **Další**.
   
   > [!IMPORTANT]
   > V tomto názorném postupu použijete k ověřování vaší Azure Data Lake Storage Gen1 spravovanou identitu pro prostředky Azure. Chcete-li udělit příslušná oprávnění v Azure Data Lake Storage Gen1 spravovanou identitu, postupujte podle [tyto pokyny](connector-azure-data-lake-store.md#managed-identity).
   
   ![Zadejte účet Azure Data Lake Storage Gen1](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen1-account.png)
      
6. Na **zvolte vstupní soubor nebo složku** stránce, přejděte do složky a souboru, který chcete zkopírovat. Vyberte složky nebo souboru a vyberte **zvolit**.

    ![Zvolte vstupní soubor nebo složku](./media/load-azure-data-lake-storage-gen2-from-gen1/choose-input-folder.png)

7. Zadejte chování kopírování tak, že vyberete **kopírovat soubory rekurzivně** a **binární kopie** možnosti. Vyberte **Další**.

    ![Zadejte výstupní složka](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-binary-copy.png)
    
8. Na **cílového úložiště dat** stránce **+ vytvořit nové připojení** > **Azure Data Lake Storage Gen2**  >   **Pokračovat**.

    ![Stránka Cílové úložiště dat](./media/load-azure-data-lake-storage-gen2-from-gen1/destination-data-storage-page.png)

9. Na **připojení zadejte Azure Data Lake Storage Gen2** stránce, postupujte podle těchto kroků:

   a. Vyberte svůj účet Data Lake Storage Gen2 podporující z **název účtu úložiště** rozevíracího seznamu.
   
   b. Vyberte **Dokončit** k vytvoření připojení. Pak vyberte **Další**.
   
   ![Zadejte účet Azure Data Lake Storage Gen2](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-account.png)

10. Na **zvolte výstupní soubor nebo složku** zadejte **copyfromadlsgen1** jako název složky výstupu a vyberte **Další**. Data Factory vytvoří odpovídající systém souborů Azure Data Lake Storage Gen2 a podsložky během kopírování, pokud ještě neexistují.

    ![Zadejte výstupní složka](./media/load-azure-data-lake-storage-gen2-from-gen1/specify-adls-gen2-path.png)

11. Na **nastavení** stránce **Další** použít výchozí nastavení.

12. Na **Souhrn** stránky, zkontrolujte nastavení a vyberte **Další**.

    ![Stránka souhrnu](./media/load-azure-data-lake-storage-gen2-from-gen1/copy-summary.png)
13. Na **stránku nasazení**vyberte **monitorování** a začněte monitorovat kanál.

    ![Stránka Nasazení](./media/load-azure-data-lake-storage-gen2-from-gen1/deployment-page.png)
14. Všimněte si, že je vlevo automaticky vybraná karta **Monitorování**. Sloupec **Akce** obsahuje odkazy na zobrazení podrobností o spuštění aktivit a na opětovné spuštění kanálu.

    ![Monitorování spuštění kanálu](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-pipeline-runs.png)

15. Pokud chcete zobrazit spuštění aktivit, které jsou spojeny se spuštěním kanálu, vyberte **zobrazit spuštění aktivit** odkaz v **akce** sloupce. Kanál obsahuje pouze jednu aktivitu (aktivita kopírování), takže se zobrazí pouze jedna položka. Pokud chcete přepnout zpět na zobrazení spuštění kanálu, vyberte **kanály** odkazu v horní části. Seznam můžete aktualizovat výběrem možnosti **Aktualizovat**. 

    ![Monitorování spuštění aktivit](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-runs.png)

16. Pokud chcete monitorovat spuštění podrobnosti o každé aktivitě kopírování, vyberte **podrobnosti** odkaz (obrázek brýlí) ve skupinovém rámečku **akce** v aktivitě zobrazení monitorování. Podrobnosti můžete sledovat, jako je objem dat zkopírovanou ze zdroje do jímky, propustnost dat, provádění kroků s určitou dobu a použít konfigurace.

    ![Podrobnosti o spuštění aktivit monitorování](./media/load-azure-data-lake-storage-gen2-from-gen1/monitor-activity-run-details.png)

17. Ověřte, že se data kopírují do svého účtu Azure Data Lake Storage Gen2.

## <a name="best-practices"></a>Osvědčené postupy

K vyhodnocení, upgrade z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2 obecně platí, naleznete v tématu [Upgrade vlastních analytických řešení pro velké objemy dat z Azure Data Lake Storage Gen1 na Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-upgrade.md). Následující části představují doporučené postupy pro používání služby Data Factory pro upgrade dat z Data Lake Storage Gen1 Data Lake Storage Gen2.

### <a name="data-partition-for-historical-data-copy"></a>Oddíl dat pro kopírování historických dat.

- Pokud vaše celková velikost dat v Data Lake Storage Gen1 je menší než 30 TB a počet souborů, které je menší než 1 milion, můžete zkopírovat všechna data v aktivitě kopírování jednoho spuštění.
- Pokud máte větší množství dat pro kopírování nebo chtějí flexibilně využívat ke správě migrace dat v dávkách a ujistěte se, každý z nich dokončení v konkrétním časovém rámci, rozdělte data. Dělení také snižuje riziko jakýkoli neočekávaný problém.

Testování konceptu použijte k ověření-ucelené řešení a testovací kopie propustnosti ve vašem prostředí. Hlavních kroků testování konceptu: 

1. Vytvořte jeden kanál služby Data Factory s aktivitou jeden kopírování pro kopírování několika TB dat z Data Lake Storage Gen1 do Data Lake Storage Gen2 získat základní úrovně výkonu kopírování. Začněte s [jednotky integrace dat (DIUs)](copy-activity-performance.md#data-integration-units) jako 128. 
2. Odhadovaná doba potřebná pro veškerá data migrace na základě kopie propustnosti, který jste získali v kroku 1, výpočtu. 
3. (Volitelné) Vytvoření ovládacího prvku tabulky a zadejte filtr souborů při vytváření oddílů soubory, které chcete migrovat. Způsob rozdělení souborů je: 

    - Oddíl podle názvu složky nebo název složky s filtrem zástupný znak. Doporučujeme tuto metodu.
    - Oddíl, do souboru na čas poslední změny.

### <a name="network-bandwidth-and-storage-io"></a>Šířku pásma a úložiště v / v sítě 

Můžete řídit souběžnost úloh kopírování objektu pro vytváření dat číst data z Data Lake Storage Gen1 a zapisování dat do Data Lake Storage Gen2. Tímto způsobem můžete spravovat používání na vstupy/výstupy, aby během migrace by to ovlivnilo práce běžných obchodních v Data Lake Storage Gen1 tohoto úložiště.

### <a name="permissions"></a>Oprávnění 

Ve službě Data Factory [konektor Data Lake Storage Gen1](connector-azure-data-lake-store.md) podporuje služby instančního objektu a spravovaných identit pro ověřování prostředků Azure. [Konektor Data Lake Storage Gen2](connector-azure-data-lake-storage.md) podporuje účet klíče, instanční objekt a spravovaná identita pro ověřování prostředků Azure. Chcete-li přejděte a zkopírujte všechny soubory nebo seznamy řízení přístupu (ACL potřebujete, vysoká udělení dostatečných oprávnění pro účet, který poskytnete přístup, čtení, nebo zápis všechny soubory a pokud budete chtít nastavit seznamy ACL) služby Data Factory. Udělte mu role superuživatele nebo vlastník během období migrace. 

### <a name="preserve-acls-from-data-lake-storage-gen1"></a>Zachovat seznamy ACL v Data Lake Storage Gen1

Pokud chcete replikovat seznamy ACL spolu s datovými soubory při upgradu z Data Lake Storage Gen1 na Gen2 úložiště Data Lake, přečtěte si téma [zachovat seznamy ACL v Data Lake Storage Gen1](connector-azure-data-lake-storage.md#preserve-acls-from-data-lake-storage-gen1). 

### <a name="incremental-copy"></a>Přírůstkové kopírování 

Můžete použít několik přístupů k načtení jenom nových nebo aktualizovaných souborů z Data Lake Storage Gen1:

- Načtení nových nebo aktualizovaných souborů podle názvu dělené soubor nebo složku času. Příkladem je/2019/05/13 / *.
- Načtení nových nebo aktualizovaných souborů pomocí LastModifiedDate.
- Identifikaci nových nebo aktualizovaných souborů pomocí libovolného nástroje třetí strany nebo řešení. Pomocí parametru, tabulky nebo soubor předejte název souboru nebo složky do kanálu služby Data Factory. 

Správné frekvence provádět přírůstkové načítání závisí na celkový počet souborů v Azure Data Lake Storage Gen1 a objemu nových nebo aktualizovaných souborů, které mají být načteny pokaždé, když. 

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Přehled aktivit kopírování](copy-activity-overview.md)
> [konektor Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)
> [konektor Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md)