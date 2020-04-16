---
title: Instanční objekty pro službu Azure Kubernetes Service (AKS)
description: Vytvoření a správa instančního objektu služby Azure Active Directory pro cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 04/02/2020
ms.openlocfilehash: 2c792eb4dc060e3f5d7fa2d8f2176bdd51538c43
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392728"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Instanční objekty se službou Azure Kubernetes Service (AKS)

Pro interakci s rozhraními API Azure vyžaduje cluster AKS buď [instanční objekt služby Azure Active Directory (AD),][aad-service-principal] nebo [spravovanou identitu](use-managed-identity.md). Instanční objekt nebo spravovanou identitu je potřeba k dynamickému vytváření a správě dalších prostředků Azure, jako je například azure vyrovnávání zatížení nebo registr kontejnerů (ACR).

Tento článek ukazuje, jak vytvořit a používat instanční objekt pro vaše clustery služby AKS.

## <a name="before-you-begin"></a>Než začnete

Abyste mohli vytvořit instanční objekt služby Azure AD, musíte mít oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném. Pokud nemáte potřebná oprávnění, možná budete muset požádat správce služby Azure AD nebo předplatného o jejich přiřazení nebo vytvořit instanční objekt pro použití se službou AKS předem.

Pokud používáte instanční objekt z jiného klienta Azure AD, existují další aspekty kolem oprávnění, která jsou k dispozici při nasazení clusteru. Pravděpodobně nemáte příslušná oprávnění ke čtení a zápisu informací o adresáři. Další informace najdete [v tématu Jaká výchozí uživatelská oprávnění ve službě Azure Active Directory?][azure-ad-permissions]

Potřebujete také nainstalované a nakonfigurované verze Azure CLI verze 2.0.59 nebo novější. Spuštěním `az --version` najděte verzi. Pokud potřebujete nainstalovat nebo upgradovat, přečtěte si informace [o instalaci příkazového příkazového příkazu k webu Azure][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatické vytvoření a použití instančního objektu

Při vytváření clusteru AKS v portálu Microsoft Azure nebo pomocí příkazu [az aks create][az-aks-create], služba Azure může automaticky vygenerovat instanční objekt.

V následujícím příkladu rozhraní příkazového řádku Azure není zadán instanční objekt. V tomto scénáři rozhraní příkazového řádku Azure vytvoří instanční objekt pro cluster AKS. K úspěšnému dokončení této operace musí mít váš účet Azure příslušná práva k vytvoření instančního objektu.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Ruční vytvoření instančního objektu

Pokud chcete ručně vytvořit instanční objekt pomocí Azure CLI, použijte příkaz [az ad sp create-for-rbac][az-ad-sp-create]. V následujícím příkladu parametr `--skip-assignment` zakazuje jakékoli další přiřazení výchozích přiřazení:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment --name myAKSClusterServicePrincipal
```

Výstup se podobá následujícímu příkladu. Poznamenejte si sami `appId` a `password`. Tyto hodnoty se používají při vytváření clusteru AKS v další části.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "myAKSClusterServicePrincipal",
  "name": "http://myAKSClusterServicePrincipal",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Specifikace instančního objektu pro cluster AKS

Chcete-li použít existující instanční objekt, když vytváříte cluster AKS pomocí příkazu [az aks create][az-aks-create], použijte parametry `--service-principal` a `--client-secret` k určení `appId` a `password` z výstupu příkazu [az ad sp create-for-rbac][az-ad-sp-create]:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

> [!NOTE]
> Pokud používáte existující instanční objekt s přizpůsobeným tajným klíči, ujistěte se, že tajný klíč není delší než 190 bajtů.

Pokud nasadíte cluster AKS pomocí portálu Microsoft Azure, na stránce *ověřování* dialogového okna **Vytvořit cluster Kubernetes** zvolit **Konfigurace instančního objektu**. Vyberte **Použít existující** a zadejte následující hodnoty:

- **ID klienta instančního objektu** je vaše *appId*
- **Tajný klíč klienta instančního objektu služby** je hodnota *hesla*

![Obrázek přechodu na aplikaci Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegování přístupu k dalším prostředkům Azure

Instanční objekt pro cluster AKS lze použít pro přístup k jiným prostředkům. Například pokud chcete nasadit cluster AKS do existující podsítě virtuální sítě Azure nebo se připojit k registru kontejnerů Azure (ACR), musíte delegovat přístup k těmto prostředkům na instanční objekt.

Chcete-li delegovat oprávnění, vytvořte přiřazení role pomocí příkazu [vytvořit přiřazení role az.][az-role-assignment-create] Přiřaďte `appId` konkrétní obor, například skupinu prostředků nebo prostředek virtuální sítě. Role pak definuje, jaká oprávnění má instanční objekt pro prostředek, jak je znázorněno v následujícím příkladu:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

Pro `--scope` prostředek musí být úplné ID prostředku, například */subscriptions/\<\>guid /resourceGroups/myResourceGroup* nebo */subscriptions/\<guid\>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

V následujících částech jsou podrobně popsány běžné delegace, které je možná nutné provést.

### <a name="azure-container-registry"></a>Azure Container Registry

Pokud používáte Azure Container Registry (ACR) jako úložiště bitových kopií kontejneru, budete muset udělit oprávnění k instančnímu objektu pro cluster AKS ke čtení a vyžádat image. V současné době je doporučená konfigurace je použít [az aks vytvořit][az-aks-create] nebo [az aks aktualizovat][az-aks-update] příkaz pro integraci s registrem a přiřadit příslušnou roli pro instanční objekt. Podrobné kroky najdete v [tématu Ověření pomocí registru kontejnerů Azure ze služby Azure Kubernetes][aks-to-acr].

### <a name="networking"></a>Sítě

Můžete použít rozšířené sítě, kde virtuální síť a podsítě nebo veřejné IP adresy jsou v jiné skupině prostředků. Přiřaďte jednu z následujících sad oprávnění role:

- Vytvořte [vlastní roli][rbac-custom-role] a definujte následující oprávnění role:
  - *Microsoft.Network/virtualNetworks/podsítě/join/action*
  - *Microsoft.Network/virtualNetworks/podsítě/čtení*
  - *Microsoft.Network/virtualNetworks/podsítě/zápis*
  - *Microsoft.Network/publicIPAdresy/připojit/akce*
  - *Microsoft.Network/publicIPAdresy/čtení*
  - *Microsoft.Network/publicIPAdresy/zápis*
- Nebo přiřaďte integrovanou roli [síťového přispěvatele][rbac-network-contributor] v podsíti ve virtuální síti.

### <a name="storage"></a>Storage

Možná budete muset získat přístup k existujícím diskovým prostředkům v jiné skupině prostředků. Přiřaďte jednu z následujících sad oprávnění role:

- Vytvořte [vlastní roli][rbac-custom-role] a definujte následující oprávnění role:
  - *Microsoft.Compute/disky/čtení*
  - *Microsoft.Compute/disky/zápis*
- Nebo přiřaďte integrovanou roli [přispěvatele účtu úložiště][rbac-storage-contributor] ve skupině prostředků.

### <a name="azure-container-instances"></a>Azure Container Instances

Pokud používáte Virtual Kubelet k integraci s AKS a zvolte spuštění azure container instance (ACI) ve skupině prostředků oddělené od clusteru AKS, musí být instančnímu objektu AKS udělena oprávnění *přispěvatele* ve skupině prostředků ACI.

## <a name="additional-considerations"></a>Další aspekty

Při použití instančních objektů služeb Azure AD a AKS mějte na paměti následující informace.

- Instanční objekt pro Kubernetes je součástí konfigurace clusteru. K nasazení clusteru ale nepoužívejte identitu.
- Ve výchozím nastavení jsou pověření instančního objektu platná po dobu jednoho roku. Pověření [zaregistrovaný objekt služby][update-credentials] můžete kdykoli aktualizovat nebo otočit.
- Každý instanční objekt je přidružený k aplikaci Azure AD. Instanční objekt pro cluster Kubernetes může být přidružen k libovolnému *https://www.contoso.org/example*platnému názvu aplikace Azure AD (například: ). Adresa URL aplikace nemusí být skutečný koncový bod.
- Při zadávání **ID klienta** instančního objektu použijte hodnotu `appId`.
- Na virtuálních počítačích uzlů agenta v clusteru Kubernetes jsou pověření instančního objektu uložena v souboru`/etc/kubernetes/azure.json`
- Pokud použijete příkaz [az aks create][az-aks-create] k automatickému vygenerování instančního objektu, zapíší se přihlašovací údaje instančního objektu do souboru `~/.azure/aksServicePrincipal.json` na počítači, který jste ke spuštění příkazu použili.
- Pokud nepředáte konkrétně instanční objekt v dalších příkazech Příkazového `~/.azure/aksServicePrincipal.json` příkazu KKS, použije se výchozí instanční objekt umístěný na adrese.  
- Můžete také volitelně odebrat soubor aksServicePrincipal.json a AKS vytvoří nový instanční objekt.
- Při odstraňování clusteru AKS vytvořeného příkazem [az aks create][az-aks-create] se instanční objekt, který se vytvořil automaticky, neodstraní.
    - Chcete-li odstranit instanční objekt, zadejte dotaz na clusterovou *službuPrincipalProfile.clientId* a poté odstraňte pomocí [odstranění aplikace az az az][az-ad-app-delete]. Nahraďte následující názvy skupin prostředků a clusterů vlastními hodnotami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Řešení potíží

Pověření instancí pro cluster AKS jsou uložena v mezipaměti pomocí příkazového příkazu k příkazu Konto Azure. Pokud platnost těchto pověření vypršela, dojde k chybám při nasazování clusterů AKS. Následující chybová zpráva při spuštění [az aks vytvořit][az-aks-create] může znamenat problém s přihlašovacími údaji služby uložené v mezipaměti:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Stáří souboru pověření zkontrolujte pomocí následujícího příkazu:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Výchozí doba vypršení platnosti pověření instančního objektu je jeden rok. Pokud je váš soubor *aksServicePrincipal.json* starší než jeden rok, odstraňte soubor a pokuste se znovu nasadit cluster AKS.

## <a name="next-steps"></a>Další kroky

Další informace o objektech služby Azure Active Directory naleznete v tématu [Objekty zaregistrovaný objekty aplikace a služby][service-principal].

Informace o tom, jak aktualizovat pověření, naleznete v [tématu Aktualizace nebo otočení pověření pro instanční objekt v AKS][update-credentials].

<!-- LINKS - internal -->
[aad-service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[acr-intro]: ../container-registry/container-registry-intro.md
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[azure-load-balancer-overview]: ../load-balancer/load-balancer-overview.md
[install-azure-cli]: /cli/azure/install-azure-cli
[service-principal]:../active-directory/develop/app-objects-and-service-principals.md
[user-defined-routes]: ../load-balancer/load-balancer-overview.md
[az-ad-app-list]: /cli/azure/ad/app#az-ad-app-list
[az-ad-app-delete]: /cli/azure/ad/app#az-ad-app-delete
[az-aks-create]: /cli/azure/aks#az-aks-create
[az-aks-update]: /cli/azure/aks#az-aks-update
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
