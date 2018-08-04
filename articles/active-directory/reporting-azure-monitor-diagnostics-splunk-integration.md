---
title: Postup pro integraci protokolů Azure Active Directory Splunk pomocí Azure monitoru (preview) | Dokumentace Microsoftu
description: Zjistěte, jak integrace protokolů Azure Active Directory s Splunk pomocí Azure monitoru (preview)
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
ms.openlocfilehash: 42dbb8c7e74bd3acb99028477f34f99f1334d577
ms.sourcegitcommit: 9222063a6a44d4414720560a1265ee935c73f49e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/03/2018
ms.locfileid: "39503838"
---
# <a name="integrate-azure-ad-logs-with-splunk-by-using-azure-monitor-preview"></a>Integrace protokolů Azure AD s Splunk pomocí Azure monitoru (preview)

V tomto článku se dozvíte, jak integrovat Splunk protokoly služby Azure Active Directory (Azure AD) pomocí Azure monitoru. První směrování protokolů do služby Azure event hub a pak integruje centra událostí se Splunk.

## <a name="prerequisites"></a>Požadavky

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Protokoly centra událostí Azure, který obsahuje aktivitu Azure AD. Zjistěte, jak [streamování protokolů aktivit do centra událostí](reporting-azure-monitor-diagnostics-azure-event-hub.md). 
* Rozšíření Azure Monitor umožňující Splunk. [Stáhnout a nakonfigurovat instanci Splunk](https://github.com/Microsoft/AzureMonitorAddonForSplunk/blob/master/README.md).

## <a name="tutorial"></a>Kurz 

1. Vaše instance Splunk otevřete a vyberte **souhrn dat**.

    ![Tlačítko "Souhrn dat"](./media/reporting-azure-monitor-diagnostics-splunk-integration/DataSummary.png)

2. Vyberte **Sourcetypes** kartu a potom vyberte **amal: aadal:audit**

    ![Na kartě Sourcetypes souhrn dat](./media/reporting-azure-monitor-diagnostics-splunk-integration/sourcetypeaadal.png)

    Aktivity služby Azure AD protokoly jsou zobrazené na následujícím obrázku:

    ![Protokoly aktivit](./media/reporting-azure-monitor-diagnostics-splunk-integration/activitylogs.png)

> [!NOTE]
> Pokud doplněk nemůžete instalovat ve vaší instanci Splunk (například, pokud už používáte proxy server nebo spouštíte na Splunk Cloud), můžete tyto události přeposílat ke kolekci událostí Splunk HTTP. K tomu použít tento [funkce Azure Functions](https://github.com/Microsoft/AzureFunctionforSplunkVS), která se aktivuje nové zprávy do centra událostí. 
>

## <a name="next-steps"></a>Další postup

* [Interpretace schéma protokolů auditování ve službě Azure Monitor](reporting-azure-monitor-diagnostics-audit-log-schema.md)
* [Interpretace protokolů přihlášení schématu ve službě Azure Monitor](reporting-azure-monitor-diagnostics-sign-in-log-schema.md)
* [Nejčastější dotazy a známé problémy](reporting-azure-monitor-diagnostics-overview.md#frequently-asked-questions)
