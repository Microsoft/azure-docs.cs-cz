---
title: Azure zásady správy rozhraní API-Ukázka – implementace X-proti útokům CSRF vzor | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak implementovat X proti útokům CSRF vzor používá mnoho rozhraní API. Tento příklad je konkrétně pro bránu SAP.
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
ms.openlocfilehash: 3a2067836a1488d117dced96f3935f2d1f8b1b48
ms.sourcegitcommit: e34afd967d66aea62e34d912a040c4622a737acb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36946019"
---
# <a name="implement-x-csrf-pattern"></a>Implementace X proti útokům CSRF – vzor

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak implementovat X proti útokům CSRF vzor používá mnoho rozhraní API. Tento příklad je konkrétně pro bránu SAP. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

