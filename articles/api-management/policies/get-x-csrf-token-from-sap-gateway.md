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
ms.openlocfilehash: 2e9f51cc2afadf70c02a23a85a4c2a866f3fd149
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36285466"
---
# <a name="implement-x-csrf-pattern"></a>Implementace X proti útokům CSRF – vzor

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak implementovat X proti útokům CSRF vzor používá mnoho rozhraní API. Tento příklad je konkrétně pro bránu SAP. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

