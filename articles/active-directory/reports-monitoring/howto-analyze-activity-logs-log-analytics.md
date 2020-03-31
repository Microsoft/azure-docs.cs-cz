---
title: Analýza protokolů aktivit pomocí protokolů Azure Monitor | Dokumenty společnosti Microsoft
description: Zjistěte, jak analyzovat protokoly aktivit služby Azure Active Directory pomocí protokolů Azure Monitoru
services: active-directory
documentationcenter: ''
author: MarkusVi
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
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d6212692465270182db541889bed5f03a08a345
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74008274"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Analýza protokolů aktivit Azure AD pomocí protokolů Azure Monitor

Po [integraci protokolů aktivit Azure AD s protokoly Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md), můžete použít výkon protokolů Azure Monitor získat přehled o vašem prostředí. Můžete také nainstalovat [zobrazení analýzy protokolů pro protokoly aktivit Azure AD](howto-install-use-log-analytics-views.md) získat přístup k předem sestavené sestavy kolem auditu a přihlášení události ve vašem prostředí.

V tomto článku se dozvíte, jak analyzovat protokoly aktivit Azure AD v pracovním prostoru Analýzy protokolů. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Požadavky 

Chcete-li sledovat, musíte:

* Pracovní prostor Analýzy protokolů ve vašem předplatném Azure. Přečtěte si, jak [vytvořit pracovní prostor Analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Nejprve proveďte kroky ke [směrování protokolů aktivit Azure AD do pracovního prostoru Analýzy protokolů](howto-integrate-activity-logs-with-log-analytics.md).
*  [Přístup k](https://docs.microsoft.com/azure/azure-monitor/platform/manage-access#manage-access-using-workspace-permissions) pracovnímu prostoru analýzy protokolů
* Následující role ve službě Azure Active Directory (pokud přistupujete k Log Analytics prostřednictvím portálu Azure Active Directory)
    - Správce zabezpečení
    - Čtečka zabezpečení
    - Čtečka sestav
    - Globální správce
    
## <a name="navigate-to-the-log-analytics-workspace"></a>Přechod do pracovního prostoru Log Analytics

1. Přihlaste se k [portálu Azure](https://portal.azure.com). 

2. Vyberte **Azure Active Directory**a pak v části **Monitorování** vyberte **Protokoly** a otevřete pracovní prostor Analýzy protokolů. Pracovní prostor se otevře s výchozím dotazem.

    ![Výchozí dotaz](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Zobrazení schématu pro protokoly aktivit Azure AD

Protokoly jsou zasunuty do **tabulek AuditLogs** a **SigninLogs** v pracovním prostoru. Chcete-li zobrazit schéma pro tyto tabulky:

1. Ve výchozím zobrazení dotazu v předchozí části vyberte **schéma** a rozbalte pracovní prostor. 

2. Rozbalte část **Správa protokolů** a potom rozbalte **auditní logy** nebo **signinlogs,** abyste zobrazili schéma protokolu.
    ![Protokoly](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png) ![přihlášení protokoly auditu](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Dotaz na protokoly aktivit Azure AD

Nyní, když máte protokoly v pracovním prostoru, můžete nyní spouštět dotazy proti nim. Chcete-li například získat nejlepší aplikace použité v posledním týdnu, nahraďte výchozí dotaz následujícím a vyberte **spustit**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Chcete-li získat nejvyšší události auditu za poslední týden, použijte následující dotaz:

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Výstraha na data protokolu aktivit Azure AD

Můžete také nastavit upozornění na dotaz. Chcete-li například nakonfigurovat výstrahu při použití více než 10 aplikací v posledním týdnu:

1. V pracovním prostoru vyberte **Nastavit výstrahu,** abyste otevřeli stránku **Vytvořit pravidlo.**

    ![Nastavit výstrahu](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Vyberte výchozí **kritéria výstrah vytvořených** ve výstraze a **aktualizujte prahovou hodnotu** ve výchozí metrice na 10.

    ![Kritéria výstrah](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Zadejte název a popis výstrahy a zvolte úroveň závažnosti. Pro náš příklad bychom jej mohli nastavit na **Informační**.

4. Vyberte **skupinu akcí,** která bude upozorněna, když dojde k signálu. Můžete se rozhodnout upozornit svůj tým e-mailem nebo textovou zprávou, nebo můžete akci automatizovat pomocí webhooků, funkcí Azure nebo aplikací logiky. Další informace o [vytváření a správě skupin výstrah najdete na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. Jakmile výstrahu nakonfigurujete, vyberte **příkaz Vytvořit výstrahu,** abyste ji povolili. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Instalace a použití předem vytvořených zobrazení pro protokoly aktivit Azure AD

Můžete si také stáhnout předem sestavená zobrazení analýzy protokolů pro protokoly aktivit Azure AD. Zobrazení poskytují několik sestav týkajících se běžných scénářů zahrnujících audit a události přihlášení. Můžete také upozornit na libovolné údaje uvedené v sestavách pomocí kroků popsaných v předchozí části.

* **Události zřizování účtu Azure AD**: Toto zobrazení zobrazuje sestavy související s aktivitou zřizování auditování, jako je počet nových uživatelů zřízených a zřizování selhání, počet uživatelů aktualizované a aktualizace selhání a počet uživatelů deprovisioned a odpovídající selhání.    
* **Události přihlášení**: Toto zobrazení zobrazuje nejdůležitější sestavy související s aktivitou přihlašování monitorováním, jako je přihlášení podle aplikace, uživatele, zařízení a také souhrnné zobrazení sledující počet přihlášení v průběhu času.
* **Uživatelé, kteří vyřizují souhlas**: Toto zobrazení zobrazuje sestavy související se souhlasem uživatele, jako je například udělení souhlasu uživatelem, přihlášení uživateli, kteří udělili souhlas, a přihlášení aplikací pro všechny aplikace založené na souhlasu. 

Zjistěte, jak [nainstalovat a používat zobrazení analýzy protokolů pro protokoly aktivit Azure AD](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Další kroky

* [Začínáme s dotazy v protokolech Azure Monitoru](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Vytváření a správa skupin výstrah na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Instalace a použití zobrazení analýzy protokolů pro Službu Azure Active Directory](howto-install-use-log-analytics-views.md)
