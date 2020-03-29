---
title: Analýza dat v Gen1 úložiště datových jezer Azure pomocí Power BI | Dokumenty společnosti Microsoft
description: Použití Power BI k analýze dat uložených v Azure Data Lake Storage Gen1
services: data-lake-store
documentationcenter: ''
author: twooley
manager: mtillman
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: twooley
ms.openlocfilehash: d8717b8f365e692b5f27bf8a04d65c5147b8f31b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65603213"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Analýza dat v Azure Data Lake Storage Gen1 pomocí Power BI
V tomto článku se dozvíte, jak pomocí Power BI Desktopu analyzovat a vizualizovat data uložená v Azure Data Lake Storage Gen1.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Gen1 úložiště datového jezera**. Postupujte podle pokynů na [webu Začínáme s Azure Data Lake Storage Gen1 pomocí portálu Azure](data-lake-store-get-started-portal.md). Tento článek předpokládá, že jste již vytvořili účet Data Lake Storage Gen1, nazvaný **myadlsg1**, a nahráli do něj ukázkový datový soubor (**Drivers.txt**). Tento ukázkový soubor je k dispozici ke stažení z [azure data lake git repozitáře](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Můžete si jej stáhnout ze [služby Stažení softwaru](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu
1. Spusťte Power BI Desktop na počítači.
2. Na pásu karet **Domů** klikněte na **Získat data**a potom klikněte na Další. V dialogovém okně **Získat data** klikněte na **Azure**, klikněte na Azure Data **Lake Store**a potom klikněte na **Připojit**.
   
    ![Připojení k úložišti datových jezer Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "Připojení k úložišti datových jezer Gen1")
3. Pokud se zobrazí dialogové okno o spojnici, která je ve fázi vývoje, rozhodněte se pokračovat.
4. V dialogovém okně **Azure Data Lake Store** zadejte adresu URL svého účtu Data Lake Storage Gen1 a klikněte na **OK**.
   
    ![Adresa URL pro Úložiště datového jezera Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "Adresa URL pro Úložiště datového jezera Gen1")
5. V dalším dialogovém okně klikněte na **Přihlásit** se a přihlaste se k účtu Data Lake Storage Gen1. Budete přesměrováni na přihlašovací stránku vaší organizace. Podle pokynů se přihlaste k účtu.
   
    ![Přihlášení do úložiště datového jezera Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "Přihlášení do úložiště datového jezera Gen1")
6. Po úspěšném přihlášení klepněte na tlačítko **Připojit**.
   
    ![Připojení k úložišti datových jezer Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "Připojení k úložišti datových jezer Gen1")
7. V dalším dialogovém okně se zobrazí soubor, který jste nahráli do účtu Data Lake Storage Gen1. Ověřte informace a klepněte na tlačítko **Načíst**.
   
    ![Načtení dat z úložiště datového jezera Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "Načtení dat z úložiště datového jezera Gen1")
8. Po úspěšném načtení dat do Power BI se na kartě **Pole** zobrazí následující pole.
   
    ![Importovaná pole](./media/data-lake-store-power-bi/imported-fields.png "Importovaná pole")
   
    Pro vizualizaci a analýzu dat však upřednostňujeme, aby data byla k dispozici v následujících polích.
   
    ![Požadovaná pole](./media/data-lake-store-power-bi/desired-fields.png "Požadovaná pole")
   
    V dalších krocích aktualizujeme dotaz tak, aby převedl importovaná data v požadovaném formátu.
9. Na pásu **karet Domů** klikněte na **Upravit dotazy**.
   
    ![Úprava dotazů](./media/data-lake-store-power-bi/edit-queries.png "Úprava dotazů")
10. V Editoru dotazů klikněte ve sloupci **Obsah** na **Binární**.
    
    ![Úprava dotazů](./media/data-lake-store-power-bi/convert-query1.png "Úprava dotazů")
11. Zobrazí se ikona souboru, která představuje soubor **Drivers.txt,** který jste nahráli. Klepněte pravým tlačítkem myši na soubor a klepněte na příkaz **CSV**.    
    
    ![Úprava dotazů](./media/data-lake-store-power-bi/convert-query2.png "Úprava dotazů")
12. Měli byste vidět výstup, jak je znázorněno níže. Data jsou nyní k dispozici ve formátu, který můžete použít k vytvoření vizualizací.
    
    ![Úprava dotazů](./media/data-lake-store-power-bi/convert-query3.png "Úprava dotazů")
13. Na pásu karet **Domů** klikněte na **Zavřít a použít**a potom klikněte na Zavřít a **použít**.
    
    ![Úprava dotazů](./media/data-lake-store-power-bi/load-edited-query.png "Úprava dotazů")
14. Po aktualizaci dotazu se na kartě **Pole** zobrazí nová pole dostupná pro vizualizaci.
    
    ![Aktualizovaná pole](./media/data-lake-store-power-bi/updated-query-fields.png "Aktualizovaná pole")
15. Vytvořme výsečový graf, který bude reprezentovat řidiče v jednotlivých městě pro danou zemi nebo oblast. Chcete-li tak učinit, proveďte následující výběry.
    
    1. Na kartě Vizualizace klikněte na symbol výsečového grafu.
       
        ![Vytvoření výsečového grafu](./media/data-lake-store-power-bi/create-pie-chart.png "Vytvoření výsečového grafu")
    2. Sloupce, které budeme používat, jsou **sloupec 4** (název města) a **sloupec 7** (název země nebo oblasti). Přetáhněte tyto sloupce z karty **Pole** na kartu **Vizualizace,** jak je znázorněno níže.
       
        ![Vytváření vizualizací](./media/data-lake-store-power-bi/create-visualizations.png "Vytváření vizualizací")
    3. Výsečový graf by se nyní měl podobat tomu, který je znázorněn níže.
       
        ![Výsečový graf](./media/data-lake-store-power-bi/pie-chart.png "Vytváření vizualizací")
16. Výběrem konkrétní země nebo oblasti z filtrů na úrovni stránky nyní můžete zobrazit počet řidičů v každém městě vybrané země nebo oblasti. Například na kartě **Vizualizace** včásti **Filtry úrovně stránky**vyberte **Brazílie**.
    
    ![Vyberte zemi](./media/data-lake-store-power-bi/select-country.png "Vybrat zemi/oblast")
17. Výsečový graf se automaticky aktualizuje tak, aby zobrazoval ovladače ve městech Brazílie.
    
    ![Řidiči v zemi](./media/data-lake-store-power-bi/driver-per-country.png "Řidiči podle země/oblasti")
18. V nabídce **Soubor** klikněte na **Uložit** a uložte vizualizaci jako soubor Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikování sestavy do služby Power BI
Po vytvoření vizualizací v Power BI Desktopu je můžete sdílet s ostatními tak, že je publikujete ve službě Power BI. Pokyny k tomu, jak to udělat, najdete [v tématu Publikování z Power BI Desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Viz také
* [Analýza dat v aplikaci Data Lake Storage Gen1 pomocí analýzy datových jezer](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

