---
title: Zásady služby Azure API management ukázkový – přidání hlavičky předané | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak přidat hlavičku předané v příchozího požadavku, aby back-endového rozhraní API k vytvoření správné adresy URL.
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
ms.openlocfilehash: b857d1780e9734ce891ce2c0ce4bedf50dfe13e9
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52871451"
---
# <a name="add-a-forwarded-header"></a>Přidat hlavičku předané

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak přidat hlavičku předané v příchozího požadavku, aby back-endového rozhraní API k vytvoření správné adresy URL. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="code"></a>Kód

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)
