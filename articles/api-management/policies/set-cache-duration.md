---
title: Ukázková zásada API Management-nastavit dobu trvání mezipaměti odpovědí
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak nastavit dobu trvání mezipaměti odpovědí pomocí hodnoty maxAge v Cache-Control hlavičce odesílané back-endu.
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
ms.openlocfilehash: 088dfbcbfe00357c47de4662ac31b52e83d1d8ab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92070975"
---
# <a name="set-response-cache-duration"></a>Nastavení doby uložení odpovědi v mezipaměti

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak nastavit dobu trvání mezipaměti odpovědí pomocí hodnoty maxAge v Cache-Control hlavičce odesílané back-endu. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)