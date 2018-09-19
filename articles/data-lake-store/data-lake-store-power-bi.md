---
title: Pomocí Power BI analyzovat data v Azure Data Lake Storage Gen1 | Dokumentace Microsoftu
description: Pomocí Power BI analyzovat data uložená v Azure Data Lake Storage Gen1
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
ms.openlocfilehash: 5db9d18a31af4d6b407fcd9172ac80fc6f93f085
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297175"
---
# <a name="analyze-data-in-azure-data-lake-storage-gen1-by-using-power-bi"></a>Pomocí Power BI analyzovat data v Azure Data Lake Storage Gen1
V tomto článku se dozvíte, jak analyzovat a vizualizovat data uložená v Azure Data Lake Storage Gen1 pomocí Power BI Desktopu.

## <a name="prerequisites"></a>Požadavky
Je nutné, abyste před zahájením tohoto kurzu měli tyto položky:

* **Předplatné Azure**. Viz [Získání bezplatné zkušební verze Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Účet Data Lake Storage Gen1**. Postupujte podle pokynů na adrese [Začínáme s Azure Data Lake Storage Gen1 pomocí webu Azure portal](data-lake-store-get-started-portal.md). Tento článek předpokládá, že jste již vytvořili účet Data Lake Storage Gen1 volá **myadlsg1**a nahrát ukázkový datový soubor (**Drivers.txt**) k němu. Tento ukázkový soubor je k dispozici ke stažení z [úložiště Git Azure Data Lake](https://github.com/Azure/usql/tree/master/Examples/Samples/Data/AmbulanceData/Drivers.txt).
* **Power BI Desktop**. Můžete stáhnout z [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=45331). 

## <a name="create-a-report-in-power-bi-desktop"></a>Vytvoření sestavy v Power BI Desktopu
1. Spusťte Power BI Desktopu ve vašem počítači.
2. Z **Domů** pásu karet, klikněte na tlačítko **získat Data**a potom klikněte na tlačítko Další. V **získat Data** dialogové okno, klikněte na tlačítko **Azure**, klikněte na tlačítko **Azure Data Lake Store**a potom klikněte na tlačítko **připojit**.
   
    ![Připojte se k Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account.png "připojení k Data Lake Storage Gen1")
3. Pokud se zobrazí dialogové okno o konektoru ve fázi vývoje, rozhodnout pokračovat.
4. V **Azure Data Lake Store** dialogové okno, zadejte adresu URL k vašemu účtu Data Lake Storage Gen1 a potom klikněte na tlačítko **OK**.
   
    ![Adresa URL pro Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-url.png "adresu URL pro Data Lake Storage Gen1")
5. V dalším dialogovém okně klikněte na tlačítko **přihlášení** k přihlašování do účtu Data Lake Storage Gen1. Budete přesměrováni na přihlašovací stránce vaší organizace. Postupujte podle pokynů k přihlásit k účtu.
   
    ![Přihlaste se k Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-signin.png "přihlásit ke službě Data Lake Storage Gen1")
6. Po úspěšném přihlášení, klikněte na tlačítko **připojit**.
   
    ![Připojte se k Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-connect.png "připojení k Data Lake Storage Gen1")
7. Další dialogové okno zobrazuje soubor, který jste nahráli do svého účtu Data Lake Storage Gen1. Zkontrolujte informace a klikněte na **zatížení**.
   
    ![Načtení dat z Data Lake Storage Gen1](./media/data-lake-store-power-bi/get-data-lake-store-account-load.png "načítat data z Data Lake Storage Gen1")
8. Po data byla úspěšně načtena do Power BI, zobrazí se následující pole v **pole** kartu.
   
    ![Importovat pole](./media/data-lake-store-power-bi/imported-fields.png "importovat pole")
   
    Nicméně umožňuje vizualizovat a analyzovat data, můžeme dáváte přednost data, která mají být k dispozici pro následující pole
   
    ![Požadované pole](./media/data-lake-store-power-bi/desired-fields.png "požadované pole")
   
    V dalších krocích budeme aktualizovat dotaz převést importovaná data v požadovaném formátu.
9. Z **Domů** pásu karet, klikněte na tlačítko **upravit dotazy**.
   
    ![Upravit dotazy](./media/data-lake-store-power-bi/edit-queries.png "upravit dotazy")
10. V editoru dotazů v rámci **obsahu** sloupce, klikněte na tlačítko **binární**.
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/convert-query1.png "upravit dotazy")
11. Se zobrazuje ikona souboru, který představuje **Drivers.txt** soubor, který jste nahráli. Klikněte pravým tlačítkem na soubor a klikněte na tlačítko **sdíleného svazku clusteru**.    
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/convert-query2.png "upravit dotazy")
12. Výstup byste měli vidět, jak je znázorněno níže. Vaše data jsou teď k dispozici ve formátu, který vám umožní vytvářet vizualizace.
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/convert-query3.png "upravit dotazy")
13. Z **Domů** pásu karet, klikněte na tlačítko **zavřít a použít**a potom klikněte na tlačítko **zavřít a použít**.
    
    ![Upravit dotazy](./media/data-lake-store-power-bi/load-edited-query.png "upravit dotazy")
14. Po aktualizaci dotazu **pole** kartě se zobrazí nová pole, které jsou k dispozici pro vizualizaci.
    
    ![Aktualizovat pole](./media/data-lake-store-power-bi/updated-query-fields.png "aktualizovat pole")
15. Dejte nám prosím vytvořte výsečový graf představující ovladače v každé město pro danou zemi. Uděláte to tak, proveďte následující výběr.
    
    1. Na kartě vizualizace klikněte na symbol ve výsečovém grafu.
       
        ![Vytvořte výsečový graf](./media/data-lake-store-power-bi/create-pie-chart.png "vytvořte výsečový graf")
    2. Sloupce, které budeme používat **4 sloupci** (název města) a **7 sloupci** (název země). Přetáhněte tyto sloupce z **pole** záložku **vizualizace** kartu, jak je znázorněno níže.
       
        ![Vytvoření vizualizace](./media/data-lake-store-power-bi/create-visualizations.png "vytvářet vizualizace")
    3. Výsečový graf by měl nyní vypadat podobně jako následující.
       
        ![Výsečový graf](./media/data-lake-store-power-bi/pie-chart.png "vytvářet vizualizace")
16. Tak, že vyberete určitou zemi filtry na úrovni stránek, můžete nyní zobrazit počet ovladačů v každé město vybrané zemi. Například pod položkou **vizualizace** ve skupině **filtry na úrovni stránek**vyberte **Brazílie**.
    
    ![Vyberte zemi](./media/data-lake-store-power-bi/select-country.png "vyberte zemi.")
17. Výsečový graf se automaticky aktualizuje a zobrazí ve městech Brazílie ovladače.
    
    ![Ovladače v určité zemi](./media/data-lake-store-power-bi/driver-per-country.png "ovladače podle země")
18. Z **souboru** nabídky, klikněte na tlačítko **Uložit** vizualizaci uložit jako soubor Power BI Desktopu.

## <a name="publish-report-to-power-bi-service"></a>Publikování sestavy do služby Power BI
Po vytvoření vizualizací v Power BI Desktopu, budete ho sdílet s ostatními ji publikujete do služby Power BI. Pokyny, jak to provést, najdete v části [publikování z Power BI Desktopu](https://powerbi.microsoft.com/documentation/powerbi-desktop-upload-desktop-files/).

## <a name="see-also"></a>Další informace najdete v tématech
* [Analýza dat v Data Lake Storage Gen1 pomocí služby Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)

