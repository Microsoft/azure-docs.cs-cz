---
title: Import souboru Power BI Desktopu do služby Azure Analysis Services | Dokumentace Microsoftu
description: Popisuje postup při importu souboru Power BI Desktopu (pbix) pomocí webu Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 10/11/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3adf0c9c2e2b264904e66b82716447d634aaeee7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50209645"
---
# <a name="import-a-power-bi-desktop-file"></a>Import souboru Power BI Desktopu

Datový model v souboru Power BI Desktopu (pbix) můžete importovat do služby Azure Analysis Services. Model metadat, data uložená v mezipaměti a připojení zdrojů dat budou importovány. Sestavy a vizualizace se neimportují. Importovat datové modely z Power BI Desktopu se na úrovni kompatibility 1400.

> [!IMPORTANT]
> Tato funkce je zastaralá. Může být odebrán nebo významně změnil v budoucí aktualizaci. Doporučuje se, že je můžete přestat používat tuto funkci v nové a existující projekty pro zachování kompatibility s budoucími aktualizacemi. Pro pokročilejší model vývoje a testování je nejvhodnější použít Visual Studio (SSDT) a SQL Server Management Studio (SSMS).

**Omezení**   


- Pokud je datový model vytvořený v Power BI Desktopu (2.60.5169.3201) aktualizace z července 2018 nebo později, ujistěte se, že jsou povolené žádné funkce ve verzi preview. Funkce ve verzi Preview nejsou ještě podporované ve službě Azure Analysis Services. Pokud se zobrazí následující chyba, při importu, soubor pbix je povolena funkce ve verzi preview, které se zatím nepodporují ve službě Azure Analysis Services.

    ![Upozornění úrovně kompatibility](./media/analysis-services-import-pbix/aas-import-pbix-cl-warning.png)   
- Musí mít oprávnění správce serveru pro import ze souboru pbix.
- Soubor pbix modelu může připojit k **Azure SQL Database** a **Azure SQL Data Warehouse** pouze zdroje dat.
- Soubor pbix model nemůže mít živé připojení nebo připojení DirectQuery. 


## <a name="to-import-from-pbix"></a>Chcete-li importovat z pbix

1. Na vašem serveru **přehled** > **webový Návrhář**, klikněte na tlačítko **otevřít**.

    ![Vytvoření modelu na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. V **webový Návrhář** > **modely**, klikněte na tlačítko **+ přidat**.

    ![Vytvoření modelu na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. V **nový model**, zadejte název modelu a potom vyberte soubor Power BI Desktopu.

    ![Dialogové okno Nový model na webu Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. V **Import**, vyhledejte a vyberte soubor.

     ![Připojit dialogového okna na webu Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="change-credentials"></a>Změna přihlašovacích údajů

Při importu modelu dat ze souboru pbix, ve výchozím nastavení se pověření používaná k připojení ke zdroji dat jsou nastaveny na ServiceAccount. Po modelu byla naimportována ze soubor pbix, můžete změnit přihlašovací údaje pomocí následujících metod:

- Použití července 2018 (verze 17.8.1) nebo novější verze aplikace SSMS na Upravit přihlašovací údaje. Toto je nejjednodušší.
- Použít TMSL [příkazu Alter](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/alter-command-tmsl) na [objektu zdroje dat](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/datasources-object-tmsl) změnit vlastnost připojovací řetězec. 
- Otevření modelu v sadě Visual Studio, upravit přihlašovací údaje pro připojení zdroje dat a poté model nasaďte znovu.

Chcete-li změnit pověření s použitím aplikace SSMS. 

1. V aplikaci SSMS, rozbalte databázi > **připojení**. 
2. Klikněte pravým tlačítkem na připojení k databázi a potom klikněte na tlačítko **aktualizovat přihlašovací údaje**. 

    ![Aktualizovat přihlašovací údaje](./media/analysis-services-import-pbix/aas-import-pbix-creds.png)

3. V dialogovém okně přihlašovací údaje vyberte typ přihlašovacích údajů a zadejte přihlašovací údaje. Ověřování SQL vyberte databázi. Účet organizace (OAuth) vyberte účet Microsoft.
    ![Upravit přihlašovací údaje](./media/analysis-services-import-pbix/aas-import-pbix-edit-creds.png)

. Července 2018 verzi Power BI Desktopu obsahuje novou funkci pro změnu oprávnění datasource. Na **Domů** klikněte na tlačítko **upravit dotazy**  > **nastavení zdroje dat**. Vyberte připojení zdroje dat a pak klikněte na tlačítko **upravit oprávnění**.


## <a name="see-also"></a>Další informace najdete v tématech

[Vytvoření modelu na webu Azure portal](analysis-services-create-model-portal.md)   
[Připojení ke službě Azure Analysis Services](analysis-services-connect.md)  
