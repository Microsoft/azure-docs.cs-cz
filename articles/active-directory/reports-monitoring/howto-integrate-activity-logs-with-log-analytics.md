---
title: Stream protokolů služby Azure Active Directory do protokolů Azure Monitor | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s protokoly Azure monitoru
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
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60437303"
---
# <a name="integrate-azure-ad-logs-with-azure-monitor-logs"></a>Integrace protokolů Azure AD s protokoly Azure monitoru

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Protokoly služby Azure Monitor vám umožní k dotazování dat. Chcete-li najít konkrétní události, analýze trendů a provádět korelaci v různých zdrojích dat. Díky integraci služby Azure AD protokoly aktivit Azure Monitor protokoly, teď můžete provádět úkoly, jako je:

 * Porovnání služby Azure AD přihlášení protokolů proti protokolů zabezpečení, publikování pomocí služby Azure Security Center

 * Řešení potíží s problémových míst výkonu na přihlašovací stránku vaší aplikace pomocí korelace dat o výkonu aplikace ze služby Azure Application Insights.  

Toto video z relace Ignite ukazuje výhody použití protokoly Azure monitoru pro protokoly služby Azure AD v praktické uživatelských scénářů.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

V tomto článku se dozvíte, jak integrovat protokoly služby Azure Active Directory (Azure AD) pomocí Azure monitoru.

## <a name="supported-reports"></a>Podporované sestavy

Můžete směrovat protokoly aktivit auditu a protokolů aktivit přihlašování na protokoly Azure monitoru pro další analýzy. 

* **Protokoly auditu**: [Sestava aktivit protokolů auditu](concept-audit-logs.md) dává vám přístup k historii každé úlohy, které se provádí ve vašem tenantovi.
* **Protokoly přihlášení**: S [sestavy aktivit přihlašování](concept-sign-ins.md), můžete určit, kdo provedl úlohy, které jsou hlášeny v protokolech auditování.

> [!NOTE]
> Zatím není dostupná podpora protokolů aktivit auditu a přihlašování souvisejících s B2C.
>

## <a name="prerequisites"></a>Požadavky 

Pokud chcete používat tuto funkci, potřebujete tyto položky:

* Předplatné Azure. Pokud předplatné Azure nemáte, můžete si [zaregistrovat bezplatnou zkušební verzi](https://azure.microsoft.com/free/).
* Tenanta Azure AD.
* Uživatele, který je *globálním správcem* nebo *správcem zabezpečení* pro tohoto tenanta Azure AD.
* Pracovní prostor Log Analytics ve vašem předplatném Azure. Zjistěte, jak [vytvořit pracovní prostor Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).

## <a name="send-logs-to-azure-monitor"></a>Odeslání protokolů do Azure monitoru

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory** > **nastavení diagnostiky** -> **přidejte nastavení diagnostiky**. Můžete také vybrat **Export nastavení** z **protokoly auditu** nebo **přihlášení** stránky na stránku konfigurace nastavení diagnostiky.  
    
3. V **nastavení diagnostiky** nabídku, vyberte **odesílání do pracovního prostoru Log Analytics** zaškrtněte políčko a potom vyberte **konfigurovat**.

4. Vyberte pracovní prostor Log Analytics, které chcete odeslat protokoly a nebo vytvořte nový pracovní prostor v poli zobrazeném dialogu.  

5. Proveďte jednu nebo obě z následujících akcí:
    * Pokud chcete odeslat protokoly auditu do pracovního prostoru Log Analytics, vyberte **mají** zaškrtávací políčko. 
    * K odeslání protokolů přihlášení do pracovního prostoru Log Analytics, vyberte **SignInLogs** zaškrtávací políčko.

6. Vyberte **Uložit** a nastavení se uloží.

    ![Nastavení diagnostiky](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po přibližně 15 minut ověřte, že události se streamují do pracovního prostoru Log Analytics.

## <a name="next-steps"></a>Další postup

* [Analýza služby Azure AD protokolů aktivit se protokoly Azure monitoru](howto-analyze-activity-logs-log-analytics.md)
* [Nainstalovat a používat zobrazení log analytics pro Azure Active Directory](howto-install-use-log-analytics-views.md)