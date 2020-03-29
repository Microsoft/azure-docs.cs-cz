---
title: Vzorové zásady správy rozhraní API – autorizace přístupu na základě deklarací JWT
titleSuffix: Azure API Management
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak autorizovat přístup ke konkrétním metodám HTTP v rozhraní API založeném na deklaracích JWT.
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
ms.openlocfilehash: 1b84854688fbdcc017b16698dfebfe54a7210110
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75422187"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizace přístupu na základě deklarací JWT

Tento článek ukazuje ukázku zásad správy rozhraní Azure API, která ukazuje, jak autorizovat přístup ke konkrétním metodám HTTP v rozhraní API založeném na deklaracích JWT. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

