---
title: Streamování protokolů do SumoLogic pomocí Azure Monitor | Microsoft Docs
description: Informace o tom, jak integrovat protokoly Azure Active Directory s využitím SumoLogic pomocí Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 2c3db9a8-50fa-475a-97d8-f31082af6593
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 70c3b72ab650eb506dfaac378e10a7170b8f041d
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819750"
---
# <a name="integrate-azure-active-directory-logs-with-sumologic-using-azure-monitor"></a>Integrace protokolů Azure Active Directory s využitím SumoLogic pomocí Azure Monitor

V tomto článku se dozvíte, jak integrovat protokoly Azure Active Directory (Azure AD) s SumoLogic pomocí Azure Monitor. Nejprve protokoly směrujete do centra událostí Azure a potom integrujete centrum událostí pomocí SumoLogic.

## <a name="prerequisites"></a>Předpoklady

Pokud chcete používat tuto funkci, potřebujete tyto položky:
* Centrum událostí Azure, které obsahuje protokoly aktivit služby Azure AD. Naučte se, jak [streamovat protokoly aktivit do centra událostí](quickstart-azure-monitor-stream-logs-to-event-hub.md). 
* Předplatné s povoleným SumoLogicm jednotným přihlašováním.

## <a name="steps-to-integrate-azure-ad-logs-with-sumologic"></a>Postup integrace protokolů služby Azure AD s SumoLogic 

1. Nejdřív [Streamujte protokoly Azure AD do centra událostí Azure](quickstart-azure-monitor-stream-logs-to-event-hub.md).
2. Nakonfigurujte instanci SumoLogic pro [shromažďování protokolů pro Azure Active Directory](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Collect_Logs_for_Azure_Active_Directory).
3. [Nainstalujte aplikaci Azure AD SumoLogic](https://help.sumologic.com/Send-Data/Applications-and-Other-Data-Sources/Azure_Active_Directory/Install_the_Azure_Active_Directory_App_and_View_the_Dashboards) , abyste mohli používat předem nakonfigurované řídicí panely, které poskytují analýzu vašeho prostředí v reálném čase.

   ![Řídicí panel](./media/howto-integrate-activity-logs-with-sumologic/overview-dashboard.png)

## <a name="next-steps"></a>Další kroky

* [Interpretace schématu protokolů auditu ve službě Azure Monitor](reference-azure-monitor-audit-log-schema.md)
* [Interpretace schématu protokolů přihlášení ve službě Azure Monitor](reference-azure-monitor-sign-ins-log-schema.md)
* [Nejčastější dotazy a známé problémy](concept-activity-logs-azure-monitor.md#frequently-asked-questions)