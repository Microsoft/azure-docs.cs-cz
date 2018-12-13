---
title: Rozhraní API správy zásad ukázkový Azure – použití OAuth2 k ověřování mezi bránou a back-endu. | Dokumentace Microsoftu
description: Azure API management zásady ukázkový – popisuje způsob použití OAuth2 k ověřování mezi bránou a back-endu. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu.
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
ms.openlocfilehash: 519233cb9e77bf48f67d869a54af771c17c7827e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/04/2018
ms.locfileid: "52874549"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Použití OAuth2 k ověřování mezi bránou a back-endu.

Tento článek ukazuje ukázkové zásady správu rozhraní API služby Azure, který ukazuje, jak používat OAuth2 k ověřování mezi bránou a back-endu. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu. 

Nastavení nebo úprava zásad kódu, postupujte podle kroků popsaných v [nastavení nebo úprava zásad](../set-edit-policies.md). Další příklady najdete v tématu [ukázky zásad](../policy-samples.md).

Následující skript pomocí vlastností, které se zobrazují v {{vlastnost}}. Další informace o vlastnosti a způsobu jejich použití v zásady služby API Management najdete v tématu [to](../api-management-howto-properties.md) tématu.
 
## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Další postup

Další informace týkající se služby APIM zásad:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

