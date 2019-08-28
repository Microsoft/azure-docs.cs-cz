---
title: Ukázka zásad Azure API Management – žádost o autorizaci pomocí externího autorizačního správce | Microsoft Docs
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
ms.openlocfilehash: 92836e0bfe43a41ad6547c68bc0b9a326528862c
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70074167"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizovat žádosti pomocí externího autorizačního

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak zabezpečit přístup přes rozhraní API pomocí externího autorizačního objektu, který zapouzdřuje vlastní ověřování a logiku autorizace. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady omezení přístupu](../api-management-access-restriction-policies.md)
+ [Ukázky zásad](../policy-samples.md)
