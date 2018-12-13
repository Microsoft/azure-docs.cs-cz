---
title: Rozhraní API správy zásad ukázkový Azure – vzor implementovat X-CSRF | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak implementovat vzor X CSRF používané mnoha rozhraní API. Tento příklad je konkrétně pro bránu SAP.
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
ms.openlocfilehash: 2f4d26702443ef3113dad98cde1d13b292fe657a
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52870006"
---
# <a name="implement-x-csrf-pattern"></a>Implementace X-CSRF vzor

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak implementovat vzor X CSRF používané mnoha rozhraní API. Tento příklad je konkrétně pro bránu SAP. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get X-CSRF token from SAP gateway using send request.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

