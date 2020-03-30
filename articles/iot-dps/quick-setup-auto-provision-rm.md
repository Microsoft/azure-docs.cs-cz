---
title: Nastavení zřizování zařízení Azure IoT Hub pomocí šablony Azure Resource Manager
description: Azure quickstart – nastavení služby Azure IoT Hub Device Provisioning Service (DPS) pomocí šablony
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 482401b75cadf44e2cef03cced8dd216d0980524
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "74969577"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-an-azure-resource-manager-template"></a>Úvodní příručka: Nastavení služby zřizování zařízení služby IoT Hub pomocí šablony Správce prostředků Azure

Pomocí [Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) můžete prostřednictvím kódu programu nastavit cloudové prostředky Azure potřebné ke zřizování zařízení. Tyto kroky ukazují, jak vytvořit centrum IoT a novou službu zřizování zařízení služby IoT Hub a propojit obě služby pomocí šablony Azure Resource Manager. Tento rychlý start používá [rozhraní PŘÍKAZKa Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-cli) k provedení programových kroků nezbytných k vytvoření skupiny prostředků a nasazení šablony, ale můžete snadno použít [portál Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy-portal), [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-deploy), .NET, Ruby nebo jiné programovací jazyky k provedení těchto kroků a nasazení šablony. 


## <a name="prerequisites"></a>Požadavky

- Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.
- Tento rychlý start vyžaduje, abyste místní spouštěli nastavení příkazového příkazu Konzumu Azure. Musíte mít nainstalovanou verzi Azure CLI 2.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete nainstalovat nebo upgradovat rozhraní příkazového řádku (CLI), přečtěte si článek [Instalace Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).


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

