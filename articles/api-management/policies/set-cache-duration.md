---
title: Azure API management zásady ukázkový – nastavte dobu odezvy mezipaměti | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak nastavit dobu odezvy mezipaměti pomocí maxAge hodnota v hlavičce Cache-Control odesílaných back-endu...
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
ms.openlocfilehash: 042fab72da2d4b890314b6ee9c7237241b492fba
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52869217"
---
# <a name="set-response-cache-duration"></a>Nastavení mezipaměti Doba trvání odpovědi

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak nastavit dobu odezvy mezipaměti pomocí maxAge hodnota v hlavičce Cache-Control odesílaných back-endu. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

