---
title: Použití spravovaných identit ve službě Azure Kubernetes
description: Naučte se používat spravované identity ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 06/04/2020
ms.openlocfilehash: ae66c6a6fbfef2a6052a037e010ecdeb4256bfd8
ms.sourcegitcommit: ba8df8424d73c8c4ac43602678dae4273af8b336
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2020
ms.locfileid: "84456432"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Použití spravovaných identit ve službě Azure Kubernetes

Cluster služby Azure Kubernetes (konkrétně poskytovatel cloudu Kubernetes) vyžaduje identitu k vytváření dalších prostředků, jako jsou nástroje pro vyrovnávání zatížení a spravované disky v Azure. Tato identita může být buď *spravovaná identita* , nebo *instančního objektu*. Pokud používáte [instanční objekt](kubernetes-service-principal.md), musíte buď zadat jeden, nebo AKS ho vytvořit vaším jménem. Pokud používáte spravovanou identitu, vytvoří se pro vás AKS automaticky. Clustery s použitím instančních objektů nakonec dosáhnou stavu, ve kterém musí být instanční objekt obnovený, aby mohl cluster fungovat. Správa instančních objektů přináší složitost, což je důvod, proč je místo toho snazší použít spravované identity. Stejné požadavky oprávnění platí pro instanční objekty i spravované identity.

*Spravované identity* jsou v podstatě obálkou objektů služby a zjednoduší se jejich správa. K rotaci přihlašovacích údajů pro MSI dojde automaticky každých 46 dní podle Azure Active Directory výchozí. Pokud se chcete dozvědět víc, přečtěte si o [spravovaných identitách prostředků Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

AKS vytvoří dvě spravované identity:

- **Spravovaná identita přiřazená systémem**: identita, kterou poskytovatel cloudu Kubernetes používá k vytvoření prostředků Azure jménem uživatele, jako je třeba [Nástroj pro vyrovnávání zatížení](load-balancer-standard.md) nebo [Veřejná IP adresa](static-ip.md). Životní cyklus identity přiřazené systémem se váže k tomuto clusteru a měl by ho používat jenom poskytovatel cloudu. Identita se odstraní, když se cluster odstraní.

- **Spravovaná identita přiřazená uživatelem**: identita, která se používá k autorizaci v clusteru a cokoli jiného, co chcete ovládat. Například identita přiřazená uživatelem se používá k autorizaci AKS k použití záznamů ACR (Azure Container Registrys) nebo k autorizaci kubelet pro získání metadat z Azure.

Pomocí spravované identity se taky ověřují i doplňky. Pro každý doplněk je spravovaná identita vytvořena pomocí AKS a trvá po dobu života doplňku.

## <a name="before-you-begin"></a>Před zahájením

Musíte mít nainstalované následující prostředky:

- Rozhraní příkazového řádku Azure, verze 2.2.0 nebo novější

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

Úspěšné vytvoření clusteru pomocí spravovaných identit obsahuje tyto informace o profilu hlavního objektu služby:

```json
"servicePrincipalProfile": {
    "clientId": "msi",
    "secret": null
  }
```

> [!NOTE]
> Pro vytvoření a použití vlastní virtuální sítě, statické IP adresy nebo připojeného disku Azure, kde jsou prostředky mimo skupinu prostředků MC_ *, použijte k provedení přiřazení role PrincipalIDy spravované identity přiřazené systémem clusteru. Další informace o přiřazení rolí najdete v tématu [delegování přístupu k jiným prostředkům Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> K naplnění může trvat 60 minut i oprávnění pro spravovanou identitu clusteru, kterou používá poskytovatel cloudu Azure.

Nakonec Získejte přihlašovací údaje pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Cluster se vytvoří během několika minut. Pak můžete nasadit úlohy aplikace do nového clusteru a s nimi pracovat stejně jako s clustery AKS založenými na instančních službách.

> [!IMPORTANT]
>
> - Clustery AKS se spravovanými identitami se dají povolit jenom během vytváření clusteru.
> - Existující clustery AKS se nedají aktualizovat ani upgradovat, aby se povolily spravované identity.
