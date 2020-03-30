---
title: Co je monitorování Azure Active Directory? | Dokumentace Microsoftu
description: Poskytuje obecný přehled monitorování Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: e2b3d8ce-708a-46e4-b474-123792f35526
ms.service: active-directory
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: d63f8440ca527a746f73574bc156037d85fc3599
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "68988210"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Co je monitorování Azure Active Directory?

S využitím monitorování Azure Active Directory (Azure AD) teď můžete směrovat protokoly aktivit služby Azure AD do různých koncových bodů. Následně je můžete ukládat pro účely dlouhodobého používání nebo integrovat s nástroji pro správu akcí a informací o zabezpečení (SIEM) třetích stran a získat tak přehled o vašem prostředí.

V současné době můžete směrovat protokoly do následujících umístění:

- Účet úložiště Azure.
- Centrum událostí Azure, takže můžete provést integraci s instancemi Splunk a SumoLogic.
- Pracovní prostor služby Azure Log Analytics, kde můžete analyzovat data a vytvářet řídicí panely a upozornění na určité události.

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

Směrování protokolů do centra událostí Azure vám umožní provést integraci s nástroji SIEM třetích stran, jako je SumoLogic a Splunk. Tato integrace umožňuje kombinovat data protokolu aktivit Azure AD s dalšími daty spravovanými vaším SIEM, abyste poskytli bohatší přehled o vašem prostředí. Informace o [streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Odesílání protokolů do protokolů Azure Monitoru

[Protokoly Azure Monitor je](https://docs.microsoft.com/azure/log-analytics/log-analytics-overview) řešení, které konsoliduje data monitorování z různých zdrojů a poskytuje dotazovací jazyk a analytický modul, který vám poskytne přehled o provozu vašich aplikací a prostředků. Odesláním protokolů aktivit Azure AD do protokolů Azure Monitoru můžete rychle načítat, monitorovat a upozorňovat na shromážděná data. Přečtěte si, jak [odesílat data do protokolů Azure Monitoru](howto-integrate-activity-logs-with-log-analytics.md).

Můžete také nainstalovat předem připravená zobrazení, aby protokoly aktivit služby Azure AD monitorovaly běžné scénáře včetně událostí přihlášení a auditu. Zjistěte, jak [nainstalovat a používat zobrazení analýzy protokolů pro protokoly aktivit Azure AD](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Další kroky

* [Protokoly aktivit ve službě Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Odesílání protokolů do protokolů Azure Monitoru](howto-integrate-activity-logs-with-log-analytics.md)