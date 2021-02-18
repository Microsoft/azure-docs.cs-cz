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
ms.openlocfilehash: 763e628183e5f6ad7b7bdbb8ee7ce6db572f44ad
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100577782"
---
# <a name="what-is-azure-active-directory-monitoring"></a>Co je monitorování Azure Active Directory?

S využitím monitorování Azure Active Directory (Azure AD) teď můžete směrovat protokoly aktivit služby Azure AD do různých koncových bodů. Následně je můžete ukládat pro účely dlouhodobého používání nebo integrovat s nástroji pro správu akcí a informací o zabezpečení (SIEM) třetích stran a získat tak přehled o vašem prostředí.

V současné době můžete směrovat protokoly do následujících umístění:

- Účet úložiště Azure.
- Centrum událostí Azure, takže můžete provést integraci s instancemi Splunk a SumoLogic.
- Pracovní prostor služby Azure Log Analytics, kde můžete analyzovat data a vytvářet řídicí panely a upozornění na určité události.

**Požadovaná role**: globální správce

> [!VIDEO https://www.youtube.com/embed/syT-9KNfug8]

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="licensing-and-prerequisites-for-azure-ad-reporting-and-monitoring"></a>Licencování a předpoklady pro Azure AD Reporting a monitoring

Pro přístup k protokolům přihlášení Azure AD budete potřebovat licenci Azure AD Premium.

Podrobné informace o funkcích a licencování najdete v [Azure Active Directory cenové příručce](https://azure.microsoft.com/pricing/details/active-directory/).

K nasazení monitorování a vytváření sestav Azure AD budete potřebovat uživatele, který je globálním správcem nebo správcem zabezpečení pro tenanta Azure AD.

V závislosti na konečném cíli dat protokolu budete potřebovat jednu z následujících možností:

* Účet úložiště Azure, pro který máte oprávnění ListKeys. Doporučujeme použít obecný účet úložiště, ne účet úložiště objektů blob. Informace o cenách úložiště najdete v [cenové kalkulačce služby Azure Storage](https://azure.microsoft.com/pricing/calculator/?service=storage).

* Obor názvů Azure Event Hubs, který se má integrovat s řešeními SIEM třetích stran.

* Pracovní prostor Azure Log Analytics k odesílání protokolů do protokolů Azure Monitor.

## <a name="diagnostic-settings-configuration"></a>Konfigurace nastavení diagnostiky

Pokud chcete spravovat nastavení monitorování pro protokoly aktivit služby Azure AD, nejprve se přihlaste k webu [Azure Portal](https://portal.azure.com) a pak vyberte **Azure Active Directory**. Odtud můžete na stránku konfigurace nastavení diagnostiky přejít dvěma způsoby:

* V části **Monitorování** vyberte **Nastavení diagnostiky**.

    ![Nastavení diagnostiky](./media/overview-monitoring/diagnostic-settings.png)
    
* Vyberte **Protokoly auditu** nebo **Protokoly přihlášení** a pak vyberte **Exportovat nastavení**. 

    ![Export nastavení](./media/overview-monitoring/export-settings.png)


## <a name="route-logs-to-storage-account"></a>Směrování protokolů do účtu úložiště

Směrováním protokolů do účtu úložiště Azure můžete zajistit jejich uložení po delší dobu, než je výchozí období uchovávání uvedené v našich [zásadách uchovávání](reference-reports-data-retention.md). Informace o [směrování dat do účtu úložiště](quickstart-azure-monitor-route-logs-to-storage-account.md).

## <a name="stream-logs-to-event-hub"></a>Streamování protokolů do centra událostí

Směrování protokolů do centra událostí Azure vám umožní provést integraci s nástroji SIEM třetích stran, jako je SumoLogic a Splunk. Tato integrace umožňuje kombinovat data protokolu aktivit Azure AD s jinými daty spravovanými vaším SIEM a poskytnout tak lepší přehled o vašem prostředí. Informace o [streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md).

## <a name="send-logs-to-azure-monitor-logs"></a>Odeslat protokoly do protokolů Azure Monitor

[Protokoly Azure monitor](../../azure-monitor/logs/log-query-overview.md) jsou řešení, které konsoliduje data monitorování z různých zdrojů a poskytuje dotazovací jazyk a analytické moduly, které vám poskytnou přehled o provozu aplikací a prostředků. Odesláním protokolů aktivit Azure AD do Azure Monitor protokolů můžete rychle načíst, monitorovat a upozornit shromážděná data. Naučte se [odesílat data do protokolů Azure monitor](howto-integrate-activity-logs-with-log-analytics.md).

Můžete také nainstalovat předem připravená zobrazení, aby protokoly aktivit služby Azure AD monitorovaly běžné scénáře včetně událostí přihlášení a auditu. Naučte se [instalovat a používat zobrazení Log Analytics pro protokoly aktivit Azure AD](howto-install-use-log-analytics-views.md).

## <a name="next-steps"></a>Další kroky

* [Protokoly aktivit ve službě Azure Monitor](concept-activity-logs-azure-monitor.md)
* [Streamování protokolů do centra událostí](tutorial-azure-monitor-stream-logs-to-event-hub.md)
* [Odeslat protokoly do protokolů Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md)
