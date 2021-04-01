---
title: Ukázkové zásady API managementu – při použití Application Gateway filtrovat IP adresu
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak filtrovat IP adresu žádosti při použití Application Gateway.
services: api-management
documentationcenter: ''
author: jftl6y
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/13/2020
ms.author: joscot
ms.custom: fasttrack-new
ms.openlocfilehash: 8249cc543c6334841c8e5152d5d1ceb84d4097dc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92076109"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Při použití Application Gateway filtrovat podle IP adresy žádosti

Tento článek ukazuje ukázku zásad služby Azure API Management, která předvádí, jak filtr na IP adrese žádosti v případě, kdy je API Management instance dostupná prostřednictvím Application Gateway nebo jiného zprostředkovatele. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady omezení přístupu](../api-management-access-restriction-policies.md)
+ [Ukázky zásad](../policy-reference.md)