---
title: Vzorové zásady správy rozhraní API – autorizace požadavku pomocí externího autorizátoru
titleSuffix: Azure API Management
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak autorizovat požadavky pomocí externího autorizátoru zapouzdření vlastní nebo starší logiky ověřování a autorizace.
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
ms.openlocfilehash: 99bf1068042eb7ab0c43e2a683ca7116d2e426f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442500"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizace požadavků na používání externího objektu Authorizer

Tento článek ukazuje ukázku zásad správy rozhraní Azure API, která ukazuje, jak zabezpečit přístup k rozhraní API pomocí externího autorizačního nástroje, který zapouzdřuje vlastní logiku ověřování a autorizace. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady omezení přístupu](../api-management-access-restriction-policies.md)
+ [Ukázky zásad](../policy-samples.md)
