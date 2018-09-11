---
title: Nasazení aplikace Service Fabric sítě z registru image kontejneru soukromého se Azure Service Fabric | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci z registru imagí kontejneru soukromého se Service Fabric pomocí Azure CLI.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: azure-cli
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/20/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: a2791c86512790f36477e562cb82718174df8dc3
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296582"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Nasazení aplikace Service Fabric sítě z registru image kontejneru soukromého

Tento článek ukazuje, jak nasadit aplikaci Azure Service Fabric sítě, která používá image privátní registr.

## <a name="prerequisites"></a>Požadavky

### <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku služby Service Fabric Mesh

Použijte místní instalaci rozhraní příkazového řádku Azure a dokončete tuto úlohu. Nainstalujte rozšiřující modul Azure Service Fabric Mesh CLI pomocí těchto [pokynů](service-fabric-mesh-howto-setup-cli.md).

## <a name="install-docker"></a>Instalace Dockeru

#### <a name="windows-10"></a>Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker] sloužící k podpoře kontejnerizovaných aplikací Service Fabric používaných službou Service Fabric Mesh.

Když se během instalace zobrazí výzva, vyberte **Use Windows containers instead of Linux containers** (Použít kontejnery Windows místo kontejnerů Linuxu). 

Pokud na vašem počítači není povolená technologie Hyper-V, v instalačním programu Dockeru se zobrazí možnost ji povolit. Po zobrazení příslušné výzvy klikněte na **OK**.

#### <a name="windows-server-2016"></a>Windows Server 2016

Pokud nemáte povolenou roli Hyper-V, otevřete PowerShell jako správce, spuštěním následujícího příkazu povolte Hyper-V a pak restartuje počítač. Další informace najdete v tématu [Docker Enterprise Edition pro Windows Server][download-docker-server].

```powershell
Install-WindowsFeature -Name Hyper-V -IncludeManagementTools
```

Restartujte počítač.

Otevřete PowerShell jako správce a spusťte následující příkaz pro instalaci Dockeru:

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavit aktivní předplatné.

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Vytvoření registru kontejnerů a nasdílet image do něj

Použijte následující postup k vytvoření služby Azure Container Registry.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. Pomocí následujícího příkazu vytvořte skupinu prostředků s názvem *myResourceGroup* v *eastus* umístění.

```azurecli
az group create --name myResourceGroup --location eastus
```

### <a name="create-a-container-registry"></a>Vytvoření registru kontejnerů

Vytvořte pomocí instanci Azure container registry (ACR) `az acr create` příkazu. Název registru musí být v rámci Azure jedinečný a musí obsahovat 5 až 50 alfanumerických znaků. V následujícím příkladu název *myContainerRegistry007* se používá. Pokud dojde k chybě, která se používá název registru, zvolte jiný název. Použijte tento název everywhere `<acrName>` se zobrazí v těchto pokynech.

```azurecli
az acr create --resource-group myResourceGroup --name myContainerRegistry007 --sku Basic
```

Po vytvoření registru se zobrazí výstup podobný následujícímu:

```json
{
  "adminUserEnabled": false,
  "creationDate": "2017-09-08T22:32:13.175925+00:00",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry007",
  "location": "eastus",
  "loginServer": "myContainerRegistry007.azurecr.io",
  "name": "myContainerRegistry007",
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

### <a name="push-the-image-to-azure-container-registry"></a>Nahrání image do služby Azure Container Registry

Pokud chcete nasdílet image do služby Azure container registry (ACR), musíte nejprve mít image kontejneru. Pokud ještě nemáte žádné Image místního kontejneru, spusťte následující příkaz, který o přijetí změn image z Docker Hubu (možná budete muset přepnout Docker pro práci s imagí Linuxu tak, že kliknete pravým tlačítkem na ikonu dockeru a vyberete **přepnout na kontejnery Linux**).

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR.

Spuštěním následujícího příkazu získejte úplný název přihlašovacího serveru vaší instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Bude odkazovat úplný název přihlašovacího serveru, který je vrácen jako `<acrLoginServer>` v celé zbývající části tohoto článku.

Nyní označit pomocí image dockeru [docker tag] [ docker-tag] příkazu. V následujícím příkazu nahraďte `<acrLoginServer>` použijte název přihlašovacího serveru hlášených výše uvedeného příkazu. Následující příklad značek seabreeze/azure síť – helloworld:1.1 – alpine image. Pokud používáte jinou image, nahraďte název image v následujícím příkazu.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Příklad: `docker tag seabreeze/azure-mesh-helloworld:1.1-alpine myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

