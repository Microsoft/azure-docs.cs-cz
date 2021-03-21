---
title: Použití spravovaných identit ve službě Azure Kubernetes
description: Naučte se používat spravované identity ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 12/16/2020
ms.openlocfilehash: 3ace7f1c93ab3918f460d245a863db43d98f1db5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102176089"
---
# <a name="use-managed-identities-in-azure-kubernetes-service"></a>Použití spravovaných identit ve službě Azure Kubernetes

V současné době cluster Azure Kubernetes Service (AKS) (konkrétně poskytovatel cloudu Kubernetes) vyžaduje identitu k vytváření dalších prostředků, jako jsou nástroje pro vyrovnávání zatížení a spravované disky v Azure. Tato identita může být buď *spravovaná identita* , nebo *instanční objekt*. Pokud používáte [instanční objekt](kubernetes-service-principal.md), musíte buď zadat jeden, nebo AKS ho vytvořit vaším jménem. Pokud používáte spravovanou identitu, vytvoří se pro vás AKS automaticky. Clustery s použitím instančních objektů nakonec dosáhnou stavu, ve kterém musí být instanční objekt obnovený, aby mohl cluster fungovat. Správa instančních objektů přináší složitost, což je důvod, proč je místo toho snazší použít spravované identity. Stejné požadavky oprávnění platí pro instanční objekty i spravované identity.

*Spravované identity* jsou v podstatě obálkou objektů služby a zjednoduší se jejich správa. K rotaci přihlašovacích údajů pro MI dochází automaticky každých 46 dní podle Azure Active Directory výchozí. AKS používá spravované typy identit přiřazené systémem i uživatelem. Tyto identity jsou momentálně neměnné. Pokud se chcete dozvědět víc, přečtěte si o [spravovaných identitách prostředků Azure](../active-directory/managed-identities-azure-resources/overview.md).

## <a name="before-you-begin"></a>Než začnete

Musíte mít nainstalované následující prostředky:

- Rozhraní příkazového řádku Azure, verze 2.15.1 nebo novější

## <a name="limitations"></a>Omezení

