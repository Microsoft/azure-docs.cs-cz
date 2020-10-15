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
ms.openlocfilehash: df72ae5e9a1471e1387539d2c89a1eca0b09d866
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92078608"
---
# <a name="add-a-forwarded-header"></a>Přidat předanou hlavičku

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak přidat předanou hlavičku ve vstupní žádosti, aby back-end rozhraní API umožnilo vytváření správných adres URL. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="code"></a>Kód

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)