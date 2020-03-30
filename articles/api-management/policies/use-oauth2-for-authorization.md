---
title: Ukázkové zásady správy rozhraní Azure API – použití OAuth2 pro autorizaci mezi bránou a back-endem
titleSuffix: Azure API Management
description: Ukázka zásad správy rozhraní Azure API – ukazuje, jak používat OAuth2 pro autorizaci mezi bránou a back-endem. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu.
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
ms.openlocfilehash: 09d51759c07e7dacc25d5b5ffce9698831c37a7d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75442351"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>Použití OAuth2 pro autorizaci mezi bránou a back-endem

Tento článek ukazuje ukázku zásad správy rozhraní Azure, která ukazuje, jak používat OAuth2 pro autorizaci mezi bránou a back-endem. Ukazuje, jak získat token přístupu z AAD a předat ho do back-endu. 

Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v části [Nastavení nebo úpravu zásady](../set-edit-policies.md). Další příklady naleznete v [tématu ukázky zásad](../policy-samples.md).

Následující skript používá vlastnosti, které se zobrazují v {{property}}. Další informace o vlastnostech a jejich použití v zásadách správy rozhraní API najdete v [tomto](../api-management-howto-properties.md) tématu.
 
## <a name="policy"></a>Zásada

Vložte kód do **příchozího** bloku.

[!code-xml[Main](../../../api-management-policy-samples/examples/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Další kroky

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

