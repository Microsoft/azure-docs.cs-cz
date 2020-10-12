---
title: Ukázkové zásady API managementu – přidání schopností do back-endové služby
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak přidat funkce do back-endové služby. Příkladem je přijmutí názvu místa namísto zeměpisné šířky a délky v rozhraní API předpovědi počasí.
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
ms.openlocfilehash: e286f77009a17870332ed5caf1c66c18dd21d020
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442474"
---
# <a name="add-capabilities-to-a-backend-service"></a>Přidání schopností do back-endové služby

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat funkce do back-endové služby. Příkladem je přijmutí názvu místa namísto zeměpisné šířky a délky v rozhraní API předpovědi počasí. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

