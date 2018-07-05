---
title: Import souboru Power BI Desktopu do služby Azure Analysis Services | Dokumentace Microsoftu
description: Popisuje postup při importu souboru Power BI Desktopu (pbix) pomocí webu Azure portal.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3dd90fc862e64812c0ba17bef74818d18788f4b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440976"
---
# <a name="import-a-power-bi-desktop-file"></a>Import souboru Power BI Desktopu

Datový model v souboru Power BI Desktopu (pbix) můžete importovat do služby Azure Analysis Services. Model metadat, data uložená v mezipaměti a připojení zdrojů dat budou importovány. Sestavy a vizualizace se neimportují. Importovat datové modely z Power BI Desktopu se na úrovni kompatibility 1400.

**Omezení**   
- Soubor pbix modelu může připojit k **Azure SQL Database** a **Azure SQL Data Warehouse** pouze zdroje dat. 
- Soubor pbix model nemůže mít živé připojení nebo připojení DirectQuery. 
- Import se nemusí podařit, pokud soubor pbix datového modelu obsahuje metadata nejsou podporovány ve službě Analysis Services.

## <a name="to-import-from-pbix"></a>Chcete-li importovat z pbix

1. Na vašem serveru **přehled** > **webový Návrhář**, klikněte na tlačítko **otevřít**.

    ![Vytvoření modelu na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. V **webový Návrhář** > **modely**, klikněte na tlačítko **+ přidat**.

    ![Vytvoření modelu na webu Azure portal](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. V **nový model**, zadejte název modelu a potom vyberte soubor Power BI Desktopu.

    ![Dialogové okno Nový model na webu Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. V **Import**, vyhledejte a vyberte soubor.

     ![Připojit dialogového okna na webu Azure portal](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Další informace najdete v tématech

[Vytvoření modelu na webu Azure portal](analysis-services-create-model-portal.md)   
[Připojení ke službě Azure Analysis Services](analysis-services-connect.md)  
