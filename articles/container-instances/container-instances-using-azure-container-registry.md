---
title: Nasazení do služby Azure Container Instances ze služby Azure Container Registry
description: Zjistěte, jak nasadit kontejnery ve službě Azure Container Instances pomocí imagí kontejnerů ve službě Azure container registry.
services: container-instances
author: mmacy
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 03/30/2018
ms.author: marsma
ms.custom: mvc
ms.openlocfilehash: 5c3cf162caf5cf9aa88b012257d4caab37b7893c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/02/2018
ms.locfileid: "39424207"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Nasazení do služby Azure Container Instances ze služby Azure Container Registry

Azure Container Registry je založené na Azure privátní registr pro Image kontejnerů Docker. Tento článek popisuje, jak nasazovat Image kontejneru se ukládají ve službě Azure container registry do služby Azure Container Instances.

## <a name="prerequisites"></a>Požadavky

**Služba Azure Container Registry**: budete potřebovat registru kontejnerů Azure – a alespoň jeden kontejner image v registru - k dokončení kroků v tomto článku. Pokud potřebujete registr, najdete v článku [vytvoření registru kontejnerů pomocí Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: V příkladech příkazového řádku v tomto článku používají [rozhraní příkazového řádku Azure](/cli/azure/) a jsou formátována pro prostředí Bash. Je možné [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) místně, nebo použijte [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

V každý produkční scénář, by měl být poskytuje přístup do služby Azure container registry pomocí [instanční](../container-registry/container-registry-auth-service-principal.md). Instanční objekty vám umožní poskytnout řízení přístupu k vašim imagím kontejnerů na základě role. Můžete například nakonfigurovat instanční objekt s přístupem k registru pouze ke čtení.

V této části vytvoříte služby Azure key vault a hlavního názvu služby a uložení přihlašovacích údajů instančního objektu v trezoru.

### <a name="create-key-vault"></a>Vytvoření trezoru klíčů

Pokud ještě nemáte trezor ve službě [Azure Key Vault](/azure/key-vault/), vytvořte si ho v Azure CLI pomocí následujících příkazů.

Aktualizace `RES_GROUP` proměnné s názvem skupiny prostředků, ve kterém chcete vytvořit trezor klíčů a `ACR_NAME` s názvem vašeho registru kontejneru. Zadejte název nového trezoru klíčů v `AKV_NAME`. Název trezoru musí být v rámci Azure jedinečný a musí být 3 až 24 alfanumerických znaků dlouhý začínat písmenem, končit písmenem nebo číslicí a nemůže obsahovat po sobě jdoucí pomlčky.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Vytvoření instančního objektu a uložení přihlašovacích údajů

Teď je potřeba vytvořit instanční objekt a uložit jeho přihlašovací údaje do trezoru klíčů.

Následující příkaz používá [az ad sp create-for-rbac] [ az-ad-sp-create-for-rbac] k vytvoření instančního objektu služby a [az keyvault secret sady] [ az-keyvault-secret-set] ukládat instanční objekt služby **heslo** v trezoru.

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

Argument `--role` v předchozím příkazu nakonfiguruje instanční objekt rolí *čtenáře*, která mu udělí přístup k registru pouze ke čtení. Pokud chcete udělit přístup pro zápis i čtení, změňte argument `--role` na *přispěvatel*.

V dalším kroku uložení objektu služby *appId* v trezoru, který je **uživatelské jméno** předáte do služby Azure Container Registry pro ověřování.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Vytvořili jste Azure Key Vault a uložili jste do něj dva tajné kódy:

* `$ACR_NAME-pull-usr`: ID instančního objektu, které se bude používat jako **uživatelské jméno** registru kontejneru.
* `$ACR_NAME-pull-pwd`: heslo instančního objektu, které se bude používat jako **heslo** registru kontejneru.

Teď můžete na tyto tajné kódy odkazovat názvem, když vy nebo vaše aplikace a služby budou načítat image z tohoto registru.

## <a name="deploy-container-with-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

Teď, když pověření instančního objektu ukládají v tajných kódů služby Azure Key Vault, aplikace a služby můžete využít k přístupu svého privátního registru.

Spuštěním následujícího příkazu [az container create][az-container-create] nasaďte instanci kontejneru. Příkaz použije přihlašovací údaje instančního objektu uložená ve službě Azure Key Vault k ověření do vašeho registru kontejneru a předpokládá, kterou jste dříve odeslali [aci-helloworld](container-instances-quickstart.md) image do registru. Aktualizace `--image` hodnotu, pokud chcete použít jinou image z registru.

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

`--dns-name-label` Hodnota musí být jedinečný v rámci Azure, takže ve výstupu předchozího příkazu přidá náhodné číslo popisku názvu DNS kontejneru. Výstup příkazu zobrazí plně kvalifikovaný název domény kontejneru, například:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_NAME.azurecr.io/aci-helloworld:v1 --registry-login-server $ACR_NAME.azurecr.io --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
"aci-demo-25007.eastus.azurecontainer.io"
```

Jakmile kontejneru se úspěšně spustila, můžete přejít na jeho plně kvalifikovaný název domény v prohlížeči a ověřte, že aplikace je úspěšně spuštěná.

## <a name="deploy-with-azure-resource-manager-template"></a>Nasazení pomocí šablony Azure Resource Manageru

Je-li zadat vlastnosti služby Azure Container Registry v šabloně Azure Resource Manageru, včetně `imageRegistryCredentials` vlastnost definice skupiny kontejnerů:

```JSON
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
```

Podrobnosti k odkazování tajných kódů služby Azure Key Vault v šabloně Resource Manageru najdete v tématu [použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Nasazení pomocí webu Azure portal

Pokud je Správa imagí kontejnerů v Azure Container Registry, můžete snadno vytvořit kontejner ve službě Azure Container Instances pomocí webu Azure portal.

1. Na webu Azure Portal přejděte do vašeho registru kontejneru.

1. Vyberte **úložišť**, pak vyberte úložiště, které chcete nasadit, klikněte pravým tlačítkem na značku pro image kontejneru, kterou chcete nasadit a vyberte **spustit instanci**.

    !["Spuštění instance" ve službě Azure Container Registry na webu Azure Portal][acr-runinstance-contextmenu]

1. Zadejte název kontejneru a název skupiny prostředků. Pokud chcete, můžete také změnit výchozí hodnoty.

    ![Vytvoření nabídky pro Azure Container Instances][acr-create-deeplink]

1. Po dokončení nasazení můžete přejít na skupinu kontejnerů v podokně oznámení k nalezení jeho IP adresu a další vlastnosti.

    ![Zobrazení podrobností pro skupinu kontejnerů Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Další postup

Další informace o ověřování Azure Container Registry, najdete v části [ověřování pomocí služby Azure container registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-powershell]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set