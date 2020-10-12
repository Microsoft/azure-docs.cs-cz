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
ms.openlocfilehash: 8cee2c13386a076f0321619754468cfc1e9fb31c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75442432"
---
# <a name="route-the-request-based-on-the-size-of-its-body"></a>Směrování požadavku na základě velikosti jeho obsahu

Tento článek ukazuje ukázku zásad služby Azure API Management, která ukazuje, jak směrovat požadavky na základě velikosti jejich orgánů. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Route requests based on size.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

