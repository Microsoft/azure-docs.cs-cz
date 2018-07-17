---
title: Nasadit aplikaci z privátního registru do Azure Service Fabric mřížky | Dokumentace Microsoftu
description: Zjistěte, jak nasadit aplikaci, která bude privátním registru kontejneru se Service Fabric pomocí Azure CLI.
services: service-fabric
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
ms.date: 07/16/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 0a70cd1bd8cd7df099250ca59b3f00b1cab29e5c
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076002"
---
# <a name="deploy-a-service-fabric-mesh-app-from-a-private-container-image-registry"></a>Nasazení aplikace Service Fabric sítě z registru image kontejneru soukromého

Tento článek ukazuje, jak nasadit aplikaci Azure Service Fabric sítě, která používá image privátní registr.

## <a name="prerequisites"></a>Požadavky

### <a name="set-up-service-fabric-mesh-cli"></a>Nastavení rozhraní příkazového řádku mřížky na Service Fabric 
K dokončení této úlohy můžete použít Azure Cloud Shell nebo místní instalaci Azure CLI. Instalace modulu Azure Service Fabric mřížky CLI rozšíření pomocí těchto [pokyny](service-fabric-mesh-howto-setup-cli.md).

### <a name="install-docker"></a>Instalace Dockeru

Nainstalujte Docker pro podporu kontejnerizovaných aplikací Service Fabric používat sítě pro Service Fabric.

### <a name="windows-10"></a>Windows 10

Stáhněte a nainstalujte nejnovější verzi [Docker Community Edition pro Windows][download-docker]. 

Během instalace, vybrat **kontejnery Windows použijte namísto kontejnery Linuxu** když se zobrazí výzva. Budete muset pak odhlásit a zase přihlásit. Po přihlášení zpět, pokud jste nepovolili dříve Hyper-V vám může zobrazit výzva k povolení technologie Hyper-V. Musíte povolit Hyper-V a restartujte počítač.

Po restartování počítače, Docker vás vyzve k zapnutí **kontejnery** funkcí, povolte ji a restartujte počítač.

### <a name="windows-server-2016"></a>Windows Server 2016

Použijte následující příkazy prostředí PowerShell k instalaci Dockeru. Další informace najdete v tématu [Docker Enterprise Edition pro Windows Server][download-docker-server].

```powershell
Install-Module DockerMsftProvider -Force
Install-Package Docker -ProviderName DockerMsftProvider -Force
Install-WindowsFeature Containers
```

Restartujte počítač.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k Azure a nastavit aktivní předplatné:

```azurecli-interactive
az login
az account set --subscription "<subscriptionName>"
```

## <a name="create-a-container-registry-and-push-an-image-to-it"></a>Vytvoření registru kontejnerů a nasdílet image do něj

