---
title: Instanční objekty pro službu Azure Kubernetes Service (AKS)
description: Vytvoření a správa instančního objektu služby Azure Active Directory pro cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: b8cbeacda98aec639724f30fe3a7e94346f05ba4
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308750"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Instanční objekty se službou Azure Kubernetes Service (AKS)

Pro interakci s rozhraními API Azure vyžaduje cluster AKS [instanční objekt služby Azure Active Directory (AD)][aad-service-principal]. Instanční objekt je potřeba k dynamickému vytváření a správě dalších prostředků Azure, jako je například nástroj pro vyrovnávání zatížení nebo registr kontejneru Azure (ACR).

Tento článek ukazuje, jak vytvořit a používat instanční objekt pro vaše clustery služby AKS.

## <a name="before-you-begin"></a>Před zahájením

Abyste mohli vytvořit instanční objekt služby Azure AD, musíte mít oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném. Pokud nemáte potřebná oprávnění, možná budete muset požádat správce služby Azure AD nebo předplatného o jejich přiřazení nebo vytvořit instanční objekt pro použití se službou AKS předem.

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Spustit `az --version` k vyhledání verze. Pokud potřebujete instalaci nebo upgrade, naleznete v tématu [instalace Azure CLI][install-azure-cli].

## <a name="automatically-create-and-use-a-service-principal"></a>Automatické vytvoření a použití instančního objektu

Při vytváření clusteru AKS v portálu Microsoft Azure nebo pomocí příkazu [az aks create] [ az-aks-create], služba Azure může automaticky vygenerovat instanční objekt.

V následujícím příkladu rozhraní příkazového řádku Azure není zadán instanční objekt. V tomto scénáři rozhraní příkazového řádku Azure vytvoří instanční objekt pro cluster AKS. K úspěšnému dokončení této operace musí mít váš účet Azure příslušná práva k vytvoření instančního objektu.

```azurecli
az aks create --name myAKSCluster --resource-group myResourceGroup --generate-ssh-keys
```

## <a name="manually-create-a-service-principal"></a>Ruční vytvoření instančního objektu

Pokud chcete ručně vytvořit instanční objekt pomocí Azure CLI, použijte příkaz [az ad sp create-for-rbac][az-ad-sp-create]. V následujícím příkladu parametr `--skip-assignment` zakazuje jakékoli další přiřazení výchozích přiřazení:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Výstup se podobá následujícímu příkladu. Poznamenejte si sami `appId` a `password`. Tyto hodnoty se používají při vytváření clusteru AKS v další části.

```json
{
  "appId": "559513bd-0c19-4c1a-87cd-851a26afd5fc",
  "displayName": "azure-cli-2018-09-25-21-10-19",
  "name": "http://azure-cli-2018-09-25-21-10-19",
  "password": "e763725a-5eee-40e8-a466-dc88d980f415",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db48"
}
```

## <a name="specify-a-service-principal-for-an-aks-cluster"></a>Specifikace instančního objektu pro cluster AKS

Chcete-li použít existující instanční objekt, když vytváříte cluster AKS pomocí příkazu [az aks create] [ az-aks-create], použijte parametry `--service-principal` a `--client-secret` k určení `appId` a `password` z výstupu příkazu [az ad sp create-for-rbac] [ az-ad-sp-create]:

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

## <a name="delegate-access-to-other-azure-resources"></a>Delegování přístupu k jiným prostředkům Azure

Instanční objekt pro AKS cluster je možné pro přístup k dalším prostředkům. Například pokud chcete použít rozšířeného sítě pro připojení k existující virtuální sítě nebo připojení k Azure Container Registry (ACR), musíte delegovat přístup k objektu služby.

Delegovat oprávnění, můžete vytvořit přiřazení role pomocí [vytvořit přiřazení role az] [ az-role-assignment-create] příkazu. Můžete přiřadit `appId` na konkrétní rozsah, jako jsou skupiny prostředků nebo prostředek virtuální sítě. Role potom definuje, jaká oprávnění instančního objektu má na prostředek, jak je znázorněno v následujícím příkladu:

```azurecli
az role assignment create --assignee <appId> --scope <resourceScope> --role Contributor
```

`--scope` Pro prostředek musí být úplné ID prostředku, jako například */subscriptions/\<guid\>/resourceGroups/myResourceGroup* nebo */subscriptions/\<guid \>/resourceGroups/myResourceGroupVnet/providers/Microsoft.Network/virtualNetworks/myVnet*