Přihlaste se do služby Azure Container Registry.

```bash
az acr login -n <acrName>
```

Příklad: `az acr login -n myContainerRegistry007`

Nahrání image do služby azure container registry pomocí následujícího příkazu:

```bash
docker push <acrLoginServer>/seabreeze/azure-mesh-helloworld:1.1-alpine
```

Příklad: `docker push myContainerRegistry007.azurecr.io/seabreeze/azure-mesh-helloworld:1.1-alpine`

### <a name="list-container-images"></a>Výpis imagí kontejnerů

Následující příklad uvádí úložiště v registru. Následující příklady předpokládají, že používáte azure-mřížky-helloworld:1.1 – alpine image. Pokud používáte jinou image, nahraďte jeho název, kde se používá image azure sítě helloworld.

```azurecli
az acr repository list --name <acrName> --output table
```
Příklad: `az acr repository list --name myContainerRegistry007 --output table`

Výstup:

```bash
Result
-------------------------------
seabreeze/azure-mesh-helloworld
```

Následující příklad vypíše značky v **azure sítě helloworld** úložiště.

```azurecli
az acr repository show-tags --name <acrName> --repository seabreeze/azure-mesh-helloworld --output table
```

Příklad: `az acr repository show-tags --name myContainerRegistry007 --repository seabreeze/azure-mesh-helloworld --output table`

Výstup:

```bash
Result
--------
1.1-alpine
```

Tento výstup potvrzuje přítomnost `azure-mesh-helloworld:1.1-alpine` v privátní registr.

## <a name="retrieve-credentials-for-the-registry"></a>Načtení přihlašovacích údajů registru

> [!IMPORTANT]
> Povolení uživatele s rolí správce ve službě Azure container registry se nedoporučuje pro produkční scénáře. Zde je použito, aby tato ukázka (BRIEF). Pro produkční scénáře použití [instanční objekt služby](https://docs.microsoft.com/azure/container-registry/container-registry-auth-service-principal) pro uživatele a systémové ověřování v produkčních scénářích.

Abyste mohli nasadit instanci kontejneru z registru, který byl vytvořen, je třeba zadat pověření během nasazení. Povolte uživatele s rolí správce ve svém registru pomocí následujícího příkazu:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Příklad: `az acr update --name myContainerRegistry007 --admin-enabled true`

Získáte název registru serveru, uživatelské jméno a heslo pomocí následujících příkazů:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Hodnoty poskytnuté před příkazy jsou odkazovány jako `<acrLoginServer>`, `<acrUserName>`, a `<acrPassword>` níže.

## <a name="deploy-the-template"></a>Nasazení šablony

Vytvoření aplikace a související prostředky, pomocí následujícího příkazu a zadejte přihlašovací údaje z předchozího kroku.

`registry-password` Parametr v šabloně je zabezpečený řetězec. Se nebude zobrazovat ve stavu nasazení a `az mesh service show` příkazy.

Pokud používáte konzolu Bash, spusťte následující příkaz:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

Pokud používáte konzolu PowerShellu, spusťte následující příkaz:

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{'location': {'value': 'eastus'}, 'registry-server': {'value': '<acrLoginServer>'}, 'registry-username': {'value': '<acrUserName>'}, 'registry-password': {'value': '<acrPassword>'}}"
```

Během několika minut byste měli vidět:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>`

## <a name="open-the-application"></a>Otevření aplikace

Po úspěšném nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřít v prohlížeči. Zobrazí se webová stránka s logem sítě pro Service Fabric.

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Volitelně můžete také zadávat dotazy sítě prostředek, který chcete zjistit veřejnou IP adresu koncového bodu služby. 
 
Název sítě prostředků pro tuto aplikaci je `helloWorldPrivateRegistryNetwork`, načíst informace o něm pomocí následujícího příkazu. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Odstranit prostředky

Často odstraníte prostředky, které už používáte v Azure. Pokud chcete odstranit prostředky související se v tomto příkladu, odstraňte skupinu prostředků ve které se nasadily (čímž odstraníte všechno, co spojený s vybranou skupinou prostředků) pomocí následujícího příkazu:

```azurecli-interactive
az group delete --resource-group myResourceGroup
```

Pokud jste přešli Docker pracovat s imagí Linuxu pro toto cvičení, a potřebují mít možnost Přejít zpět k práci s imagemi Windows, klikněte pravým tlačítkem na ikonu dockeru a vyberte **přepnout na kontejnery Windows**

## <a name="next-steps"></a>Další postup

- Zobrazit ukázkové aplikace Hello World na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/