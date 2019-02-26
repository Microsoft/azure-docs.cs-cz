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
ms.collection: M365-identity-device-management
ms.openlocfilehash: d81905d3e42302a5654b51cc0269e546fee49d2f
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56816772"
---
# <a name="what-is-azure-active-directory-monitoring-preview"></a>Co je monitorování Azure Active Directory? (Preview)

S využitím monitorování Azure Active Directory (Azure AD) teď můžete směrovat protokoly aktivit služby Azure AD do různých koncových bodů. Následně je můžete ukládat pro účely dlouhodobého používání nebo integrovat s nástroji pro správu akcí a informací o zabezpečení (SIEM) třetích stran a získat tak přehled o vašem prostředí.

V současné době můžete směrovat protokoly do následujících umístění:

- Účet úložiště Azure.
- Centrum událostí Azure, takže můžete provést integraci s instancemi Splunk a SumoLogic.
- Pracovní prostor Azure Log Analytics, kde můžete analyzovat data a vytvářet řídicí panely a upozornění na určité události.

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

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

## <a name="send-logs-to-azure-monitor-logs"></a>Odeslání protokolů s protokoly Azure monitoru

[Protokoly Azure monitoru](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) je řešení, které konsoliduje data monitorování z různých zdrojů a poskytuje dotazovací jazyk a analytický modul, který poskytuje přehled o fungování vašich aplikací a prostředků. Odesláním Azure AD protokoly aktivit na protokoly Azure monitoru, můžete snadno obnovit, monitorování a upozornění na data shromážděná. Zjistěte, jak [data posílat protokoly Azure monitoru](howto-integrate-activity-logs-with-log-analytics.md).

Můžete také nainstalovat předem připravená zobrazení, aby protokoly aktivit služby Azure AD monitorovaly běžné scénáře včetně událostí přihlášení a auditu. Zjistěte, jak [nainstalovat a používat zobrazení log analytics pro Azure AD aktivitu protokoly](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Další postup

* [Protokoly aktivit ve službě Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Odeslání protokolů s protokoly Azure monitoru](howto-integrate-activity-logs-with-log-analytics.md)
