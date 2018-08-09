---
title: Nastavení služby Device Provisioning pomocí šablony Azure Resource Manageru | Microsoft Docs
description: Rychlý start Azure – Nastavení služby Azure IoT Hub Device Provisioning pomocí šablony
author: wesmc7777
ms.author: wesmc
ms.date: 06/18/2018
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: e3aa2cf93e529fcc430162ac90be06a75690fb21
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39523440"
---
# <a name="set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Nastavení služby IoT Hub Device Provisioning pomocí šablony Azure Resource Manageru

Pomocí [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) můžete prostřednictvím kódu programu nastavit cloudové prostředky Azure potřebné ke zřizování zařízení. Tyto kroky ukazují, jak vytvořit centrum IoT, novou službu IoT Hub Device Provisioning a vzájemně tyto dvě služby propojit pomocí šablony Azure Resource Manageru. V tomto rychlém startu se k provedení kroků potřebných k vytvoření skupiny prostředků a nasazení šablony používá [Azure CLI 2.0](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli), ale můžete k tomu snadno použít i [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby nebo jiné programovací jazyky. 


## <a name="prerequisites"></a>Požadavky

- Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.
- Tento rychlý start vyžaduje, abyste používali Azure CLI místně. Musíte mít nainstalovanou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade rozhraní příkazového řádku, přečtěte si téma [Instalace Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).


## <a name="sign-in-to-azure-and-create-a-resource-group"></a>Přihlášení k Azure a vytvoření skupiny prostředků

Přihlaste se ke svému účtu Azure a vyberte své předplatné.

1. Na příkazovém řádku spusťte [příkaz pro přihlášení][lnk-login-command]:
    
    ```azurecli
    az login
    ```

    Postupujte podle pokynů pro ověření pomocí kódu a přihlaste se ke svému účtu Azure ve webovém prohlížeči.

2. Pokud máte více předplatných Azure, přihlášením k Azure získáte přístup ke všem účtům Azure přidruženým k vašim přihlašovacím údajům. Pomocí následujícího [příkazu zobrazte výpis účtů Azure][lnk-az-account-command], které můžete použít:
    
    ```azurecli
    az account list 
    ```

    Pomocí následujícího příkazu vyberte předplatné, které chcete použít ke spuštění příkazů pro vytvoření centra IoT. Můžete použít název nebo ID předplatného z výstupu předchozího příkazu:

    ```azurecli
    az account set --subscription {your subscription name or id}
    ```

3. Když vytváříte cloudové prostředky Azure, jako jsou centra IoT a služby zřizování, vytváříte je ve skupině prostředků. Použijte existující skupinu prostředků, nebo spusťte následující [příkaz pro vytvoření skupiny prostředků][lnk-az-resource-command]:
    
    ```azurecli
     az group create --name {your resource group name} --location westus
    ```

    > [!TIP]
    > Předchozí příklad vytvoří skupinu prostředků v umístění USA – západ. Seznam dostupných umístění můžete zobrazit spuštěním příkazu `az account list-locations -o table`.
    >
    >

## <a name="create-a-resource-manager-template"></a>Vytvoření šablony Resource Manageru

Pomocí šablony JSON ve své skupině prostředků vytvořte službu zřizování a propojené centrum IoT. Pomocí šablony Azure Resource Manageru můžete také provádět změny existující služby zřizování nebo centra IoT.

1. Pomocí textového editoru vytvořte šablonu Azure Resource Manageru **template.json** s následující kostrou obsahu. 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {},
       "variables": {},
       "resources": []
   }
   ```

2. Část **parameters** (parametry) nahraďte následujícím obsahem. Část s parametry určuje parametry, které je možné předat z jiného souboru. Tato část určuje název centra IoT a služby zřizování, které se mají vytvořit. Určuje také umístění centra IoT i služby zřizování. Hodnoty jsou omezené na oblasti Azure, které podporují centra IoT a služby zřizování. Seznam podporovaných umístění služby Device Provisioning můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo můžete přejít na stránku [Stav Azure](https://azure.microsoft.com/status/) a vyhledat službu Device Provisioning.

   ```json
    "parameters": {
        "iotHubName": {
            "type": "string"
        },
        "provisioningServiceName": {
            "type": "string"
        },
        "hubLocation": {
            "type": "string",
            "allowedValues": [
                "eastus",
                "westus",
                "westeurope",
                "northeurope",
                "southeastasia",
                "eastasia"
            ]
        }
    },

   ```

3. Část **variables** (proměnné) nahraďte následujícím obsahem. Tato část definuje hodnoty, které se později použijí k vytvoření připojovacího řetězce centra IoT, který je potřeba k propojení služby zřizování a centra IoT. 
 
   ```json
    "variables": {        
        "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
        "iotHubKeyName": "iothubowner",
        "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
    },

   ```

4. Centrum IoT vytvoříte přidáním následujících řádků do kolekce **resources** (prostředky). Kód JSON určuje minimum vlastností potřebných k vytvoření centra IoT. Vlastnosti **name** (název) a **location** (umístění) se předávají jako parametry. Další informace o vlastnostech, které můžete pro centrum IoT v šabloně zadat, najdete v [referenčních informacích k šabloně Microsoft.Devices/IotHubs](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

   ```json
        {
            "apiVersion": "2017-07-01",
            "type": "Microsoft.Devices/IotHubs",
            "name": "[parameters('iotHubName')]",
            "location": "[parameters('hubLocation')]",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "tags": {
            },
            "properties": {
            }            
        },

   ``` 

5. Službu zřizování vytvoříte přidáním následujících řádků za specifikaci centra IoT v kolekci **resources** (prostředky). Vlastnosti **name** (název) a **location** (umístění) služby zřizování se předávají v parametrech. V kolekci **iotHubs** zadejte centra IoT, která se mají se službou zřizování propojit. Pro každé propojené centrum IoT musíte zadat minimálně vlastnosti **connectionString** (připojovací řetězec) a **location** (umístění). Pro každé centrum IoT můžete nastavit také vlastnosti jako **allocationWeight** (váha přidělení) a **applyAllocationPolicy** (použít zásady přidělování) a pro samotnou službu zřizování můžete nastavit vlastnosti jako **allocationPolicy** (zásady přidělování) a **authorizationPolicies** (zásady autorizace). Další informace najdete v [referenčních informacích k šabloně Microsoft.Devices/provisioningServices](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   Vlastnost **dependsOn** slouží k zajištění, že Resource Manager vytvoří nejprve centrum IoT a až pak službu zřizování. Šablona vyžaduje připojovací řetězec centra IoT určující jeho propojení se službou zřizování, takže je nejprve potřeba vytvořit centrum a jeho klíče. Šablona vytváří připojovací řetězec pomocí funkcí jako **concat** a **listKeys**. Další informace najdete v tématu [Funkce šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

   ```json
        {
            "type": "Microsoft.Devices/provisioningServices",
            "sku": {
                "name": "S1",
                "capacity": 1
            },
            "name": "[parameters('provisioningServiceName')]",
            "apiVersion": "2017-11-15",
            "location": "[parameters('hubLocation')]",
            "tags": {},
            "properties": {
                "iotHubs": [
                    {
                        "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                        "location": "[parameters('hubLocation')]",
                        "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                    }
                ]
            },
            "dependsOn": ["[parameters('iotHubName')]"]
        }

   ```

6. Uložte soubor šablony. Dokončená šablona by měla vypadat následovně:

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {
           "iotHubName": {
               "type": "string"
           },
           "provisioningServiceName": {
               "type": "string"
           },
           "hubLocation": {
               "type": "string",
               "allowedValues": [
                   "eastus",
                   "westus",
                   "westeurope",
                   "northeurope",
                   "southeastasia",
                   "eastasia"
               ]
           }
       },
       "variables": {        
           "iotHubResourceId": "[resourceId('Microsoft.Devices/Iothubs', parameters('iotHubName'))]",
           "iotHubKeyName": "iothubowner",
           "iotHubKeyResource": "[resourceId('Microsoft.Devices/Iothubs/Iothubkeys', parameters('iotHubName'), variables('iotHubKeyName'))]"
       },
       "resources": [
           {
               "apiVersion": "2017-07-01",
               "type": "Microsoft.Devices/IotHubs",
               "name": "[parameters('iotHubName')]",
               "location": "[parameters('hubLocation')]",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "tags": {
               },
               "properties": {
               }            
           },
           {
               "type": "Microsoft.Devices/provisioningServices",
               "sku": {
                   "name": "S1",
                   "capacity": 1
               },
               "name": "[parameters('provisioningServiceName')]",
               "apiVersion": "2017-11-15",
               "location": "[parameters('hubLocation')]",
               "tags": {},
               "properties": {
                   "iotHubs": [
                       {
                           "connectionString": "[concat('HostName=', reference(variables('iotHubResourceId')).hostName, ';SharedAccessKeyName=', variables('iotHubKeyName'), ';SharedAccessKey=', listkeys(variables('iotHubKeyResource'), '2017-07-01').primaryKey)]",
                           "location": "[parameters('hubLocation')]",
                           "name": "[concat(parameters('iotHubName'),'.azure-devices.net')]"
                       }
                   ]
               },
               "dependsOn": ["[parameters('iotHubName')]"]
           }
       ]
   }
   ```

## <a name="create-a-resource-manager-parameter-file"></a>Vytvoření souboru parametrů Resource Manageru

Šablona, kterou jste definovali v posledním kroku, využívá parametry k určení názvu centra IoT, názvu služby zřizování a umístění (oblast Azure), ve kterém se mají vytvořit. Tyto parametry předáváte v samostatném souboru. Tímto způsobem můžete stejnou šablonu znovu použít pro více nasazení. Soubor parametrů vytvoříte podle následujícího postupu:

1. Pomocí textového editoru vytvořte soubor parametrů Azure Resource Manageru **parameters.json** s následující kostrou obsahu: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Do části s parametry přidejte hodnotu **iotHubName** (název centra IoT). Pokud název změníte, ujistěte se, že splňuje příslušné zásady vytváření názvů pro centrum IoT. Musí být dlouhý 3 až 50 znaků a může obsahovat pouze velké a malé alfanumerické znaky nebo spojovníky (-). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Do části s parametry přidejte hodnotu **provisioningServiceName** (název služby zřizování). Pokud název změníte, ujistěte se, že splňuje příslušné zásady vytváření názvů pro službu IoT Hub Device Provisioning. Musí být dlouhý 3 až 64 znaků a může obsahovat pouze velké a malé alfanumerické znaky nebo spojovníky (-).

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
    }

   ```

4. Do části s parametry přidejte hodnotu **hubLocation** (umístění centra). Tato hodnota určuje umístění centra IoT i služby zřizování. Hodnota musí odpovídat jednomu z umístění zadaných v kolekci **allowedValues** (povolené hodnoty) v definici parametru v souboru šablony. Tato kolekce omezuje hodnoty na umístění Azure, které podporují centra IoT i služby zřizování. Seznam podporovaných umístění služby Device Provisioning můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo můžete přejít na stránku [Stav Azure](https://azure.microsoft.com/status/) a vyhledat službu Device Provisioning.

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
        "provisioningServiceName": {
            "value": "my-sample-provisioning-service"
        },
        "hubLocation": {
            "value": "westus"
        }
    }

   ```

