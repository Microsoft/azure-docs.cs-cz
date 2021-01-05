---
title: IT Service Management Connector – zabezpečení exportu v Azure Monitor – konfigurace pomocí ServiceNow
description: V tomto článku se dozvíte, jak připojit ITSM produkty/služby k ServiceNow při zabezpečeném exportu v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: fc384ffbc246f5ce9fa84b8161cbc4a5226fa5c8
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/31/2020
ms.locfileid: "97839401"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Připojení ServiceNow k Azure Monitor

Následující části obsahují podrobné informace o tom, jak připojit produkt ServiceNow a zabezpečený export v Azure.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste splnili následující požadavky:

* Služba Azure AD je zaregistrovaná.
* Máte podporovanou verzi ServiceNow Event Management-ITOM (verze Orlandu nebo novější).

## <a name="configure-the-servicenow-connection"></a>Konfigurace připojení ServiceNow

1. Pro definici zabezpečeného exportu použijte linku https://(název instance). Service-Now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor identifikátor URI.

2. Postupujte podle pokynů podle verze:
   * [Paříž](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlandu](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)