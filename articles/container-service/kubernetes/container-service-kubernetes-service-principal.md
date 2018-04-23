---
title: Instanční objekt pro cluster Azure Kubernetes
description: Vytvoření a správa instančního objektu služby Azure Active Directory pro cluster Kubernetes v Azure Container Service
services: container-service
author: neilpeterson
manager: timlt
ms.service: container-service
ms.topic: get-started-article
ms.date: 02/26/2018
ms.author: nepeters
ms.custom: mvc
ms.openlocfilehash: 02588ca770ae519615360ce3e8935231aa74f8cf
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
---
# <a name="set-up-an-azure-ad-service-principal-for-a-kubernetes-cluster-in-container-service"></a>Nastavení instančního objektu služby Azure AD pro cluster Kubernetes ve službě Container Service

[!INCLUDE [aks-preview-redirect.md](../../../includes/aks-preview-redirect.md)]

Cluster Kubernetes vyžaduje v Azure Container Service [instanční objekt služby Azure Active Directory](../../active-directory/develop/active-directory-application-objects.md) pro interakci s rozhraními API Azure. Instanční objekt je potřeba k dynamické správě prostředků, jako jsou například [uživatelem definované trasy](../../virtual-network/virtual-networks-udr-overview.md) a [vrstva 4 služby Azure Load Balancer](../../load-balancer/load-balancer-overview.md).


