---
title: Nasazení image kontejneru z registru kontejnerů Azure
description: Zjistěte, jak nasadit kontejnery v instanci kontejneru Azure tím, že vytáhnete ibi kontejnerů z registru kontejnerů Azure.
services: container-instances
ms.topic: article
ms.date: 02/18/2020
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 212624b857d65297830995018603c2627f83369b
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453519"
---
# <a name="deploy-to-azure-container-instances-from-azure-container-registry"></a>Nasazení do instancí kontejnerů Azure z registru kontejnerů Azure

[Azure Container Registry](../container-registry/container-registry-intro.md) je služba registru spravovaného kontejneru založená na Azure, která se používá k ukládání privátních ibi kontejnerů Dockeru. Tento článek popisuje, jak vyžádat image kontejneru uložené v registru kontejneru Azure při nasazování do Azure Container Instances. Doporučeným způsobem konfigurace přístupu k registru je vytvoření instančního objektu a hesla služby Azure Active Directory a uložení přihlašovacích údajů do trezoru klíčů Azure.

## <a name="prerequisites"></a>Požadavky

**Registr kontejnerů Azure**: K dokončení kroků v tomto článku potřebujete registr kontejnerů Azure – a alespoň jednu image kontejneru v registru. Pokud potřebujete registr, přečtěte [si informace o vytvoření registru kontejnerů pomocí příkazového příkazového příkazu k řešení Azure](../container-registry/container-registry-get-started-azure-cli.md).

**Azure CLI**: Příklady příkazového řádku v tomto článku použít [azure cli](/cli/azure/) a jsou formátovány pro prostředí Bash. Můžete [nainstalovat Azure CLI](/cli/azure/install-azure-cli) místně nebo použít [Azure Cloud Shell][cloud-shell-bash].

## <a name="configure-registry-authentication"></a>Konfigurace ověřování registru

V produkčním scénáři, kde poskytujete přístup k "bezhlavým" službám a aplikacím, se doporučuje nakonfigurovat přístup k registru pomocí [instančního objektu](../container-registry/container-registry-auth-service-principal.md). Instanční objekt umožňuje poskytovat [řízení přístupu na základě rolí](../container-registry/container-registry-roles.md) k iontům kontejneru. Můžete například nakonfigurovat instanční objekt s přístupem k registru pouze ke čtení.

Azure Container Registry poskytuje další [možnosti ověřování](../container-registry/container-registry-authentication.md).

> [!NOTE]
> Nelze ověřit na Azure Container Registry pro vyžádat image během nasazení skupiny kontejnerů pomocí [spravované identity](container-instances-managed-identity.md) nakonfigurované ve stejné skupině kontejnerů.

V následující části vytvoříte trezor klíčů Azure a instanční objekt a uložíte pověření instančního objektu do trezoru. 

### <a name="create-key-vault"></a>Vytvoření trezoru klíčů

Pokud ještě nemáte trezor ve službě [Azure Key Vault](../key-vault/general/overview.md), vytvořte si ho v Azure CLI pomocí následujících příkazů.

Aktualizujte `RES_GROUP` proměnnou názvem existující skupiny prostředků, ve které `ACR_NAME` chcete vytvořit trezor klíčů, a názvem registru kontejnerů. Pro stručnost příkazy v tomto článku předpokládají, že instance registru, trezoru klíčů a kontejneru jsou vytvořeny ve stejné skupině prostředků.

 Zadejte název nového trezoru klíčů v aplikaci `AKV_NAME`. Název úschovny musí být jedinečný v rámci Azure a musí mít délku 3 až 24 alfanumerických znaků, musí začínat písmenem, končit písmenem nebo číslicí a nesmí obsahovat po sobě jdoucí spojovníky.

```azurecli
RES_GROUP=myresourcegroup # Resource Group name
ACR_NAME=myregistry       # Azure Container Registry registry name
AKV_NAME=mykeyvault       # Azure Key Vault vault name

az keyvault create -g $RES_GROUP -n $AKV_NAME
```

### <a name="create-service-principal-and-store-credentials"></a>Vytvoření instančního objektu a uložení přihlašovacích údajů

Nyní vytvořte instanční objekt a uložte jeho pověření do trezoru klíčů.

Následující příkaz používá [az ad sp create-for-rbac][az-ad-sp-create-for-rbac] k vytvoření instančního objektu a [sadu tajných klíčů az pro][az-keyvault-secret-set] uložení **hesla** instančního objektu do trezoru.

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

