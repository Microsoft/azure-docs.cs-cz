---
title: Analyzovat data v Azure Data Lake Storage Gen2 pomocí Power BI | Microsoft Docs
description: Použití Power BI k analýze dat uložených v Azure Data Lake Storage Gen2
author: normesta
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.topic: conceptual
ms.date: 07/18/2019
ms.author: normesta
ms.reviewer: bensack
ms.openlocfilehash: cb82d47be11570baf7865468c804162a20a7685f
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2019
ms.locfileid: "68855471"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen2-by-using-power-bi"></a>Analýza dat v Azure Data Lake Storage Gen2 pomocí Power BI

V tomto článku se naučíte, jak pomocí Power BI Desktop analyzovat a vizualizovat data, která jsou uložená v účtu úložiště s hierarchickým oborem názvů (Azure Data Lake Storage Gen2).

## <a name="prerequisites"></a>Požadavky

Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

> [!div class="checklist"]
> * Předplatné Azure. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
> * Účet úložiště, který má hierarchický obor názvů. Pokud ho chcete vytvořit, postupujte podle [těchto](data-lake-storage-quickstart-create-account.md) pokynů.
> V tomto článku se předpokládá, že jste vytvořili účet `myadlsg2`s názvem.
> * Ukázkový datový soubor s názvem `Drivers.txt` umístěný ve vašem účtu úložiště.
> Tuto ukázku si můžete stáhnout z [Azure Data Lake úložiště Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceDataDrivers.txt)a pak tento soubor nahrát do svého účtu úložiště.
> * **Power BI Desktop**. Můžete si ho stáhnout z [webu Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu

1. V počítači spusťte Power BI Desktop.
2. Na kartě **Domů** na pásu karet klikněte na **získat data**a pak klikněte na **Další**.
3. V dialogovém okně **získat data** klikněte na **Azure**, klikněte na **Azure Data Lake Store Gen2 (beta)** a pak klikněte na **připojit**.

    ![Stránka získat data](media/data-lake-storage-use-power-bi/get-data-page.png)

4. V dialogovém okně **Azure Data Lake Storage Gen2** můžete zadat adresu URL účtu Azure Data Lake Storage Gen2, systému souborů nebo podsložky pomocí formátu koncového bodu systému souborů. Adresy URL pro data Lake Storage Gen2 mají následující vzor `https://<accountname>.dfs.core.windows.net/<filesystemname>/<subfolder>` a pak klikněte na tlačítko **OK**.

    ![URL](media/data-lake-storage-use-power-bi/adls-url.png)

5. V dalším dialogovém okně klikněte na **Přihlásit** se a přihlaste se k účtu úložiště. Budete přesměrováni na přihlašovací stránku vaší organizace. Podle pokynů se přihlaste k účtu.

    ![Přihlašovací stránka](media/data-lake-storage-use-power-bi/sign-in.png)

6. Po úspěšném přihlášení klikněte na **připojit**.

    ![Přihlášená stránka](media/data-lake-storage-use-power-bi/signed-in.png)

7. V dalším dialogovém okně se zobrazí všechny soubory pod adresou URL, kterou jste zadali v kroku 4, včetně souboru, který jste nahráli do svého účtu úložiště. Ověřte informace a klikněte na tlačítko **načíst**.

    ![Systémy souborů](media/data-lake-storage-use-power-bi/file-systems.png)

8. Po úspěšném načtení dat do Power BI se na kartě **pole** zobrazí následující pole.

    ![Karta pole](media/data-lake-storage-use-power-bi/fields.png)

    Chcete-li však vizualizovat a analyzovat data, doporučujeme, aby data byla k dispozici podle následujících polí.

    ![Fields (Pole)](media/data-lake-storage-use-power-bi/preferred-fields.png)

    V dalších krocích aktualizujeme dotaz na převod importovaných dat v požadovaném formátu.

9. Na kartě **Domů** na pásu karet klikněte na **Upravit dotazy**.

    ![Dotazy](media/data-lake-storage-use-power-bi/queries.png)

10. V **Editoru dotazů**pod sloupcem **obsah** klikněte na **binární**. Soubor bude automaticky rozpoznán jako sdílený svazek clusteru a měl by se zobrazit výstup, jak je uvedeno níže. Vaše data jsou teď dostupná ve formátu, který můžete použít k vytváření vizualizací.

    ![Výstup](media/data-lake-storage-use-power-bi/binary.png)

11. Na kartě **Domů** na pásu karet klikněte na tlačítko **Zavřít** a **použít**a pak klikněte na tlačítko **Zavřít** a **použít**.

    ![Zavřít a použít](media/data-lake-storage-use-power-bi/close-apply.png)

12. Po aktualizaci dotazu se na kartě **pole** zobrazí nová pole, která jsou k dispozici pro vizualizaci.

    ![Nová pole](media/data-lake-storage-use-power-bi/new-fields.png)

13. Můžeme vytvořit výsečový graf, který bude reprezentovat ovladače v jednotlivých městech pro danou zemi. Provedete to tak, že provedete následující výběry.

    Na kartě **vizualizace** klikněte na symbol výsečového grafu.

    ![Vizualizace](media/data-lake-storage-use-power-bi/visualizations.png)

    Sloupce, které použijeme, jsou sloupce 4 (název města) a sloupec 7 (název země). Přetáhněte tyto sloupce z karty **pole** na kartu **vizualizace** , jak vidíte níže.

    ![Přetáhnout pole](media/data-lake-storage-use-power-bi/visualizations-drag-fields.png)

    Výsečový graf by měl nyní vypadat podobně jako na následujícím obrázku.

    ![Výsečový graf](media/data-lake-storage-use-power-bi/pie-chart.png)

14. Výběrem konkrétní země z filtrů na úrovni stránek teď můžete zobrazit počet ovladačů v každém městě vybrané země. Například na kartě vizualizace v části **filtry na úrovni stránky**vyberte **Brazílie**.

    ![Filtry stránky](media/data-lake-storage-use-power-bi/page-filters.png)

15. Výsečový graf se automaticky aktualizuje, aby zobrazoval ovladače ve městech Brazílie.

    ![Brazílie](media/data-lake-storage-use-power-bi/pie-chart-updated.png)

16. V nabídce **soubor** klikněte na **Uložit** a uložte vizualizaci jako soubor Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikovat sestavu pro služba Power BI

Po vytvoření vizualizací v Power BI Desktop ji můžete sdílet s ostatními tím, že ji publikujete do služba Power BI. Pokyny k tomu, jak to provést, najdete v tématu [publikování z Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).