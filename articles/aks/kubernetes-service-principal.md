---
title: Instanční objekty pro službu Azure Kubernetes Service (AKS)
description: Vytvoření a správa instančního objektu služby Azure Active Directory pro cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: mlearned
ms.openlocfilehash: e24d930ec82ea92a040efeed3056a10917ce2b2a
ms.sourcegitcommit: b4665f444dcafccd74415fb6cc3d3b65746a1a31
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72263917"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Instanční objekty se službou Azure Kubernetes Service (AKS)

Pro interakci s rozhraními API Azure vyžaduje cluster AKS [instanční objekt služby Azure Active Directory (AD)][aad-service-principal]. Instanční objekt je potřeba k dynamickému vytváření a správě dalších prostředků Azure, jako je například nástroj pro vyrovnávání zatížení nebo registr kontejneru Azure (ACR).

Tento článek ukazuje, jak vytvořit a používat instanční objekt pro vaše clustery služby AKS.

## <a name="before-you-begin"></a>Než začnete

Abyste mohli vytvořit instanční objekt služby Azure AD, musíte mít oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném. Pokud nemáte potřebná oprávnění, možná budete muset požádat správce služby Azure AD nebo předplatného o jejich přiřazení nebo vytvořit instanční objekt pro použití se službou AKS předem.

Pokud používáte instanční objekt z jiného tenanta Azure AD, existují další okolnosti týkající se oprávnění, která jsou k dispozici při nasazení clusteru. Možná nemáte potřebná oprávnění ke čtení a zápisu informací o adresáři. Další informace najdete v tématu [co jsou výchozí oprávnění uživatele v Azure Active Directory?][azure-ad-permissions]

Potřebujete také nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.59 nebo novější. Pro nalezení verze spusťte `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [instalace Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatické vytvoření a použití instančního objektu

Když vytvoříte cluster AKS v Azure Portal nebo pomocí příkazu [AZ AKS Create][az-aks-create] , může Azure automaticky vygenerovat instanční objekt.

V následujícím příkladu rozhraní příkazového řádku Azure není zadán instanční objekt. V tomto scénáři rozhraní příkazového řádku Azure vytvoří instanční objekt pro cluster AKS. K úspěšnému dokončení této operace musí mít váš účet Azure příslušná práva k vytvoření instančního objektu.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup
```

## <a name="manually-create-a-service-principal"></a>Ruční vytvoření instančního objektu

Pokud chcete ručně vytvořit instanční objekt pomocí Azure CLI, použijte příkaz [AZ AD SP Create-for-RBAC][az-ad-sp-create] . V následujícím příkladu parametr `--skip-assignment` zakazuje jakékoli další přiřazení výchozích přiřazení:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu. Poznamenejte si sami `appId` a `password`. Tyto hodnoty se používají při vytváření clusteru AKS v další části.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2019-03-04-21-35-28",
  "name": "http://azure-cli-2019-03-04-21-35-28",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Specifikace instančního objektu pro cluster AKS

Pokud chcete použít existující instanční objekt, když vytvoříte cluster AKS pomocí příkazu [AZ AKS create][az-aks-create] `--client-secret` `--service-principal`, zadejte `appId` a `password` z výstupu příkazu [AZ AD SP Create-for-RBAC][az-ad-sp-create] :

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --service-principal <appId> \
    --client-secret <password>
