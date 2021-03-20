---
title: Streamování protokolů Azure Active Directory do Azure Monitor protokolů | Microsoft Docs
description: Informace o tom, jak integrovat protokoly Azure Active Directory pomocí protokolů Azure Monitor
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f328ed44252f7fb314552d6d05df9806f59d972
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100591133"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrace protokolů služby Azure AD s protokoly Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Protokoly Azure Monitor vám umožňují zadávat dotazy na data a vyhledávat konkrétní události, analyzovat trendy a provádět korelace napříč různými zdroji dat. Díky integraci protokolů aktivit Azure AD v Azure Monitorch protokolech teď můžete provádět úkoly, jako je:

 * Porovnejte protokoly přihlášení služby Azure AD s protokoly zabezpečení publikovanými nástrojem Azure Security Center

 * Vyřešte potíže s výkonem na přihlašovací stránce vaší aplikace korelacemi dat o výkonu aplikací z Azure Application Insights.  

Následující video z Ignite relace ukazuje výhody použití protokolů Azure Monitor pro protokoly Azure AD v praktických scénářích uživatelů.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

V tomto článku se dozvíte, jak integrovat protokoly Azure Active Directory (Azure AD) s Azure Monitor.

## <a name="supported-reports"></a>Podporované sestavy

Protokoly aktivit auditu a protokoly aktivit přihlašování můžete směrovat do protokolů Azure Monitor k další analýze. 

* **Protokoly auditu:**[Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.
* **Protokoly přihlašování:** Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené v protokolech auditu.
* **Protokoly zřizování**: pomocí [protokolů zřizování](../app-provisioning/application-provisioning-log-analytics.md)můžete sledovat, které uživatele se vytvořily, aktualizovaly a odstranily ve všech aplikacích třetích stran. 

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Předpoklady 

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure AD.
* Uživatele, který je *globálním správcem* nebo *správcem zabezpečení* pro tohoto tenanta Azure AD.
* Log Analytics pracovní prostor ve vašem předplatném Azure. Naučte se, jak [vytvořit pracovní prostor Log Analytics](../../azure-monitor/logs/quick-create-workspace.md).

## <a name="licensing-requirements"></a>Licenční požadavky

Použití této funkce vyžaduje licenci Azure AD Premium P1 nebo P2. Pokud chcete najít správnou licenci pro vaše požadavky, přečtěte si [porovnání obecně dostupných funkcí edic Free, Basic a Premium](https://azure.microsoft.com/pricing/details/active-directory/).

## <a name="send-logs-to-azure-monitor"></a>Odeslat protokoly do Azure Monitor

1. Přihlaste se na [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory**  >  **nastavení diagnostiky**  ->  **Přidat nastavení diagnostiky**. Můžete také vybrat **Exportovat nastavení** na stránce **protokoly auditu** nebo **přihlášení** a získat tak stránku konfigurace nastavení diagnostiky.  
    
3. V nabídce **nastavení diagnostiky** zaškrtněte políčko **Odeslat do Log Analytics pracovní prostor** a pak vyberte **Konfigurovat**.

4. Vyberte pracovní prostor Log Analytics, do kterého chcete protokoly odeslat, nebo v zobrazeném dialogovém okně vytvořte nový pracovní prostor.  

5. Proveďte jednu nebo obě z následujících akcí:
    * Chcete-li do pracovního prostoru Log Analytics Odeslat protokoly auditu, zaškrtněte políčko **AuditLogs** . 
    * Pokud chcete odesílat protokoly přihlášení do pracovního prostoru Log Analytics, zaškrtněte políčko **SignInLogs** .

6. Vyberte **Uložit** a nastavení se uloží.

    ![Nastavení diagnostiky](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po asi 15 minutách ověřte, že jsou události streamované do vašeho pracovního prostoru Log Analytics.

## <a name="next-steps"></a>Další kroky

* [Analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalace a použití zobrazení Log Analytics pro Azure Active Directory](howto-install-use-log-analytics-views.md)