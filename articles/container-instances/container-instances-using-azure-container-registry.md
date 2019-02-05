---
title: Nasazení do služby Azure Container Instances ze služby Azure Container Registry
description: Zjistěte, jak nasadit kontejnery ve službě Azure Container Instances pomocí imagí kontejnerů ve službě Azure container registry.
services: container-instances
author: dlepow
ms.service: container-instances
ms.topic: article
ms.date: 01/04/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: f577a7dd9f517be6ab7b632a82227e4807862ba5
ms.sourcegitcommit: 3aa0fbfdde618656d66edf7e469e543c2aa29a57
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2019
ms.locfileid: "55727904"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Nasazení do služby Azure Container Instances ze služby Azure Container Registry

[Služba Azure Container Registry](../container-registry/container-registry-intro.md) je služba registru kontejneru založené na Azure, kterou spravuje používá k ukládání privátních imagí kontejnerů Dockeru. Tento článek popisuje, jak nasazovat Image kontejneru se ukládají ve službě Azure container registry do služby Azure Container Instances.

## <a name="prerequisites"></a>Požadavky

**Registr kontejnerů Azure**: Potřebujete registr kontejnerů Azure – a alespoň jeden kontejner image v registru - k dokončení kroků v tomto článku. Pokud potřebujete registr, najdete v článku [vytvoření registru kontejnerů pomocí Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: V příkladech příkazového řádku v tomto článku používají [rozhraní příkazového řádku Azure](/cli/azure/) a jsou formátována pro prostředí Bash. Je možné [instalace rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli) místně, nebo použijte [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

V každý produkční scénář, by měl být poskytuje přístup do služby Azure container registry pomocí [instanční](../container-registry/container-registry-auth-service-principal.md). Instanční objekty umožňují poskytovat [řízení přístupu na základě rolí](../container-registry/container-registry-roles.md) do imagí kontejnerů. Můžete například nakonfigurovat instanční objekt s přístupem k registru pouze ke čtení.

V následující části Vytvoření služby Azure key vault a hlavního názvu služby a uložení přihlašovacích údajů instančního objektu v trezoru. 

### <a name="create-key-vault"></a>Vytvoření trezoru klíčů

Pokud ještě nemáte trezor ve službě [Azure Key Vault](/azure/key-vault/), vytvořte si ho v Azure CLI pomocí následujících příkazů.

Aktualizace `RES_GROUP` proměnné s názvem existující skupinu prostředků, ve kterém chcete vytvořit trezor klíčů a `ACR_NAME` s názvem vašeho registru kontejneru. Zadejte název nového trezoru klíčů v `AKV_NAME`. Název trezoru musí být v rámci Azure jedinečný a musí být 3 až 24 alfanumerických znaků dlouhý začínat písmenem, končit písmenem nebo číslicí a nemůže obsahovat po sobě jdoucí pomlčky.

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
                --name http://$ACR_NAME-pull \
                --scopes $(az acr show --name $ACR_NAME --query id --output tsv) \
                --role acrpull \
                --query password \
                --output tsv)
```

`--role` Argument v předchozím příkazu nakonfiguruje instanční objekt s *acrpull* roli, která mu udělí vyžádanou přístup k registru. Chcete-li udělit i push a pull přístup, změňte `--role` argument *acrpush*.

V dalším kroku uložení objektu služby *appId* v trezoru, který je **uživatelské jméno** předáte do služby Azure Container Registry pro ověřování.

```azurecli
# Store service principal ID in AKV (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Vytvořili jste Azure Key Vault a uložili jste do něj dva tajné kódy:

* `$ACR_NAME-pull-usr`: ID objektu zabezpečení služby pro použití jako registr kontejneru **uživatelské jméno**.
* `$ACR_NAME-pull-pwd`: Heslo instančního objektu služby pro použití jako registr kontejneru **heslo**.

Teď můžete na tyto tajné kódy odkazovat názvem, když vy nebo vaše aplikace a služby budou načítat image z tohoto registru.

## <a name="deploy-container-with-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

Teď, když pověření instančního objektu ukládají v tajných kódů služby Azure Key Vault, aplikace a služby můžete využít k přístupu svého privátního registru.

Nejprve získejte název přihlašovacího serveru registru pomocí [az acr show] [ az-acr-show] příkazu. Název přihlašovacího serveru je všechna malá písmena, podobně jako `myregistry.azurecr.io`.

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Spuštěním následujícího příkazu [az container create][az-container-create] nasaďte instanci kontejneru. Příkaz použije přihlašovací údaje instančního objektu uložená ve službě Azure Key Vault k ověření do vašeho registru kontejneru a předpokládá, kterou jste dříve odeslali [aci-helloworld](container-instances-quickstart.md) image do registru. Aktualizace `--image` hodnotu, pokud chcete použít jinou image z registru.

```azurecli
az container create \
    --name aci-demo \
    --resource-group $RES_GROUP \
    --image $ACR_LOGIN_SERVER/aci-helloworld:v1 \
    --registry-login-server $ACR_LOGIN_SERVER \
    --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) \
    --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) \
    --dns-name-label aci-demo-$RANDOM \
    --query ipAddress.fqdn
```

`--dns-name-label` Hodnota musí být jedinečný v rámci Azure, takže ve výstupu předchozího příkazu přidá náhodné číslo popisku názvu DNS kontejneru. Výstup příkazu zobrazí plně kvalifikovaný název domény kontejneru, například:

```console
$ az container create --name aci-demo --resource-group $RES_GROUP --image $ACR_LOGIN_SERVER/aci-helloworld:v1 --registry-login-server $ACR_LOGIN_SERVER --registry-username $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-usr --query value -o tsv) --registry-password $(az keyvault secret show --vault-name $AKV_NAME -n $ACR_NAME-pull-pwd --query value -o tsv) --dns-name-label aci-demo-$RANDOM --query ipAddress.fqdn
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

Pokud je Správa imagí kontejnerů ve službě Azure container registry, můžete snadno vytvořit kontejner ve službě Azure Container Instances pomocí webu Azure portal. Během používání portálu nasadit instanci kontejneru z registru kontejneru, je nutné povolit v registru [účet správce](../container-registry/container-registry-authentication.md#admin-account). Účet správce je určená pro jednoho uživatele pro přístup k registru, především pro účely testování. 

1. Na webu Azure Portal přejděte do vašeho registru kontejneru.

1. Potvrďte, že je povoleno účet správce, vyberte **přístupové klíče**a v části **uživatele s rolí správce** vyberte **povolit**.

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
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set