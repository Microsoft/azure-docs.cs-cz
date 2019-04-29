---
title: Rozhraní API správy zásad ukázkový Azure – odeslat informace o kontextu požadavku do back-end službu | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – ukazuje, jak odesílat informace o kontextu požadavku do back-end službu.
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
ms.openlocfilehash: 3369f3b3349e8daf9ff540b824c10bbd618a1147
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60859949"
---
# <a name="send-request-context-information-to-the-backend-service"></a>Odeslat informace o kontextu požadavku službě back-endu

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak odesílat informace o kontextu požadavku do back-end službu. Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Send request context information to the backend service.policy.xml)]

## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

