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
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70067751"
---
# <a name="filter-response-content"></a>Filtrovat obsah odpovědi

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak filtrovat datové prvky z datové části odpovědi na základě produktu přidruženého k žádosti. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do výstupního bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

