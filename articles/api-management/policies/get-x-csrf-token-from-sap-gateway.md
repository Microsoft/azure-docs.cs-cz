---
title: Ukázka zásad Azure API Management-implementace X-CSRF vzoru | Microsoft Docs
description: Ukázka zásad Azure API Management – ukazuje, jak implementovat vzor X-CSRF, který používá mnoho rozhraní API. Tento příklad je konkrétně pro bránu SAP.
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
ms.openlocfilehash: bc6f60397c2d432dd6547a8be34280fcf3acd05d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "92078404"
---
# <a name="implement-x-csrf-pattern"></a>Implementovat vzor X-CSRF

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak implementovat vzor X-CSRF, který používá mnoho rozhraní API. Tento příklad je konkrétně pro bránu SAP. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)