```

Pokud nasadíte cluster AKS pomocí portálu Microsoft Azure, na stránce *ověřování* dialogového okna **Vytvořit cluster Kubernetes** zvolit **Konfigurace instančního objektu**. Vyberte **Použít existující** a zadejte následující hodnoty:

- **ID klienta instančního objektu** je vaše *appId*
- **Tajný klíč klienta instančního objektu služby** je hodnota *hesla*

![Obrázek přechodu na aplikaci Azure Vote](media/kubernetes-service-principal/portal-configure-service-principal.png)

## <a name="delegate-access-to-other-azure-resources"></a>Delegovat přístup k jiným prostředkům Azure

Instanční objekt pro cluster AKS se dá použít pro přístup k jiným prostředkům. Pokud například chcete nasadit cluster AKS do existující podsítě virtuální sítě Azure nebo připojit k Azure Container Registry (ACR), budete muset k těmto prostředkům delegovat přístup k instančnímu objektu.

Chcete-li delegovat oprávnění, vytvořte přiřazení role pomocí příkazu [AZ role Assignment Create][az-role-assignment-create] . Přiřaďte `appId` k určitému oboru, jako je například skupina prostředků nebo prostředek virtuální sítě. Role pak definuje, jaká oprávnění má instanční objekt u prostředku, jak je znázorněno v následujícím příkladu:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

`--scope` pro prostředek musí být ID úplného prostředku, jako je například */subscriptions/\<guid\>/resourceGroups/myResourceGroup* nebo */subscriptions/\<GUID\>/resourceGroups/myResourceGroupVnet/Providers/Microsoft.Network/virtualNetworks/myVnet*

V následujících částech najdete podrobné informace o běžných delegováních, která může být potřeba udělat.

### <a name="azure-container-registry"></a>Azure Container Registry

Pokud jako úložiště imagí kontejneru použijete Azure Container Registry (ACR), budete muset udělit oprávnění k instančnímu objektu pro váš cluster AKS ke čtení a vyžádání imagí. V současné době je doporučeným nastavením použití příkazu [AZ AKS Create][az-aks-create] nebo [az AKS Update] [az-AKS-Update] k integraci s registrem a přiřazení příslušné role k instančnímu objektu. Podrobný postup najdete v tématu [ověření pomocí Azure Container Registry služby Azure Kubernetes][aks-to-acr].

### <a name="networking"></a>Sítě

Můžete použít pokročilé sítě, ve kterých se virtuální síť a podsíť nebo veřejné IP adresy nacházejí v jiné skupině prostředků. Přiřaďte jednu z následujících sad oprávnění role:

- Vytvořte [vlastní roli][rbac-custom-role] a definujte následující oprávnění role:
  - *Microsoft. Network/virtualNetworks/subnets/JOIN/Action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/virtualNetworks/subnets/write*
  - *Microsoft. Network/publicIPAddresses/JOIN/Action*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
- Nebo přiřaďte integrovanou roli [Přispěvatel sítě][rbac-network-contributor] k podsíti v rámci virtuální sítě.

### <a name="storage"></a>Storage

Možná budete potřebovat přístup k existujícím diskovým prostředkům v jiné skupině prostředků. Přiřaďte jednu z následujících sad oprávnění role:

- Vytvořte [vlastní roli][rbac-custom-role] a definujte následující oprávnění role:
  - *Microsoft. COMPUTE/disky/číst*
  - *Microsoft. COMPUTE/disks/Write*
- Nebo přiřaďte do skupiny prostředků integrovanou roli [přispěvatele účtu úložiště][rbac-storage-contributor] .

### <a name="azure-container-instances"></a>Azure Container Instances

Pokud k integraci s AKS používáte Virtual Kubelet a v případě, že se rozhodnete spustit Azure Container Instances (ACI) ve skupině prostředků oddělené ke clusteru AKS, musí být ve skupině prostředků AKS udělená oprávnění *přispěvatele* .

## <a name="additional-considerations"></a>Další aspekty

Při použití instančních objektů služeb Azure AD a AKS mějte na paměti následující informace.

- Instanční objekt pro Kubernetes je součástí konfigurace clusteru. K nasazení clusteru ale nepoužívejte identitu.
- Ve výchozím nastavení jsou přihlašovací údaje instančního objektu platné po dobu jednoho roku. [Přihlašovací údaje instančního objektu můžete kdykoli aktualizovat nebo otáčet][update-credentials] .
- Každý instanční objekt je přidružený k aplikaci Azure AD. Instanční objekt pro cluster Kubernetes může být přidružený k jakémukoli platnému názvu aplikace Azure AD (například *https://www.contoso.org/example* ). Adresa URL aplikace nemusí být skutečný koncový bod.
- Při zadávání **ID klienta** instančního objektu použijte hodnotu `appId`.
- Na virtuálních počítačích uzlů agentů v clusteru Kubernetes se přihlašovací údaje instančního objektu ukládají do souboru `/etc/kubernetes/azure.json`
- Když použijete příkaz [AZ AKS Create][az-aks-create] k automatickému vygenerování instančního objektu, zapíší se přihlašovací údaje instančního objektu do souboru `~/.azure/aksServicePrincipal.json` v počítači, který se používá ke spuštění příkazu.
- Když odstraníte cluster AKS, který byl vytvořen pomocí [AZ AKS Create][az-aks-create], objekt služby, který byl vytvořen automaticky, nebude odstraněn.
    - Pokud chcete odstranit instanční objekt, zadejte dotaz na svůj cluster *servicePrincipalProfile. ClientID* a pak ho odstraňte pomocí příkazu [AZ AD App Delete][az-ad-app-delete]. Nahraďte následující názvy skupin prostředků a názvů clusterů vlastními hodnotami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="troubleshoot"></a>Řešení potíží

Přihlašovací údaje instančního objektu pro cluster AKS se ukládají do mezipaměti rozhraní příkazového řádku Azure CLI. Pokud vypršela platnost těchto přihlašovacích údajů, narazíte na chyby při nasazování AKS clusterů. Při spuštění [AZ AKS Create][az-aks-create] může znamenat problém s přihlašovacími údaji instančního objektu uloženého v mezipaměti následující chybová zpráva:

```console
Operation failed with status: 'Bad Request'.
Details: The credentials in ServicePrincipalProfile were invalid. Please see https://aka.ms/aks-sp-help for more details.
(Details: adal: Refresh request failed. Status Code = '401'.
```

Pomocí následujícího příkazu ověřte stáří souboru s přihlašovacími údaji:

```console
ls -la $HOME/.azure/aksServicePrincipal.json
```

Výchozí čas vypršení platnosti přihlašovacích údajů instančního objektu je jeden rok. Pokud je soubor *aksServicePrincipal. JSON* starší než jeden rok, odstraňte soubor a pokuste se znovu nasadit cluster AKS.

## <a name="next-steps"></a>Další kroky

Další informace o Azure Active Directory instančních objektech naleznete v tématu [Application and Service instanční objekty][service-principal].

Informace o tom, jak aktualizovat přihlašovací údaje, najdete v tématu [aktualizace nebo otočení přihlašovacích údajů pro instanční objekt v AKS][update-credentials].

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
[rbac-network-contributor]: ../role-based-access-control/built-in-roles.md#network-contributor
[rbac-custom-role]: ../role-based-access-control/custom-roles.md
[rbac-storage-contributor]: ../role-based-access-control/built-in-roles.md#storage-account-contributor
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[aks-to-acr]: cluster-container-registry-integration.md
[update-credentials]: update-credentials.md
[azure-ad-permissions]: ../active-directory/fundamentals/users-default-permissions.md
