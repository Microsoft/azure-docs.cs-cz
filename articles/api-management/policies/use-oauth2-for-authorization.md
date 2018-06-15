---
title: Azure zásady správy rozhraní API-ukázka - OAuth2 použití k ověřování mezi bránou rozhraní a back-end | Microsoft Docs
description: Azure API management zásad ukázka - demonstruje použití OAuth2 pro ověřování mezi bránou rozhraní a back-end. Ukazuje, jak získat přístupový token z AAD a předá je na back-end.
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
ms.openlocfilehash: 240f78bc66af681d0089c45229ab142adf515e60
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
ms.locfileid: "33933303"
---
# <a name="use-oauth2-for-authorization-between-the-gateway-and-a-backend"></a>OAuth2 pro autorizaci použít mezi bránou rozhraní a back-end

Tento článek ukazuje rozhraní API služby Azure správy zásad vzorku, který ukazuje, jak používat OAuth2 pro ověřování mezi bránou rozhraní a back-end. Ukazuje, jak získat přístupový token z AAD a předá je na back-end. 

Chcete-li nastavit nebo upravit kód zásad, postupujte podle kroků popsaných v [sadu nebo upravit zásadu](../set-edit-policies.md). Další příklady najdete v sekci [ukázky zásad](../policy-samples.md).

Následující skript používá vlastnosti, které se zobrazují v {{vlastnost}}. Další informace o vlastnosti a jejich použití v zásadách API Management najdete v tématu [to](../api-management-howto-properties.md) tématu.
 
## <a name="policy"></a>Zásada

Vložte kód do **příchozí** bloku.

[!code-xml[Main](../../../api-management-policy-samples/Snippets/Get OAuth2 access token from AAD and forward it to the backend.policy.xml)]
  
## <a name="next-steps"></a>Další postup

Další informace o zásadách APIM:

+ [Zásady transformace](../api-management-transformation-policies.md)
+ [Ukázky zásad](../policy-samples.md)

