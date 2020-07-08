---
title: Použití spravovaných identit ve službě Azure Kubernetes
description: Naučte se používat spravované identity ve službě Azure Kubernetes (AKS).
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/30/2020
ms.author: mlearned
ms.openlocfilehash: 30d1290f9eb7b2750f09e5e256d4dd212c7e4607
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610281"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Použití spravovaných identit ve službě Azure Kubernetes

V současné době cluster Azure Kubernetes Service (AKS) (konkrétně poskytovatel cloudu Kubernetes) vyžaduje identitu k vytváření dalších prostředků, jako jsou nástroje pro vyrovnávání zatížení a spravované disky v Azure. Tato identita může být buď *spravovaná identita* , nebo *instanční objekt*. Pokud používáte [instanční objekt](kubernetes-service-principal.md), musíte buď zadat jeden, nebo AKS ho vytvořit vaším jménem. Pokud používáte spravovanou identitu, vytvoří se pro vás AKS automaticky. Clustery s použitím instančních objektů nakonec dosáhnou stavu, ve kterém musí být instanční objekt obnovený, aby mohl cluster fungovat. Správa instančních objektů přináší složitost, což je důvod, proč je místo toho snazší použít spravované identity. Stejné požadavky oprávnění platí pro instanční objekty i spravované identity.

*Spravované identity* jsou v podstatě obálkou objektů služby a zjednoduší se jejich správa. K rotaci přihlašovacích údajů pro MI dochází automaticky každých 46 dní podle Azure Active Directory výchozí. AKS používá spravované typy identit přiřazené systémem i uživatelem. Tyto identity jsou momentálně neměnné. Pokud se chcete dozvědět víc, přečtěte si o [spravovaných identitách prostředků Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview).

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalované následující prostředky:

- Rozhraní příkazového řádku Azure, verze 2.2.0 nebo novější

## <a name="limitations"></a>Omezení

* Používání vlastních spravovaných identit se v současnosti nepodporuje.
* Clustery AKS se spravovanými identitami se dají povolit jenom během vytváření clusteru.
* Existující clustery AKS se nedají aktualizovat ani upgradovat, aby se povolily spravované identity.
* Během operací s **upgradem** clusteru je spravovaná identita dočasně nedostupná.

## <a name="summary-of-managed-identities"></a>Souhrn spravovaných identit

AKS používá několik spravovaných identit pro předdefinované služby a doplňky.

| Identita                       | Name    | Případ použití | Výchozí oprávnění | Přineste si vlastní identitu
|----------------------------|-----------|----------|
| Řídicí rovina | neviditelné | Používá AKS ke správě síťových prostředků, například k vytvoření nástroje pro vyrovnávání zatížení pro příchozí, veřejnou IP adresu atd.| Role přispěvatele pro skupinu prostředků uzlu | Aktuálně se nepodporuje.
| Kubelet | Název clusteru AKS – neznámá | Ověřování pomocí Azure Container Registry (ACR) | Role čtecího modulu pro skupinu prostředků uzlu | Aktuálně se nepodporuje.
| Doplněk | AzureNPM | Není nutná žádná identita. | NA | No
| Doplněk | Monitorování sítě AzureCNI | Není nutná žádná identita. | NA | No
| Doplněk | azurepolicy (gatekeeper) | Není nutná žádná identita. | NA | No
| Doplněk | azurepolicy | Není nutná žádná identita. | NA | No
| Doplněk | Calico | Není nutná žádná identita. | NA | No
| Doplněk | Řídicí panel | Není nutná žádná identita. | NA | No
| Doplněk | HTTPApplicationRouting | Spravuje požadované síťové prostředky. | Role čtenáře pro skupinu prostředků uzlu, roli přispěvatele pro zónu DNS | No
| Doplněk | Aplikační brána příchozího přenosu dat | Spravuje požadované síťové prostředky.| Role přispěvatele pro skupinu prostředků uzlu | No
| Doplněk | omsagent | Slouží k posílání AKS metrik pro Azure Monitor | Role vydavatele metrik monitorování | No
| Doplněk | Virtuální uzel (ACIConnector) | Spravuje požadované síťové prostředky pro Azure Container Instances (ACI). | Role přispěvatele pro skupinu prostředků uzlu | No


## <a name="create-an-aks-cluster-with-managed-identities"></a>Vytvoření clusteru AKS se spravovanými identitami

Pomocí následujících příkazů rozhraní příkazového řádku teď můžete vytvořit cluster AKS se spravovanými identitami.

Nejdřív vytvořte skupinu prostředků Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location westus2
```

Pak vytvořte cluster AKS:

```azurecli-interactive
az aks create -g myResourceGroup -n myManagedCluster --enable-managed-identity
```

Úspěšné vytvoření clusteru pomocí spravovaných identit obsahuje tyto informace o profilu hlavního objektu služby:

```json
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Použijte následující příkaz k dotazování objectID spravované identity vaší řídicí plochy:

```azurecli-interactive
az aks show -g myResourceGroup -n MyManagedCluster --query "identity"
```

Výsledek by měl vypadat takto:

```json
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

> [!NOTE]
> Při vytváření a používání vlastní virtuální sítě, statické IP adresy nebo připojeného disku Azure, kde jsou prostředky mimo skupinu prostředků uzlu pracovního procesu, se k provedení přiřazení role používá PrincipalID spravované identity přiřazené systémem clusteru. Další informace o přiřazení rolí najdete v tématu [delegování přístupu k jiným prostředkům Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> K naplnění může trvat 60 minut i oprávnění pro spravovanou identitu clusteru, kterou používá poskytovatel cloudu Azure.

Nakonec Získejte přihlašovací údaje pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```

Cluster se vytvoří během několika minut. Pak můžete nasadit úlohy aplikace do nového clusteru a s nimi pracovat stejně jako s clustery AKS založenými na instančních službách.

## <a name="next-steps"></a>Další kroky
* Pomocí [šablon Azure Resource Manager (ARM)][aks-arm-template] vytvoříte clustery s povolenou správou identit.

<!-- LINKS - external -->
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters
