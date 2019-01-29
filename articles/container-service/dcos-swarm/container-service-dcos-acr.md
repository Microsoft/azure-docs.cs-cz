---
title: (NEPOUŽÍVANÉ) Použití služby ACR s clusterem Azure DC/OS
description: Použití služby Azure Container Registry s clusterem DC/OS ve službě Azure Container Service
services: container-service
author: julienstroheker
manager: dcaro
ms.service: container-service
ms.topic: tutorial
ms.date: 03/23/2017
ms.author: juliens
ms.custom: mvc
ms.openlocfilehash: 8319f2f5405271679d0c11d4ac68492cdec8fc14
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100117"
---
# <a name="deprecated-use-acr-with-a-dcos-cluster-to-deploy-your-application"></a>(NEPOUŽÍVANÉ) Použití služby ACR s clusterem DC/OS k nasazení aplikace

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

V tomto článku se podíváme na to, jak používat službu Azure Container Registry s clusterem DC/OS. ACR umožňuje privátní ukládání a správu imagí kontejneru. Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Nasazení služby Azure Container Registry (v případě potřeby)
> * Konfigurace ověřování služby ACR v clusteru DC/OS
> * Odeslání image do služby Azure Container Registry
> * Spuštění image kontejneru ze služby Azure Container Registry

K provedení kroků v tomto kurzu potřebujete cluster DC/OS ACS. V případě potřeby si ho můžete nechat vytvořit pomocí [tohoto ukázkového skriptu](./../kubernetes/scripts/container-service-cli-deploy-dcos.md).

Tento kurz vyžaduje Azure CLI verze 2.0.4 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete upgradovat, přečtěte si článek [Instalace Azure CLI]( /cli/azure/install-azure-cli). 

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="deploy-azure-container-registry"></a>Nasazení služby Azure Container Registry

