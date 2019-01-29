---
title: Co je monitorování Azure Active Directory? (Preview) | Microsoft Docs
description: Poskytuje obecný přehled monitorování Azure Active Directory.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: e2d47abcb7252375419296009d2dbc4a1d3d2812
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55150815"
---
# <a name="what-is-azure-active-directory-monitoring-preview"></a>Co je monitorování Azure Active Directory? (Preview)

S využitím monitorování Azure Active Directory (Azure AD) teď můžete směrovat protokoly aktivit služby Azure AD do různých koncových bodů. Následně je můžete ukládat pro účely dlouhodobého používání nebo integrovat s nástroji pro správu akcí a informací o zabezpečení (SIEM) třetích stran a získat tak přehled o vašem prostředí.

V současné době můžete směrovat protokoly do následujících umístění:

- Účet úložiště Azure.
- Centrum událostí Azure, takže můžete provést integraci s instancemi Splunk a SumoLogic.
- Pracovní prostor Azure Log Analytics, kde můžete analyzovat data a vytvářet řídicí panely a upozornění na určité události.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

## <a name="diagnostic-settings-configuration"></a>Konfigurace nastavení diagnostiky

Pokud chcete spravovat nastavení monitorování pro protokoly aktivit služby Azure AD, nejprve se přihlaste k webu [Azure Portal](https://portal.azure.com) a pak vyberte **Azure Active Directory**. Odtud můžete na stránku konfigurace nastavení diagnostiky přejít dvěma způsoby:

* V části **Monitorování** vyberte **Nastavení diagnostiky**.

    ![Nastavení diagnostiky](./media/overview-monitoring/diagnostic-settings.png)
    
* Vyberte **Protokoly auditu** nebo **Protokoly přihlášení** a pak vyberte **Exportovat nastavení**. 

    ![Export nastavení](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Směrování protokolů do účtu úložiště

Směrováním protokolů do účtu úložiště Azure můžete zajistit jejich uložení po delší dobu, než je výchozí období uchovávání uvedené v našich [zásadách uchovávání](reference-reports-data-retention.md). Informace o [směrování dat do účtu úložiště](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Streamování protokolů do centra událostí

Směrování protokolů do centra událostí Azure vám umožní provést integraci s nástroji SIEM třetích stran, jako je SumoLogic a Splunk. Tato integrace umožňuje kombinovat data protokolu aktivit Azure AD s jinými daty spravuje vašeho systému SIEM k poskytování bohatších přehled o svém prostředí. Informace o [streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-log-analytics"></a>Odesílání protokolů do Log Analytics

[Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) je řešení, které konsoliduje data monitorování z různých zdrojů a nabízí dotazovací jazyk a analytický modul poskytující přehled o operacích vašich aplikací a prostředků. Odesílání protokolů aktivit služby Azure AD do Log Analytics vám umožní rychle načítat, monitorovat a upozorňovat na shromážděná data. Informace o [odesílání dat do Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

Můžete také nainstalovat předem připravená zobrazení, aby protokoly aktivit služby Azure AD monitorovaly běžné scénáře včetně událostí přihlášení a auditu. Informace o [instalaci a používání zobrazení Log Analytics pro protokoly aktivit služby Azure AD](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Další postup

* [Protokoly aktivit ve službě Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Odesílání protokolů do Log Analytics](howto-integrate-activity-logs-with-log-analytics.md)
