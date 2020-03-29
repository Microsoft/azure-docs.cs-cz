---
title: Vzorové zásady správy rozhraní API – odeslání informací o kontextu požadavku do back-endové služby
titleSuffix: Azure API Management
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak odesílat informace o kontextu požadavku do back-endové služby.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 8cef989ca7ce8ee649c4f4aeb579c43bf8a8d6ab
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442404"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Odeslání informací o kontextu požadavku back-endové službě

Tento článek ukazuje ukázku zásad správy rozhraní Azure, která ukazuje, jak odesílat informace o kontextu požadavku back-endové službě. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