5. Uložte soubor. 


> [!IMPORTANT]
> Centrum IoT i služba zřizování budou veřejně zjistitelné jako koncové body DNS, takže se ujistěte, že při jejich pojmenování nepoužíváte žádné citlivé údaje.
>

## <a name="deploy-the-template"></a>Nasazení šablony

Pomocí následujících příkazů Azure CLI nasaďte své šablony a ověřte nasazení.

1. Šablonu nasadíte spuštěním následujícího [příkazu pro spuštění nasazení](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Ve výstupu vyhledejte vlastnost **provisioningState** (stav zřizování) nastavenou na hodnotu Succeeded (Úspěch). 

   ![Výstup zřizování](./media/quick-setup-auto-provision-rm/output.png) 


2. Pokud chcete nasazení ověřit, spusťte následující [příkaz pro výpis prostředků](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) a ve výstupu vyhledejte novou službu zřizování a centrum IoT:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud pokračovat nechcete, můžete pomocí Azure CLI [odstranit jednotlivé prostředky][lnk-az-resource-command], jako je centrum IoT nebo služba zřizování, nebo odstranit skupinu prostředků a všechny její prostředky.

Pokud chcete odstranit službu zřizování, spusťte následující příkaz:

```azurecli
az iot hub delete --name {your provisioning service name} --resource-group {your resource group name}
```
Pokud chcete odstranit centrum IoT, spusťte následující příkaz:

```azurecli
az iot hub delete --name {your iot hub name} --resource-group {your resource group name}
```

Pokud chcete odstranit skupinu prostředků a všechny její prostředky, spusťte následující příkaz:

```azurecli
az group delete --name {your resource group name}
```

Skupiny prostředků i jednotlivé prostředky můžete odstranit také pomocí webu Azure Portal, PowerShellu nebo rozhraní REST API nebo podporovaných sad SDK platformy pro Azure Resource Manager nebo službu IoT Hub Device Provisioning.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili centrum IoT a instanci služby Device Provisioning a propojili jste tyto dva prostředky. Pokud chcete zjistit, jak pomocí tohoto nastavení zřídit simulované zařízení, pokračujte k rychlému startu pro vytvoření simulovaného zařízení.

> [!div class="nextstepaction"]
> [Rychlý start k vytvoření simulovaného zařízení](./quick-create-simulated-device.md)


<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-CLI-install]: https://docs.microsoft.com/cli/azure/install-az-cli2
[lnk-login-command]: https://docs.microsoft.com/cli/azure/get-started-with-az-cli2
[lnk-az-account-command]: https://docs.microsoft.com/cli/azure/account
[lnk-az-register-command]: https://docs.microsoft.com/cli/azure/provider
[lnk-az-addcomponent-command]: https://docs.microsoft.com/cli/azure/component
[lnk-az-resource-command]: https://docs.microsoft.com/cli/azure/resource
[lnk-az-iot-command]: https://docs.microsoft.com/cli/azure/iot
[lnk-iot-pricing]: https://azure.microsoft.com/pricing/details/iot-hub/
[lnk-devguide]: iot-hub-devguide.md
[lnk-portal]: iot-hub-create-through-portal.md 
