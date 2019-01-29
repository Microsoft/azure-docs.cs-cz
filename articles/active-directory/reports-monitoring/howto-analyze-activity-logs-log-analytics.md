---
title: Analýza protokolů aktivit Azure Active Directory pomocí služby Log Analytics (preview) | Dokumentace Microsoftu
description: Naučte se analyzovat protokoly aktivit Azure Active Directory pomocí Log Analytics (Náhled)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: a508c8a2bb4a28372f5dd8f833f6e55adc3c39ed
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55158736"
---
# <a name="analyze-azure-ad-activity-logs-with-log-analytics-preview"></a>Analýza služby Azure AD protokoly aktivit s využitím Log Analytics (Náhled)

Poté co [integrovat Azure AD protokoly aktivit s využitím Log Analytics](howto-integrate-activity-logs-with-log-analytics.md), výkon služby Log Analytics můžete použít k získání přehledu o vašem prostředí. Můžete také nainstalovat [zobrazení Log Analytics pro Azure AD aktivitu protokoly](howto-install-use-log-analytics-views.md) získat přístup k předdefinované sestavy na základě auditu a události přihlášení ve vašem prostředí.

V tomto článku se dozvíte, jak Azure analyzovat protokoly AD aktivit ve vašem pracovním prostoru Log Analytics. 

## <a name="prerequisites"></a>Požadavky 

Pokud chcete postupovat s námi, budete potřebovat:

* Pracovní prostor Log Analytics ve vašem předplatném Azure. Zjistěte, jak [vytvořit pracovní prostor Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Nejprve dokončete postup [trasy Azure AD protokolů aktivit do pracovního prostoru Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Přejděte do pracovního prostoru Log Analytics

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com). 

2. Vyberte **Azure Active Directory**a pak vyberte **protokoly** z **monitorování** části pro otevření pracovního prostoru Log Analytics. Otevře se pracovní prostor s výchozím dotazem.

    ![Výchozí dotaz](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Zobrazit schéma pro Azure AD aktivitu protokoly

Protokoly se nasdílejí do **mají** a **SigninLogs** tabulek v pracovním prostoru. Chcete-li zobrazit schéma pro tyto tabulky:

1. Výchozí zobrazení dotazu v předchozí části, vyberte **schématu** a rozbalte pracovním prostoru. 

2. Rozbalte **Správa protokolů** části a pak rozbalte **mají** nebo **SignInLogs** a zobrazit tak schéma protokolu.
    ![Protokoly auditu](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![Signin protokoly](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Protokoly aktivit dotazů Azure AD

Teď, když máte protokoly ve vašem pracovním prostoru, můžete nyní spouštět dotazy proti nim. Například chcete-li získat hlavní aplikace používané v posledním týdnu, nahraďte výchozí dotaz na následující a vyberte **spuštění**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Začátek auditovat události za poslední týden získáte pomocí následujícího dotazu:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Upozornění na data protokolu aktivit Azure AD

Můžete také nastavit výstrahy na váš dotaz. Například pokud chcete nakonfigurovat výstrahy, když se více než 10 aplikací se používají v posledním týdnu:

1. V pracovním prostoru vyberte **nastavit upozornění** otevřít **vytvořit pravidlo** stránky. 
    ![Nastavení upozornění](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Vyberte výchozí **upozornění kritéria** vytvořené v výstrahy a aktualizace **prahová hodnota** ve výchozí metriku na 10. 
    ![Kritéria výstrah](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Zadejte název a popis pro výstrahu a vyberte úroveň závažnosti. V našem příkladu jsme ji nastavit na **informativní**.

4. Vyberte **skupiny akcí** , který bude dostat oznámení, když dojde k signálu. Můžete nastavit upozornění týmu prostřednictvím e-mailu nebo textovou zprávu nebo může Automatizujte akce pomocí webhooků, služba Azure functions nebo logic apps. Další informace o [vytváření a Správa výstrah skupiny na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Po nakonfigurování výstrahy vyberte **vytvořit výstraha** ho chcete povolit. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalace a používání předdefinovaných zobrazení aktivity služby Azure AD protokoly

Můžete si také stáhnout předem připravené zobrazení Log Analytics pro Azure AD aktivitu protokoly. Zobrazení poskytují několik sestav související s běžné scénáře zahrnující auditu a události přihlášení. Vám může také upozornit na žádném z dat v sestavách, pomocí kroků popsaných v předchozí části.

* **Účet Azure AD zřizování události**: Toto zobrazení uvádí sestavy k auditování aktivity zajišťování na aktivitu, jako je počet nových uživatelů, které jsou zřízené a selhání zřizování, počet uživatelů, aktualizovat a aktualizovat chyby a počet uživatelů, zrušení zřízení a odpovídající selhání.    
* **Události přihlášení**: Toto zobrazení uvádí relevantní zprávy související s monitorování aktivit přihlašování, jako je například přihlášení aplikace, uživatelů, zařízení, stejně jako přehled sledování počet přihlášení v čase.
* **Uživatelů, kteří provádějí souhlasu**: Toto zobrazení uvádí sestavy související se souhlas uživatele, jako například uděluje souhlas uživatele, přihlášení podle uživatele, kteří udělili souhlas, jakož i přihlášení aplikace pro všechny aplikace na základě vyjádření souhlasu. 

Informace o [instalaci a používání zobrazení Log Analytics pro protokoly aktivit služby Azure AD](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Další postup

* [Začínáme s dotazy v Log Analytics](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Vytvoření a Správa výstrah skupin na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Nainstalovat a používat zobrazení Log Analytics pro Azure Active Directory](howto-install-use-log-analytics-views.md)
