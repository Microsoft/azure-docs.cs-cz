---
title: Kurz – Nasazení aplikace do služby Azure Service Fabric Mesh | Microsoft Docs
description: V tomto kurzu se dozvíte, jak nasadit aplikaci do služby Service Fabric Mesh pomocí šablony.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/11/2019
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 97b1efbcb02277028782764ca1018b195ab21277
ms.sourcegitcommit: f4b78e2c9962d3139a910a4d222d02cda1474440
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2019
ms.locfileid: "54246360"
---
# <a name="tutorial-deploy-an-application-to-service-fabric-mesh-using-a-template"></a>Kurz: Nasazení aplikace do služby Service Fabric Mesh pomocí šablony

Tento kurz je první částí série. Dozvíte se, jak nasadit aplikaci Azure Service Fabric Mesh pomocí šablony.  Aplikace se skládá z webové front-end služby ASP.NET a back-endové služby s webovým rozhraním API ASP.NET Core, které najdete v Docker Hubu.  Tyto dvě image kontejneru si stáhnete z Docker Hubu a pak je nasdílíte do vlastního privátního registru. Pak pro aplikaci vytvoříte šablonu Azure Resource Manageru a nasadíte aplikaci ze svého registru kontejneru do služby Service Fabric Mesh. Jakmile budete hotovi, budete mít jednoduchou aplikaci seznamu úkolů spuštěnou ve službě Service Fabric Mesh.

V první části této série se naučíte:

> [!div class="checklist"]
> * Vytvoření privátní instance služby Azure Container Registry
> * Nasdílení image kontejneru do registru
> * Vytvoření šablony Resource Manageru a souborů parametrů
> * Nasazení aplikace do služby Service Fabric Mesh

