---
title: Azure zásady správy rozhraní API-ukázka - obsah odpovědi filtr | Microsoft Docs
description: Azure API management zásad ukázka - ukazuje, jak filtrovat elementy data z datové části odpovědi na základě produktu přidružený k požadavku.
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
ms.openlocfilehash: f6475b272239e9352211a80985e1b46da9c6f8e0
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="filter-response-content"></a>Filtrování obsahu odpovědi

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak filtrovat elementy data z datové části odpovědi na základě produktu přidružený k požadavku. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **odchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Filter response content based on product name.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

