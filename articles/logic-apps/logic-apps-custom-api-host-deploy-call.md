---
title: Nasazení a volání webového rozhraní API a rozhraní REST API z Azure Logic Apps | Microsoft Docs
description: Nasazení a volání webového rozhraní API a rozhraní REST API pro systém integrace pracovních postupů v Azure Logic Apps
keywords: webové rozhraní API, rozhraní REST API, konektorů, pracovní postupy, integrace v rámci systému, ověření
services: logic-apps
author: stepsic-microsoft-com
manager: anneta
editor: ''
documentationcenter: ''
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/26/2017
ms.author: LADocs; stepsic
ms.openlocfilehash: c7a240bf5b7ed5e7780b90f438d2e336ee79f0b3
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
# <a name="deploy-and-call-custom-apis-from-logic-app-workflows"></a>Nasazení a volání vlastním rozhraním API z logiky pracovní postupy aplikace

Po jste [vytvořte vlastní rozhraní API](./logic-apps-create-api-app.md) pro použití v pracovních postupech aplikace logiky, musíte nasadit vaše rozhraní API, než bude možné volat. Můžete nasadit vaše rozhraní API jako [webové aplikace](../app-service/app-service-web-overview.md), ale zvažte nasazení vašich rozhraní API jako [aplikace API](../app-service/app-service-web-tutorial-rest-api.md), které usnadňují vaši práci při sestavení, hostovat a používat rozhraní API v cloudu a místně. Nemáte změnit spuštěním kódu vaše rozhraní API-kódu do aplikace API jen nasadit. Vaše rozhraní API můžete hostovat na [Azure App Service](../app-service/app-service-web-overview.md), platformy jako služba (PaaS) nabídka, která nabízí vysoce škálovatelnou a snadno hostování rozhraní API.

I když můžete volat jakéhokoli rozhraní API z aplikace logiky, pro dosažení co nejlepších výsledků, přidejte [OpenAPI (dříve Swagger) metadata](http://swagger.io/specification/) operace vaše rozhraní API a parametry, který popisuje. Tento soubor OpenAPI pomáhá rozhraní API snadněji integrovat a lépe pracovat s logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Nasadit své rozhraní API jako webové aplikace nebo aplikace API

Než bude možné volat vlastního rozhraní API z aplikace logiky, nasaďte své rozhraní API jako webové aplikace nebo aplikace API Azure App Service. Také, aby vaše OpenAPI souboru přečíst návrháře aplikace logiky, nastavte vlastnosti definice rozhraní API a zapněte [(CORS) pro sdílení prostředků různého původu](../app-service/app-service-web-overview.md) pro webovou aplikaci nebo aplikaci API.

1. V [portál Azure](https://portal.azure.com), vyberte webovou aplikaci nebo aplikaci API.

2. V nabídce aplikace, které se otevře v části **rozhraní API**, zvolte **definice rozhraní API**. Nastavte **umístění definice rozhraní API** na adresu URL pro váš soubor swagger.json OpenAPI.

   Adresa URL obvykle, zobrazí se v tomto formátu: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Odkaz na soubor OpenAPI pro vaše vlastní rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. V části **rozhraní API**, zvolte **CORS**. Nastavení zásad CORS pro **povolené zdroje** k **' *'** (povolit všechny).

   Toto nastavení umožňuje požadavky z návrháře aplikace logiky.

   ![Povolení požadavků z návrháře aplikace logiky do vlastního rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Další informace najdete v tématu [hostování rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Volání vlastního rozhraní API z logiku aplikace pracovních postupů

Po nastavení vlastnosti definice rozhraní API a CORS by měla být k dispozici můžete zahrnout do pracovního postupu aplikace logiky triggery a akce vlastního rozhraní API. 

*  Chcete-li zobrazit weby, které mají OpenAPI adresy URL, můžete procházet své předplatné weby v Návrháři logiku aplikace.

*  Chcete-li zobrazit dostupné akce a vstupy tak, že odkazuje na dokument OpenAPI, použijte [HTTP + Swagger akce](../connectors/connectors-native-http-swagger.md).

*  K volání jakéhokoli rozhraní API, včetně rozhraní API, která nesmí mít ani vystavit dokument OpenAPI můžete kdykoli vytvořit žádost o se [akce HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Další postup

* [Vlastní konektor – přehled](../logic-apps/custom-connector-overview.md)