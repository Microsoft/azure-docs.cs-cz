---
title: Zásady služby Azure API management ukázkový – přidání funkcí do back-end službu | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak přidat možnosti do back-end službu. Příkladem je přijmutí názvu místa namísto zeměpisné šířky a délky v rozhraní API předpovědi počasí.
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
ms.openlocfilehash: 7c9edbf4b2d231453cd336521a04ba6b7714b696
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52873819"
---
# <a name="add-capabilities-to-a-backend-service"></a>Přidání možností do back-end službu

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak přidat možnosti do back-end službu. Příkladem je přijmutí názvu místa namísto zeměpisné šířky a délky v rozhraní API předpovědi počasí. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Call out to an HTTP endpoint and cache the response.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

