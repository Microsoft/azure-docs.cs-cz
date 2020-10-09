---
title: Ukázka zásad Azure API managementu – přidejte předanou hlavičku | Microsoft Docs
description: Ukázka zásad Azure API Management – ukazuje, jak přidat předanou hlavičku v příchozím požadavku, aby back-end rozhraní API umožnilo vytváření správných adres URL.
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
ms.openlocfilehash: e525029ae8eab086d11126a4e18958423e207aa1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "70067504"
---
# <a name="add-a-forwarded-header"></a>Přidat předanou hlavičku

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat předanou hlavičku ve vstupní žádosti, aby back-end rozhraní API umožnilo vytváření správných adres URL. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="code"></a>Kód

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)
