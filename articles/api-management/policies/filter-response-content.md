---
title: Ukázka zásad správy rozhraní Azure API – obsah odpovědí filtru | Dokumenty společnosti Microsoft
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak filtrovat datové prvky z datové části odpovědí na základě produktu přidruženého k požadavku.
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
ms.openlocfilehash: 462db76b06e5071571ae475d420a627e57dad92e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067751"
---
# <a name="filter-response-content"></a>Filtrování obsahu odpovědi

Tento článek ukazuje ukázku zásad správy rozhraní Azure API, která ukazuje, jak filtrovat datové prvky z datové části odpovědi na základě produktu přidruženého k požadavku. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **odchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

