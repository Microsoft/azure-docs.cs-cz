---
title: Analýza dat v Data Lake Store pomocí Power BI | Microsoft Docs
description: Pomocí Power BI k analýze dat uložených v Azure Data Lake Store
services: data-lake-store
documentationcenter: ''
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 57d19d27-e135-49d9-a7ea-46c48ef4e3bd
ms.service: data-lake-store
ms.devlang: na
ms.topic: conceptual
ms.date: 05/29/2018
ms.author: nitinme
ms.openlocfilehash: 37cde17703a15ba5659814e1b65a54d24b67edad
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34624783"
---
# <a name="analyze-data-in-data-lake-store-by-using-power-bi"></a>Analýza dat v Data Lake Store pomocí Power BI
V tomto článku se dozvíte, jak analyzovat a vizualizovat data uložená v Azure Data Lake Store pomocí Power BI Desktop.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Azure Data Lake Store**. Postupujte podle pokynů v tématu [Začínáme s Azure Data Lake Store s použitím webu Azure Portal](data-lake-store-get-started-portal.md). Tento článek předpokládá, že jste již vytvořili účet Data Lake Store, názvem **mybidatalakestore**a odesláno ukázkový datový soubor (**Drivers.txt**) k němu. Tento ukázkový soubor je k dispozici ke stažení [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Si můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu
1. Power BI Desktop spustíte ve vašem počítači.
2. Z **Domů** pásu karet, klikněte na tlačítko **načíst Data**a pak klikněte na tlačítko Další. V **načíst Data** dialogové okno, klikněte na tlačítko **Azure**, klikněte na tlačítko **Azure Data Lake Store**a potom klikněte na **Connect**.
   
    ![Připojení k Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account.png "připojení k Data Lake Store")
3. Pokud se zobrazí dialogové okno o konektoru se ve fázi vývoje, rozhodnout pokračovat.
4. V **Microsoft Azure Data Lake Store** dialogové okno, zadejte adresu URL k vašemu účtu Data Lake Store a pak klikněte na tlačítko **OK**.
   
    ![Adresa URL pro Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "adresu URL pro Data Lake Store")
5. V dialogovém okně Další klikněte na **přihlášení** pro přihlášení do účtu Data Lake Store. Budete přesměrováni na přihlašovací stránku vaší organizace. Postupujte podle výzev a přihlaste se k účtu.
   
    ![Přihlaste se k Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "přihlásit ke službě Data Lake Store")
6. Po úspěšném přihlášení, klikněte na tlačítko **Connect**.
   
    ![Připojení k Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "připojení k Data Lake Store")
7. Dialogové okno Další ukazuje soubor, který jste nahráli do účtu Data Lake Store. Ověřte informace a pak klikněte na tlačítko **zatížení**.
   
    ![Načtení dat z Data Lake Store](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "načtení dat z Data Lake Store")
8. Po data byla úspěšně načtena do Power BI, zobrazí se následující pole v **pole** kartě.
   
    ![Importovat pole](./media/data-lake-store-power-bi/imported-fields.png "importovat pole")
   
    Však k vizualizaci a analýzu dat, můžeme dáváte přednost data, která mají být k dispozici pro následující pole
   
    ![Požadovaného pole](./media/data-lake-store-power-bi/desired-fields.png "požadovaných polí")
   
    V dalších krocích budeme aktualizovat dotaz převést importovaných dat v požadovaném formátu.
9. Z **Domů** pásu karet, klikněte na tlačítko **upravit dotazy**.
   
    ![Upravit dotazy](./media/data-lake-store-power-bi/edit-queries.png "úpravy dotazů")
10. V editoru dotazů v rámci **obsahu** sloupce, klikněte na tlačítko **binární**.
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/convert-query1.png "úpravy dotazů")
11. Zobrazí se ikona souboru, který představuje **Drivers.txt** soubor, který jste nahráli. Klikněte pravým tlačítkem na soubor a klikněte na tlačítko **CSV**.    
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/convert-query2.png "úpravy dotazů")
12. Měli byste vidět výstup, jak je uvedeno níže. Vaše data jsou nyní k dispozici ve formátu, který slouží k vytváření vizualizací.
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/convert-query3.png "úpravy dotazů")
13. Z **Domů** pásu karet, klikněte na tlačítko **zavřete a použít**a potom klikněte na **zavřete a použít**.
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/load-edited-query.png "úpravy dotazů")
14. Po aktualizaci dotazu **pole** se zobrazí nová pole, které jsou k dispozici pro vizualizaci.
    
    ![Aktualizovat pole](./media/data-lake-store-power-bi/updated-query-fields.png "aktualizovat pole")
15. Dejte nám vytvořte výsečový graf představující ovladače v každé město pro dané země. To pokud chcete udělat, vyberte následující možnosti.
    
    1. Na kartě vizualizace kliknutím na symbol pro výsečového grafu.
       
        ![Vytvoření výsečového grafu](./media/data-lake-store-power-bi/create-pie-chart.png "vytvořit výsečového grafu")
    2. Sloupce, které jsme se chystáte použít **sloupec 4** (název města) a **7 sloupci** (název země). Přetáhněte tyto sloupce z **pole** kartu k **vizualizace** kartě, jak je uvedeno níže.
       
        ![Vytváření vizualizací](./media/data-lake-store-power-bi/create-visualizations.png "vytváření vizualizací")
    3. Výsečový graf by měl nyní vypadat podobně jako následující.
       
        ![Výsečový graf](./media/data-lake-store-power-bi/pie-chart.png "vytváření vizualizací")
16. Výběrem konkrétní země ze stránky úrovně filtry, uvidíte nyní počet ovladačů v každé město vybrané země. Například v položce **vizualizace** v části **stránky úrovně filtry**, vyberte **Brazílie**.
    
    ![Vyberte zemi](./media/data-lake-store-power-bi/select-country.png "vyberte zemi")
17. Výsečový graf se automaticky aktualizuje a zobrazí ovladače ve městech Brazílie.
    
    ![Ovladače v určité zemi](./media/data-lake-store-power-bi/driver-per-country.png "ovladače podle země")
18. Z **soubor** nabídky, klikněte na tlačítko **Uložit** uložit vizualizaci jako soubor Power BI Desktop.

## <a name="publish-report-to-power-bi-service"></a>Publikovat sestavy do služby Power BI
Po vytvoření vizualizacemi v Power BI Desktop, můžete ji sdílet s ostatními ji publikujete do služby Power BI. Pokyny o tom, jak to udělat najdete v tématu [publikování z Power BI Desktop](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Další informace najdete v tématech
* [Analýza dat v Data Lake Store pomocí Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

