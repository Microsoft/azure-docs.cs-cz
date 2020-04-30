---
title: Rychlý Start – vytvoření profilu a koncového bodu pomocí šablon Správce prostředků
titleSuffix: Azure Content Delivery Network
description: Naučte se vytvářet profil sítě a koncový bod pro Azure Content Delivery pomocí šablon Správce prostředků.
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: azure-cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: dfd7b933502b96c0952a24dbee563e9b537dcdd8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/29/2020
ms.locfileid: "81683474"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Rychlý Start: vytvoření profilu Azure CDN a koncového bodu pomocí šablony Správce prostředků

V tomto rychlém startu nasadíte šablonu Azure Resource Manager pomocí rozhraní příkazového řádku. Šablona, kterou vytvoříte, nasadí profil CDN a koncový bod CDN pro frontu vaší webové aplikace.
Dokončení těchto kroků by mělo trvat přibližně deset minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Informace

Pro účely tohoto rychlého startu musíte mít webovou aplikaci, kterou chcete použít jako zdroj. Ukázková webová aplikace použitá v tomto rychlém startu byla nasazena dohttps://cdndemo.azurewebsites.net

Další informace najdete v tématu [Vytvoření webové aplikace ve statickém HTML v Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být nasazeny ve stejné skupině prostředků.

Vytvořte skupinu prostředků v umístění, které vyberete. Tento příklad ukazuje vytvoření skupiny prostředků s názvem CDN v umístění Východní USA.

```azurecli-interactive
az group create --name cdn --location eastus
```

![New Resource Group (Nová skupina prostředků)](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto kroku vytvoříte soubor šablony, který nasadí prostředky.

I když tento příklad projde obecným scénářem akcelerace webu, existuje mnoho dalších nastavení, která je možné nakonfigurovat. Tato nastavení jsou k dispozici v referenčních informacích k šabloně Azure Resource Manager. Podívejte se prosím na odkazy na [profil CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) a [koncový bod profilu CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Upozorňujeme, že Microsoft CDN nepodporuje úpravu seznamu typu obsahu.

Uložte šablonu jako **Resource-Manager-CDN. JSON**.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "cdnProfileSku": {
            "type": "string",
            "allowedValues": [
                "Standard_Microsoft",
                "Standard_Akamai",
                "Standard_Verizon",
                "Premium_Verizon"
            ]
        },
        "endpointOriginHostName": {
            "type": "string"
        }
    },
    "variables": {
        "profile": {
            "name": "[replace(toLower(parameters('cdnProfileSku')), '_', '-')]"
        },
        "endpoint": {
            "name": "[replace(toLower(parameters('endpointOriginHostName')), '.', '-')]",
            "originHostName": "[parameters('endpointOriginHostName')]"
        }
    },
    "resources": [
        {
            "type": "Microsoft.Cdn/profiles",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[variables('profile').name]",
            "sku": {
                "name": "[parameters('cdnProfileSku')]"
            }
        },
        {
            "dependsOn": [
                "[resourceId('Microsoft.Cdn/profiles', variables('profile').name)]"
            ],
            "type": "Microsoft.Cdn/profiles/endpoints",
            "apiVersion": "2017-10-12",
            "location": "[resourceGroup().location]",
            "name": "[concat(variables('profile').name, '/', variables('endpoint').name)]",
            "properties": {
                "hostName": "[concat(variables('endpoint').name, '.azureedge.net')]",
                "originHostHeader": "[variables('endpoint').originHostName]",
                "isHttpAllowed": true,
                "isHttpsAllowed": true,
                "queryStringCachingBehavior": "IgnoreQueryString",
                "origins": [
                    {
                        "name": "[replace(variables('endpoint').originHostName, '.', '-')]",
                        "properties": {
                            "hostName": "[variables('endpoint').originHostName]",
                            "httpPort": 80,
                            "httpsPort": 443
                        }
                    }
                ],
                "contentTypesToCompress": [
                    "application/eot",
                    "application/font",
                    "application/font-sfnt",
                    "application/javascript",
                    "application/json",
                    "application/opentype",
                    "application/otf",
                    "application/pkcs7-mime",
                    "application/truetype",
                    "application/ttf",
                    "application/vnd.ms-fontobject",
                    "application/xhtml+xml",
                    "application/xml",
                    "application/xml+rss",
                    "application/x-font-opentype",
                    "application/x-font-truetype",
                    "application/x-font-ttf",
                    "application/x-httpd-cgi",
                    "application/x-javascript",
                    "application/x-mpegurl",
                    "application/x-opentype",
                    "application/x-otf",
                    "application/x-perl",
                    "application/x-ttf",
                    "font/eot",
                    "font/ttf",
                    "font/otf",
                    "font/opentype",
                    "image/svg+xml",
                    "text/css",
                    "text/csv",
                    "text/html",
                    "text/javascript",
                    "text/js",
                    "text/plain",
                    "text/richtext",
                    "text/tab-separated-values",
                    "text/xml",
                    "text/x-script",
                    "text/x-component",
                    "text/x-java-source"
                ],
                "isCompressionEnabled": true,
                "optimizationType": "GeneralWebDelivery"
            }
        }
    ],
    "outputs": {
        "cdnUrl": {
            "type": "string",
            "value": "[concat('https://', variables('endpoint').name, '.azureedge.net')]"
        }
    }
}
```

## <a name="create-the-resources"></a>Vytvoření prostředků

Nasaďte šablonu pomocí Azure CLI. Zobrazí se výzva k zadání dvou vstupů:

**cdnProfileSku** – poskytovatel CDN, který chcete použít. Dostupné možnosti:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** – koncový bod, který bude OBSLUHOVÁN prostřednictvím CDN, například cdndemo.azurewebsites.NET.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Nasazení šablony Správce prostředků](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Zobrazit profil CDN

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Zobrazit profil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Zobrazit koncový bod CDN pro profil standard – Microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Zobrazit koncový bod CDN](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

K zobrazení obsahu použijte název hostitele. Například přístup k protokolu https:\//cdndemo-azurewebsites-NET.azureedge.NET pomocí prohlížeče.

## <a name="clean-up"></a>Vyčištění

Odstraněním skupiny prostředků se automaticky odeberou všechny prostředky, které jsou v něm nasazené.

```azurecli-interactive
az group delete --name cdn
```

![Odstranit skupinu prostředků](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Odkazy

* Profil CDN – [odkaz na šablonu Azure Resource Manager](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* [Referenční dokumentace k šabloně](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints) koncového bodu CDN – Azure Resource Manager

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak do koncového bodu CDN přidat vlastní doménu, projděte si následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)
