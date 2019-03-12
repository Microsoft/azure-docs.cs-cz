---
title: Rychlý start – vytvoření Azure Content Delivery Network profilu a koncového bodu pomocí šablon Resource Manageru | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit síť Azure Content Delivery Network profilu a koncového bodu pomocí šablon Resource Manageru
services: cdn
documentationcenter: ''
author: senthuransivananthan
manager: danielgi
editor: ''
ms.assetid: cbc2ff98-916d-4339-8959-622823c5b772
ms.service: cdn
ms.workload: ''
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/05/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 5c393916bc977f4e0bc51913bdb2dfbbd6677c97
ms.sourcegitcommit: 235cd1c4f003a7f8459b9761a623f000dd9e50ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2019
ms.locfileid: "57727533"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint-using-resource-manager-template"></a>Rychlý start: Vytvoření profilu Azure CDN a koncový bod pomocí šablony Resource Manageru

V tomto rychlém startu nasadíte šablonu Azure Resource Manageru pomocí rozhraní příkazového řádku. Šablona, kterou vytvoříte nasadí profil CDN a koncový bod CDN pro front-webové aplikace.
Proveďte následující kroky má trvat asi každých deset minut.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prequisites"></a>Požadavky

Pro účely tohoto rychlého startu musí mít webovou aplikaci pro použití jako váš původní název. V příkladu použita webové aplikace v tomto rychlém startu je nasazená do https://cdndemo.azurewebsites.net

Další informace najdete v tématu [vytvoření statické webové aplikace HTML v Azure](https://docs.microsoft.com/azure/app-service/app-service-web-get-started-html).

## <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Všechny prostředky musí být ve stejné skupině prostředků nasazeny.

Vytvořte skupinu prostředků v umístění, které jste vybrali. Tento příklad ukazuje vytvoření skupiny prostředků s názvem cdn v umístění východní USA.

```bash
az group create --name cdn --location eastus
```

![Nová skupina prostředků](./media/create-profile-resource-manager-template/cdn-create-resource-group.png)

## <a name="create-the-resource-manager-template"></a>Vytvoření šablony Resource Manageru

V tomto kroku vytvoříte soubor šablony, který se nasazuje prostředky.

Přestože tento příklad vás provede scénáři obecné akcelerace webu, existují mnoho dalších nastavení, které lze nakonfigurovat. Tato nastavení jsou dostupná v odkazu na šablony Azure Resource Manageru. Podrobnosti najdete odkazy pro [profil CDN](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles) a [koncového bodu CDN profilu](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints).

Všimněte si, že Microsoft CDN nepodporuje úpravy seznamu typ obsahu.

Uložit šablonu jako **resource-manager-cdn.json**.

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

Nasazení šablony pomocí rozhraní příkazového řádku Azure. Zobrazí se výzva k zadání 2 vstupy:

**cdnProfileSku** – poskytovatel CDN, kterou chcete použít. Dostupné možnosti:

* Standard_Microsoft
* Standard_Akamai
* Standard_Verizon
* Premium_Verizon.

**endpointOriginHostName** -koncového bodu, který bude obsluhovat prostřednictvím CDN, například cdndemo.azurewebsites.net.

```bash
az group deployment create --resource-group cdn --template-file arm-cdn.json
```

![Nasazení šablony Resource Manageru](./media/create-profile-resource-manager-template/cdn-deploy-resource-manager.png)

## <a name="view-the-cdn-profile"></a>Zobrazit profil CDN

```bash
az cdn profile list --resource-group cdn -o table
```

![Zobrazit profil CDN](./media/create-profile-resource-manager-template/cdn-view-profile.png)

## <a name="view-the-cdn-endpoint-for-the-profile-standard-microsoft"></a>Zobrazit koncového bodu CDN pro profil standard – microsoft

```bash
az cdn endpoint list --profile-name standard-microsoft --resource-group cdn -o table
```

![Koncový bod CDN zobrazení](./media/create-profile-resource-manager-template/cdn-view-endpoint.png)

Chcete-li zobrazit obsah pomocí názvu hostitele. Například k https://cdndemo-azurewebsites-net.azureedge.net pomocí prohlížeče.

## <a name="clean-up"></a>Vyčištění

Odstraněním skupiny prostředků se automaticky odeberou všechny prostředky, které se nasadily do něj.

```bash
az group delete --name cdn
```

![Odstranit skupinu prostředků](./media/create-profile-resource-manager-template/cdn-delete-resource-group.png)

## <a name="references"></a>Odkazy

* Profil CDN – [odkaz na šablonu Azure Resource Manageru](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles)
* Koncový bod CDN - [dokumentaci odkaz na šablonu Azure Resource Manageru](https://docs.microsoft.com/en-us/azure/templates/microsoft.cdn/2017-10-12/profiles/endpoints)

## <a name="next-steps"></a>Další postup

Pokud chcete zjistit, jak do koncového bodu CDN přidat vlastní doménu, projděte si následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)
