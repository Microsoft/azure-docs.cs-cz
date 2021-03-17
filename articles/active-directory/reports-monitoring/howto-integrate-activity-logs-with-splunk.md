---
title: Integrace Splunk pomocí Azure Monitor | Microsoft Docs
description: Naučte se integrovat protokoly Azure Active Directory pomocí Azure Monitor Splunk.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/10/2021
ms.author: markvi
ms.reviewer: besiler
ms.collection: M365-identity-device-management
ms.openlocfilehash: afb6a597d4fd58646f56e271cb6027fb46db1e26
ms.sourcegitcommit: b572ce40f979ebfb75e1039b95cea7fce1a83452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "102634222"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Postupy: integrace protokolů Azure Active Directory s využitím Splunk pomocí Azure Monitor

V tomto článku se dozvíte, jak integrovat protokoly Azure Active Directory (Azure AD) s Splunk pomocí Azure Monitor. Nejprve protokoly směrujete do centra událostí Azure a potom integrujete centrum událostí pomocí Splunk.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

- Centrum událostí Azure, které obsahuje protokoly aktivit služby Azure AD. Naučte se, jak [streamovat protokoly aktivit do centra událostí](./tutorial-azure-monitor-stream-logs-to-event-hub.md). 

-  [Doplněk Splunk pro Microsoft Cloud Services](https://splunkbase.splunk.com/app/3110/#/details). 

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

* [Interpretace schématu protokolů auditu v Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)