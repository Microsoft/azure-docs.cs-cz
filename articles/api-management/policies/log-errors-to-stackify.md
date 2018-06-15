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
ms.openlocfilehash: e47a3736e814229e881a314c7cb05054369db1f0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33935241"
---
# <a name="send-errors-to-stackify-for-logging"></a>Odeslání chyb při Stackify pro protokolování

Tento článek ukazuje ukázkové zásady správy rozhraní API služby Azure, který ukazuje, jak přidat zásadu protokolování chyba k odesílání chyb do Stackify pro protokolování. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **při chybě** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Log errors to Stackify.policy.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

