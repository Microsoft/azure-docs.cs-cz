---
title: Importovat soubor Power BI Desktop do Azure Analysis Services | Microsoft Docs
description: Popisuje, jak importovat soubor Power BI Desktop (soubor pbix) pomocí portálu Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601067"
---
# <a name="import-a-power-bi-desktop-file"></a>Importovat soubor Power BI Desktop

Datový model v souboru Power BI Desktop (soubor pbix) můžete importovat do služby Azure Analysis Services. Metadata modelu, data uložená v mezipaměti a připojení zdroje dat jsou importovány. Nelze importovat, sestavy a vizualizací. Importovat data, která nejsou na úrovni kompatibility 1400 modely z Power BI Desktop.

**Omezení**   
- Soubor pbix modelu se může připojit k **Azure SQL Database** a **Azure SQL Data Warehouse** pouze zdroje dat. 
- Soubor pbix model nemůže mít za provozu nebo DirectQuery připojení. 
- Import se nemusí podařit, pokud váš model dat soubor pbix obsahuje metadata, která není podporována ve službě Analysis Services.

## <a name="to-import-from-pbix"></a>Chcete-li importovat z soubor pbix

1. Na vašem serveru **přehled** > **Návrhář**, klikněte na tlačítko **otevřete**.

    ![Vytvoření modelu na portálu Azure](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. V **Návrhář** > **modely**, klikněte na tlačítko **+ přidat**.

    ![Vytvoření modelu na portálu Azure](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. V **nový model**, zadejte název modelu a potom vyberte soubor Power BI Desktop.

    ![Dialogové okno Nový model na portálu Azure](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. V **Import**, vyhledejte a vyberte soubor.

     ![Připojit dialogové okno na portálu Azure](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Další informace najdete v tématech

[Vytvoření modelu na portálu Azure](analysis-services-create-model-portal.md)   
[Připojení ke službě Azure Analysis Services](analysis-services-connect.md)  
