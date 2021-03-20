---
title: Ukázkové zásady API managementu – odeslání chyb do Stackify pro protokolování
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak přidat zásady protokolování chyb pro odeslání chyb do Stackify pro protokolování.
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
ms.openlocfilehash: 89428e9a64c6d4ae78d333c0cf597531588b1638
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92072063"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb do Stackify pro protokolování

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat zásady protokolování chyb pro odeslání chyb do Stackify pro protokolování. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do bloku **On-Error** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)