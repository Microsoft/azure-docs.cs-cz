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
ms.openlocfilehash: 46b6d391d6a1ee569dc27c31a0718b23a120c632
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36286714"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb při Stackify pro protokolování

Tento článek ukazuje ukázkové zásady správy rozhraní API služby Azure, který ukazuje, jak přidat zásadu protokolování chyba k odesílání chyb do Stackify pro protokolování. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **při chybě** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

