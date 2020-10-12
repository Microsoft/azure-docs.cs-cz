---
title: Volání vlastních webových rozhraní API & rozhraní REST API
description: Volání vlastních webových rozhraní API & rozhraní REST API z Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 05/13/2020
ms.openlocfilehash: 7b4d00e8c0366d10fddafa66db699c1a59fd9ad7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "83659786"
---
# <a name="deploy-and-call-custom-apis-from-workflows-in-azure-logic-apps"></a>Nasazení a volání vlastních rozhraní API z pracovních postupů v Azure Logic Apps

Po [Vytvoření vlastních rozhraní API](./logic-apps-create-api-app.md) pro použití v pracovních postupech aplikace logiky je nutné nasadit tato rozhraní API předtím, než je můžete volat. Rozhraní API můžete nasadit jako [webové aplikace](../app-service/overview.md), ale zvažte nasazení rozhraní API jako [aplikací API](../app-service/app-service-web-tutorial-rest-api.md), které usnadňuje práci při sestavování, hostování a využívání rozhraní API v cloudu i místně. Nemusíte měnit žádný kód v rozhraních API – stačí kód nasadit do aplikace API. Své rozhraní API můžete hostovat na [Azure App Service](../app-service/overview.md), jako je nabídka typu platforma jako služba (PaaS), která poskytuje vysoce škálovatelné a jednoduché hostování rozhraní API.

I když můžete volat libovolné rozhraní API z aplikace logiky, pro dosažení co nejlepších výsledků přidejte [metadata Swagger](https://swagger.io/specification/) , která popisují operace a parametry vašeho rozhraní API. Tento dokument Swagger usnadňuje integraci rozhraní API a lepší práci s Logic Apps.

## <a name="deploy-your-api-as-a-web-app-or-api-app"></a>Nasazení rozhraní API jako webové aplikace nebo aplikace API

Předtím, než budete moci volat vlastní rozhraní API z aplikace logiky, nasaďte rozhraní API jako webovou aplikaci nebo aplikaci API a Azure App Service. Aby dokument Swagger byl čitelný návrhářem Logic Apps, nastavte vlastnosti definice rozhraní API a zapněte [sdílení prostředků mezi zdroji (CORS)](../app-service/overview.md) pro vaši webovou aplikaci nebo aplikaci API.

1. V [Azure Portal](https://portal.azure.com)vyberte webovou aplikaci nebo aplikaci API.

2. V nabídce aplikace, která se otevře, vyberte v části **rozhraní API** **definice rozhraní API**. Nastavte **umístění definice rozhraní API** na adresu URL vašeho swagger.jsv souboru.

   Adresa URL se obvykle zobrazuje v tomto formátu: `https://{name}.azurewebsites.net/swagger/docs/v1)`

   ![Odkaz na dokument Swagger pro vaše vlastní rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-swagger-url.png)

3. V části **rozhraní API**vyberte **CORS**. Nastavte zásadu CORS pro **Povolené zdroje** na ***** (Povolit vše).

   Toto nastavení povoluje žádosti z návrháře aplikace logiky.

   ![Povolit žádosti z návrháře aplikace logiky na vaše vlastní rozhraní API](./media/logic-apps-custom-api-deploy-call/custom-api-cors.png)

Další informace najdete v tématu [hostování rozhraní RESTful API s CORS v Azure App Service](../app-service/app-service-web-tutorial-rest-api.md).

## <a name="call-your-custom-api-from-logic-app-workflows"></a>Volání vlastního rozhraní API z pracovních postupů aplikací logiky

Po nastavení vlastností definice rozhraní API a CORS by vaše triggery a akce vlastního rozhraní API měly být k dispozici, abyste je mohli zahrnout do pracovního postupu aplikace logiky. 

*  Pokud chcete zobrazit weby, které mají adresy URL OpenAPI, můžete procházet weby předplatného v Návrháři Logic Apps.

*  Chcete-li zobrazit dostupné akce a vstupy ukázáním v dokumentu Swagger, použijte [akci HTTP + Swagger](../connectors/connectors-native-http-swagger.md).

*  Chcete-li volat libovolné rozhraní API, včetně rozhraní API, která nemají nebo zpřístupňují dokument Swagger, můžete vždy vytvořit požadavek s [akcí http](../connectors/connectors-native-http.md).

## <a name="next-steps"></a>Další kroky

* [Přehled vlastních konektorů](../logic-apps/custom-connector-overview.md)
