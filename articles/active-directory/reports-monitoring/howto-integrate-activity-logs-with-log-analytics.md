---
title: Streamování protokolů služby Azure Active Directory do protokolů azure monitoru | Dokumenty společnosti Microsoft
description: Zjistěte, jak integrovat protokoly služby Azure Active Directory s protokoly Azure Monitoru
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 213fb6e73ae2fc4314320d0e3e593632d8eb7f85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79266439"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrace protokolů Azure AD s protokoly Azure Monitor

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Protokoly Azure Monitor umožňuje dotazovat data k vyhledání konkrétních událostí, analyzovat trendy a provádět korelace napříč různými zdroji dat. Díky integraci protokolů aktivit Azure AD do protokolů Azure Monitoru teď můžete provádět úlohy, jako jsou:

 * Porovnání protokolů přihlášení azure ad s protokoly zabezpečení publikovanými službou Azure Security Center

 * Řešení potíží s problémovými místy výkonu na přihlašovací stránce vaší aplikace korelacemi dat o výkonu aplikací z Azure Application Insights.  

Následující video z relace Ignite ukazuje výhody používání protokolů Azure Monitor pro protokoly Azure AD v praktických uživatelských scénářích.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

V tomto článku se dozvíte, jak integrovat protokoly Azure Active Directory (Azure AD) s Azure Monitor.

## <a name="supported-reports"></a>Podporované sestavy

Protokoly aktivit auditu a protokoly přihlašovacích aktivit můžete směrovat do protokolů Azure Monitor u dalšího analýzy. 

* **Protokoly auditu:**[Sestava aktivit protokolů auditu](concept-audit-logs.md) poskytuje přístup k historii každé úlohy provedené ve vašem tenantovi.
* **Protokoly přihlašování:** Se [sestavou aktivit přihlašování](concept-sign-ins.md) můžete určit, kdo provedl úlohy hlášené v protokolech auditu.

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Požadavky 

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure AD.
* Uživatele, který je *globálním správcem* nebo *správcem zabezpečení* pro tohoto tenanta Azure AD.
* Pracovní prostor Analýzy protokolů ve vašem předplatném Azure. Přečtěte si, jak [vytvořit pracovní prostor Analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Odesílání protokolů do Služby Azure Monitor

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

2. Vyberte -> Nastavení **diagnostiky služby Azure Active Directory** > **Diagnostic settings**Přidat**diagnostické nastavení**. Můžete také vybrat **nastavení exportu** na stránce **Protokoly auditu** nebo **Přihlášení** a dostat se na stránku konfigurace nastavení diagnostiky.  
    
3. V nabídce **Nastavení diagnostiky** zaškrtněte políčko **Odeslat do protokolu Analytics v pracovním prostoru** a pak vyberte **Konfigurovat**.

4. Vyberte pracovní prostor Log Analytics, do kterého chcete protokoly odeslat, nebo vytvořte nový pracovní prostor v poskytnutém dialogovém okně.  

5. Proveďte jednu nebo obě z následujících akcí:
    * Chcete-li odeslat protokoly auditu do pracovního prostoru Analýzy protokolů, zaškrtněte políčko **AuditLogs.** 
    * Chcete-li odeslat protokoly přihlášení do pracovního prostoru Log Analytics, zaškrtněte políčko **SignInLogs.**

6. Vyberte **Uložit** a nastavení se uloží.

    ![Nastavení diagnostiky](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po přibližně 15 minutách ověřte, že se události streamují do pracovního prostoru Log Analytics.

## <a name="next-steps"></a>Další kroky

* [Analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Instalace a použití zobrazení analýzy protokolů pro Službu Azure Active Directory](howto-install-use-log-analytics-views.md)