Následující části popisují běžné delegování, které musíte provést.

### <a name="azure-container-registry"></a>Azure Container Registry

Pokud používáte jako vaše úložiště imagí kontejnerů Azure Container Registry (ACR), budete muset udělit oprávnění pro váš cluster AKS ke čtení a stažení imagí. Instanční objekt clusteru AKS je potřeba delegovat *čtečky* role v registru. Podrobné pokyny najdete v článku [AKS udělit přístup do služby ACR][aks-to-acr].

### <a name="networking"></a>Sítě

Můžete použít rozšířeného sítě kde virtuální sítě a podsítě nebo veřejné IP adresy jsou v jiné skupině prostředků. Přiřadíte jednu z následující sadu oprávnění role:

- Vytvoření [vlastní roli] [ rbac-custom-role] a definovat následující oprávnění role:
  - *Microsoft.Network/virtualNetworks/subnets/join/action*
  - *Microsoft.Network/virtualNetworks/subnets/read*
  - *Microsoft.Network/publicIPAddresses/read*
  - *Microsoft.Network/publicIPAddresses/write*
  - *Microsoft.Network/publicIPAddresses/join/action*
- Nebo přiřadit [Přispěvatel sítě] [ rbac-network-contributor] předdefinovaná role v podsíti ve virtuální síti

### <a name="storage"></a>Storage

Potřebujete přístup k existující prostředky disku v jiné skupině prostředků. Přiřadíte jednu z následující sadu oprávnění role:

- Vytvoření [vlastní roli] [ rbac-custom-role] a definovat následující oprávnění role:
  - *Microsoft.Compute/disks/read*
  - *Microsoft.Compute/disks/write*
- Nebo přiřadit [Přispěvatel účtů úložiště] [ rbac-storage-contributor] předdefinovanou roli ve skupině prostředků.

### <a name="azure-container-instances"></a>Azure Container Instances

Pokud používáte Virtual Kubelet k integraci s AKS a zvolit spuštění služby Azure Container Instances (ACI) ve skupině prostředků samostatný AKS cluster, musí mít udělena instanční objekt služby AKS *Přispěvatel* oprávnění pro prostředek ACI Skupina.

## <a name="additional-considerations"></a>Další aspekty

Při použití instančních objektů služeb Azure AD a AKS mějte na paměti následující informace.

- Instanční objekt pro Kubernetes je součástí konfigurace clusteru. K nasazení clusteru ale nepoužívejte identitu.
- Každý instanční objekt je přidružený k aplikaci Azure AD. Instanční objekt pro cluster Kubernetes může být přidružený k jakémukoli platnému názvu aplikace Azure AD (například *https://www.contoso.org/example*). Adresa URL aplikace nemusí být skutečný koncový bod.
- Při zadávání **ID klienta** instančního objektu použijte hodnotu `appId`.
- Na hlavním virtuálním počítači a virtuálních počítačích uzlů v clusteru Kubernetes jsou pověření instančního objektu uložená v souboru `/etc/kubernetes/azure.json`.
- Pokud použijete příkaz [az aks create][az-aks-create] k automatickému vygenerování instančního objektu, zapíší se přihlašovací údaje instančního objektu do souboru `~/.azure/aksServicePrincipal.json` na počítači, který jste ke spuštění příkazu použili.
- Při odstraňování clusteru AKS vytvořeného příkazem [az aks create][az-aks-create] se instanční objekt, který se vytvořil automaticky, neodstraní.
    - Chcete-li odstranit objekt služby, dotaz pro váš cluster *servicePrincipalProfile.clientId* a odstraňte s [az ad app delete][az-ad-app-delete]. Následující zdroje skupiny a cluster názvy nahraďte vlastními hodnotami:

        ```azurecli
        az ad sp delete --id $(az aks show -g myResourceGroup -n myAKSCluster --query servicePrincipalProfile.clientId -o tsv)
        ```

## <a name="next-steps"></a>Další postup

Další informace o instančních objektech služby Azure Active Directory najdete v nabídce [Aplikace a instanční objekty služby][service-principal].

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
[aks-to-acr]: ../container-registry/container-registry-auth-aks.md?toc=%2fazure%2faks%2ftoc.json#grant-aks-access-to-acr
