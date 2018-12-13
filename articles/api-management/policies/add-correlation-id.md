---
title: Zásady služby Azure API management ukázkový – přidání záhlaví obsahující identifikátor korelace | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak přidat záhlaví obsahující id korelace a příchozího požadavku.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/13/2017
ms.author: apimpm
ms.openlocfilehash: 126701674b6dc529404f5a7854cda9b31c336170
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52868425"
---
# <a name="add-a-header-containing-a-correlation-id"></a>Přidat záhlaví obsahující id korelace

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak přidat záhlaví obsahující id korelace a příchozího požadavku. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Add correlation id to inbound request.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

