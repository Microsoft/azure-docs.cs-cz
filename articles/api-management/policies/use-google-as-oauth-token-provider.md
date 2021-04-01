---
title: Ukázkové zásady API managementu – autorizace přístupu pomocí tokenu Google OAuth
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak autorizovat přístup k koncovým bodům pomocí Google jako poskytovatele tokenu OAuth.
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
ms.openlocfilehash: 0f6c9fe2146414f78e90d6ade1a00045cdf3a04f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92078013"
---
# <a name="authorize-access-using-google-oauth-token"></a>Autorizace přístupu pomocí tokenu Google OAuth

Tento článek ukazuje ukázku zásad služby Azure API Management, která demonstruje, jak autorizovat přístup k koncovým bodům pomocí Google jako poskytovatele tokenu OAuth. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Simple Google OAuth validate-jwt.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)