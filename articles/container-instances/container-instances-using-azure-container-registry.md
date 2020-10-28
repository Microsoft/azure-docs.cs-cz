---
title: Nasadit image kontejneru z Azure Container Registry
description: Naučte se nasazovat kontejnery v Azure Container Instances tím, že nasadíte image kontejneru z Azure Container Registry.
services: container-instances
ms.topic: article
ms.date: 07/02/2020
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: cca1001f0f84f4e4fc87df233f872fc1efdb3267
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2020
ms.locfileid: "92736724"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Nasazení ze služby Azure Container Registry do služby Azure Container Instances

[Azure Container Registry](../container-registry/container-registry-intro.md) je spravovaná služba registru kontejnerů založená na Azure, která slouží k ukládání privátních imagí kontejneru Docker. Tento článek popisuje, jak načítat image kontejneru uložené v Azure Container Registry při nasazení do Azure Container Instances. Doporučeným způsobem, jak nakonfigurovat přístup k registru, je vytvořit Azure Active Directory instanční objekt a heslo a přihlašovací údaje uložit v trezoru klíčů Azure.

## <a name="prerequisites"></a>Předpoklady

**Azure Container Registry** : potřebujete službu Azure Container Registry, a alespoň jednu Image kontejneru v registru, abyste mohli dokončit kroky v tomto článku. Pokud potřebujete registr, přečtěte si téma [Vytvoření registru kontejnerů pomocí Azure CLI](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI** : Příklady příkazového řádku v tomto článku používají [Azure CLI](/cli/azure/) a jsou naformátované pro prostředí bash. [Azure CLI můžete nainstalovat](/cli/azure/install-azure-cli) místně nebo použít [Azure Cloud Shell][cloud-shell-bash].

## <a name="limitations"></a>Omezení

* Pomocí [spravované identity](container-instances-managed-identity.md) nakonfigurované ve stejné skupině kontejnerů se nemůžete ověřit, aby se při nasazení skupiny kontejnerů načetly image Azure Container Registry.
* V tuto chvíli nemůžete načítat image z [Azure Container Registry](../container-registry/container-registry-vnet.md) nasazených do Azure Virtual Network.

## <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

V produkčním scénáři, kdy poskytujete přístup k "bezobslužným" službám a aplikacím, se doporučuje nakonfigurovat přístup k registru pomocí [instančního objektu](../container-registry/container-registry-auth-service-principal.md). Instanční objekt umožňuje poskytnout vašim imagím kontejneru [řízení přístupu na základě role Azure (Azure RBAC)](../container-registry/container-registry-roles.md) . Můžete například nakonfigurovat instanční objekt s přístupem k registru pouze ke čtení.

Azure Container Registry poskytuje další [Možnosti ověřování](../container-registry/container-registry-authentication.md).

V následující části vytvoříte Trezor klíčů Azure a instanční objekt a uložíte přihlašovací údaje instančního objektu do trezoru.

### <a name="create-key-vault"></a>Vytvoření trezoru klíčů

Pokud ještě nemáte trezor ve službě [Azure Key Vault](../key-vault/general/overview.md), vytvořte si ho v Azure CLI pomocí následujících příkazů.

Aktualizujte `RES_GROUP` proměnnou názvem existující skupiny prostředků, ve které chcete vytvořit Trezor klíčů, a `ACR_NAME` názvem vašeho registru kontejneru. V případě zkrácení se v příkazech v tomto článku předpokládají, že jsou všechny služby registru, trezoru klíčů a instance kontejnerů vytvořené ve stejné skupině prostředků.

 Zadejte název nového trezoru klíčů v `AKV_NAME` . Název trezoru musí být v rámci Azure jedinečný a musí mít 3-24 alfanumerických znaků, začínat písmenem, končit písmenem nebo číslicí a nesmí obsahovat po sobě jdoucí spojovníky.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Vytvoření instančního objektu a uložení přihlašovacích údajů

Teď vytvořte instanční objekt a uložte jeho přihlašovací údaje do trezoru klíčů.

Následující příkaz pomocí příkazu [AZ AD SP Create-for-RBAC][az-ad-sp-create-for-rbac] vytvoří instanční objekt a [vyaz heslo tajného klíče trezoru klíčů][az-keyvault-secret-set] , aby se uložilo **heslo** instančního objektu do trezoru.

```azurecli
# Create service principal, store its password in vault (the registry *password*)
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

`--role`Argument v předchozím příkazu nakonfiguruje instanční objekt pomocí role *acrpull* , která uděluje přístup pouze pro získání přístupu k registru. Chcete-li udělit přístup push i Pull, změňte `--role` argument na *acrpush* .

Dále do trezoru uložte identifikátor *appId* objektu služby, což je **uživatelské jméno** , které jste předali Azure Container Registry k ověřování.

```azurecli
# Store service principal ID in vault (the registry *username*)
az keyvault secret set \
    --vault-name $AKV_NAME \
    --name $ACR_NAME-pull-usr \
    --value $(az ad sp show --id http://$ACR_NAME-pull --query appId --output tsv)
```

Vytvořili jste trezor klíčů Azure a uložili jste do něj dva tajné kódy:

* `$ACR_NAME-pull-usr`: ID instančního objektu, které se bude používat jako **uživatelské jméno** registru kontejneru.
* `$ACR_NAME-pull-pwd`: heslo instančního objektu, které se bude používat jako **heslo** registru kontejneru.

Teď můžete na tyto tajné kódy odkazovat názvem, když vy nebo vaše aplikace a služby budou načítat image z tohoto registru.

## <a name="deploy-container-with-azure-cli"></a>Nasazení kontejneru pomocí Azure CLI

Teď, když jsou přihlašovací údaje instančního objektu uložené v Azure Key Vault tajných klíčích, můžou vaše aplikace a služby používat k přístupu k privátnímu registru.

Nejprve pomocí příkazu [AZ ACR show][az-acr-show] načtěte název přihlašovacího serveru registru. Název přihlašovacího serveru je malými písmeny a podobně jako `myregistry.azurecr.io` .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Spuštěním následujícího příkazu [az container create][az-container-create] nasaďte instanci kontejneru. Příkaz používá přihlašovací údaje instančního objektu uložené v Azure Key Vault k ověření ve vašem registru kontejneru a předpokládá, že jste do svého registru dřív posunuli image [ACI-Hello](container-instances-quickstart.md) . Aktualizujte `--image` hodnotu, pokud chcete použít jiný obrázek z registru.

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

`--dns-name-label`Hodnota musí být v rámci Azure jedinečná, takže předchozí příkaz připojí náhodné číslo k popisku názvu DNS kontejneru. Výstup příkazu zobrazí plně kvalifikovaný název domény kontejneru, například:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Po úspěšném spuštění kontejneru můžete v prohlížeči přejít na jeho plně kvalifikovaný název domény a ověřit, jestli je aplikace spuštěná úspěšně.

## <a name="deploy-with-azure-resource-manager-template"></a>Nasazení pomocí šablony Azure Resource Manager

Vlastnosti služby Azure Container Registry můžete zadat v šabloně Azure Resource Manager zahrnutím `imageRegistryCredentials` vlastnosti do definice skupiny kontejnerů. Můžete například zadat přihlašovací údaje registru přímo:

```JSON
[...]
"imageRegistryCredentials": [
  {
    "server": "imageRegistryLoginServer",
    "username": "imageRegistryUsername",
    "password": "imageRegistryPassword"
  }
]
[...]
```

Kompletní nastavení skupiny kontejnerů najdete v [referenčních informacích k šabloně správce prostředků](/azure/templates/Microsoft.ContainerInstance/2019-12-01/containerGroups).    

Podrobnosti o odkazování Azure Key Vault tajných kódů v šabloně Správce prostředků najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Nasazení pomocí Azure Portal

Pokud udržujete image kontejnerů ve službě Azure Container Registry, můžete kontejner snadno vytvořit v Azure Container Instances pomocí Azure Portal. Při použití portálu k nasazení instance kontejneru z registru kontejneru je nutné povolit [účet správce](../container-registry/container-registry-authentication.md#admin-account)registru. Účet správce je navržený pro jednoho uživatele, který má přístup k registru, hlavně pro účely testování. 

1. V Azure Portal přejděte do registru kontejneru.

1. Pokud chcete potvrdit, že je účet správce povolený, vyberte **přístupové klíče** a v části **uživatel s oprávněními** vyberte **Povolit** .

1. Vyberte úložiště a pak vyberte úložiště, ze kterého chcete nasadit, klikněte pravým tlačítkem na značku pro Image **kontejneru, kterou** chcete nasadit, a vyberte **spustit instanci** .

    !["Run instance" v Azure Container Registry Azure Portal][acr-runinstance-contextmenu]

1. Zadejte název kontejneru a název skupiny prostředků. V případě potřeby můžete také změnit výchozí hodnoty.

    ![Vytvořit nabídku pro Azure Container Instances][acr-create-deeplink]

1. Po dokončení nasazení můžete přejít do skupiny kontejnerů z podokna oznámení a vyhledat jeho IP adresu a další vlastnosti.

    ![Zobrazení podrobností pro Azure Container Instances skupinu kontejnerů][aci-detailsview]

## <a name="next-steps"></a>Další kroky

Další informace o ověřování Azure Container Registry najdete v tématu [ověřování pomocí služby Azure Container Registry](../container-registry/container-registry-authentication.md).

<!-- IMAGES -->
[acr-create-deeplink]: ./media/container-instances-using-azure-container-registry/acr-create-deeplink.png
[aci-detailsview]: ./media/container-instances-using-azure-container-registry/aci-detailsview.png
[acr-runinstance-contextmenu]: ./media/container-instances-using-azure-container-registry/acr-runinstance-contextmenu.png

<!-- LINKS - External -->
[cloud-shell-bash]: https://shell.azure.com/bash
[cloud-shell-try-it]: https://shell.azure.com/powershell

<!-- LINKS - Internal -->
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-ad-sp-create-for-rbac]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-container-create]: /cli/azure/container#az-container-create
[az-keyvault-secret-set]: /cli/azure/keyvault/secret#az-keyvault-secret-set
