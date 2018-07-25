---
title: Postup pro integraci protokolů Azure Active Directory Splunk používat Azure Monitor (preview) | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s Splunk používat Azure Monitor (preview)
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: c4b605b6-6fc0-40dc-bd49-101d03f34665
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 07/13/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: d1dd62d06c7e3ed634795604ce9660694ea073ca
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/25/2018
ms.locfileid: "39239849"
---
# <a name="integrate-azure-active-directory-logs-with-splunk-using-azure-monitor-preview"></a>Integrace protokolů Azure Active Directory s Splunk používat Azure Monitor (preview)

V tomto článku se dozvíte, jak integrovat protokoly služby Azure Active Directory s Splunk používat Azure Monitor. Nejprve je třeba směrovat protokoly do služby Azure event hub a pak ji integrovat s Splunk.

## <a name="prerequisites"></a>Požadavky

1. Protokoly se Centrum událostí Azure obsahující aktivit Azure AD. Zjistěte, jak [streamování protokolů aktivit do centra událostí](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
2. Pomocí následujících [pokyny](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md) stáhnout doplněk Azure monitor pro Splunk a umožňuje nakonfigurovat instanci Splunk.

## <a name="tutorial"></a>Kurz 

1. Otevřít vaše instance Splunk a klikněte na tlačítko **souhrn dat**.
    ![Shrnutí dat](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png "souhrn dat")

2. Přejděte **Sourcetypes** kartě a vyberte **amal: aadal:audit** ![Sourcetypes kartu](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png "Sourcetypes kartu")

3. Zobrazí se vám Azure protokoly aktivit AD, jak je znázorněno na následujícím obrázku.
    ![Protokoly aktivit](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png "protokolů aktivit")

> [!NOTE]
> Pokud doplněk nemůžete instalovat ve vaší instanci Splunk (například, pokud už používáte proxy server nebo spouštíte na Splunk Cloud), můžete tyto události přeposílat ke kolekci událostí HTTP Splunk použití této funkce [funkce Azure Functions, která se spustí pomocí nové zprávy ve Centrum událostí](https://github.com/Microsoft/AzureFunctionforSplunkVS). " 
>

## <a name="next-steps"></a>Další postup

* [Interpretace schéma protokolů auditování ve službě Azure monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretace protokolů přihlášení schématu ve službě Azure monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Nejčastější dotazy a známé problémy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)