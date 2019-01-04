---
title: Nasazení a volání webového rozhraní API a rozhraní REST API z Azure Logic Apps | Dokumentace Microsoftu
description: Nasazení a zavolání webového rozhraní API a rozhraní REST API pro systém integratio pracovních postupů v Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, stepsic, LADocs
ms.topic: article
ms.assetid: f113005d-0ba6-496b-8230-c1eadbd6dbb9
ms.date: 05/26/2017
ms.openlocfilehash: e95b20a12fafa9d1dbcbd641ce1c9f2674314489
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53606431"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Nasazení a volání vlastních rozhraní API z pracovních postupů v Azure Logic Apps

Poté co [vytvoření vlastních rozhraní API](./logic-apps-create-api-app.md) pro použití v pracovních postupů aplikace logiky, musíte nasadit svoje rozhraní API, než bude možné volat. Můžete nasadit jako rozhraní API [webové aplikace](../app-service/overview.md), ale zvažte nasazení vašich rozhraní API jako [API apps](../app-service/app-service-web-tutorial-rest-api.md), které usnadní práci při vytváření, hostování a používání rozhraní API v cloudu i v místním prostředí. Nemusíte měnit žádný kód v rozhraní API – stačí nasazení kódu do aplikace API. Rozhraní API můžete hostovat na [služby Azure App Service](../app-service/overview.md), platform-as-a-service (PaaS) nabídka, která poskytuje vysoce škálovatelnou a jednoduché rozhraní API hostování.

I když bude možné volat jakékoli rozhraní API z aplikace logiky, pro dosažení co nejlepších výsledků, přidejte [OpenAPI (dříve Swagger) metadat](http://swagger.io/specification/) , který popisuje operace a parametry vašeho rozhraní API. Tento soubor OpenAPI pomáhá vaše rozhraní API snadno integrovat a lépe fungují s logic apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Nasadit své rozhraní API jako webovou aplikaci nebo aplikaci API

Předtím, než bude možné volat vlastní API z aplikace logiky, nasaďte své rozhraní API jako webovou aplikaci nebo aplikaci API do služby Azure App Service. Také, aby OpenAPI soubor přečíst návrháře pro Logic Apps, nastavte vlastnosti definice rozhraní API a zapnout [prostředků mezi zdroji (CORS) pro sdílení obsahu](../app-service/overview.md) pro webovou aplikaci nebo aplikaci API.

1. V [webu Azure portal](https://portal.azure.com), vyberte webovou aplikaci nebo aplikaci API.

2. V nabídce aplikace, které se otevře v části **API**, zvolte **definice rozhraní API**. Nastavte **umístění definice rozhraní API** na adresu URL pro váš soubor swagger.json OpenAPI.

   Adresa URL obvykle, zobrazí se v tomto formátu: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Odkaz na soubor OpenAPI pro vlastní rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. V části **API**, zvolte **CORS**. Nastavení zásad CORS **povolené zdroje** k **' *'** (povolit všechny).

   Toto nastavení povoluje požadavky z návrháře aplikace logiky.

   ![Umožňovala žádostí z návrháře aplikace logiky do vlastního rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Další informace najdete v tématu [hostovat rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Volání vlastních rozhraní API od logiky pracovní postupy aplikace

Po nastavení vlastnosti definice rozhraní API a CORS by měl být dostupný pro zahrnout do pracovního postupu aplikace logiky aktivační události a akce vašeho vlastního rozhraní API. 

*  Chcete-li zobrazit weby, které mají adresy URL OpenAPI, můžete procházet vaše předplatné weby v návrháři pro Logic Apps.

*  Chcete-li zobrazit dostupné akce a vstupy najetím myší na dokument OpenAPI, použijte [HTTP + Swagger akce](../connectors/connectors-native-http-swagger.md).

*  K volání jakékoli rozhraní API, včetně rozhraní API, která nemáte nebo zpřístupnit dokument OpenAPI můžete vždy vytvořit požadavek s [akce HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Další postup

* [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md)