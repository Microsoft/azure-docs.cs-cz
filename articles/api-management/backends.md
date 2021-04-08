---
title: Back-endy Azure API Management | Microsoft Docs
description: Další informace o vlastních back-endy v API Management
services: api-management
documentationcenter: ''
author: dlepow
editor: ''
ms.service: api-management
ms.topic: article
ms.date: 01/29/2021
ms.author: apimpm
ms.openlocfilehash: 54a46e999391507f5ec7d927f62b88fcd2169b75
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99500614"
---
# <a name="backends-in-api-management"></a>Back-endy v API Management

*Back* -end (nebo *back-end rozhraní API*) v API Management je služba HTTP, která implementuje vaše front-end rozhraní API a jeho operace.

Při importu určitých rozhraní API API Management nakonfiguruje back-end rozhraní API automaticky. API Management například nakonfiguruje back-end při importu [specifikace openapi](import-api-from-oas.md), [rozhraní SOAP API](import-soap-api.md)nebo prostředků Azure, jako je [Azure Function App](import-function-app-as-api.md) nebo [Aplikace logiky](import-logic-app-as-api.md)aktivované protokolem HTTP.

API Management taky podporuje používání dalších prostředků Azure, jako je [Service Fabric cluster](how-to-configure-service-fabric-backend.md) nebo vlastní služba jako back-endu rozhraní API. Použití těchto vlastních back-endy vyžaduje dodatečnou konfiguraci, například k autorizaci přihlašovacích údajů k back-endu služby a k definování operací rozhraní API. Tyto back-endy nakonfigurujete a spravujete v Azure Portal nebo pomocí rozhraní API nebo nástrojů Azure.

Po vytvoření back-endu můžete na adresu URL back-endu odkazovat v rozhraních API. Pomocí této [`set-backend-service`](api-management-transformation-policies.md#SetBackendService) zásady můžete přesměrovat příchozí požadavek rozhraní API na vlastní back-end místo na výchozí back-end pro toto rozhraní API.

## <a name="benefits-of-backends"></a>Výhody back-endu

Vlastní back-end má několik výhod, včetně:

* Vyabstrakce informace o back-end službě, což zvyšuje možnosti opětovné použitelnosti napříč rozhraními API a vylepšené řízení  
* Dá se snadno použít konfigurací zásady transformace pro existující rozhraní API.
* Využívá výhod API Management funkce pro zachování tajných kódů v Azure Key Vault Pokud jsou [pojmenované hodnoty](api-management-howto-properties.md) nakonfigurované pro záhlaví nebo ověřování parametrů dotazů.

## <a name="next-steps"></a>Další kroky

* Nastavte [Service Fabric back-end](how-to-configure-service-fabric-backend.md) pomocí Azure Portal.
* Back-endy lze také konfigurovat pomocí šablon API Management [REST API](/rest/api/apimanagement), [Azure PowerShell](/powershell/module/az.apimanagement/new-azapimanagementbackend)nebo [Azure Resource Manager](../service-fabric/service-fabric-tutorial-deploy-api-management.md).

