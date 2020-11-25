---
title: Řízení přístupu k datům vzdáleného monitorování – Azure | Microsoft Docs
description: Tento článek poskytuje informace o tom, jak můžete nakonfigurovat řízení přístupu pro Time Series Insights Průzkumník telemetrie v akcelerátoru řešení vzdáleného monitorování.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 08/06/2018
ms.topic: conceptual
ms.openlocfilehash: 9d5d572c3e32e3645e65ba8d6fc28b567b3c1e9a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "96005958"
---
# <a name="configure-access-controls-for-the-time-series-insights-telemetry-explorer"></a>Konfigurace ovládacích prvků přístupu pro Time Series Insights Průzkumník telemetrie

Tento článek poskytuje informace o tom, jak nakonfigurovat řízení přístupu pro aplikaci Time Series Insights Explorer v akcelerátoru řešení vzdáleného monitorování. Chcete-li uživatelům povolit akcelerátor řešení pro přístup k aplikaci Time Series Insights Explorer, je třeba udělit každému uživateli přístup k datům.

Zásady přístupu k datům udělují oprávnění k vydávání dotazů na data, zpracování referenčních dat v rámci prostředí a sdílení uložených dotazů a perspektiv přidruženým k danému prostředí.

## <a name="grant-data-access"></a>Udělení přístupu k datům

Pomocí těchto kroků udělíte přístup k datům pro objekt zabezpečení uživatele:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

2. Najděte své Time Series Insights prostředí. Do **vyhledávacího** pole zadejte **časovou řadu** . Ve výsledcích hledání vyberte **prostředí Time Series** . 

3. Ze seznamu vyberte vaše prostředí Time Series Insights.

4. Vyberte **zásady přístupu k datům** a pak vybrat **+ Přidat**.
    ![Správa zdroje Time Series Insights – prostředí](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access1.png)

5. Vyberte **Vybrat uživatele**.  Vyhledejte uživatelské jméno nebo e-mailovou adresu a vyhledejte uživatele, kterého chcete přidat. Kliknutím na tlačítko **Vybrat** potvrďte výběr. 

    ![Správa zdroje Time Series Insights – přidat](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access2.png)

6. Vyberte **Vybrat roli**. Vyberte příslušnou roli přístupu pro uživatele:
   - Vyberte **Přispěvatel** , pokud chcete, aby uživatel mohl měnit referenční data a sdílet uložené dotazy a perspektivy s ostatními uživateli tohoto prostředí. 
   - V opačném případě vyberte **Čtenář** , který uživateli umožní dotazovat se na data v prostředí a ukládat osobní (nesdílené) dotazy v prostředí.

     Výběrem **OK** potvrďte volbu role.

     ![Správa zdroje Time Series Insights – vybrat uživatele](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access3.png)

7. Na stránce **Vybrat roli uživatele** vyberte **OK** .

    ![Správa zdroje Time Series Insights – vybrat roli](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access4.png)

8. Na stránce **zásady přístupu k datům** jsou uvedeni uživatelé a role pro každého uživatele.

    ![Správa zdroje Time Series Insights – výsledky](media/iot-accelerators-remote-monitoring-rbac-tsi/getstarted-grant-data-access5.png)

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak se udělují ovládací prvky přístupu pro Time Series Insights Exploreru v akcelerátoru řešení vzdáleného monitorování.

Další koncepční informace o akcelerátoru řešení vzdáleného monitorování najdete v tématu [architektura vzdáleného monitorování](iot-accelerators-remote-monitoring-sample-walkthrough.md) .

Další informace o přizpůsobení řešení vzdáleného monitorování najdete v tématu [přizpůsobení a opětovné nasazení mikroslužeb](iot-accelerators-microservices-example.md) .
<!-- Next tutorials in the sequence -->