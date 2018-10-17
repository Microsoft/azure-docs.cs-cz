---
title: Instanční objekty pro službu Azure Kubernetes Service (AKS)
description: Vytvoření a správa instančního objektu služby Azure Active Directory pro cluster ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: get-started-article
ms.date: 09/26/2018
ms.author: iainfou
ms.openlocfilehash: ef3139c4b3f06644b219e177fad0c094ed600fb6
ms.sourcegitcommit: d1aef670b97061507dc1343450211a2042b01641
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2018
ms.locfileid: "47394586"
---
# <a name="service-principals-with-azure-kubernetes-service-aks"></a>Instanční objekty se službou Azure Kubernetes Service (AKS)

Pro interakci s rozhraními API Azure vyžaduje cluster AKS [instanční objekt služby Azure Active Directory (AD)][aad-service-principal]. Instanční objekt je potřeba k dynamickému vytváření a správě dalších prostředků Azure, jako je například nástroj pro vyrovnávání zatížení nebo registr kontejneru Azure (ACR).

Tento článek ukazuje, jak vytvořit a používat instanční objekt pro vaše clustery služby AKS.

## <a name="before-you-begin"></a>Než začnete

Abyste mohli vytvořit instanční objekt služby Azure AD, musíte mít oprávnění k registraci aplikace v tenantu Azure AD a přiřazení aplikace k roli v předplatném. Pokud nemáte potřebná oprávnění, možná budete muset požádat správce služby Azure AD nebo předplatného o jejich přiřazení nebo vytvořit instanční objekt pro použití se službou AKS předem.

Také musíte mít nainstalované a nakonfigurované rozhraní Azure CLI verze 2.0.46 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI][install-azure-cli].

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

## <a name="additional-considerations"></a>Další aspekty

Při použití instančních objektů služeb Azure AD a AKS mějte na paměti následující informace.

- Instanční objekt pro Kubernetes je součástí konfigurace clusteru. K nasazení clusteru ale nepoužívejte identitu.
- Každý instanční objekt je přidružený k aplikaci Azure AD. Instanční objekt pro cluster Kubernetes může být přidružený k jakémukoli platnému názvu aplikace Azure AD (například *https://www.contoso.org/example*). Adresa URL aplikace nemusí být skutečný koncový bod.
- Při zadávání **ID klienta** instančního objektu použijte hodnotu `appId`.
- Na hlavním virtuálním počítači a virtuálních počítačích uzlů v clusteru Kubernetes jsou pověření instančního objektu uložená v souboru `/etc/kubernetes/azure.json`.
- Pokud použijete příkaz [az aks create][az-aks-create] k automatickému vygenerování instančního objektu, zapíší se přihlašovací údaje instančního objektu do souboru `~/.azure/aksServicePrincipal.json` na počítači, který jste ke spuštění příkazu použili.
- Při odstraňování clusteru AKS vytvořeného příkazem [az aks create][az-aks-create] se instanční objekt, který se vytvořil automaticky, neodstraní.
    - Pokud chcete tento instanční objekt odstranit, nejdřív získejte jeho ID pomocí příkazu [az ad app list][az-ad-app-list]. Následující příkaz se dotazuje na cluster nazvaný *myAKSCluster* a potom odstraní ID aplikace pomocí příkazu [az ad app delete][az-ad-app-delete]. Tyto názvy nahraďte vlastními hodnotami:

        ```azurecli
        az ad app list --query "[?displayName=='myAKSCluster'].{Name:displayName,Id:appId}" --output table
        az ad app delete --id <appId>
        ```

## <a name="next-steps"></a>Další kroky

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
