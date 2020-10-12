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
ms.openlocfilehash: 6662761df005211729dffb16282b8e0a8e2a8444
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442441"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb do Stackify pro protokolování

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat zásady protokolování chyb pro odeslání chyb do Stackify pro protokolování. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do bloku **On-Error** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

