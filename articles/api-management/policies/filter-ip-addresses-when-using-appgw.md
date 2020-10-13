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
ms.openlocfilehash: 45e16c9aa9e4b04e7225320951e9f839fae75ba3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75942474"
---
# <a name="filter-on-request-ip-address-when-using-an-application-gateway"></a>Při použití Application Gateway filtrovat podle IP adresy žádosti

Tento článek ukazuje ukázku zásad služby Azure API Management, která předvádí, jak filtr na IP adrese žádosti v případě, kdy je API Management instance dostupná prostřednictvím Application Gateway nebo jiného zprostředkovatele. Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Filter on IP Address when using Application Gateway.policy.xml)]

## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady omezení přístupu](../api-management-access-restriction-policies.md)
+ [Ukázky zásad](../policy-samples.md)
