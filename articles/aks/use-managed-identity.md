---
title: Použití spravovaných identit ve službě Azure Kubernetes
description: Naučte se používat spravované identity ve službě Azure Kubernetes (AKS).
services: container-service
author: saudas
manager: saudas
ms.service: container-service
ms.topic: article
ms.date: 09/11/2019
ms.author: saudas
ms.openlocfilehash: 10ec07cab94184810e083c643e862cebe8e2431c
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2020
ms.locfileid: "77121428"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Verze Preview – použití spravovaných identit ve službě Azure Kubernetes

Cluster služby Azure Kubernetes (konkrétně poskytovatel cloudu Kubernetes) ale v současnosti vyžaduje *instanční objekt* k vytváření dalších prostředků, jako jsou nástroje pro vyrovnávání zatížení a spravované disky v Azure. Buď musíte zadat instanční objekt, nebo AKS ho vytvoří vaším jménem. Instanční objekty mají obvykle datum vypršení platnosti. Clustery nakonec dosáhnou stavu, ve kterém je nutné obnovit instanční objekt, aby byl cluster funkční. Správa objektů služby přináší složitost.

*Spravované identity* jsou v podstatě obálkou objektů služby a zjednoduší se jejich správa. Pokud se chcete dozvědět víc, přečtěte si o [spravovaných identitách prostředků Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS vytvoří dvě spravované identity:

- **Spravovaná identita přiřazená systémem**: identita, kterou poskytovatel cloudových služeb Kubernetes používá k vytvoření prostředků Azure jménem uživatele. Životní cyklus identity přiřazené systémem je svázán s clusterem. Identita se odstraní, když se cluster odstraní.
- **Spravovaná identita přiřazená uživatelem**: identita, která se používá k autorizaci v clusteru. Identita přiřazená uživatelem se například používá k autorizaci AKS k použití záznamů řízení přístupu (záznamů ACR) nebo k autorizaci kubelet k získání metadat z Azure.

V této verzi Preview se stále vyžaduje instanční objekt. Slouží k autorizaci doplňků, jako je monitorování, virtuální uzly, Azure Policy a směrování aplikace HTTP. Pracujeme na odebrání závislosti doplňků u hlavního názvu služby (SPN). Požadavek hlavního názvu služby (SPN) v AKS bude nakonec odebrán úplně.

> [!IMPORTANT]
> Funkce AKS ve verzi Preview jsou k dispozici na samoobslužné službě, na základě souhlasu. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace najdete v následujících článcích podpory:
>
> - [Zásady podpory AKS](support-policies.md)
> - [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalované následující zdroje:

- Rozhraní příkazového řádku Azure, verze 2.0.70 nebo novější
- Rozšíření AKS-Preview 0.4.14

Pokud chcete nainstalovat rozšíření AKS-Preview 0.4.14 nebo novější, použijte následující příkazy rozhraní příkazového řádku Azure CLI:

```azurecli
az extension add --name aks-preview
az extension list
```

> [!CAUTION]
> Po registraci funkce v předplatném nemůžete tuto funkci v tuto chvíli zrušit. Pokud povolíte některé funkce verze Preview, můžete použít výchozí nastavení pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Místo toho použijte k testování funkcí ve verzi Preview samostatné předplatné a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Může trvat několik minut, než se stav zobrazí jako **zaregistrované**. Stav registrace můžete zjistit pomocí příkazu [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Pokud se stav zobrazuje jako zaregistrované, aktualizujte registraci poskytovatele prostředků `Microsoft.ContainerService` pomocí příkazu [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identities"></a>Vytvoření clusteru AKS se spravovanými identitami

Pomocí následujících příkazů rozhraní příkazového řádku teď můžete vytvořit cluster AKS se spravovanými identitami.

Nejdřív vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Pak vytvořte cluster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

Nakonec Získejte přihlašovací údaje pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Cluster se vytvoří během několika minut. Pak můžete nasadit úlohy aplikace do nového clusteru a s nimi pracovat stejně jako s clustery AKS založenými na instančních službách.

> [!IMPORTANT]
>
> - Clustery AKS se spravovanými identitami se dají povolit jenom během vytváření clusteru.
> - Existující clustery AKS se nedají aktualizovat ani upgradovat, aby se povolily spravované identity.