Tento článek ukazuje různé možnosti nastavení instančního objektu pro cluster Kubernetes. Pokud jste například nainstalovali a nastavili [Azure CLI 2.0](/cli/azure/install-az-cli2), můžete spustit příkaz [`az acs create`](/cli/azure/acs#az_acs_create) a vytvořit současně cluster Kubernetes i instanční objekt.


## <a name="requirements-for-the-service-principal"></a>Požadavky pro instanční objekt

Můžete vytvořit existující instanční objekt služby Azure AD splňující následující požadavky nebo můžete vytvořit nový.

* **Obor:** Skupina prostředků

* **Role:** Přispěvatel

* **Tajný klíč klienta:** Musí to být heslo. V současné době nemůžete použít instanční objekt nastavený pro ověření certifikátu.

> [!IMPORTANT]
> Abyste mohli vytvořit instanční objekt, musíte mít oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném. Pokud chcete zjistit, jestli máte požadovaná oprávnění, [podívejte se na portál](../../azure-resource-manager/resource-group-create-service-principal-portal.md#required-permissions).
>

## <a name="option-1-create-a-service-principal-in-azure-ad"></a>Možnost 1: Vytvoření instančního objektu v Azure AD

Pokud chcete instanční objekt služby Azure AD vytvořit před nasazením clusteru Kubernetes, Azure k tomu nabízí několik metod.

Příkazy v následujícím příkladu vám ukážou, jak to můžete udělat pomocí [Azure CLI 2.0](../../azure-resource-manager/resource-group-authenticate-service-principal-cli.md). Instanční objekt můžete případně vytvořit pomocí [Azure PowerShellu](../../azure-resource-manager/resource-group-authenticate-service-principal.md), [portálu](../../azure-resource-manager/resource-group-create-service-principal-portal.md) nebo jinou metodou.

```azurecli
az login

az account set --subscription "mySubscriptionID"

az group create --name "myResourceGroup" --location "westus"

az ad sp create-for-rbac --role="Contributor" --scopes="/subscriptions/<subscriptionID>/resourceGroups/<resourceGroupName>"
```

Výstup je podobný tomuto (zobrazuje se zde zrevidovaně):

![Vytvoření instančního objektu](./media/container-service-kubernetes-service-principal/service-principal-creds.png)

Zvýrazní se **ID klienta** (`appId`) a **tajný kód klienta** (`password`), které se použijí jako parametry instančního objektu pro nasazení clusteru.


### <a name="specify-service-principal-when-creating-the-kubernetes-cluster"></a>Zadání instančního objektu při vytváření clusteru Kubernetes

Při vytváření clusteru Kubernetes zadejte **ID klienta** (pro ID aplikace také označované jako `appId`) a **tajný kód klienta** (`password`) existujícího instančního objektu jako parametry. Ujistěte se, že instanční objekt splňuje požadavky uvedené na začátku tohoto článku.

Tyto parametry můžete zadat při nasazování clusteru Kubernetes pomocí [rozhraní příkazového řádku Azure (CLI) 2.0](container-service-kubernetes-walkthrough.md), webu [Azure Portal](../dcos-swarm/container-service-deployment.md) nebo jiné metody.

>[!TIP]
>Při zadávání **ID klienta** se ujistěte, že používáte `appId` instančního objektu, a nikoli `ObjectId` instančního objektu.
>

Následující příklad ukazuje jeden ze způsobů předání parametrů pomocí Azure CLI 2.0. Tento příklad používá [šablonu Kubernetes pro rychlý start](https://github.com/Azure/azure-quickstart-templates/tree/master/101-acs-kubernetes).

1. [Stáhněte si](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.parameters.json) soubor parametrů šablony `azuredeploy.parameters.json` z GitHubu.

2. Instanční objekt specifikujte zadáním hodnot pro `servicePrincipalClientId` a `servicePrincipalClientSecret` v souboru. (Pro `dnsNamePrefix` a `sshRSAPublicKey` musíte zadat také vlastní hodnoty. V druhém případě se jedná o veřejný klíč SSH pro přístup ke clusteru.) Uložte soubor.

    ![Předání parametrů instančního objektu](./media/container-service-kubernetes-service-principal/service-principal-params.png)

3. Spusťte následující příkaz a možnost `--parameters` použijte k nastavení cesty k souboru azuredeploy.parameters.json. Tento příkaz nasadí cluster ve vámi vytvořené skupině prostředků s názvem `myResourceGroup` v oblasti Západní USA.

    ```azurecli
    az login

    az account set --subscription "mySubscriptionID"

    az group create --name "myResourceGroup" --location "westus"

    az group deployment create -g "myResourceGroup" --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-acs-kubernetes/azuredeploy.json" --parameters @azuredeploy.parameters.json
    ```


## <a name="option-2-generate-a-service-principal-when-creating-the-cluster-with-az-acs-create"></a>Možnost 2: Vygenerování instančního objektu při vytváření clusteru pomocí příkazu `az acs create`

Pokud vytváříte cluster Kubernetes spuštěním příkazu [`az acs create`](/cli/azure/acs#az_acs_create), máte možnost instanční objekt vygenerovat automaticky.

Stejně jako u ostatních možností vytvoření clusteru Kubernetes můžete při spuštění příkazu `az acs create` určit parametry pro existující instanční objekt. Pokud však tyto parametry vynecháte, Azure CLI automaticky vytvoří instanční objekt pro použití se službou Container Service. Tato akce se provede transparentně během nasazení.

Následující příkaz vytvoří cluster Kubernetes a vygeneruje klíče SSH a pověření instančního objektu:

```console
az acs create -n myClusterName -d myDNSPrefix -g myResourceGroup --generate-ssh-keys --orchestrator-type kubernetes
```

> [!IMPORTANT]
> Pokud váš účet nemá v Azure AD a předplatném oprávnění k vytvoření instančního objektu, příkaz vygeneruje chybu podobnou této: `Insufficient privileges to complete the operation.`.
>

## <a name="additional-considerations"></a>Další aspekty

* Pokud nemáte oprávnění k vytvoření instančního objektu v předplatném, možná budete muset požádat správce Azure AD nebo předplatného o přidělení potřebných oprávnění nebo o instanční objekt pro použití s Azure Container Service.

* Instanční objekt pro Kubernetes je součástí konfigurace clusteru. K nasazení clusteru ale nepoužívejte identitu.

* Každý instanční objekt je přidružený k aplikaci Azure AD. Instanční objekt pro cluster Kubernetes může být přidružený k jakémukoli platnému názvu aplikace Azure AD (například `https://www.contoso.org/example`). Adresa URL aplikace nemusí být skutečný koncový bod.

* Při zadávání **ID klienta** instančního objektu můžete použít hodnotu `appId` (jak je ukázáno v tomto článku) nebo odpovídající `name` instančního objektu (například `https://www.contoso.org/example`).

* Na hlavním virtuálním počítači a virtuálních počítačích agentů v clusteru Kubernetes jsou pověření instančního objektu uložená v souboru `/etc/kubernetes/azure.json`.

* Pokud použijete příkaz `az acs create` k automatickému vygenerování instančního objektu, zapíší se přihlašovací údaje instančního objektu do souboru `~/.azure/acsServicePrincipal.json` na počítači, který jste ke spuštění příkazu použili.

* Pokud použijete příkaz `az acs create` k automatickému vygenerování instančního objektu, bude se tento instanční objekt moci ověřovat také pomocí služby [Azure Container Registry](../../container-registry/container-registry-intro.md) vytvořené ve stejném předplatném.

* Přihlašovací údaje instančního objektu můžou vypršet, což způsobí přechod uzlů clusteru do stavu **NotReady**. Informace o omezení rizik najdete v části [Vypršení platnosti přihlašovacích údajů](#credential-expiration).

## <a name="credential-expiration"></a>Vypršení platnosti přihlašovacích údajů

Pokud při vytváření instančního objektu nezadáte vlastní okno platnosti pomocí parametru `--years`, jsou přihlašovací údaje platné po dobu jednoho roku od času vytvoření. Když vyprší platnost přihlašovacích údajů, můžou uzly clusteru přejít do stavu **NotReady**.

Pokud chcete zkontrolovat datum vypršení platnosti instančního objektu, spusťte příkaz [az ad app show](/cli/azure/ad/app#az_ad_app_show) s parametrem `--debug` a vyhledejte hodnotu `endDate` pro `passwordCredentials` u konce výstupu:

```azurecli
az ad app show --id <appId> --debug
```

Výstup (zkrácené zobrazení):

```json
...
"passwordCredentials":[{"customKeyIdentifier":null,"endDate":"2018-11-20T23:29:49.316176Z"
...
```

Pokud vypršela platnost přihlašovacích údajů instančního objektu, použijte příkaz [az ad sp reset-credentials](/cli/azure/ad/sp#az_ad_sp_reset_credentials), abyste přihlašovací údaje aktualizovali:

```azurecli
az ad sp reset-credentials --name <appId>
```

Výstup:

```json
{
  "appId": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "name": "4fd193b0-e6c6-408c-a21a-803441ad2851",
  "password": "404203c3-0000-0000-0000-d1d2956f3606",
  "tenant": "72f988bf-0000-0000-0000b-2d7cd011db47"
}
```

Potom aktualizujte soubor `/etc/kubernetes/azure.json` novými přihlašovacími údaji na všech uzlech clusteru a uzly restartujte.

## <a name="next-steps"></a>Další kroky

* [Začněte používat Kubernetes](container-service-kubernetes-walkthrough.md) v clusteru služby kontejneru.

* Informace o řešení potíží s instančním objektem pro Kubernetes najdete v [dokumentaci k modulu ACS](https://github.com/Azure/acs-engine/blob/master/docs/kubernetes.md#troubleshooting).