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
ms.openlocfilehash: a5717d8ee44e4d2e086a6e7bc1b7c3d0deb614c8
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71827546"
---
# <a name="preview---use-managed-identities-in-azure-kubernetes-service"></a>Verze Preview – použití spravovaných identit ve službě Azure Kubernetes

V současné době musí uživatel pro cluster AKS (konkrétně poskytovatel cloudu Kubernetes) vytvořit instanční objekt, který má za účelem vytvoření dalších prostředků, jako jsou nástroje pro vyrovnávání zatížení a spravované disky v Azure, AKS. Instanční objekty se obvykle vytvářejí s datem vypršení platnosti. Clustery nakonec dosáhnou stavu, ve kterém bude potřeba obnovit instanční objekt, jinak cluster nebude fungovat. Správa objektů služby přináší složitost. Spravované identity jsou v podstatě obálkou objektů služby a zjednodušují správu. Další informace o [spravovaných identitách](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) najdete v článku.

AKS vytvoří dvě spravované identity pomocí jedné spravované identity přiřazené systémem a druhé identity přiřazené uživatelem. Spravovaná identita přiřazená systémem se používá poskytovatelem cloudu Kubernetes k vytváření prostředků Azure jménem uživatele. Životní cyklus této spravované identity přiřazené systémem se váže k tomu, že se cluster odstraní a že se odstraní. AKS také vytvoří spravovanou identitu přiřazenou uživatelem, která se používá v clusteru pro autorizaci AKS přístupu k záznamů ACR, kubelet pro získání metadat z Azure atd.

V této verzi Preview se stále vyžaduje instanční objekt. Bude se používat pro autorizaci doplňků, jako je monitorování, virtuální uzel, zásady Azure a směrování aplikace HTTP. V průběhu odebrání závislosti doplňků na hlavní název služby (SPN) a nakonec bude požadavek na hlavní název služby (SPN) v AKS zcela odebrán.

> [!IMPORTANT]
> Funkce služby AKS ve verzi Preview jsou samoobslužné přihlašovací. Verze Preview jsou k dispozici "tak jak jsou" a "jako dostupné" a jsou vyloučeny ze smluv o úrovni služeb a omezené záruky. AKS verze Preview jsou částečně pokryté zákaznickou podporou na základě nejlepšího úsilí. V takovém případě tyto funkce nejsou určeny pro použití v produkčním prostředí. Další informace o tom, jak se zaregistrují, najdete v následujících článcích podpory:
>
> * [Zásady podpory AKS](support-policies.md)
> * [Nejčastější dotazy k podpoře Azure](faq.md)

## <a name="before-you-begin"></a>Než začnete

Musíte mít následující:

* Budete také potřebovat Azure CLI verze 2.0.70 nebo novější a rozšíření 0.4.14 AKS-Preview.

## <a name="install-latest-aks-cli-preview-extension"></a>Nainstalovat nejnovější rozšíření AKS CLI Preview

Potřebujete rozšíření **AKS-Preview 0.4.14** nebo novější.

```azurecli
az extension update --name aks-preview 
az extension list
```

> [!CAUTION]
> Když zaregistrujete funkci v rámci předplatného, nemůžete tuto funkci v tuto chvíli zrušit. Po povolení některých funkcí verze Preview se můžou použít výchozí hodnoty pro všechny clustery AKS vytvořené v rámci předplatného. Nepovolujte funkce ve verzi Preview u produkčních předplatných. Použijte samostatné předplatné k testování funkcí ve verzi Preview a získejte zpětnou vazbu.

```azurecli-interactive
az feature register --name MSIPreview --namespace Microsoft.ContainerService
```

Zobrazení stavu *registrace*může trvat několik minut. Stav registrace můžete zjistit pomocí příkazu [az Feature list] [az-Feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/MSIPreview')].{Name:name,State:properties.state}"
```

Když je stav zaregistrován, aktualizujte registraci poskytovatele prostředků *Microsoft. ContainerService* pomocí příkazu [az Provider Register] [az-Provider-Register].

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-managed-identity"></a>Vytvoření clusteru AKS se spravovanou identitou

Cluster AKS se spravovanými identitami teď můžete vytvořit pomocí následujícího příkazu CLI.
```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

## <a name="create-an-aks-cluster"></a>Vytvoření clusteru AKS
```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-managed-identity
```

## <a name="get-credentials-to-access-the-cluster"></a>Získat přihlašovací údaje pro přístup ke clusteru
```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Po vytvoření clusteru v několika minutách můžete nasadit úlohy aplikace a s ní pracovat stejně jako s clustery AKS s využitím instančních služeb. 

> [!IMPORTANT]
> * Clustery AKS se spravovanými identitami se můžou povolit jenom při vytváření clusteru.
> * Stávající clustery AKS se nedají aktualizovat nebo upgradovat, aby se povolily spravované identity.