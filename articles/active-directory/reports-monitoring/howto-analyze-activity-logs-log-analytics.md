---
title: Analýza protokolů aktivit Azure Active Directory pomocí protokolů Azure Monitor | Microsoft Docs
description: Naučte se analyzovat Azure Active Directory protokoly aktivit pomocí protokolů Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77eb03089d956d0fb32ef0463b3d1cdb49ff0dbb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989812"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor

Po [integraci protokolů aktivit služby Azure AD s protokoly Azure monitor](howto-integrate-activity-logs-with-log-analytics.md)můžete využít sílu Azure monitor protokolů a získat tak přehled o vašem prostředí. Můžete taky nainstalovat [zobrazení Log Analytics pro protokoly aktivit Azure AD](howto-install-use-log-analytics-views.md) a získat tak přístup k předem vytvořeným sestavám s událostmi auditu a přihlašování ve vašem prostředí.

V tomto článku se dozvíte, jak analyzovat protokoly aktivit Azure AD v pracovním prostoru Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky 

Pokud chcete postup sledovat, potřebujete:

* Log Analytics pracovní prostor ve vašem předplatném Azure. Naučte se, jak [vytvořit pracovní prostor Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Nejdřív proveďte kroky pro [Směrování protokolů aktivit služby Azure AD do vašeho pracovního prostoru Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Přejděte do pracovního prostoru Log Analytics.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory**a potom v části **monitorování** vyberte **protokoly** . tím otevřete pracovní prostor Log Analytics. Pracovní prostor se otevře s výchozím dotazem.

    ![Výchozí dotaz](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Zobrazit schéma pro protokoly aktivit Azure AD

Protokoly jsou vloženy do tabulek **AuditLogs** a **SigninLogs** v pracovním prostoru. Postup zobrazení schématu pro tyto tabulky:

1. V zobrazení výchozí dotaz v předchozí části vyberte **schéma** a rozbalte pracovní prostor. 

2. Rozbalte část **Správa protokolů** a potom rozbalte buď **AuditLogs** nebo **SignInLogs** , a zobrazte schéma protokolu.
    ![](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) Protokoly![auditu přihlášení protokoly](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Dotazování protokolů aktivit služby Azure AD

Teď, když máte protokoly v pracovním prostoru, můžete pro ně nyní spouštět dotazy. Pokud například chcete získat nejdůležitější aplikace používané v posledním týdnu, nahraďte výchozí dotaz následujícím a vyberte **Spustit** .

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

K získání nejdůležitějších událostí auditu za poslední týden použijte následující dotaz:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Upozornění na data protokolu aktivit Azure AD

Pro svůj dotaz můžete také nastavit výstrahy. Pokud třeba chcete nakonfigurovat výstrahu, když se v minulém týdnu použije více než 10 aplikací, postupujte takto:

1. V pracovním prostoru vyberte **nastavit výstrahu** a otevřete stránku **vytvořit pravidlo** .

    ![Nastavit upozornění](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Vyberte výchozí **kritéria výstrahy** vytvořená v upozornění a aktualizujte **prahovou hodnotu** ve výchozí metrikě na hodnotu 10.

    ![Kritéria upozornění](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Zadejte název a popis výstrahy a vyberte úroveň závažnosti. V našem příkladu jsme ho mohli nastavit na **informativní**.

4. Vyberte **skupinu akcí** , která bude upozorněna, když dojde k signálu. Můžete se rozhodnout, že budete svému týmu informovat prostřednictvím e-mailu nebo textové zprávy, nebo můžete automatizovat akci pomocí webhooků, Azure Functions nebo Logic Apps. Přečtěte si další informace o [vytváření a správě skupin výstrah v Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Po nakonfigurování výstrahy vyberte **vytvořit výstrahu** a povolte ji. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalace a použití předem připravených zobrazení pro protokoly aktivit Azure AD

Můžete si také stáhnout předem připravené zobrazení Log Analytics pro protokoly aktivit služby Azure AD. Zobrazení poskytují několik sestav, které se týkají běžných scénářů týkajících se událostí auditu a přihlašování. Pomocí postupu popsaného v předchozí části můžete také upozornit na kterékoli z dat, která jsou uvedena v sestavách.

* **Události zřizování účtu Azure AD**: Toto zobrazení obsahuje sestavy týkající se aktivity zřizování auditování, jako je počet nových uživatelů, kteří se zřídili a provisionují chyby, počet uživatelů, kteří se aktualizovaly a aktualizují chyby, a počet nezřízených uživatelů a odpovídající selhání.    
* **Události přihlášení**: Toto zobrazení obsahuje nejrelevantnější sestavy týkající se monitorování přihlašovacích aktivit, jako jsou například přihlášení podle aplikace, uživatele, zařízení a také souhrnné zobrazení sledování počtu přihlášení v průběhu času.
* **Uživatel, který provádí souhlas**: V tomto zobrazení se zobrazují sestavy týkající se souhlasu uživatele, jako je třeba souhlas udělený uživatelem, přihlášení uživatelů, kteří udělili souhlas, a také přihlášení aplikací pro všechny aplikace založené na souhlasu. 

Naučte se [instalovat a používat zobrazení Log Analytics pro protokoly aktivit Azure AD](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Další kroky

* [Začínáme s dotazy v protokolech Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Vytváření a Správa skupin výstrah v Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalace a použití zobrazení Log Analytics pro Azure Active Directory](howto-install-use-log-analytics-views.md)
