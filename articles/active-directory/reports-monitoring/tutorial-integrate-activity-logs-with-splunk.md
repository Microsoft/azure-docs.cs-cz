---
title: Stream protokolů služby Azure Active Directory na Splunk používat Azure Monitor (preview) | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s Splunk pomocí Azure monitoru (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 11/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 31a4f5028cc6711ec92a495b19a17e8a0fbf11aa
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56170451"
---
# <a name="integrate-azure-ad-logs-with-splunk-using-azure-monitor-preview"></a>Integrace protokolů Azure AD s Splunk používat Azure Monitor (preview)

V tomto článku se dozvíte, jak integrovat Splunk protokoly služby Azure Active Directory (Azure AD) pomocí Azure monitoru. První směrování protokolů do služby Azure event hub a pak integruje centra událostí se Splunk.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Protokoly centra událostí Azure, který obsahuje aktivitu Azure AD. Zjistěte, jak [streamování protokolů aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Rozšíření Azure Monitor umožňující Splunk. [Stáhnout a nakonfigurovat instanci Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Kurz 

1. Vaše instance Splunk otevřete a vyberte **souhrn dat**.

    ![Tlačítko "Souhrn dat"](./media/tutorial-integrate-activity-logs-with-splunk/DataSummary.png)

2. Vyberte **Sourcetypes** kartu a potom vyberte **amal: aadal:audit**

    ![Na kartě Sourcetypes souhrn dat](./media/tutorial-integrate-activity-logs-with-splunk/sourcetypeaadal.png)

    Aktivity služby Azure AD protokoly jsou zobrazené na následujícím obrázku:

    ![Protokoly aktivit](./media/tutorial-integrate-activity-logs-with-splunk/activitylogs.png)

> [!NOTE]
> Pokud doplněk nemůžete instalovat ve vaší instanci Splunk (například, pokud už používáte proxy server nebo spouštíte na Splunk Cloud), můžete tyto události přeposílat ke kolekci událostí Splunk HTTP. K tomu použít tento [funkce Azure Functions](https://github.com/Microsoft/AzureFunctionforSplunkVS), která se aktivuje nové zprávy do centra událostí. 
>

## <a name="next-steps"></a>Další postup

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)
