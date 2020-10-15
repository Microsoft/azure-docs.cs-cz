---
title: Ukázková zásada služby Azure API Management – použití OAuth2 k autorizaci mezi bránou a back-endu
titleSuffix: Azure API Management
description: Ukázka zásad Azure API Management – ukazuje, jak používat OAuth2 k autorizaci mezi bránou a back-endu. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu.
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
ms.openlocfilehash: 0f5a10eb2ebc3b3a7c527dd718e37faf03c927bc
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92076092"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Použití OAuth2 k autorizaci mezi bránou a back-endu

Tento článek ukazuje ukázku zásad služby Azure API Management, která demonstruje použití OAuth2 k autorizaci mezi bránou a back-endu. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu. 

Pokud chcete nastavit nebo upravit kód zásady, postupujte podle kroků popsaných v tématu [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-reference.md).

Následující skript používá vlastnosti, které se zobrazují v {{Property}}. Další informace o vlastnostech a jejich použití v zásadách API Management najdete v [tomto](../api-management-howto-properties.md) tématu.
 
## <a name="policy"></a>Zásady

Vložte kód do **vstupního** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-reference.md)