* Klienti se přesunou/nemigrují spravované clustery s povolenou identitou nejsou podporováni.
* Pokud je cluster `aad-pod-identity` povolený, Node-Managedí identity (NMI) lusky mění uzly softwaru iptables tak, aby zachytil volání koncového bodu metadat instance Azure. Tato konfigurace znamená, že všechny požadavky na koncový bod metadat jsou zachyceny NMI i v případě, že pole pod nepoužívá `aad-pod-identity` . AzurePodIdentityException CRD je možné nakonfigurovat tak, aby informovala `aad-pod-identity` , že všechny požadavky na koncový bod metadat pocházející z objektu pod, který odpovídá popiskům definovaným v CRD, by měly být proxy bez jakéhokoli zpracování v NMI. Systém lusky s `kubernetes.azure.com/managedby: aks` návěštím v oboru názvů _Kube-System_ by měl být vyloučený v `aad-pod-identity` konfiguraci AzurePodIdentityException CRD. Další informace najdete v tématu [zakázání identity AAD-pod-identity pro konkrétního pod nebo aplikaci](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).
  Chcete-li konfigurovat výjimku, nainstalujte [YAML s výjimkou mikrofonu](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="summary-of-managed-identities"></a>Souhrn spravovaných identit

AKS používá několik spravovaných identit pro předdefinované služby a doplňky.

| Identita                       | Name    | Případ použití | Výchozí oprávnění | Přineste si vlastní identitu
|----------------------------|-----------|----------|
| Řídicí rovina | neviditelné | Používá se součástmi ovládacího prvku AKS ke správě prostředků clusteru, včetně nástrojů pro vyrovnávání zatížení vstupu a AKS spravovaných veřejných IP adres a operací automatického škálování clusteru. | Role přispěvatele pro skupinu prostředků uzlu | Podporuje se
| Kubelet | Název clusteru AKS – neznámá | Ověřování pomocí Azure Container Registry (ACR) | NEDEF (pro Kubernetes v 1.15 +) | Aktuálně se nepodporuje.
| Doplněk | AzureNPM | Není nutná žádná identita. | NA | No
| Doplněk | Monitorování sítě AzureCNI | Není nutná žádná identita. | NA | No
| Doplněk | zásady Azure (gatekeeper) | Není nutná žádná identita. | NA | No
| Doplněk | Azure – zásada | Není nutná žádná identita. | NA | No
| Doplněk | Calico | Není nutná žádná identita. | NA | No
| Doplněk | Řídicí panel | Není nutná žádná identita. | NA | No
| Doplněk | HTTPApplicationRouting | Spravuje požadované síťové prostředky. | Role čtenáře pro skupinu prostředků uzlu, roli přispěvatele pro zónu DNS | No
| Doplněk | Aplikační brána příchozího přenosu dat | Spravuje požadované síťové prostředky.| Role přispěvatele pro skupinu prostředků uzlu | No
| Doplněk | omsagent | Slouží k posílání AKS metrik pro Azure Monitor | Role vydavatele metrik monitorování | No
| Doplněk | Virtual-Node (ACIConnector) | Spravuje požadované síťové prostředky pro Azure Container Instances (ACI). | Role přispěvatele pro skupinu prostředků uzlu | No
| Projekt OSS | AAD-pod-identity | Umožňuje aplikacím zabezpečeně přistupovat k prostředkům cloudu pomocí Azure Active Directory (AAD). | NA | Kroky pro udělení oprávnění v https://github.com/Azure/aad-pod-identity#role-assignment .

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

```output
"servicePrincipalProfile": {
    "clientId": "msi"
  }
```

Použijte následující příkaz k dotazování objectID spravované identity vaší řídicí plochy:

```azurecli-interactive
az aks show -g myResourceGroup -n myManagedCluster --query "identity"
```

Výsledek by měl vypadat takto:

```output
{
  "principalId": "<object_id>",   
  "tenantId": "<tenant_id>",      
  "type": "SystemAssigned"                                 
}
```

Po vytvoření clusteru můžete nasadit úlohy aplikace do nového clusteru a pracovat s nimi stejným způsobem jako s clustery AKS založenými na instančních službách.

> [!NOTE]
> Při vytváření a používání vlastní virtuální sítě, statické IP adresy nebo připojeného disku Azure, kde jsou prostředky mimo skupinu prostředků uzlu pracovního procesu, se k provedení přiřazení role používá PrincipalID spravované identity přiřazené systémem clusteru. Další informace o přiřazení rolí najdete v tématu [delegování přístupu k jiným prostředkům Azure](kubernetes-service-principal.md#delegate-access-to-other-azure-resources).
>
> K naplnění může trvat 60 minut i oprávnění pro spravovanou identitu clusteru, kterou používá poskytovatel cloudu Azure.

Nakonec Získejte přihlašovací údaje pro přístup ke clusteru:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
## <a name="update-an-aks-cluster-to-managed-identities-preview"></a>Aktualizace clusteru AKS na spravované identity (Preview)

Teď můžete aktualizovat cluster AKS, který aktuálně pracuje s instančními objekty, aby fungoval se spravovanými identitami, a to pomocí následujících příkazů rozhraní příkazového řádku.

Nejdřív Zaregistrujte příznak funkce pro identitu přiřazenou systémem:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n MigrateToMSIClusterPreview
```

Aktualizujte identitu přiřazenou systémem:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity
```

Zaregistrujte příznak funkce pro uživatelem přiřazenou identitu:

```azurecli-interactive
az feature register --namespace Microsoft.ContainerService -n UserAssignedIdentityPreview
```

Aktualizujte identitu přiřazenou uživatelem:

```azurecli-interactive
az aks update -g <RGName> -n <AKSName> --enable-managed-identity --assign-identity <UserAssignedIdentityResourceID> 
```
> [!NOTE]
> Až budou identity přiřazené systémem nebo uživatelem přiřazené k spravované identitě aktualizované, proveďte `az aks nodepool upgrade --node-image-only` na svých uzlech aktualizaci spravované identity.

## <a name="bring-your-own-control-plane-mi"></a>Přineste si vlastní plochu ovládacího prvku MI
Vlastní identita roviny ovládacího prvku umožňuje přístup k existující identitě před vytvořením clusteru. Tato funkce umožňuje scénáře, jako je například použití vlastní virtuální sítě nebo outboundType UDR s předem vytvořenou spravovanou identitou.

Musíte mít nainstalované rozhraní příkazového řádku Azure CLI, verze 2.15.1 nebo novější.

### <a name="limitations"></a>Omezení
* Azure Government se momentálně nepodporuje.
* Azure Čína 21Vianet se momentálně nepodporuje.

Pokud ještě nemáte spravovanou identitu, měli byste se k tomu vytvořit například pomocí [AZ identity CLI][az-identity-create].

```azurecli-interactive
az identity create --name myIdentity --resource-group myResourceGroup
```
Výsledek by měl vypadat takto:

```output
{                                                                                                                                                                                 
  "clientId": "<client-id>",
  "clientSecretUrl": "<clientSecretUrl>",
  "id": "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity", 
  "location": "westus2",
  "name": "myIdentity",
  "principalId": "<principalId>",
  "resourceGroup": "myResourceGroup",                       
  "tags": {},
  "tenantId": "<tenant-id>>",
  "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
}
```

Pokud je vaše spravovaná identita součástí vašeho předplatného, můžete k jejímu dotazování použít [příkaz AZ identity CLI][az-identity-list] .  

```azurecli-interactive
az identity list --query "[].{Name:name, Id:id, Location:location}" -o table
```

Nyní můžete pomocí následujícího příkazu vytvořit cluster s existující identitou:

```azurecli-interactive
az aks create \
    --resource-group myResourceGroup \
    --name myManagedCluster \
    --network-plugin azure \
    --vnet-subnet-id <subnet-id> \
    --docker-bridge-address 172.17.0.1/16 \
    --dns-service-ip 10.2.0.10 \
    --service-cidr 10.2.0.0/24 \
    --enable-managed-identity \
    --assign-identity <identity-id> \
```

Úspěšné vytvoření clusteru s použitím vlastních spravovaných identit obsahuje tyto informace o profilu userAssignedIdentities:

```output
 "identity": {
   "principalId": null,
   "tenantId": null,
   "type": "UserAssigned",
   "userAssignedIdentities": {
     "/subscriptions/<subscriptionid>/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myIdentity": {
       "clientId": "<client-id>",
       "principalId": "<principal-id>"
     }
   }
 },
```

## <a name="next-steps"></a>Další kroky
* Pomocí [šablon Azure Resource Manager (ARM) ][aks-arm-template] vytvoříte clustery s povolenou správou identit.

<!-- LINKS - external -->
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[az-identity-create]: /cli/azure/identity#az-identity-create
[az-identity-list]: /cli/azure/identity#az-identity-list