Vytvořte registr kontejneru Azure podle pokynů v [vytvoření privátního registru Dockeru v Azure pomocí Azure CLI](../container-registry/container-registry-get-started-azure-cli.md). Proveďte kroky až [přihlášení do služby ACR](../container-registry/container-registry-get-started-azure-cli.md#log-in-to-acr) kroku. 

### <a name="push-image-to-acr"></a>Nasdílení image do služby ACR

Pokud chcete nasdílet image do služby Azure Container Registry, musíte nejprve mít nějakou image. Pokud ještě nemáte žádné image místního kontejneru, spusťte následující příkaz a vyžádejte si existující image z Docker Hubu.

```bash
docker pull seabreeze/azure-mesh-helloworld:1.1-alpine
```

Předtím než můžete nasdílet image do registru, musíte ji označit plně kvalifikovaným názvem přihlašovacího serveru ACR. Spuštěním následujícího příkazu získáte úplný název přihlašovacího serveru instance ACR.

```azurecli
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
```

Označte image pomocí příkazu [docker tag][docker-tag]. Nahraďte `<acrLoginServer>` názvem přihlašovacího serveru vaší instance ACR.

```bash
docker tag seabreeze/azure-mesh-helloworld:1.1-alpine <acrLoginServer>/azure-mesh-helloworld:1.1-alpine
```
### <a name="list-container-images"></a>Výpis imagí kontejnerů

Následující příklad uvádí úložiště v registru:

```azurecli
az acr repository list --name <acrName> --output table
```

Výstup:

```bash
Result
----------------
azure-mesh-helloworld
```

Následující příklad vypíše značky v **azure sítě helloworld** úložiště.

```azurecli
az acr repository show-tags --name <acrName> --repository azure-mesh-helloworld --output table
```

Výstup:

```bash
Result
--------
1.1-alpine
```

To ukazuje, že `azure-mesh-helloworld:1.1-alpine` image je k dispozici v privátní registr.

## <a name="retrieve-credentials-for-the-registry"></a>Načtení přihlašovacích údajů registru

Abyste mohli nasadit instanci kontejneru z registru, který byl vytvořen, je třeba zadat přihlašovací údaje během nasazení. Povolte uživatele s rolí správce ve svém registru pomocí následujícího příkazu:

```azurecli-interactive
az acr update --name <acrName> --admin-enabled true
```

Získáte název registru serveru, uživatelské jméno a heslo pomocí následujících příkazů:

```azurecli-interactive
az acr list --resource-group myResourceGroup --query "[].{acrLoginServer:loginServer}" --output table
az acr credential show --name <acrName> --query username
az acr credential show --name <acrName> --query "passwords[0].value"
```

Hodnoty poskytnuté předchozí příkazy se odkazuje jako `<acrLoginServer>`, `<acrUserName>`, a `<acrPassword>` v následujícím příkazu.

## <a name="deploy-the-template"></a>Nasazení šablony

Vytvoření aplikace a související prostředky, pomocí následujícího příkazu a zadejte přihlašovací údaje z předchozího kroku.

`registry-password` Parametr v šabloně `securestring`. Se nebude zobrazovat ve stavu nasazení a `az mesh service show` příkazy. Ujistěte se, že je správně zadán v následujícím příkazu.

```azurecli-interactive
az mesh deployment create --resource-group myResourceGroup --template-uri https://sfmeshsamples.blob.core.windows.net/templates/helloworld/mesh_rp.private_registry.linux.json --parameters "{\"location\": {\"value\": \"eastus\"}, \"registry-server\": {\"value\": \"<acrLoginServer>\"}, \"registry-username\": {\"value\": \"<acrUserName>\"}, \"registry-password\": {\"value\": \"<acrPassword>\"}}"
```

Za pár minut by měla vrátit příkazu pomocí:

`helloWorldPrivateRegistryApp has been deployed successfully on helloWorldPrivateRegistryNetwork with public ip address <IP Address>` 

## <a name="open-the-application"></a>Otevřete aplikaci
Po úspěšném nasazení aplikace, získejte veřejnou IP adresu pro koncový bod služby a otevřít v prohlížeči. Ji by měl zobrazení webové stránky s logem sítě pro Service Fabric.

Nasazení příkazu vrátí veřejnou IP adresu koncového bodu služby. Můžete také zadávat dotazy sítě prostředek, který chcete zjistit veřejnou IP adresu koncového bodu služby.
 
Název sítě prostředků pro tuto aplikaci je `helloWorldPrivateRegistryNetwork`, načíst informace o něm pomocí následujícího příkazu. 

```azurecli-interactive
az mesh network show --resource-group myResourceGroup --name helloWorldPrivateRegistryNetwork
```

## <a name="delete-the-resources"></a>Odstranit prostředky

Pokud chcete ušetřit omezené prostředky přiřazené do programu preview, odstraňte prostředky často. Odstranit prostředky vztahující se na tomto příkladu, odstraňte skupinu prostředků, ve které se nasadily.

```azurecli-interactive
az group delete --resource-group myResourceGroup 
```

## <a name="next-steps"></a>Další postup
- Zobrazit ukázkové aplikace Hello World na [Githubu](https://github.com/Azure-Samples/service-fabric-mesh/tree/master/src/helloworld).
- Další informace o modelu prostředků služby Service Fabric najdete v tématu [modelu služby Service Fabric mřížky prostředků](service-fabric-mesh-service-fabric-resources.md).
- Další informace o Service Fabric sítě najdete v článku [sítě pro Service Fabric přehled](service-fabric-mesh-overview.md).

[download-docker-server]: https://docs.docker.com/install/windows/docker-ee/
[download-docker]: https://store.docker.com/editions/community/docker-ce-desktop-windows
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/