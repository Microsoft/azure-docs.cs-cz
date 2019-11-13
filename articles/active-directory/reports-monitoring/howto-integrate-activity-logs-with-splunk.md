---
title: Integrace Splunk pomocí Azure Monitor | Microsoft Docs
description: Informace o tom, jak integrovat protokoly Azure Active Directory s využitím SumoLogic pomocí Azure Monitor
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
ms.date: 05/13/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7da3a545847382f8fed192a6ec4fe2ac75bb8b35
ms.sourcegitcommit: ae8b23ab3488a2bbbf4c7ad49e285352f2d67a68
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2019
ms.locfileid: "74014398"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Postupy: integrace protokolů Azure Active Directory s využitím Splunk pomocí Azure Monitor

V tomto článku se dozvíte, jak integrovat protokoly Azure Active Directory (Azure AD) s Splunk pomocí Azure Monitor. Nejprve protokoly směrujete do centra událostí Azure a potom integrujete centrum událostí pomocí Splunk.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Centrum událostí Azure, které obsahuje protokoly aktivit služby Azure AD. Naučte se, jak [streamovat protokoly aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Doplněk Azure Monitor pro Splunk. [Stáhněte a nakonfigurujte svou instanci Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrace protokolů Azure Active Directory 

1. Otevřete instanci Splunk a vyberte **data Summary (souhrn dat**).

    ![Tlačítko Souhrn dat](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Vyberte kartu **sourceType** a pak vyberte **Amal: aadal: audit.**

    ![Karta karta pro shrnutí dat](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Protokoly aktivit Azure AD jsou zobrazené na následujícím obrázku:

    ![Protokoly aktivit](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Pokud nemůžete nainstalovat doplněk do instance Splunk (například pokud používáte proxy server nebo spuštěný v Splunk cloudu), můžete tyto události pře do sběrače událostí protokolu HTTP Splunk. Uděláte to tak, že použijete tuto [funkci Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), která se aktivuje novými zprávami v centru událostí. 
>

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)