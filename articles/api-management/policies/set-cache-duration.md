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
ms.openlocfilehash: 3101c5695272e8fa6b577ad313897cbc1fa29629
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442395"
---
# <a name="set-response-cache-duration"></a>Nastavení doby uložení odpovědi v mezipaměti

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak nastavit dobu trvání mezipaměti odpovědí pomocí hodnoty maxAge v Cache-Control hlavičce odesílané back-endu. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Set cache duration using response cache control header.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

