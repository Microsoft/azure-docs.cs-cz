---
title: zahrnout soubor
description: zahrnout soubor
services: time-series-insights
author: ashannon7
ms.service: time-series-insights
ms.topic: include
ms.date: 08/20/2018
ms.author: anshan
ms.custom: include file
ms.openlocfilehash: c9daa86bf36b260001d9969385b9e8a98a8ac0cf
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443190"
---
## <a name="grant-data-access"></a>Udělení přístupu k datům

Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte prostředí Time Series Insights. Typ **časové řady** v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání. 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.

4. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.
    ![Správa zdroje Time Series Insights – prostředí](media/iot-tsi-data-access/getstarted-grant-data-access1.png)

5. Vyberte **vybrat uživatele**.  Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr. 

    ![Správa zdroje Time Series Insights – přidat](media/iot-tsi-data-access/getstarted-grant-data-access2.png)

6. Vyberte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:
   - Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí. 
   - V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.

     Vyberte **Ok** k potvrzení volby role.

     ![Správa zdroje Time Series Insights – vybrat uživatele](media/iot-tsi-data-access/getstarted-grant-data-access3.png)

7. Vyberte **Ok** v **vybrat roli uživatele** stránky.

    ![Správa zdroje Time Series Insights – vybrat roli](media/iot-tsi-data-access/getstarted-grant-data-access4.png)

8. **Zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

    ![Správa zdroje Time Series Insights – výsledky](media/iot-tsi-data-access/getstarted-grant-data-access5.png)