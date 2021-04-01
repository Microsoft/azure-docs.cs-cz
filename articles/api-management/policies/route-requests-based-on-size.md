---
title: Vzorová zásada API Management – požadavek na směrování na základě velikosti textu zprávy
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak směrovat požadavky na základě velikosti jejich orgánů.
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
ms.openlocfilehash: e59aece0be39bf671e3d1e62f1a0e4c2a42a1ec8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076466"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Směrování požadavku na základě velikosti jeho obsahu

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak směrovat požadavky na základě velikosti jejich orgánů. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)