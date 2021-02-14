---
title: IT Service Management Connector – zabezpečení exportu v Azure Monitor – konfigurace pomocí ServiceNow
description: V tomto článku se dozvíte, jak připojit ITSM produkty/služby k ServiceNow při zabezpečeném exportu v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7801145ba1bcaa2ffd543becfe190f05d232e8c8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104956"
---
# <a name="connect-servicenow-to-azure-monitor"></a>Připojení ServiceNow k Azure Monitor

Následující části obsahují podrobné informace o tom, jak připojit produkt ServiceNow a zabezpečený export v Azure.

## <a name="prerequisites"></a>Požadavky

Ujistěte se, že jste splnili následující požadavky:

* Služba Azure AD je zaregistrovaná.
* Máte podporovanou verzi správy událostí ServiceNow-ITOM (verze New York nebo novější).

## <a name="configure-the-servicenow-connection"></a>Konfigurace připojení ServiceNow

1. Pro definici zabezpečeného exportu použijte linku https://(název instance). Service-Now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor identifikátor URI.

2. Postupujte podle pokynů podle verze:
   * [Paříž](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlandu](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
