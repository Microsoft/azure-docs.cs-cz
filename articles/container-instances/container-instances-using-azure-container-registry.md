---
title: Nasadit do Azure kontejner instancí z registru kontejner Azure
description: Informace o nasazení kontejnerů v Azure kontejner instancí pomocí bitové kopie kontejneru v registru kontejner Azure.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 7a7d2aa61f25bc4782c6a1a6744e329935477f8c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32168111"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Nasadit do Azure kontejner instancí z registru kontejner Azure

Registr kontejner Azure je využitím Azure, privátní registru pro kontejner imagí Dockeru. Tento článek popisuje postup nasazení bitových kopií kontejneru uložené v registru kontejner Azure do Azure kontejner instancí.

## <a name="prerequisites"></a>Požadavky

**Azure kontejneru registru**: potřebujete registru kontejner Azure – a minimálně jeden kontejner bitové kopie v registru – Pokud chcete provést kroky v tomto článku. Pokud potřebujete registr, najdete v části [vytvořit kontejner registru pomocí rozhraní příkazového řádku Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Rozhraní příkazového řádku Azure**: V příkladech příkazového řádku v tomto článku používají [rozhraní příkazového řádku Azure](/cli/azure/) a zda jsou formátovány pro prostředí Bash. Můžete [nainstalovat Azure CLI](/cli/azure/install-azure-cli) místně, nebo použijte [prostředí cloudu Azure][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

V každý produkční scénář by měl přístup k registru kontejner Azure poskytuje pomocí [služby objekty](../container-registry/container-registry-auth-service-principal.md). Objekty služby umožňují poskytování řízení přístupu na základě rolí do kontejneru imagí. Můžete například nakonfigurovat hlavní název služby vyžádanou přístup k registru.

V této části můžete vytvořit klíče trezoru služby Azure a objekt služby a ukládat přihlašovací údaje instančního objektu v trezoru.

### <a name="create-key-vault"></a>Vytvořit trezor klíčů

Pokud ještě nemáte trezoru [Azure Key Vault](/azure/key-vault/), vytvořit pomocí Azure CLI, použijte následující příkazy.

Aktualizace `RES_GROUP` proměnné s názvem skupiny prostředků, ve které chcete vytvořit trezor klíčů, a `ACR_NAME` s názvem vašeho kontejneru registru. Zadejte název nového trezoru klíčů v `AKV_NAME`. Název trezoru musí být jedinečný v rámci Azure a musí být alfanumerické znaky 3 až 24 délka, začínat písmenem, končit písmenem nebo číslicí a nesmí obsahovat po sobě jdoucí pomlčky.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Vytvoření instančního objektu a ukládat přihlašovací údaje

Nyní musíte vytvořit hlavní název služby a uložit svoje přihlašovací údaje v trezoru klíčů.

Následující příkaz používá [az ad sp vytvořit pro rbac] [ az-ad-sp-create-for-rbac] vytvořit objekt služby a [az keyvault tajnou sadu] [ az-keyvault-secret-set] k uložení instanční objekt **heslo** v trezoru.

```azurecli
# Create service principal, store its password in AKV (the registry *password*)
az keyvault secret set \
  --vault-name $AKV_NAME \
  --name $ACR_NAME-pull-pwd \
  --value $(az ad sp create-for-rbac \
                --name $ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role reader \
                --query password \
                --output tsv)
```

`--role` Nakonfiguruje objekt služby s argumentem v předchozím příkazu *čtečky* role, která mu udělí vyžádanou přístup k registru. Chcete-li udělit push i přístup pro vyžádání obsahu, změňte `--role` argument *Přispěvatel*.

V dalším kroku uložit objekt služby *appId* v úložišti, který je **uživatelské jméno** předat do registru kontejner Azure pro ověřování.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Po vytvoření Azure Key Vault a v ní uloženy dva tajné klíče:

* `$ACR_NAME-pull-usr`: Hlavní ID služby, používat jako kontejner registru **uživatelské jméno**.
* `$ACR_NAME-pull-pwd`: Hlavní heslo služby, používat jako kontejner registru **heslo**.

Pokud vám nebo vaší aplikace a služby pro vyžádání obsahu bitové kopie z registru, můžete odkazovat teď těchto tajných klíčů podle názvu.

## <a name="deploy-container-with-azure-cli"></a>Nasazení kontejnerů pomocí rozhraní příkazového řádku Azure

Teď, když hlavní pověření služby jsou uložené v Azure Key Vault tajné klíče, aplikací a služeb můžete je používat pro přístup k vaší privátní registru.

Spuštěním následujících [vytvořit kontejner az] [ az-container-create] příkazu nasaďte kontejner instance. Příkaz používá k ověření vaší registru kontejneru objektu služby přihlašovací údaje uložené v Azure Key Vault a předpokládá jste dříve nabídnutých [aci helloworld](container-instances-quickstart.md) bitové kopie do registru. Aktualizace `--image` hodnotu, pokud chcete použít jinou bitovou kopii z registru.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_NAME.azurecr.io/aci-helloworld:v1 \
    --registry-login-server $ACR_NAME.azurecr.io \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` Hodnota musí být jedinečný v rámci Azure, takže předchozí příkaz připojí k Popisek názvu DNS kontejneru náhodné číslo. Výstup z tohoto příkazu zobrazí kontejneru plně kvalifikovaný název domény (FQDN), třeba:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Jakmile úspěšně spustil kontejneru, můžete přejít na jeho plně kvalifikovaný název domény v prohlížeči se ověřit, zda že je aplikace spuštěna úspěšně.

## <a name="deploy-with-azure-resource-manager-template"></a>Nasazení pomocí šablony Azure Resource Manageru

Můžete zadat vlastnosti registr kontejner Azure v šablonu Azure Resource Manager včetně `imageRegistryCredentials` vlastnost v definici skupiny kontejneru:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Podrobnosti k odkazování Azure Key Vault tajných klíčů v šablony Resource Manageru najdete v tématu [použití Azure Key Vault předejte hodnotu parametru zabezpečení při nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Nasazení pomocí portálu Azure

Pokud chcete zachovat kontejneru bitové kopie v registru kontejner Azure, můžete snadno vytvořit kontejner v instancí kontejneru Azure pomocí portálu Azure.

1. Na portálu Azure přejděte do kontejneru registr.

1. Vyberte **úložiště**, pak vyberte úložiště, který chcete nasadit, klikněte pravým tlačítkem na značce pro kontejner bitovou kopii, kterou chcete nasadit a vyberte **spustí instanci**.

    !["Spustit instance" v registru kontejner Azure na portálu Azure][acr-runinstance-contextmenu]

1. Zadejte název kontejneru a název skupiny prostředků. Pokud chcete můžete taky změnit výchozí hodnoty.

    ![Vytvořit nabídku pro instance kontejner Azure][acr-create-deeplink]

1. Po dokončení nasazení můžete přejít do kontejneru skupiny z podokna oznámení najít IP adresu a další vlastnosti.

    ![Zobrazení podrobností pro skupinu kontejner instancí kontejnerů Azure][aci-detailsview]

## <a name="next-steps"></a>Další postup

Další informace o ověřování Azure kontejneru registru najdete v tématu [ověřit přístup registru kontejner Azure](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az_container_create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set