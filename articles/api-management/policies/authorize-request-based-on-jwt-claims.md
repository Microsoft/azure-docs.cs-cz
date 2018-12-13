---
title: Zásady služby Azure API management ukázkový – autorizace přístupu Azure AD na základě deklarací identity JWT | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak autorizovat přístup k specifických metod HTTP pro rozhraní API na základě deklarací identity tokenů JWT.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 60b36ceeac1cd4578ca81ac908c1a8a03c9d0180
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869122"
---
# <a name="authorize-access-based-on-jwt-claims"></a>Autorizace přístupu na základě deklarací identity tokenů JWT

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak autorizovat přístup k specifických metod HTTP pro rozhraní API na základě deklarací identity tokenů JWT. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Pre-authorize requests based on HTTP method with validate-jwt.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

