---
title: Ukázka zásad Azure API Management – odeslání chyb do Stackify pro protokolování | Microsoft Docs
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
ms.openlocfilehash: 82aab34a9815f080fe8abb8c1d8b6de66866806e
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067581"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb do Stackify pro protokolování

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat zásady protokolování chyb pro odeslání chyb do Stackify pro protokolování. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do bloku **On-Error** .

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

