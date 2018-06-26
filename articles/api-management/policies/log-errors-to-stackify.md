---
title: Azure zásady správy rozhraní API-ukázka – chyby odesílání do Stackify pro protokolování | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak přidat zásadu protokolování chyba k odesílání chyb do Stackify pro protokolování...
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
ms.openlocfilehash: 5daf21cb55289c874d56910b1240e1433f3d55d0
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36945951"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb při Stackify pro protokolování

Tento článek ukazuje ukázkové zásady správy rozhraní API služby Azure, který ukazuje, jak přidat zásadu protokolování chyba k odesílání chyb do Stackify pro protokolování. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **při chybě** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

