---
title: Ukázka zásad Azure API managementu – obsah odpovědi filtru | Microsoft Docs
description: Ukázka zásad Azure API Management – ukazuje, jak filtrovat datové prvky z datové části odpovědi na základě produktu přidruženého k žádosti.
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "70067751"
---
# <a name="filter-response-content"></a>Filtrování obsahu odpovědi

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak filtrovat datové prvky z datové části odpovědi na základě produktu přidruženého k žádosti. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **výstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

