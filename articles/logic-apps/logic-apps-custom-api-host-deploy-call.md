---
title: Nasazení a volání webových api & REST API z Aplikací logiky Azure
description: Nasazení a volání webových api & REST API pro pracovní postupy systémové integrace v aplikacích Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 05/26/2017
ms.openlocfilehash: d1305be54a22b1460000a357074cbb1f67123bd6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74790752"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Nasazení a volání vlastních api z pracovních postupů v Azure Logic Apps

Po [vytvoření vlastní chod api](./logic-apps-create-api-app.md) pro použití v pracovních postupech aplikace logiky, je nutné nasadit vaše api před jejich voláním. Rozhraní API můžete nasadit jako [webové aplikace](../app-service/overview.md), ale zvažte nasazení rozhraní API jako [aplikací rozhraní API](../app-service/app-service-web-tutorial-rest-api.md), které usnadňují práci při vytváření, hostování a využívání rozhraní API v cloudu a místně. Nemusíte měnit žádný kód ve vašich rozhraních API – stačí nasadit kód do aplikace rozhraní API. Rozhraní API můžete hostovat ve [službě Azure App Service](../app-service/overview.md), což je nabídka paas (platforma jako služba), která poskytuje vysoce škálovatelný a snadný hosting rozhraní API.

I když můžete volat libovolné rozhraní API z aplikace logiky, pro nejlepší prostředí, pro nejlepší prostředí, přidejte [OpenAPI (dříve Swagger) metadata,](https://swagger.io/specification/) která popisuje operace a parametry vašeho rozhraní API. Tento soubor OpenAPI pomáhá vašemu rozhraní API snadněji se integrovat a lépe pracovat s aplikacemi logiky.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Nasazení rozhraní API jako webové aplikace nebo aplikace ROZHRANÍ API

Než budete moci volat vlastní rozhraní API z aplikace logiky, nasaďte rozhraní API jako webovou aplikaci nebo aplikaci rozhraní API do služby Azure App Service. Chcete-li, aby byl soubor OpenAPI čitelný pomocí Návrháře logických aplikací, nastavte vlastnosti definice rozhraní API a zapněte [sdílení prostředků mezi zdroji (CORS)](../app-service/overview.md) pro vaši webovou aplikaci nebo aplikaci rozhraní API.

1. Na [webu Azure Portal](https://portal.azure.com)vyberte webovou aplikaci nebo aplikaci API.

2. V nabídce aplikace, která se otevře v části **ROZHRANÍ API**, zvolte **definici rozhraní API**. Nastavte **umístění definice rozhraní API** na adresu URL souboru OpenAPI swagger.json.

   Adresa URL se obvykle zobrazí v tomto formátu:`https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Odkaz na soubor OpenAPI pro vlastní rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. V části **API**zvolte **CORS**. Nastavte zásady CORS pro **povolené počátky** na **'*'** (povolit vše).

   Toto nastavení povoluje požadavky z Návrháře aplikací logiky.

   ![Povolit požadavky z Návrháře aplikace logiky do vlastního rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Další informace najdete [v tématu Hostování rozhraní API RESTful s CORS ve službě Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Volání vlastního rozhraní API z pracovních postupů aplikace logiky

Po nastavení vlastností definice rozhraní API a CORS, aktivační události a akce vlastního rozhraní API by měly být k dispozici pro vás zahrnout do pracovního postupu aplikace logiky. 

*  Chcete-li zobrazit weby, které mají adresy URL OpenAPI, můžete procházet webové stránky předplatného v Návrháři aplikací logiky.

*  Chcete-li zobrazit dostupné akce a vstupy zobrazením na dokument OpenAPI, použijte [akci HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Chcete-li volat libovolné rozhraní API, včetně rozhraní API, která nemají nebo vystavit dokument OpenAPI, můžete vždy vytvořit požadavek s [akcí HTTP](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Další kroky

* [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md)