Argument `--role` v předchozím příkazu konfiguruje instanční objekt s rolí *acrpull,* která mu uděluje přístup pouze pro vyžádat do registru. Chcete-li udělit přístup push `--role` i pull, změňte argument na *acrpush*.

Dále uložte *appId instančního objektu* do trezoru, což je **uživatelské jméno,** které předáte registru kontejnerů Azure pro ověřování.

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

Teď, když jsou pověření instančního objektu uloženy v tajných klíčech služby Azure Key Vault, vaše aplikace a služby je můžou použít k přístupu k vašemu soukromému registru.

Nejprve získat název přihlašovacího serveru registru pomocí příkazu [az acr show.][az-acr-show] Název přihlašovacího serveru je všechny `myregistry.azurecr.io`malá písmena a podobné .

```azurecli
ACR_LOGIN_SERVER=$(az acr show --name $ACR_NAME --resource-group $RES_GROUP --query "loginServer" --output tsv)
```

Spuštěním následujícího příkazu [az container create][az-container-create] nasaďte instanci kontejneru. Příkaz používá pověření instančního objektu uložené v Azure Key Vault k ověření do registru kontejneru a předpokládá, že jste dříve tlačil [aci-helloworld](container-instances-quickstart.md) image do registru. Pokud `--image` chcete použít jiný obrázek z registru, aktualizujte hodnotu.

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

Hodnota `--dns-name-label` musí být v rámci Azure jedinečná, takže předchozí příkaz připojí k popisku názvu DNS kontejneru náhodné číslo. Výstup příkazu zobrazí plně kvalifikovaný název domény kontejneru, například:

```output
"aci-demo-25007.eastus.azurecontainer.io"
```

Po úspěšném spuštění kontejneru můžete přejít na jeho hlavní název v prohlížeči a ověřit, zda je aplikace úspěšně spuštěna.

## <a name="deploy-with-azure-resource-manager-template"></a>Nasazení pomocí šablony Azure Resource Manager

Vlastnosti registru kontejnerů Azure můžete zadat v šabloně `imageRegistryCredentials` Azure Resource Manager zahrnutím vlastnosti v definici skupiny kontejnerů. Můžete například zadat pověření registru přímo:

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

Kompletní nastavení skupiny kontejnerů najdete v [odkazu na šablonu Správce prostředků](/azure/templates/Microsoft.ContainerInstance/2018-10-01/containerGroups).    

Podrobnosti o odkazování na tajné kódy služby Azure Key Vault v šabloně Správce prostředků najdete v tématu [Použití trezoru klíčů Azure k předání hodnoty zabezpečeného parametru během nasazení](../azure-resource-manager/templates/key-vault-parameter.md).

## <a name="deploy-with-azure-portal"></a>Nasazení pomocí portálu Azure

Pokud spravujete ibi kontejnerů v registru kontejnerů Azure, můžete snadno vytvořit kontejner v instanci kontejneru Azure pomocí portálu Azure. Při použití portálu k nasazení instance kontejneru z registru kontejneru je nutné povolit [účet správce](../container-registry/container-registry-authentication.md#admin-account)registru . Účet správce je určen pro jednoho uživatele pro přístup k registru, hlavně pro účely testování. 

1. Na webu Azure Portal přejděte do registru kontejnerů.

1. Pokud chcete ověřit, že je účet správce povolený, vyberte **Přístupové klávesy**a v části **Správce** vyberte **Povolit**.

1. Vyberte **Repozitáře**, vyberte úložiště, ze kterého chcete nasadit, klikněte pravým tlačítkem myši na značku image kontejneru, kterou chcete nasadit, a vyberte **Spustit instanci**.

    !["Spustit instanci" v registru kontejnerů Azure na webu Azure Portal][acr-runinstance-contextmenu]

1. Zadejte název kontejneru a název skupiny prostředků. Můžete také změnit výchozí hodnoty, pokud chcete.

    ![Vytvořit nabídku pro instance kontejnerů Azure][acr-create-deeplink]

1. Po dokončení nasazení můžete přejít do skupiny kontejnerů z podokna oznámení a najít jeho IP adresu a další vlastnosti.

    ![Zobrazení podrobností pro skupinu kontejnerů Azure Container Instances][aci-detailsview]

## <a name="next-steps"></a>Další kroky

Další informace o ověřování registru kontejnerů Azure najdete v [tématu Authenticate with a Azure container registry](../container-registry/container-registry-authentication.md).

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
