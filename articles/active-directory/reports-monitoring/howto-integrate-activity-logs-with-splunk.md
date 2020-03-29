---
title: Integrace Splunk pomocí Azure Monitor | Dokumenty společnosti Microsoft
description: Zjistěte, jak integrovat protokoly služby Azure Active Directory s SumoLogic pomocí Služby Azure Monitor
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
ms.date: 03/10/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eda3643a7b1a341c7ed664dbfea933145f1f927
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78968716"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Postup: Integrace protokolů služby Azure Active Directory s aplikací Splunk pomocí nástroje Azure Monitor

V tomto článku se dozvíte, jak integrovat protokoly Azure Active Directory (Azure AD) s Splunk pomocí Azure Monitor. Nejprve směrovat protokoly do centra událostí Azure a potom integrovat centrum událostí s Splunk.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:

- Centrum událostí Azure, které obsahuje protokoly aktivit Azure AD. Přečtěte si, jak [streamovat protokoly aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 

-  [Microsoft Azure Přidat pro Splunk](https://splunkbase.splunk.com/app/3757/). 

## <a name="integrate-azure-active-directory-logs"></a>Integrace protokolů služby Azure Active Directory 

1. Otevřete instanci Splunk a vyberte **Souhrn dat**.

    ![Tlačítko "Souhrn dat"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Vyberte kartu **Sourcetypes** a pak vyberte **amal: aadal:audit**

    ![Karta Zdroje souhrnu dat](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Protokoly aktivit Azure AD jsou zobrazeny na následujícím obrázku:

    ![Protokoly aktivit](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Pokud nemůžete nainstalovat doplněk v instanci Splunk (například pokud používáte proxy server nebo běží na Splunk Cloud), můžete tyto události předat kolekcí událostí Splunk HTTP. Chcete-li tak učinit, použijte tuto [funkci Azure](https://github.com/Microsoft/AzureFunctionforSplunkVS), která se aktivuje nové zprávy v centru událostí. 
>

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu v Azure Monitoru](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)