V této sérii kurzů se naučíte:
> [!div class="checklist"]
> * Nasazení aplikace do služby Service Fabric Mesh pomocí šablony
> * [Škálování služeb v aplikaci spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
> * [Upgrade aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-upgrade-app.md)
> * [Odebrání aplikace](service-fabric-mesh-tutorial-template-remove-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Požadavky

Než začnete s tímto kurzem:

* Pokud ještě nemáte předplatné Azure, můžete si [vytvořit bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

* [Instalace Dockeru](service-fabric-mesh-howto-setup-developer-environment-sdk.md#install-docker)

* [Nainstalujte si místně Azure CLI a Service Fabric Mesh CLI](service-fabric-mesh-howto-setup-cli.md#install-the-azure-service-fabric-mesh-cli).

## <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Image kontejneru přidružené k službám ve vaší aplikaci Service Fabric Mesh musí být uložené v registru kontejneru.  V tomto kurzu se používá privátní instance služby Azure Container Registry (ACR). 

Pomocí následujících kroků vytvořte instanci služby ACR.  Pokud už máte nastavenou instanci služby ACR, můžete tento krok přeskočit.

### <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k Azure a nastavte aktivní předplatné.

```azurecli
az login
az account set --subscription "<subscriptionName>"
```

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí následujícího příkazu vytvořte skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-the-container-registry"></a>Vytvoření registru kontejneru

Pomocí příkazu `az acr create` vytvořte instanci služby ACR. Název registru musí být jedinečný v rámci Azure a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu se používá název *myContainerRegistry*. Pokud dojde k chybě kvůli tomu, že se název registru už používá, zvolte jiný název.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry --sku Basic
```

Po vytvoření registru se zobrazí výstup podobný tomuto:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2018-09-13T19:43:57.388203+00:00",
  "id": "/subscriptions/<subscription>/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry",
  "location": "eastus",
  "loginServer": "mycontainerregistry.azurecr.io",
  "name": "myContainerRegistry",
  "provisioningState": "Succeeded",
  "resourceGroup": "myResourceGroup",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "status": null,
  "storageAccount": null,
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

## <a name="push-the-images-to-azure-container-registry"></a>Nasdílení imagí do služby Azure Container Registry

V tomto kurzu se jako příklad používá ukázková aplikace seznamu úkolů.  Image kontejneru pro služby [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) a [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/) najdete na Docker Hubu. Informace o vytvoření aplikace v sadě Visual Studio najdete v tématu [Vytvoření webové aplikace Service Fabric Mesh](service-fabric-mesh-tutorial-create-dotnetcore.md). Service Fabrice Mesh podporuje spouštění kontejnerů Dockeru pro Windows nebo Linux.  Pokud pracujete s kontejnery Linuxu, vyberte v Dockeru **Switch to Linux containers** (Přepnout na kontejnery Linuxu).  Pokud pracujete s kontejnery Windows, vyberte v Dockeru **Switch to Windows containers** (Přepnout na kontejnery Windows).

Pokud chcete nasdílet image do instance služby ACR, musíte nejprve mít image kontejneru. Pokud ještě nemáte žádné místní image kontejneru, pomocí příkazu [docker pull](https://docs.docker.com/engine/reference/commandline/pull/) si z Docker Hubu stáhněte image [WebFrontEnd](https://hub.docker.com/r/seabreeze/azure-mesh-todo-webfrontend/) a [ToDoService](https://hub.docker.com/r/seabreeze/azure-mesh-todo-service/).

Stažení imagí pro Windows:

```bash
docker pull seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker pull seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR.

Spuštěním následujícího příkazu získáte úplný název přihlašovacího serveru instance služby ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table

AcrLoginServer
---------------------------------
mycontainerregistry.azurecr.io
```

Teď image Dockeru označte pomocí příkazu [docker tag](https://docs.docker.com/engine/reference/commandline/tag/). Následující příklad označí image seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 a seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709. Pokud používáte jiné image, v následujícím příkazu použijte názvy těchto imagí.

```bash
docker tag seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker tag seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709 mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

Přihlaste se ke službě Azure Container Registry.

```azurecli
az acr login -n myContainerRegistry
```

Pomocí příkazu [docker push](https://docs.docker.com/engine/reference/commandline/push/) nasdílejte image do instance služby ACR:

```bash
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709
docker push mycontainerregistry.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709
```

### <a name="list-container-images"></a>Výpis imagí kontejnerů

Pokud chcete zobrazit úložiště ve vaší instanci služby ACR, spusťte následující příkaz:

```azurecli
az acr repository list --name myContainerRegistry --output table

Result
-------------------------------
seabreeze/azure-mesh-todo-webfrontend
seabreeze/azure-mesh-todo-service
```

Následující příklad vypíše značky v úložišti **azure-mesh-todo-service**.

```azurecli
az acr repository show-tags --name myContainerRegistry --repository seabreeze/azure-mesh-todo-service --output table

Result
--------
1.0-nanoserver-1709
```

Předchozí výstup potvrzuje přítomnost značky `azure-mesh-todo-service:1.0-nanoserver-1709` v privátním registru kontejneru.  Ověřte také přítomnost značky `azure-mesh-todo-webfrontend:1.0-nanoserver-1709`.

## <a name="retrieve-credentials-for-the-registry"></a>Načtení přihlašovacích údajů registru

> [!IMPORTANT]
> Pro produkční scénáře se nedoporučuje povolovat v instanci služby ACR uživatele s rolí správce. Tady se to dělá kvůli usnadnění práce. K ověřování uživatelů i systému v produkčních scénářích použijte [instanční objekt](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal).

Abyste mohli nasadit instanci kontejneru z registru vytvořeného pomocí šablony, musíte při nasazování zadat přihlašovací údaje registru. Nejprve ve svém registru pomocí následujícího příkazu povolte uživatele s rolí správce:

```azurecli
az acr update --name myContainerRegistry --admin-enabled true
```

Pak pomocí následujícího příkazu získejte název přihlašovacího serveru registru, uživatelské jméno a heslo:

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name myContainerRegistry --query username
az acr credential show --name myContainerRegistry --query "passwords[0].value"
```

Vrácené hodnoty názvu přihlašovacího serveru ACR, uživatelského jména a hesla použijte při vytváření šablony Resource Manageru a souborů parametrů v následující části.

## <a name="download-and-explore-the-template-and-parameters-files"></a>Stažení a prozkoumání šablony a souborů parametrů

Aplikace Service Fabric Mesh je prostředek Azure, který můžete nasadit a spravovat pomocí šablon Azure Resource Manageru. Pokud neznáte koncepty nasazování a správy řešení Azure, přečtěte si témata [Přehled Azure Resource Manageru](/azure/azure-resource-manager/resource-group-overview) a [Vysvětlení struktury a syntaxe šablon Resource Manageru](/azure/azure-resource-manager/resource-group-authoring-templates).

V tomto kurzu se jako příklad používá ukázka Seznam úkolů.  Místo vytváření nové šablony a souborů parametrů si stáhněte[šablonu nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) a soubory [parametrů mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).

### <a name="parameters"></a>Parametry
Pokud jsou v šabloně hodnoty, které plánujete po nasazení aplikace změnit, nebo které byste chtěli mít možnost změnit pro každé nasazení (pokud plánujete opakované použití této šablony pro další nasazení), je vhodné tyto hodnoty parametrizovat. Správným způsobem, jak to provést, je vytvořit na začátku šablony nasazení část parameters, kde zadáte názvy a vlastnosti parametrů, na které se pak bude odkazovat ve zbytku šablony nasazení. Každá definice parametru obsahuje hodnoty *type* a *defaultValue* a volitelnou část *metadata* s hodnotou *description*.

Část parameters se definuje na začátku šablony nasazení přímo před částí *resources*:

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",
    "parameters": {
      ...
    },
    "resources": [
```

V [šabloně nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json) se deklarují následující parametry: location, registryPassword, registryUserName, registryServer, frontEndImage, serviceImage, frontEndCpu, serviceCpu, frontEndMemory, serviceMemory, frontEndReplicaCount a serviceReplicaCount.  Popis jednotlivých parametrů najdete v souboru šablony nasazení.

Tyto parametry se používají v souboru [parametrů mesh_rp.windows.parameter.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.parameters.json).  Použitím samostatného souboru parametrů získáte možnost měnit hodnoty parametrů pro jednotlivá nasazení, aniž byste museli aktualizovat samotnou šablonu nasazení.

### <a name="overview-of-the-application-and-services"></a>Přehled aplikace a služeb

Služby se v šabloně určují jako vlastnosti prostředku aplikace.  Aplikace se nasazují do privátní sítě, která se v šabloně deklaruje jako prostředek.  Služby můžou k uchovávání dat používat svazky, které se v šabloně deklarují jako prostředky.  Pro každou službu se jako vlastnosti služby určuje typ operačního systému, balíčky kódu, počet replik a síť.  Pro každý balíček kódu zadejte image kontejneru, koncové body, prostředky paměti a CPU a přihlašovací údaje k úložišti imagí. Základní šablona pro aplikaci Service Fabric Mesh s několika službami vypadá nějak takto:

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
  "contentVersion": "1.0.0.0",
  "parameters": {
    ...
  },
  "resources": [
    {
      "apiVersion": "2018-09-01-preview",
      "name": "MyMeshApplication",
      "type": "Microsoft.ServiceFabricMesh/applications",
      "location": "[parameters('location')]",
      "dependsOn": [
        "Microsoft.ServiceFabricMesh/networks/MeshAppNetwork",
        "Microsoft.ServiceFabricMesh/volumes/ServiceAVolume"
      ],
      "properties": {
        "services": [
          {
            "name": "ServiceA",
            "properties": {
              "description": "ServiceA description.",
              "osType": "Linux",
              "codePackages": [
                {
                  "name": "ServiceA",
                  "image": "[parameters('frontEndImage')]",
                  "volumeRefs": [
                    {
                      "name": "[resourceId('Microsoft.ServiceFabricMesh/volumes', 'ServiceAVolume')]",
                      "destinationPath": "/app/data"
                    }
                  ],
                  "endpoints": [
                    {
                      "name": "ServiceAListener",
                      "port": 20001
                    }
                  ],
                  "resources": {
                    "requests": {
                      "cpu": "[parameters('frontEndCpu')]",
                      "memoryInGB": "[parameters('frontEndMemory')]"
                    }
                  },
                  "imageRegistryCredential": {
                    "server": "[parameters('registryServer')]",
                    "username": "[parameters('registryUserName')]",
                    "password": "[parameters('registryPassword')]"
                  }
                }
              ],
              "replicaCount": "[parameters('frontEndReplicaCount')]",
              "networkRefs": [
                {
                  "name": "[resourceId('Microsoft.ServiceFabricMesh/networks', 'MeshAppNetwork')]"
                }
              ]
            }
          },
          {
            "name": "ServiceB",
            ...
          }
        ],
        "description": "Application description."
      }
    },
    {
      "apiVersion": "2018-07-01-preview",
      "name": "MeshAppNetwork",
      "type": "Microsoft.ServiceFabricMesh/networks",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "MeshAppNetwork description.",
        "addressPrefix": "10.0.0.4/22",
        "ingressConfig": {
          "layer4": [
            {
              "name": "ServiceAIngress",
              "publicPort": "20001",
              "applicationName": "MyMeshApplication",
              "serviceName": "ServiceA",
              "endpointName": "ServiceAListener"
            }
          ]
        }
      }
    },
    {
      "apiVersion": "2018-09-01-preview",
      "name": "ServiceAVolume",
      "type": "Microsoft.ServiceFabricMesh/volumes",
      "location": "[parameters('location')]",
      "dependsOn": [],
      "properties": {
        "description": "Azure Files storage volume for counter App.",
        "provider": "SFAzureFile",
        "azureFileParameters": {
          "shareName": "[parameters('fileShareName')]",
          "accountName": "[parameters('storageAccountName')]",
          "accountKey": "[parameters('storageAccountKey')]"
        }
      }
    }
  ]
}
```

Specifika aplikace seznamu úkolů najdete v souboru [šablony nasazení mesh_rp.windows.json](https://github.com/Azure-Samples/service-fabric-mesh/blob/master/templates/todolist/mesh_rp.windows.json).

## <a name="deploy-the-application-to-service-fabric-mesh"></a>Nasazení aplikace do služby Service Fabric Mesh
Pomocí následujícího příkazu vytvořte aplikaci a související prostředky. Použijte přihlašovací údaje z předchozího kroku [Načtení přihlašovacích údajů registru](#retrieve-credentials-for-the-registry).

V souboru parametrů aktualizujte hodnoty následujících parametrů:
|Parametr|Hodnota|
|---|---|
|location|Oblast, do které se má aplikace nasadit.  Příklad: eastus|
|registryPassword|Heslo, které jste získali dříve v části [Načtení přihlašovacích údajů registru](#retrieve-credentials-for-the-registry). Tento parametr je v šabloně zabezpečeným řetězcem a nezobrazí se ve stavu nasazení ani v příkazech `az mesh service show`.|
|registryUserName|Uživatelské jméno, které jste získali v části [Načtení přihlašovacích údajů registru](#retrieve-credentials-for-the-registry).|
|registryServer|Název serveru registru, který jste získali v části [Načtení přihlašovacích údajů registru](#retrieve-credentials-for-the-registry).|
|frontEndImage|Image kontejneru pro front-endovou službu.  Příklad: <myregistry>.azurecr.io/seabreeze/azure-mesh-todo-webfrontend:1.0-nanoserver-1709|
|serviceImage|Image kontejneru pro back-endovou službu.  Příklad: <myregistry>.azurecr.io/seabreeze/azure-mesh-todo-service:1.0-nanoserver-1709|

Aplikaci nasadíte spuštěním následujícího příkazu:

```azurecli
az mesh deployment create --resource-group myResourceGroup --template-file c:\temp\mesh_rp.windows.json --parameters c:\temp\mesh_rp.windows.parameters.json
```

Tento příkaz vytvoří fragment kódu JSON, který je uveden níže. V části ```outputs``` část výstup ve formátu JSON, zkopírujte ```publicIPAddress``` vlastnost.

```json
"outputs": {
    "publicIPAddress": {
    "type": "String",
    "value": "40.83.78.216"
    }
}
```

Tyto informace pocházejí z ```outputs``` části šablony ARM. Jak je znázorněno níže, tato část odkazuje na prostředek brány, který chcete načíst veřejnou IP adresu. 

```json
  "outputs": {
    "publicIPAddress": {
      "value": "[reference('todolistappGateway').ipAddress]",
      "type": "string"
    }
  }
```

## <a name="open-the-application"></a>Otevření aplikace

Po úspěšném nasazení aplikace získejte veřejnou IP adresu koncového bodu služby. Veřejnou IP adresu koncového bodu služby vrátí příkaz pro nasazení. Volitelně můžete veřejnou IP adresu koncového bodu služby zjistit také odesláním dotazu na síťový prostředek. Název síťového prostředku pro tuto aplikaci je `todolistappNetwork`. Pomocí následujícího příkazu načtěte informace o tomto prostředku. 

```azurecli
az mesh gateway show --resource-group myResourceGroup --name todolistappGateway
```

Přejděte na IP adresu ve webovém prohlížeči.

## <a name="check-application-status"></a>Kontrola stavu aplikace

Stav aplikace můžete zkontrolovat pomocí příkazu app show. Název nasazené aplikace je todolistapp, načtěte tedy její podrobnosti.

```azurecli
az mesh app show --resource-group myResourceGroup --name todolistapp
```

Projděte si protokoly nasazené aplikace pomocí příkazu `az mesh code-package-log get`:
```azurecli
az mesh code-package-log get --resource-group myResourceGroup --application-name todolistapp --service-name WebFrontEnd --replica-name 0 --code-package-name WebFrontEnd
```

## <a name="next-steps"></a>Další postup

V této části kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření privátního registru kontejneru
> * Nasdílení image kontejneru do registru
> * Vytvoření šablony a souboru parametrů
> * Nasazení aplikace do služby Service Fabric Mesh

Přejděte k dalšímu kurzu:
> [!div class="nextstepaction"]
> [Škálování aplikace spuštěné ve službě Service Fabric Mesh](service-fabric-mesh-tutorial-template-scale-services.md)
