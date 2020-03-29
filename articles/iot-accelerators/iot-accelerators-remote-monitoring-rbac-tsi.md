---
title: Řízení přístupu k datům vzdáleného monitorování – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje informace o konfiguraci ovládacích prvků přístupu pro průzkumník telemetrie Time Series Insights v akcelerátoru řešení vzdáleného monitorování
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "65827196"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurace ovládacích prvků přístupu pro průzkumník telemetrie Time Series Insights

Tento článek obsahuje informace o konfiguraci ovládacích prvků přístupu pro průzkumníka Time Series Insights v akcelerátoru řešení vzdáleného monitorování. Chcete-li uživatelům akcelerátoru řešení povolit přístup k průzkumníku Time Series Insights, je třeba udělit přístup ke každému uživateli.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Následujícím postupem udělit přístup k datům pro objekt zabezpečení uživatele:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

2. Vyhledejte prostředí Time Series Insights. Do **vyhledávacího** pole zadejte **časové řady.** Ve výsledcích hledání vyberte **prostředí časové řady.** 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.

4. Vyberte **Zásady přístupu k datům**, pak vyberte **+ Přidat**.
    ![Správa zdroje Time Series Insights – prostředí](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Vyberte **vybrat uživatele**.  Vyhledejte uživatele, kterého chcete přidat, vyhledejte uživatelské jméno nebo e-mailovou adresu. Klepnutím na **tlačítko Vybrat** výběr potvrďte výběr. 

    ![Správa zdroje Time Series Insights – přidat](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Vyberte **vybrat roli**. Zvolte příslušnou přístupovou roli pro uživatele:
   - Pokud chcete **uživateli** povolit změnu referenčních dat a sdílet uložené dotazy a perspektivy s ostatními uživateli prostředí, vyberte přispěvatele. 
   - V opačném případě vyberte **Reader** povolit data dotazu uživatele v prostředí a uložit osobní (nesdílené) dotazy v prostředí.

     Chcete-li potvrdit volbu role, vyberte **ok.**

     ![Správa zdroje Time Series Insights – vybrat uživatele](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Na stránce **Vybrat roli uživatele** vyberte **Ok.**

    ![Správa zdroje Time Series Insights – vybrat roli](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Na stránce **Zásady přístupu k datům** jsou uvedeny uživatelé a role pro každého uživatele.

    ![Správa zdroje Time Series Insights – výsledky](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste se dozvěděli, jak jsou uděleny ovládací prvky přístupu pro průzkumník a řízení Přehledy časové řady v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování naleznete v [tématu Architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md)

Další informace o přizpůsobení řešení vzdáleného monitorování naleznete v [tématu Přizpůsobení a opětovné nasazení mikroslužeb.](iot-accelerators-microservices-example.md)
<!-- Next tutorials in the sequence -->