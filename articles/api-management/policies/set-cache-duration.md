---
title: Vzorové zásady správy rozhraní API – nastavení doby trvání mezipaměti odpovědí
titleSuffix: Azure API Management
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak nastavit dobu trvání mezipaměti odpovědí pomocí hodnoty maxAge v hlavičce Cache-Control odeslané back-endem.
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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442395"
---
# <a name="set-response-cache-duration"></a>Nastavení doby uložení odpovědi v mezipaměti

Tento článek ukazuje ukázku zásad správy rozhraní Azure API, která ukazuje, jak nastavit dobu trvání mezipaměti odpovědí pomocí hodnoty maxAge v záhlaví Cache-Control odeslaném back-endem. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

