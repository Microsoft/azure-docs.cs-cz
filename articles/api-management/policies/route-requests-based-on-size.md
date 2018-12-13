---
title: Rozhraní API správy zásad ukázkový Azure – směrování žádosti na základě velikosti jeho textu | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak směrovat žádosti na základě velikosti jejich těla.
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
ms.openlocfilehash: 7656401115cc370d6eee60fb9bddb9bcd92e4201
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52872200"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Směrování na základě velikosti jeho textu požadavku

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak směrovat žádosti na základě velikosti jejich těla. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

