---
title: Ukázkové zásady API managementu – žádost o autorizaci pomocí externího autorizačního nástroje
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak autorizovat žádosti pomocí externího autorizačního nástroje, který zapouzdřuje vlastní nebo starší logiku ověřování/autorizace.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: e38d92a13c9a66defc2d5090990b44a889cfd21c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076228"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizace požadavků na používání externího objektu Authorizer

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak zabezpečit přístup přes rozhraní API pomocí externího autorizačního objektu, který zapouzdřuje vlastní ověřování a logiku autorizace. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady omezení přístupu](../api-management-access-restriction-policies.md)
+ [Ukázky zásad](../policy-reference.md)