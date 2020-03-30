---
title: Sdílení prostředků napříč zdroji (CORS) v Azure Cosmos DB
description: Tento článek popisuje, jak nakonfigurovat sdílení prostředků mezi zdroji (CORS) v Azure Cosmos DB pomocí azure portálu a šablony Azure Resource Manager.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/11/2019
ms.author: dech
ms.openlocfilehash: 7a487cb10965a379a0a418efaa061be88c5d10dd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77082988"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurace sdílení prostředků mezi zdroji (CORS)

Sdílení prostředků mezi zdroji (CORS) je funkce HTTP, která umožňuje webové aplikaci spuštěné pod jednou doménou přístup k prostředkům v jiné doméně. Webové prohlížeče implementují omezení zabezpečení známé jako zásady stejného původu, které brání webové stránce volat api v jiné doméně. Cors však poskytuje bezpečný způsob, jak povolit původní doméně volat api v jiné doméně. Základní (SQL) rozhraní API v Azure Cosmos DB teď podporuje sdílení prostředků mezi původy (CORS) pomocí hlavičky "allowedOrigins". Po povolení podpory CORS pro váš účet Azure Cosmos jsou vyhodnoceny pouze ověřené požadavky k určení, zda jsou povoleny podle pravidel, která jste zadali.

Nastavení sdílení prostředků mezi zdroji (CORS) můžete nakonfigurovat z webu Azure Portal nebo ze šablony Azure Resource Manager. Pro účty Cosmos pomocí rozhraní API Core (SQL) Azure Cosmos DB podporuje javascriptovou knihovnu, která funguje v node.js i v prostředích založených na prohlížeči. Tato knihovna nyní může využít podporu CORS při použití režimu brány. K použití této funkce není nutná žádná konfigurace na straně klienta. Díky podpoře CORS můžou prostředky z prohlížeče přímo přistupovat k Azure Cosmos DB prostřednictvím [knihovny JavaScriptu](https://www.npmjs.com/package/@azure/cosmos) nebo přímo z [rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db/) pro jednoduché operace.

> [!NOTE]
> Podpora CORS je jenom použitelná a podporovaná pro rozhraní API Azure Cosmos DB Core (SQL). Nevztahuje se na rozhraní API Azure Cosmos DB pro Cassandra, Gremlin nebo MongoDB, protože tyto protokoly nepoužívají protokol HTTP pro komunikaci mezi klientem a serverem.

## <a name="enable-cors-support-from-azure-portal"></a>Povolení podpory CORS z portálu Azure

Pomocí následujících kroků povolte sdílení prostředků mezi zdroji pomocí portálu Azure:

1. Přejděte na svůj účet Azure Cosmos DB. Otevřete nůž **CORS.**

2. Zadejte seznam počátků oddělených čárkami, který může volat mezi původy vašeho účtu Azure Cosmos DB. Například `https://www.mydomain.com`, `https://mydomain.com` `https://api.mydomain.com`, . Můžete také použít zástupný\*znak " " k povolení všech počátků a vyberte **odeslat**. 

   > [!NOTE]
   > V současné době nelze použít zástupné znaky jako součást názvu domény. Například `https://*.mydomain.net` formát ještě není podporován. 

   ![Povolení sdílení prostředků mezi zdroji pomocí portálu Azure Portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)

## <a name="enable-cors-support-from-resource-manager-template"></a>Povolení podpory CORS ze šablony Správce prostředků

Chcete-li povolit CORS pomocí šablony Správce prostředků, přidejte oddíl "cors" s vlastností "allowedOrigins" do libovolné existující šablony. Následující JSON je příkladem šablony, která vytvoří nový účet Azure Cosmos s povoleným CORS.

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

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Použití javascriptové knihovny Azure Cosmos DB z prohlížeče

V současné době je knihovna JavaScript Azure Cosmos DB jenom verze CommonJS knihovny dodávané s jeho balíček. Chcete-li použít tuto knihovnu z prohlížeče, musíte k vytvoření knihovny kompatibilní s prohlížečem použít nástroj, jako je kumulativní nebo webpack. Některé knihovny Node.js by měly mít pro ně falešné verze prohlížeče. Následuje příklad konfiguračního souboru webpack, který má potřebné mock nastavení.

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
 
Tady je [ukázka kódu,](https://github.com/christopheranderson/cosmos-browser-sample) která používá TypeScript a Webpack s knihovnou Azure Cosmos DB JavaScript SDK k vytvoření aplikace Todo, která odesílá aktualizace v reálném čase při vytváření nových položek.
Jako osvědčený postup nepoužívejte primární klíč ke komunikaci s Azure Cosmos DB z prohlížeče. Místo toho použijte tokeny prostředků ke komunikaci. Další informace o tokenech prostředků najdete v článku [Zabezpečení přístupu k Azure Cosmos DB.](secure-access-to-data.md#resource-tokens)

## <a name="next-steps"></a>Další kroky

Další informace o dalších způsobech zabezpečení účtu Azure Cosmos najdete v následujících článcích:

* [Nakonfigurujte bránu firewall pro](how-to-configure-firewall.md) azure cosmos db článek.

* [Konfigurace přístupu k virtuální síti a podsíti pro váš účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
