---
title: Zásady služby Azure API management ukázkový – autorizace žádosti pomocí externí authorizer | Dokumentace Microsoftu
description: Azure API management zásady Ukázka - předvádí, jak povolit požadavky pomocí externí authorizer zapouzdření logiky vlastními nebo staršími ověřování/autorizace.
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
ms.date: 06/06/2018
ms.author: apimpm
ms.openlocfilehash: 65ea8622187d0665e4680f4162ddff0bc01e6eb9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869386"
---
# <a name="authorize-requests-using-external-authorizer"></a>Autorizaci požadavků pomocí externí authorizer

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak zabezpečit přístup k rozhraní API pomocí externí authorizer zapouzdření logiky ověřování/autorizace. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Authorize requests using external authorizer.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady omezení přístupu](../api-management-access-restriction-policies.md)
+ [Ukázky zásad](../policy-samples.md)
