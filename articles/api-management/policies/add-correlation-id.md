---
title: Ukázkové zásady API managementu – přidání hlavičky obsahující ID korelace
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak přidat záhlaví obsahující ID korelace do příchozího požadavku.
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
ms.openlocfilehash: 79910aa60602a80cbe79c4ce9899f6ff73fbfde9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75422253"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Přidání hlavičky obsahující ID korelace

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat hlavičku obsahující ID korelace do příchozího požadavku. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

