---
title: Sdílení prostředků mezi zdroji (CORS) v Azure Cosmos DB
description: Tento článek popisuje, jak v Azure Cosmos DB nakonfigurovat sdílení prostředků mezi zdroji (CORS) pomocí šablon Azure Portal a Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: how-to
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 863c31ecac13337ea3f91d7a7ced49b0f7141e58
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2020
ms.locfileid: "93086410"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurace sdílení prostředků mezi zdroji (CORS)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Sdílení prostředků mezi zdroji (CORS) je funkce HTTP, která umožňuje webové aplikaci spuštěné v jedné doméně přistupovat k prostředkům v jiné doméně. Webové prohlížeče implementují omezení zabezpečení označované jako zásady stejného původu, které brání webové stránce v volání rozhraní API v jiné doméně. CORS ale poskytuje zabezpečený způsob, jak, aby zdrojová doména mohla volat rozhraní API v jiné doméně. Rozhraní API Core (SQL) v Azure Cosmos DB teď podporuje sdílení prostředků mezi zdroji (CORS) pomocí hlavičky "allowedOrigins". Po povolení podpory CORS pro váš účet Azure Cosmos se vyhodnotí jenom ověřené požadavky, které určují, jestli jsou povolené podle pravidel, která jste zadali.

Nastavení sdílení prostředků mezi zdroji (CORS) můžete nakonfigurovat z Azure Portal nebo ze Azure Resource Manager šablony. Pro účty Cosmos využívající rozhraní API Core (SQL) Azure Cosmos DB podporuje knihovnu JavaScriptu, která funguje v prostředích založeném na Node.js a prohlížeči. Tato knihovna teď může využít podporu CORS při použití režimu brány. Pro použití této funkce není nutná žádná konfigurace na straně klienta. Díky podpoře CORS můžou prostředky z prohlížeče získat přímý přístup k Azure Cosmos DB prostřednictvím [knihovny JavaScriptu](https://www.npmjs.com/package/@azure/cosmos) nebo přímo z [REST API](/rest/api/cosmos-db/) pro jednoduché operace.

> [!NOTE]
> Podpora CORS je platná a podporovaná pro rozhraní API pro Azure Cosmos DB Core (SQL). Neplatí pro rozhraní Azure Cosmos DB API pro Cassandra, Gremlin nebo MongoDB, protože tyto protokoly nepoužívají pro komunikaci mezi klientem a serverem protokol HTTP.

## <a name="enable-cors-support-from-azure-portal"></a>Povolit podporu CORS z Azure Portal

Pomocí následujících kroků můžete povolit sdílení prostředků mezi zdroji pomocí Azure Portal:

1. Přejděte k účtu služby Azure Cosmos DB. Otevřete okno **CORS** .

2. Zadejte čárkami oddělený seznam počátek, který může provádět volání mezi zdroji Azure Cosmos DB účtu. Například,, `https://www.mydomain.com` `https://mydomain.com` `https://api.mydomain.com` . Můžete také použít zástupný znak \* "" pro povolení všech původu a vybrat **Odeslat** . 

   > [!NOTE]
   > V současné době nemůžete použít zástupné znaky jako součást názvu domény. Například `https://*.mydomain.net` formát není dosud podporován. 

   :::image type="content" source="./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png" alt-text="Povolení sdílení prostředků mezi zdroji pomocí Azure Portal":::

## <a name="enable-cors-support-from-resource-manager-template"></a>Povolit podporu CORS z šablony Správce prostředků

Pokud chcete povolit CORS pomocí šablony Správce prostředků, přidejte do jakékoli existující šablony oddíl CORS s vlastností "allowedOrigins". Následující JSON je příklad šablony, která vytvoří nový účet Azure Cosmos s povoleným CORS.

```json
{
  "type": "Microsoft.DocumentDB/databaseAccounts",
  "name": "[variables('accountName')]",
  "apiVersion": "2019-08-01",
  "location": "[parameters('location')]",
  "kind": "GlobalDocumentDB",
  "properties": {
    "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
    "locations": "[variables('locations')]",
    "databaseAccountOfferType": "Standard",
    "cors": [
      {
        "allowedOrigins": "*"
      }
    ]
  }
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Použití knihovny Azure Cosmos DB JavaScript z prohlížeče

V současné době má knihovna Azure Cosmos DB JavaScript pouze verzi CommonJS knihovny dodávané s jejím balíčkem. Chcete-li použít tuto knihovnu z prohlížeče, je nutné pomocí nástroje, jako je například kumulativní nebo Webpack, vytvořit knihovnu kompatibilní s prohlížečem. Některé knihovny Node.js by pro ně měly mít podobu prohlížeče. Následuje příklad konfiguračního souboru sady, který má potřebná nastavení.

```javascript
const path = require("path");

module.exports = {
  entry: "./src/index.ts",
  devtool: "inline-source-map",
  node: {
    net: "mock",
    tls: "mock"
  },
  output: {
    filename: "bundle.js",
    path: path.resolve(__dirname, "dist")
  }
};
```
 
Tady je [Ukázka kódu](https://github.com/christopheranderson/cosmos-browser-sample) , který používá TypeScript a Webpack s knihovnou Azure Cosmos DB JavaScript SDK k vytvoření aplikace todo, která při vytváření nových položek odesílá aktualizace v reálném čase.
Jako osvědčený postup nepoužívejte primární klíč ke komunikaci s Azure Cosmos DB z prohlížeče. Místo toho použijte ke komunikaci tokeny prostředků. Další informace o tokenech prostředků najdete v tématu [zabezpečení přístupu k Azure Cosmos DBmu](secure-access-to-data.md#resource-tokens) článku.

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech zabezpečení účtu Azure Cosmos najdete v následujících článcích:

* [Nakonfigurujte bránu firewall pro Azure Cosmos DB](how-to-configure-firewall.md) článek.

* [Konfigurace přístupu na základě virtuální sítě a podsítě pro účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
