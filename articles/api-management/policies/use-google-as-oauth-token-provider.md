---
title: Zásady služby Azure API management ukázkový – autorizace přístupu pomocí tokenu služby Google OAuth | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak k autorizaci přístupu pro vaše koncové body pomocí Google jako zprostředkovatel tokenu OAuth.
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
ms.openlocfilehash: 430f9e57df163ad345f0740e5bd5beca6e892a4c
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869294"
---
# <a name="authorize-access-using-google-oauth-token"></a>Povolit přístup pomocí tokenu služby Google OAuth

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak k autorizaci přístupu pro vaše koncové body pomocí Google jako zprostředkovatel tokenu OAuth. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

