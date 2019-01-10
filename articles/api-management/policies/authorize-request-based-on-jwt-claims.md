---
title: Zásady služby Azure API management ukázkový – autorizaci přístupu na základě deklarací identity JWT | Dokumentace Microsoftu
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
ms.openlocfilehash: d656cf7c7bed1d40bbde654f9c2484efcc5df25d
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157917"
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

