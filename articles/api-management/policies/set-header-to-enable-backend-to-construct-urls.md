---
title: Ukázka zásad správy rozhraní Azure API – přidání předané hlavičky | Dokumenty společnosti Microsoft
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak přidat předané záhlaví v příchozí masy, aby rozhraní API back-endu bylo možné vytvořit správné adresy URL.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "70067504"
---
# <a name="add-a-forwarded-header"></a>Přidání předaného záhlaví

Tento článek ukazuje ukázku zásad správy rozhraní Azure API, která ukazuje, jak přidat předané záhlaví v příchozím požadavku povolit rozhraní API back-endu k vytvoření správné adresy URL. Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

## <a name="code"></a>kód

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Forward gateway hostname to backend for generating correct urls in responses.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)