V případě potřeby vytvořte službu Azure Container Registry pomocí příkazu [az acr create](/cli/azure/acr#az-acr-create). 

Následující příklad vytvoří registr s náhodně vygenerovaným názvem. Registr má také prostřednictvím argumentu `--admin-enabled` nakonfigurovaný účet správce.

```azurecli-interactive
az acr create --resource-group myResourceGroup --name myContainerRegistry$RANDOM --sku Basic
```

Po vytvoření registru rozhraní Azure CLI vytvoří výstup, který obsahuje zhruba následující data. Poznamenejte si položky `name` a `loginServer`, ty se totiž používají v dalších krocích.

```azurecli
{
  "adminUserEnabled": false,
  "creationDate": "2017-06-06T03:40:56.511597+00:00",
  "id": "/subscriptions/f2799821-a08a-434e-9128-454ec4348b10/resourcegroups/myResourceGroup/providers/Microsoft.ContainerRegistry/registries/myContainerRegistry23489",
  "location": "eastus",
  "loginServer": "mycontainerregistry23489.azurecr.io",
  "name": "myContainerRegistry23489",
  "provisioningState": "Succeeded",
  "sku": {
    "name": "Basic",
    "tier": "Basic"
  },
  "storageAccount": {
    "name": "mycontainerregistr034017"
  },
  "tags": {},
  "type": "Microsoft.ContainerRegistry/registries"
}
```

Pomocí příkazu [az acr credential show](/cli/azure/acr/credential) zjistěte přihlašovací údaje registru kontejneru. Položku `--name` nahraďte údajem, který jste si poznamenali v předchozím kroku. Poznamenejte si heslo, budete ho potřebovat v pozdějším kroku.

```azurecli-interactive
az acr credential show --name myContainerRegistry23489
```

Další informace o službě Azure Container Registry najdete v článku [Úvod do privátních registrů kontejneru Dockeru](../../container-registry/container-registry-intro.md). 

## <a name="manage-acr-authentication"></a>Správa ověřování služby ACR

Běžný způsob nabízené a vyžádané instalace image z privátního registru začíná ověřením v daném registru. V klientovi, který potřebuje získat přístup k privátnímu registru, byste k tomu použili příkaz `docker login`. Jelikož ale může cluster DC/OS obsahovat mnoho uzlů, z nichž všechny potřebují provést ověření ve službě ACR, je užitečné tento proces ve všech uzlech automatizovat. 

### <a name="create-shared-storage"></a>Vytvoření sdíleného úložiště

Tento proces využívá sdílenou složku Azure vytvořenou v každém uzlu clusteru. Pokud jste ještě sdílené úložiště nevytvořili, projděte si článek [Vytvoření sdílené složky v clusteru DC/OS](container-service-dcos-fileshare.md).

### <a name="configure-acr-authentication"></a>Konfigurace ověřování služby ACR

Nejdřív získejte plně kvalifikovaný název domény předlohy clusteru DC/OS a uložte ho jako proměnnou.

```azurecli-interactive
FQDN=$(az acs list --resource-group myResourceGroup --query "[0].masterProfile.fqdn" --output tsv)
```

Vytvořte připojení SSH k této předloze (nebo první předloze) clusteru založeného na DC/OS. Pokud se při vytváření clusteru použila jiná než výchozí hodnota, aktualizujte uživatelské jméno.

```azurecli-interactive
ssh azureuser@$FQDN
```

Přihlaste se do služby Azure Container Registry pomocí následujícího příkazu. Položku `--username` nahraďte názvem registru kontejneru a položku `--password` jedním ze zadaných hesel. Argument *mycontainerregistry.azurecr.io* v příkladu nahraďte názvem serveru loginServer registru kontejneru. 

Tento příkaz uloží hodnoty ověřování do cesty `~/.docker`.

```azurecli-interactive
docker -H tcp://localhost:2375 login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry.azurecr.io
```

Vytvořte komprimovaný soubor, který obsahuje hodnoty ověřování registru kontejneru.

```azurecli-interactive
tar czf docker.tar.gz .docker
```

Zkopírujte tento soubor do sdíleného úložiště v clusteru. Tímto krokem soubor zpřístupníte ve všech uzlech v clusteru DC/OS.

```azurecli-interactive
cp docker.tar.gz /mnt/share/dcosshare
```

## <a name="upload-image-to-acr"></a>Odeslání image do služby ACR

Teď na vývojářském počítači nebo v jakémkoli jiném systému s nainstalovaným Dockerem vytvořte image a odešlete ji do služby Azure Container Registry.

Vytvořte kontejner z image Ubuntu.

```azurecli-interactive
docker run ubuntu --name base-image
```

Potom kontejner zachyťte do nové image. Název image musí obsahovat název `loginServer` registru kontejneru ve formátu `loginServer/imageName`.

```azurecli-interactive
docker -H tcp://localhost:2375 commit base-image mycontainerregistry30678.azurecr.io/dcos-demo
```

Přihlášení do služby Azure Container Registry Nahraďte název názvem serveru loginServer, položku --username názvem registru kontejneru a položku --password jedním ze zadaných hesel.

```azurecli-interactive
docker login --username=myContainerRegistry23489 --password=//=ls++q/m+w+pQDb/xCi0OhD=2c/hST mycontainerregistry2675.azurecr.io
```

Nakonec odešlete image do registru služby ACR. Tento příklad odešle image s názvem dcos-demo.

```azurecli-interactive
docker push mycontainerregistry30678.azurecr.io/dcos-demo
```

## <a name="run-an-image-from-acr"></a>Spuštění image ze služby ACR

Pokud chcete použít některou image z registru služby ACR, vytvořte soubor s názvem *acrDemo.json* a zkopírujte do něj následující text. Nahraďte název image názvem loginServer registru kontejneru a názvem image, například `loginServer/imageName`. Poznamenejte si vlastnost `uris`. Tato vlastnost obsahuje umístění souboru ověřování registru kontejneru, kterým je v tomto případě sdílená složka Azure vytvořená v každém uzlu clusteru DC/OS.

```json
{
  "id": "myapp",
  "container": {
    "type": "DOCKER",
    "docker": {
      "image": "mycontainerregistry30678.azurecr.io/dcos-demo",
      "network": "BRIDGE",
      "portMappings": [
        {
          "containerPort": 80,
          "hostPort": 80,
          "protocol": "tcp",
          "name": "80",
          "labels": null
        }
      ],
      "forcePullImage":true
    }
  },
  "instances": 3,
  "cpus": 0.1,
  "mem": 65,
  "healthChecks": [{
      "protocol": "HTTP",
      "path": "/",
      "portIndex": 0,
      "timeoutSeconds": 10,
      "gracePeriodSeconds": 10,
      "intervalSeconds": 2,
      "maxConsecutiveFailures": 10
  }],
  "uris":  [
       "file:///mnt/share/dcosshare/docker.tar.gz"
   ]
}
```

Nasaďte aplikaci pomocí rozhraní CLI DC/OS.

```azurecli-interactive
dcos marathon app add acrDemo.json
```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nakonfigurovali DC/OS pro použití služby Azure Container Registry a provedli následující kroky:

> [!div class="checklist"]
> * Nasazení služby Azure Container Registry (v případě potřeby)
> * Konfigurace ověřování služby ACR v clusteru DC/OS
> * Odeslání image do služby Azure Container Registry
> * Spuštění image kontejneru ze služby Azure Container Registry
