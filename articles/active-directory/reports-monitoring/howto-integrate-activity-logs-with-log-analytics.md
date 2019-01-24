---
title: Jak integrace protokolů Azure Active Directory pomocí služby Log Analytics pomocí Azure monitoru (preview) | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory pomocí služby Log Analytics pomocí Azure monitoru (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: 8b9189ef3fff75023316d5272bd93c136106cf94
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2019
ms.locfileid: "54824673"
---
# <a name="integrate-azure-ad-logs-with-log-analytics-using-azure-monitor-preview"></a>Integrace protokolů Azure AD pomocí služby Log Analytics pomocí Azure monitoru (preview)

Log Analytics vám umožní k dotazování dat. Chcete-li najít konkrétní události, analýze trendů a provádět korelaci v různých zdrojích dat. Díky integraci služby Azure AD protokoly aktivit ve službě Log Analytics, můžete teď provádět úkoly, jako je:

 * Porovnání služby Azure AD přihlášení protokolů proti protokolů zabezpečení, publikování pomocí služby Azure Security Center

 * Řešení potíží s problémových míst výkonu na přihlašovací stránku vaší aplikace pomocí korelace dat o výkonu aplikace ze služby Azure Application Insights.  

Toto video z relace Ignite ukazuje výhody použití Log Analytics pro Azure AD protokoly v praktické uživatelských scénářů.

> [!VIDEO https://www.youtube.com/embed/MP5IaCTwkQg?start=1894]

V tomto článku se dozvíte, jak integrovat protokoly služby Azure Active Directory (Azure AD) pomocí Log Analytics pomocí Azure monitoru.

## <a name="supported-reports"></a>Podporované sestavy

Ke službě Log Analytics můžete směrovat protokoly aktivit auditu a protokolů aktivit přihlašování pro další analýzy. 

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

## <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory** > **nastavení diagnostiky** -> **přidejte nastavení diagnostiky**. Můžete také vybrat **Export nastavení** z **protokoly auditu** nebo **přihlášení** stránky na stránku konfigurace nastavení diagnostiky.  
    
3. V **nastavení diagnostiky** nabídku, vyberte **odesílat do Log Analytics** zaškrtněte políčko a potom vyberte **konfigurovat**.

4. Vyberte pracovní prostor Log Analytics, které chcete odeslat protokoly a nebo vytvořte nový pracovní prostor v poli zobrazeném dialogu.  

5. Proveďte jednu nebo obě z následujících akcí:
    * Pokud chcete odeslat protokoly auditu do pracovního prostoru Log Analytics, vyberte **mají** zaškrtávací políčko. 
    * K odeslání protokolů přihlášení do pracovního prostoru Log Analytics, vyberte **SignInLogs** zaškrtávací políčko.

6. Vyberte **Uložit** a nastavení se uloží.

    ![Nastavení diagnostiky](./media/howto-integrate-activity-logs-with-log-analytics/Configure.png)

7. Po přibližně 15 minut ověřte, že události se streamují do pracovního prostoru Log Analytics.

## <a name="next-steps"></a>Další kroky

* [Analýza Azure AD aktivit protokolů v Log Analytics](howto-analyze-activity-logs-log-analytics.md)
* [Nainstalovat a používat zobrazení Log Analytics pro Azure Active Directory](howto-install-use-log-analytics-views.md)
