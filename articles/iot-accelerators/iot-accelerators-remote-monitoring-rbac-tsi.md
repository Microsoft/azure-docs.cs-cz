---
title: Vzdálené monitorování datové ovládací prvek – Azure | Dokumentace Microsoftu
description: Tento článek obsahuje informace o konfiguraci řízení přístupu pro Průzkumníka služby Time Series Insights telemetrie v akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: be9fcaace62709cea55beb49a5bebf4f9701f7c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2019
ms.locfileid: "65827196"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurace řízení přístupu pro telemetrii Průzkumníka Time Series Insights

Tento článek obsahuje informace o tom, jak nakonfigurovat řízení přístupu pro Průzkumníka Time Series Insights v akcelerátoru řešení vzdáleného monitorování. Povolit uživatelům akcelerátor řešení přístup k Průzkumníka služby Time Series Insights, budete muset udělit přístup k datům každého uživatele.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Použijte následující postup udělení přístupu k datům pro uživatele instančního objektu:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

2. Vyhledejte prostředí Time Series Insights. Typ **časové řady** v **hledání** pole. Vyberte **prostředí Time Series Insights** ve výsledcích hledání. 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.

4. Vyberte **zásady přístupu k datům**a pak vyberte **+ přidat**.
    ![Správa zdroje Time Series Insights – prostředí](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Vyberte **vybrat uživatele**.  Vyhledání uživatelského jména nebo e-mailové adresy k vyhledání uživatele, který chcete přidat. Klikněte na tlačítko **vyberte** potvrďte výběr. 

    ![Správa zdroje Time Series Insights – přidat](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Vyberte **vybrat roli**. Zvolte roli odpovídající přístup pro uživatele:
   - Vyberte **Přispěvatel** Pokud budete chtít povolit uživateli měnit referenční data a sdílené složky, které jsou uloženy dotazů a perspektiv s ostatními uživateli prostředí. 
   - V opačném případě vyberte **čtečky** povolení data dotazu uživatele v prostředí a ukládání osobních (ne sdílených) dotazů v prostředí.

     Vyberte **Ok** k potvrzení volby role.

     ![Správa zdroje Time Series Insights – vybrat uživatele](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Vyberte **Ok** v **vybrat roli uživatele** stránky.

    ![Správa zdroje Time Series Insights – vybrat roli](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. **Zásady přístupu k datům** stránka obsahuje seznam uživatelů a rolí pro každého uživatele.

    ![Správa zdroje Time Series Insights – výsledky](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak řízení přístupu jsou udělena pro Průzkumníka Time Series Insights v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužby](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->