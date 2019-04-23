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
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60306762"
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