2. Část **parameters** (parametry) nahraďte následujícím obsahem. Část parametrů definuje parametry, jejichž hodnoty mohou být předány z jiného souboru. Tato část definuje název služby IoT hub a zřizovací služby, kterou chcete vytvořit. Také definuje umístění pro službu IoT hub a zřizovací službu. Hodnoty budou omezeny na oblasti Azure, které podporují centra IoT a zřizovací služby. Seznam podporovaných umístění služby Device Provisioning můžete zobrazit spuštěním příkazu `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table` nebo můžete přejít na stránku [Stav Azure](https://azure.microsoft.com/status/) a vyhledat službu Device Provisioning.

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

4. Centrum IoT vytvoříte přidáním následujících řádků do kolekce **resources** (prostředky). JSON určuje minimální vlastnosti potřebné k vytvoření služby IoT hub. Hodnoty **názvu** a **umístění** budou předány jako parametry z jiného souboru. Další informace o vlastnostech, které můžete zadat pro centrum IoT v šabloně, najdete v [tématu Microsoft.Devices/IotHubs odkaz na šablonu](https://docs.microsoft.com/azure/templates/microsoft.devices/iothubs).

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

5. Službu zřizování vytvoříte přidáním následujících řádků za specifikaci centra IoT v kolekci **resources** (prostředky). **Název** a **umístění** zřizovací služby budou předány jako parametry. Kolekce **iotHubs** určuje služby IoT huby pro propojení se službou zřizování. Pro každé propojené centrum IoT musíte zadat minimálně vlastnosti **connectionString** (připojovací řetězec) a **location** (umístění). Pro každé centrum IoT můžete nastavit také vlastnosti jako **allocationWeight** (váha přidělení) a **applyAllocationPolicy** (použít zásady přidělování) a pro samotnou službu zřizování můžete nastavit vlastnosti jako **allocationPolicy** (zásady přidělování) a **authorizationPolicies** (zásady autorizace). Další informace najdete v [referenčních informacích k šabloně Microsoft.Devices/provisioningServices](https://docs.microsoft.com/azure/templates/microsoft.devices/provisioningservices).

   Vlastnost **dependsOn** slouží k zajištění, že Resource Manager vytvoří nejprve centrum IoT a až pak službu zřizování. Šablona vyžaduje připojovací řetězec centra IoT určující jeho propojení se službou zřizování, takže je nejprve potřeba vytvořit centrum a jeho klíče. Šablona používá funkce jako **concat** a **listKeys** k vytvoření připojovacího řetězce z parametrizovaných proměnných. Další informace najdete v tématu [Funkce šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-template-functions).

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

Šablona, kterou jste definovali v posledním kroku, používá parametry k určení názvu centra IoT, názvu zřizovací služby a umístění (oblast Azure) k jejich vytvoření. Tyto parametry předáte do šablony ze samostatného souboru. Tímto způsobem můžete stejnou šablonu znovu použít pro více nasazení. Soubor parametrů vytvoříte podle následujícího postupu:

1. Pomocí textového editoru vytvořte soubor parametrů Azure Resource Manageru **parameters.json** s následující kostrou obsahu: 

   ```json
   {
       "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
       "contentVersion": "1.0.0.0",
       "parameters": {}
       }
   }
   ```

2. Do části s parametry přidejte hodnotu **iotHubName** (název centra IoT).  Název centra IoT musí být v Azure globálně jedinečný, takže můžete k názvu příkladu přidat jedinečnou předponu nebo příponu nebo zvolit úplně nový název. Ujistěte se, že vaše jméno se řídí správné konvence pojmenování pro centrum IoT: by měla být 3-50 znaků na délku a může obsahovat pouze velká nebo malá písmena alfanumerické znaky nebo pomlčky ('-'). 

   ```json
    "parameters": {
        "iotHubName": {
            "value": "my-sample-iot-hub"
        },
    }
   
   ```

3. Do části s parametry přidejte hodnotu **provisioningServiceName** (název služby zřizování). Budete také muset zvolit globálně jedinečný název pro službu zřizování. Ujistěte se, že se řídí správné konvence pojmenování pro službu zřizování zařízení služby IoT Hub: měla by mít délku 3 až 64 znaků a může obsahovat pouze velká nebo malá alfanumerická písmena nebo pomlčky ('-").

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

4. Do části s parametry přidejte hodnotu **hubLocation** (umístění centra). Tato hodnota určuje umístění centra IoT i služby zřizování. Hodnota musí odpovídat jednomu z umístění zadaných v kolekci **allowedValues** (povolené hodnoty) v definici parametru v souboru šablony. Tato kolekce omezuje hodnoty na umístění Azure, které podporují centra IoT i služby zřizování. Seznam podporovaných umístění pro službu zřizování `az provider show --namespace Microsoft.Devices --query "resourceTypes[?resourceType=='ProvisioningServices'].locations | [0]" --out table`zařízení můžete spustit příkaz nebo přejít na stránku [Stav Azure](https://azure.microsoft.com/status/) a hledat v části Služba zřizování zařízení.

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

1. Chcete-li šablonu nasadit, přejděte do složky obsahující soubory šablon a parametrů a spusťte následující [příkaz pro spuštění nasazení](https://docs.microsoft.com/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):
    
    ```azurecli
     az group deployment create -g {your resource group name} --template-file template.json --parameters @parameters.json
    ```

   Tato operace může trvat několik minut. Jakmile je hotovo, vyhledejte **vlastnost provisioningState** zobrazující "Succeeded" ve výstupu. 

   ![Výstup zřizování](./media/quick-setup-auto-provision-rm/output.png) 


2. Pokud chcete nasazení ověřit, spusťte následující [příkaz pro výpis prostředků](https://docs.microsoft.com/cli/azure/resource?view=azure-cli-latest#az-resource-list) a ve výstupu vyhledejte novou službu zřizování a centrum IoT:

    ```azurecli
     az resource list -g {your resource group name}
    ```


## <a name="clean-up-resources"></a>Vyčištění prostředků

Další rychlé starty v této kolekci jsou postavené na tomto rychlém startu. Pokud chcete pokračovat v práci s dalšími rychlými starty nebo kurzy, nevyčišťujte prostředky vytvořené v rámci tohoto rychlého startu. Pokud nemáte v plánu pokračovat, můžete použít Azure CLI odstranit [jednotlivé prostředky][lnk-az-resource-command], jako je například služby IoT hub nebo zřizovací služby, nebo odstranit skupinu prostředků a všechny jeho prostředky.

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

Můžete také odstranit skupiny prostředků a jednotlivé prostředky pomocí azure portálu, PowerShellu nebo REST API, stejně jako s podporovanými platformami SDK publikovanými pro Azure Resource Manager nebo Službu zřizování zařízení Služby IoT Hub.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste nasadili službu IoT hub a instanci služby Zřizování zařízení a propojili jste tyto dva prostředky. Chcete-li se dozvědět, jak pomocí tohoto nastavení zřídit simulované zařízení, pokračujte na rychlý start pro vytvoření simulované zařízení.

> [!div class="nextstepaction"]
> [Rychlý start pro vytvoření simulovaného zařízení](./quick-create-simulated-device.md)


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
