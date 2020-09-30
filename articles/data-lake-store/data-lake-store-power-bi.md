---
title: Analyzovat data v Azure Data Lake Storage Gen1 Power BI
description: Naučte se používat Power BI Desktop k analýze a vizualizaci dat uložených v Azure Data Lake Storage Gen1.
author: twooley
ms.service: data-lake-store
ms.topic: how-to
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: bcc561cd5eea4372d798fff4580362ba0879c3a4
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2020
ms.locfileid: "91574190"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analýza dat v Azure Data Lake Storage Gen1 pomocí Power BI
V tomto článku se naučíte, jak pomocí Power BI Desktop analyzovat a vizualizovat data uložená v Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Storage Gen1 pomocí Azure Portal](data-lake-store-get-started-portal.md). V tomto článku se předpokládá, že jste už vytvořili účet Data Lake Storage Gen1 s názvem **myadlsg1**a nahráli jste do něj Ukázkový datový soubor (**Drivers.txt**). Tento ukázkový soubor je k dispozici ke stažení z [Azure Data Lake úložiště Git](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Můžete si ho stáhnout z [webu Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu
1. V počítači spusťte Power BI Desktop.
2. Na pásu karet **Domů** klikněte na **získat data**a pak klikněte na další. V dialogovém okně **získat data** klikněte na **Azure**, klikněte na **Azure Data Lake Store**a pak klikněte na **připojit**.
   
    ![Snímek obrazovky dialogového okna načíst data s zvýrazněnou možností Azure Data Lake Store a možností připojit se vyvolala.](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Připojení k Data Lake Storage Gen1")
3. Pokud se zobrazí dialogové okno s konektorem v vývojové fázi, nezapomeňte pokračovat.
4. V dialogovém okně **Azure Data Lake Store** zadejte adresu URL účtu Data Lake Storage Gen1 a pak klikněte na tlačítko **OK**.
   
    ![Adresa URL pro Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Adresa URL pro Data Lake Storage Gen1")
5. V dalším dialogovém okně klikněte na **Přihlásit** se a přihlaste se k účtu Data Lake Storage Gen1. Budete přesměrováni na přihlašovací stránku vaší organizace. Podle pokynů se přihlaste k účtu.
   
    ![Přihlaste se Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Přihlaste se Data Lake Storage Gen1")
6. Po úspěšném přihlášení klikněte na **připojit**.
   
    ![Snímek obrazovky dialogového okna Azure Data Lake Store s možností připojit s názvem.](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Připojení k Data Lake Storage Gen1")
7. V dalším dialogovém okně se zobrazí soubor, který jste nahráli do účtu Data Lake Storage Gen1. Ověřte informace a klikněte na **načíst**.
   
    ![Načíst data z Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Načíst data z Data Lake Storage Gen1")
8. Po úspěšném načtení dat do Power BI se na kartě **pole** zobrazí následující pole.
   
    ![Importovaná pole](./media/data-lake-store-power-bi/imported-fields.png "Importovaná pole")
   
    Pro vizualizaci a analýzu dat ale dáváte přednost datům, která jsou dostupná podle následujících polí.
   
    ![Požadovaná pole](./media/data-lake-store-power-bi/desired-fields.png "Požadovaná pole")
   
    V dalších krocích aktualizujeme dotaz na převod importovaných dat v požadovaném formátu.
9. Na pásu karet **Domů** klikněte na **Upravit dotazy**.
   
    ![Snímek obrazovky pásu karet domů s možností upravit dotazy s názvem.](./media/data-lake-store-power-bi/edit-queries.png "Úprava dotazů")
10. V editoru dotazů pod sloupcem **obsah** klikněte na **binární**.
    
    ![Snímek obrazovky editoru dotazů se sloupcem obsahu s názvem.](./media/data-lake-store-power-bi/convert-query1.png "Úprava dotazů")
11. Zobrazí se ikona souboru, která představuje soubor **Drivers.txt** , který jste nahráli. Klikněte na soubor pravým tlačítkem a klikněte na **CSV**.    
    
    ![Snímek obrazovky editoru dotazů s navolanou možností sdíleného svazku clusteru](./media/data-lake-store-power-bi/convert-query2.png "Úprava dotazů")
12. Měl by se zobrazit výstup, jak je znázorněno níže. Vaše data jsou teď dostupná ve formátu, který můžete použít k vytváření vizualizací.
    
    ![Snímek obrazovky editoru dotazů s výstupem zobrazeným podle očekávání](./media/data-lake-store-power-bi/convert-query3.png "Úprava dotazů")
13. Na pásu karet **Domů** klikněte na **Zavřít a použít**a pak klikněte na **Zavřít a použít**.
    
    ![Snímek obrazovky pásu karet domů s možností zavřít a použít s názvem.](./media/data-lake-store-power-bi/load-edited-query.png "Úprava dotazů")
14. Po aktualizaci dotazu se na kartě **pole** zobrazí nová pole, která jsou k dispozici pro vizualizaci.
    
    ![Aktualizovaná pole](./media/data-lake-store-power-bi/updated-query-fields.png "Aktualizovaná pole")
15. Můžeme vytvořit výsečový graf, který bude reprezentovat ovladače v jednotlivých městech pro danou zemi nebo oblast. Provedete to tak, že provedete následující výběry.
    
    1. Na kartě vizualizace klikněte na symbol výsečového grafu.
       
        ![Vytvoření výsečového grafu](./media/data-lake-store-power-bi/create-pie-chart.png "Vytvoření výsečového grafu")
    2. Sloupce, které použijeme, jsou **sloupce 4** (název města) a **sloupec 7** (název země/oblasti). Přetáhněte tyto sloupce z karty **pole** na kartu **vizualizace** , jak vidíte níže.
       
        ![Vytváření vizualizací](./media/data-lake-store-power-bi/create-visualizations.png "Vytváření vizualizací")
    3. Výsečový graf by měl nyní vypadat podobně jako na následujícím obrázku.
       
        ![Výsečový graf](./media/data-lake-store-power-bi/pie-chart.png "Vytváření vizualizací")
16. Výběrem konkrétní země nebo oblasti z filtrů na úrovni stránek teď můžete zobrazit počet ovladačů v každém městě vybrané země nebo oblasti. Například na kartě **vizualizace** v části **filtry na úrovni stránky**vyberte **Brazílie**.
    
    ![Vybrat zemi/oblast](./media/data-lake-store-power-bi/select-country.png "Vybrat zemi/oblast")
17. Výsečový graf se automaticky aktualizuje, aby zobrazoval ovladače ve městech Brazílie.
    
    ![Ovladače v zemi nebo oblasti](./media/data-lake-store-power-bi/driver-per-country.png "Ovladače na zemi nebo oblast")
18. V nabídce **soubor** klikněte na **Uložit** a uložte vizualizaci jako soubor Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikovat sestavu pro služba Power BI
Po vytvoření vizualizací v Power BI Desktop můžete ji sdílet s ostatními tím, že ji publikujete do služba Power BI. Pokyny k tomu, jak to provést, najdete v tématu [publikování z Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Viz také
* [Analyzovat data v Data Lake Storage Gen1 pomocí Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

