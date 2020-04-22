---
title: Úvodní příručka – vytvoření profilu a koncového bodu pomocí šablon Správce prostředků
titleSuffix: Azure Content Delivery Network
description: Zjistěte, jak vytvořit profil a koncový bod Azure Content Deliver Network pomocí šablon Správce prostředků.
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
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683474"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Úvodní příručka: Vytvoření profilu a koncového bodu Azure CDN pomocí šablony Správce prostředků

V tomto rychlém startu nasadíte šablonu Azure Resource Manager pomocí příkazového příkazového příkazu. Šablona, kterou vytvoříte, nasadí profil CDN a koncový bod CDN na přední stranu webové aplikace.
Dokončení těchto kroků by mělo trvat asi deset minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Prequisites

Pro účely tohoto rychlého startu musíte mít webovou aplikaci, která bude používat jako svůj původ. Ukázková webová aplikace použitá v tomto rychlém startu byla nasazenahttps://cdndemo.azurewebsites.net

Další informace najdete [v tématu Vytvoření statické webové aplikace HTML v Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být nasazeny ve stejné skupině prostředků.

Vytvořte skupinu prostředků ve vybraném umístění. Tento příklad ukazuje vytvoření skupiny prostředků s názvem CDN v umístění usa – východ.

```azurecli-interactive
az group create --name cdn --location eastus
```

![New Resource Group (Nová skupina prostředků)](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto kroku vytvoříte soubor šablony, který nasazuje prostředky.

Zatímco tento příklad prochází obecný scénář akcelerace webu, existuje mnoho dalších nastavení, které lze nakonfigurovat. Tato nastavení jsou k dispozici v odkazu na šablonu Azure Resource Manager. Viz odkazy na [profil CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles) a [koncový bod profilu CDN](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Všimněte si, že microsoft cdn nepodporuje úpravy seznamu typů obsahu.

Uložte šablonu jako **resource-manager-cdn.json**.

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

## <a name="create-the-resources"></a>Vytvoření zdrojů

Nasaďte šablonu pomocí azure cli. Budete vyzváni k zadání 2 vstupů:

**cdnProfileSku** - poskytovatel CDN, který chcete použít. Dostupné možnosti:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** - koncový bod, který bude obsluhován prostřednictvím CDN, například cdndemo.azurewebsites.net.

```azurecli-interactive
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Nasazení šablony Správce prostředků](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Zobrazení profilu CDN

```azurecli-interactive
az cdn profile list --resource-group cdn -o table
```

![Zobrazit profil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Zobrazení koncového bodu CDN pro profil standard-microsoft

```azurecli-interactive
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Zobrazení koncového bodu CDN](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

K zobrazení obsahu použijte název HostName. Například přístup https:\//cdndemo-azurewebsites-net.azureedge.net pomocí prohlížeče.

## <a name="clean-up"></a>Vyčištění

Odstraněním skupiny prostředků automaticky odeberete všechny prostředky, které v ní byly nasazeny.

```azurecli-interactive
az group delete --name cdn
```

![Odstranit skupinu prostředků](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Odkazy

* Profil CDN – [odkaz na šablonu Správce prostředků Azure](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles)
* Koncový bod CDN – [referenční dokumentace k šabloně Správce prostředků Azure](https://docs.microsoft.com/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Další kroky

Pokud chcete zjistit, jak do koncového bodu CDN přidat vlastní doménu, projděte si následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)
