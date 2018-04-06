---
title: Importovat soubor Power BI Desktop do Azure Analysis Services | Microsoft Docs
description: Popisuje, jak importovat soubor Power BI Desktop (soubor pbix) pomocí portálu Azure.
services: analysis-services
documentationcenter: ''
author: minewiskan
manager: kfile
editor: ''
tags: ''
ms.assetid: ''
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 04/03/2018
ms.author: owend
ms.openlocfilehash: 2ba9bc0e4b9a55312875fe120ee179800aeefb23
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="import-a-power-bi-desktop-file"></a>Importovat soubor Power BI Desktop

Můžete vytvořit nový model v Azure AS importováním souboru Power BI Desktop soubor (soubor pbix). Metadata modelu, data uložená v mezipaměti a připojení zdroje dat jsou importovány. Nelze importovat, sestavy a vizualizací.

**Omezení**   
- Soubor pbix modelu může připojit k Azure SQL Database a Azure SQL Data Warehouse zdroje dat pouze. 
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
