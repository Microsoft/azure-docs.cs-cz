---
title: Prostředků mezi zdroji (CORS) pro sdílení obsahu ve službě Azure Cosmos DB
description: Tento článek popisuje, jak nakonfigurovat sdílení prostředků mezi zdroji (CORS) ve službě Azure Cosmos DB pomocí šablon Azure Resource Manageru a webu Azure portal.
author: deborahc
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2018
ms.author: dech
ms.openlocfilehash: 689b0bb08e300efc7c5e93dbf346040b82a7c52d
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55468474"
---
# <a name="configure-cross-origin-resource-sharing-cors"></a>Konfigurace prostředků mezi zdroji (CORS) pro sdílení obsahu 

Sdílení prostředků mezi zdroji (CORS) je funkce protokolu HTTP, která umožňuje webové aplikaci spuštěné v rámci jedné domény pro přístup k prostředkům v jiné doméně. Webové prohlížeče implementují bezpečnostní omezení, označuje jako zásada stejného zdroje, které chrání webovou stránku před voláním rozhraní API v jiné doméně. Však CORS nabízí zabezpečený způsob, jak povolit zdrojová doména pro volání rozhraní API v jiné doméně. Rozhraní SQL API Core ve službě Azure Cosmos DB teď podporuje sdílení prostředků mezi zdroji (CORS) pomocí hlavičky "allowedOrigins". Po povolení podpory CORS pro váš účet Azure Cosmos se vyhodnocují jenom ověřených požadavků k určení, zda jsou povoleny podle pravidel, které jste zadali.

Můžete nakonfigurovat prostředků mezi zdroji (CORS) nastavení z webu Azure portal nebo šablony Azure Resource Manageru pro sdílení obsahu. Rozhraní SQL API Core ve službě Azure Cosmos DB podporuje knihovnu JavaScript, která funguje v Node.js a prohlížeči prostředí. Tato knihovna můžou využívat podporu CORS v režimu brány. Neexistuje žádná konfigurace na straně klienta potřebné pro tuto funkci používat. S podporou CORS můžete prostředky z prohlížeče přímý přístup prostřednictvím služby Azure Cosmos DB [knihovny JavaScript](https://www.npmjs.com/package/@azure/cosmos) nebo přímo z [rozhraní REST API](https://docs.microsoft.com/rest/api/cosmos-db/) pro jednoduché operace. 

## <a name="enable-cors-support-from-azure-portal"></a>Povolení podpory CORS z webu Azure portal

Povolit sdílení prostředků různého původu pomocí webu Azure portal postupujte následovně:

1. Přejděte do svého účtu Azure cosmos DB. Otevřít **CORS** okno.

2. Zadejte čárkami oddělený seznam zdrojů, které mohl provádět volání mezi zdroji do svého účtu Azure Cosmos DB. Například `https://www.mydomain.com`, `https://mydomain.com`, `https://api.mydomain.com`. Můžete také použít zástupný znak "\*" a umožňuje všechny původy vyberte **odeslat**. 

   > [!NOTE]
   > V současné době nelze použít zástupné znaky jako součást názvu domény. Například `https://*.mydomain.net` formát není dosud podporován. 
   
   ![Povolit sdílení prostředků mezi zdroji pomocí webu Azure portal](./media/how-to-configure-cross-origin-resource-sharing/enable-cross-origin-resource-sharing-using-azure-portal.png)
 
## <a name="enable-cors-support-from-resource-manager-template"></a>Povolení podpory CORS ze šablony Resource Manageru

Povolení CORS pomocí šablony Resource Manageru, přidáte v části "cors" s "allowedOrigins" vlastnosti žádné ze stávajících šablon. Následující kód JSON je příkladem šablonu, která vytvoří nový účet Azure Cosmos s povolením CORS.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {},
    "variables": {},
    "resources": [
        {
            "name": "test",
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "apiVersion": "2015-04-08",
            "location": "East US 2",
            "properties": {
                "databaseAccountOfferType": "Standard",
                "consistencyPolicy": {
                    "defaultConsistencyLevel": "Session",
                    "maxIntervalInSeconds": 5,
                    "maxStalenessPrefix": 100
                },
                "locations": [
                    {
                        "id": "test-eastus2",
                        "failoverPriority": 0,
                        "locationName": "East US 2"
                    }
                ],
                "cors": [
                    {
                        "allowedOrigins": "*"
                    }
                ]
            },
            "dependsOn": [
            ]
        }
    ]
}
```

## <a name="using-the-azure-cosmos-db-javascript-library-from-a-browser"></a>Pomocí knihovny Azure Cosmos DB JavaScript v prohlížeči

Knihovny Azure Cosmos DB JavaScript v současné době má jenom CommonJS verzi knihovny dodaných s balíčkem. Pokud chcete použít tuto knihovnu z prohlížeče, budete muset použít nástroje, jako je kumulativní nebo Webpacku vytvoření prohlížeč kompatibilní knihovny. Některé knihoven Node.js by měl mít mocks prohlížeče pro ně. Následuje příklad webpacku konfiguračního souboru, který obsahuje potřebná nastavení mock.

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
 
Tady je [vzorový kód](https://github.com/christopheranderson/cosmos-browser-sample) , který používá TypeScript a Webpacku s knihovnou Azure Cosmos DB JavaScript SDK k vytvoření aplikace seznamu úkolů, která odesílá aktualizace v reálném čase při vytvoření nové položky.
Jako osvědčený postup nepoužívejte primární klíč ke komunikaci s Azure Cosmos DB přejde v prohlížeči. Místo toho použijte tokeny prostředků komunikovat. Další informace o tokenech prostředků najdete v tématu [zabezpečení přístupu ke službě Azure Cosmos DB](secure-access-to-data.md#resource-tokens) článku.

## <a name="next-steps"></a>Další postup

Další informace o dalších způsobech k zabezpečení vašeho účtu Azure Cosmos, naleznete v následujících článcích:

* [Konfigurace brány firewall pro službu Azure Cosmos DB](how-to-configure-firewall.md) článku.

* [Konfigurace virtuální sítě a přístupu na základě podsítě pro váš účet Azure Cosmos DB](how-to-configure-vnet-service-endpoint.md)
    

