---
title: Integrace protokolů Azure Active Directory s Splunk používat Azure Monitor | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s SumoLogic používat Azure Monitor
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
ms.openlocfilehash: 8f30e19a351f7b25f995a85cfd566bcba091ac27
ms.sourcegitcommit: 1fbc75b822d7fe8d766329f443506b830e101a5e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/14/2019
ms.locfileid: "65597818"
---
# <a name="how-to-integrate-azure-active-directory-logs-with-splunk-using-azure-monitor"></a>Postup: Integrace protokolů Azure Active Directory s Splunk používat Azure Monitor

V tomto článku se dozvíte, jak integrovat Splunk protokoly služby Azure Active Directory (Azure AD) pomocí Azure monitoru. První směrování protokolů do služby Azure event hub a pak integruje centra událostí se Splunk.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Protokoly centra událostí Azure, který obsahuje aktivitu Azure AD. Zjistěte, jak [streamování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Rozšíření Azure Monitor umožňující Splunk. [Stáhnout a nakonfigurovat instanci Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="integrate-azure-active-directory-logs"></a>Integrace protokolů Azure Active Directory 

1. Vaše instance Splunk otevřete a vyberte **souhrn dat**.

    ![Tlačítko "Souhrn dat"](./media/howto-integrate-activity-logs-with-splunk/DataSummary.png)

2. Vyberte **Sourcetypes** kartu a potom vyberte **amal: aadal:audit**

    ![Na kartě Sourcetypes souhrn dat](./media/howto-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Aktivity služby Azure AD protokoly jsou zobrazené na následujícím obrázku:

    ![Protokoly aktivit](./media/howto-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Pokud doplněk nemůžete instalovat ve vaší instanci Splunk (například, pokud už používáte proxy server nebo spouštíte na Splunk Cloud), můžete tyto události přeposílat ke kolekci událostí Splunk HTTP. K tomu použít tento [funkce Azure Functions](https://github.com/Microsoft/AzureFunctionforSplunkVS), která se aktivuje nové zprávy do centra událostí. 
>

## <a name="next-steps"></a>Další postup

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)