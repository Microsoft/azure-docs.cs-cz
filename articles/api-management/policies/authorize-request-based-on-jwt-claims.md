---
title: Ukázkové zásady API managementu – autorizace přístupu na základě deklarací JWT
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak autorizovat přístup ke konkrétním metodám HTTP na rozhraní API na základě deklarací JWT.
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
ms.openlocfilehash: b5b0c2feb31eab5e39bbebd7108b3ec8504769d4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92076279"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizace přístupu na základě deklarací JWT

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak autorizovat přístup ke konkrétním metodám HTTP na rozhraní API na základě deklarací JWT. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)