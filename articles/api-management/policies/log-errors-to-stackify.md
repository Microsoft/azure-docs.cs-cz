---
title: Rozhraní API správy zásad ukázkový Azure – odesílání chyb do Stackify pro protokolování | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak přidat zásadu protokolování chyba odeslat chyby Stackify pro protokolování...
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
ms.openlocfilehash: 07cc83830fe2d467c611622bb66dfbb8c9429c2d
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60860534"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb při Stackify pro protokolování

Tento článek ukazuje ukázkové zásady správy rozhraní API služby Azure, který ukazuje, jak přidat zásadu protokolování chyba odeslat chyby Stackify pro protokolování. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **při chybě** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

