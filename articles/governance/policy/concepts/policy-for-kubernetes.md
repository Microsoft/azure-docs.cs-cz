---
title: Další informace Azure Policy Kubernetes
description: Přečtěte si, jak Azure Policy používá Rego a Open Agent zásad ke správě clusterů se systémem Kubernetes v Azure nebo místním prostředí.
ms.date: 09/29/2020
ms.topic: conceptual
ms.openlocfilehash: 67c6af4842ea1f404468497930b08c36ecd1abb9
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91540247"
---
# <a name="understand-azure-policy-for-kubernetes-clusters"></a>Principy Azure Policy pro clustery Kubernetes

Azure Policy rozšiřuje [Server Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, _Webhook kontroleru pro přístup_ k [otevřenému agentu zásad](https://www.openpolicyagent.org/) (neprů), který se použije při vynuceném škálování a ochraně vašich clusterů v centralizovaném, konzistentním způsobem. Azure Policy umožňuje spravovat a nahlásit stav dodržování předpisů clusterů Kubernetes z jednoho místa. Doplněk doplňuje následující funkce:

- Kontroluje přiřazení zásad do clusteru pomocí služby Azure Policy.
- Nasadí definice zásad do clusteru jako [šablonu omezení](https://github.com/open-policy-agent/gatekeeper#constraint-templates) a vlastní prostředky [omezení](https://github.com/open-policy-agent/gatekeeper#constraints) .
- Hlásí informace o auditování a dodržování předpisů zpátky do služby Azure Policy.

Azure Policy pro Kubernetes podporuje následující Clusterová prostředí:

- [Azure Kubernetes Service (AKS)](../../../aks/intro-kubernetes.md)
- [Kubernetes s podporou Azure Arc](../../../azure-arc/kubernetes/overview.md)
- [Modul AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md)

> [!IMPORTANT]
> Doplňky pro modul AKS a Kubernetes s podporou ARC jsou ve **verzi Preview**. Azure Policy pro Kubernetes podporují jenom fondy uzlů Linux a předdefinované definice zásad. Předdefinované definice zásad jsou v kategorii **Kubernetes** . Definice zásad omezené verze Preview s efektem **EnforceOPAConstraint** a **EnforceRegoPolicy** a související kategorií **služby Kubernetes** jsou _zastaralé_. Místo toho použijte _audit_ efektů a _Odepřít_ v režimu poskytovatele prostředků `Microsoft.Kubernetes.Data` .

## <a name="overview"></a>Přehled

Pokud chcete povolit a používat Azure Policy s clusterem Kubernetes, proveďte následující akce:

1. Nakonfigurujte cluster Kubernetes a nainstalujte doplněk:
   - [Azure Kubernetes Service (AKS)](#install-azure-policy-add-on-for-aks)
   - [Kubernetes s podporou Azure Arc](#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)
   - [Modul AKS](#install-azure-policy-add-on-for-aks-engine)

   > [!NOTE]
   > Běžné problémy s instalací najdete v tématu [řešení potíží – Azure Policy doplněk](../troubleshoot/general.md#add-on-installation-errors).

1. [Principy Azure Policyho jazyka pro Kubernetes](#policy-language)

1. [Přiřazení předdefinované definice ke clusteru Kubernetes](#assign-a-built-in-policy-definition)

1. [Počkat na ověření](#policy-evaluation)

## <a name="limitations"></a>Omezení

Na Azure Policy doplňku pro clustery Kubernetes se vztahují následující obecná omezení:

- Azure Policy doplněk pro Kubernetes je podporován na Kubernetes verze **1,14** nebo vyšší.
- Azure Policy doplňku pro Kubernetes lze nasadit pouze do fondů uzlů systému Linux.
- Podporují se jenom předdefinované definice zásad.
- Maximální počet nevyhovujících záznamů na zásadu na cluster: **500**
- Maximální počet nevyhovujících záznamů na předplatné: **1 000 000**
- Instalace serveru gatekeeper mimo Azure Policy doplňky se nepodporují. Než povolíte doplněk Azure Policy, odinstalujte všechny součásti nainstalované předchozí instalací serveru gatekeeper.
- [Důvody nedodržení předpisů](../how-to/determine-non-compliance.md#compliance-reasons) nejsou k dispozici pro `Microsoft.Kubernetes.Data` 
   [režim poskytovatele prostředků](./definition-structure.md#resource-provider-modes). Použijte [Podrobnosti součásti](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

Následující omezení platí pouze pro Azure Policy doplněk pro AKS:

- [Zásady zabezpečení AKS pod](../../../aks/use-pod-security-policies.md) a doplněk Azure Policy pro AKS nelze povolit současně. Další informace najdete v tématu [omezení zabezpečení AKS pod](../../../aks/use-pod-security-on-azure-policy.md#limitations).
- Obory názvů automaticky vyloučené Azure Policy doplněk pro vyhodnocení: _Kube-System_, _gatekeeper-System_a _AKS-Periscope_.

## <a name="recommendations"></a>Doporučení

Níže jsou uvedená obecná doporučení pro používání doplňku Azure Policy:

- Doplněk Azure Policy vyžaduje 3 součásti gatekeeper, které se mají spustit: 1 audit pod a 2 Webhook pod replikami. Tyto komponenty spotřebují více prostředků, protože počet prostředků Kubernetes a přiřazení zásad roste v clusteru, který vyžaduje operace auditu a vynucení.

  - Pro méně než 500 lusků v jednom clusteru s maximálním počtem 20 omezení: 2 vCPU a 350 MB paměti na součást.
  - Pro více než 500 lusků v jednom clusteru s maximálním počtem 40 omezení: 3 vCPU a 600 MB paměti na součást.

- Windows lusky [nepodporují kontexty zabezpečení](https://kubernetes.io/docs/concepts/security/pod-security-standards/#what-profiles-should-i-apply-to-my-windows-pods).
  Proto některé definice Azure Policy, jako je například nepovolení kořenových oprávnění, nelze eskalovat v oknech Windows a platí pouze pro lusky Linux.

Následující doporučení platí pouze pro AKS a doplněk Azure Policy:

- Použijte fond uzlů systému s příchodem `CriticalAddonsOnly` k naplánování na serveru gatekeeper lusky. Další informace najdete v tématu [použití fondů systémových uzlů](../../../aks/use-system-pools.md#system-and-user-node-pools).
- Zabezpečte odchozí přenosy z clusterů AKS. Další informace najdete v tématu [řízení provozu odchozích dat pro uzly clusteru](../../../aks/limit-egress-traffic.md).
- Pokud je cluster `aad-pod-identity` povolený, lusky NMI (Node Managed identity) upraví uzly na softwaru iptables tak, aby zachytil volání koncového bodu metadat instance Azure. Tato konfigurace znamená, že všechny požadavky na koncový bod metadat jsou zachyceny NMI i v případě, že pole pod nepoužívá `aad-pod-identity` . AzurePodIdentityException CRD je možné nakonfigurovat tak, aby informovala `aad-pod-identity` , že všechny požadavky na koncový bod metadat pocházející z objektu pod, který odpovídá popiskům definovaným v CRD, by měly být proxy bez jakéhokoli zpracování v NMI. Systém lusky s `kubernetes.azure.com/managedby: aks` návěštím v oboru názvů _Kube-System_ by měl být vyloučený v `aad-pod-identity` konfiguraci AzurePodIdentityException CRD. Další informace najdete v tématu [zakázání identity AAD-pod-identity pro konkrétního pod nebo aplikaci](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md).
  Chcete-li konfigurovat výjimku, nainstalujte [YAML s výjimkou mikrofonu](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

## <a name="install-azure-policy-add-on-for-aks"></a>Instalace doplňku Azure Policy pro AKS

Před instalací doplňku Azure Policy nebo povolením jakékoli funkce služby musí vaše předplatné umožňovat poskytovatele prostředků **Microsoft. ContainerService** a **Microsoft. PolicyInsights** .

1. Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Zaregistrujte poskytovatele prostředků a funkce ve verzi Preview.

   - Azure Portal:

     Zaregistrujte poskytovatele prostředků **Microsoft. ContainerService** a **Microsoft. PolicyInsights** . Postup najdete v tématu [poskytovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

   - Rozhraní příkazového řádku Azure:

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Kubernetes Service provider
     az provider register --namespace Microsoft.ContainerService

     # Provider register: Register the Azure Policy provider
     az provider register --namespace Microsoft.PolicyInsights
     ```

1. Pokud jsou nainstalované definice zásad s omezením verze Preview, odeberte doplněk pomocí tlačítka **Zakázat** v clusteru AKS na stránce **zásady** .

1. Cluster AKS musí mít verzi _1,14_ nebo vyšší. K ověření verze clusteru AKS použijte následující skript:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Nainstalujte verzi _2.12.0_ nebo novější z Azure CLI. Další informace najdete v tématu [instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

Po dokončení výše uvedených požadovaných kroků nainstalujte doplněk Azure Policy v clusteru AKS, který chcete spravovat.

- portál Azure

  1. Spusťte službu AKS v Azure Portal tím, že vyberete **všechny služby**a pak vyhledáte a vyberete **služby Kubernetes**.

  1. Vyberte jeden z clusterů AKS.

  1. Na levé straně stránky služby Kubernetes vyberte **zásady** .

  1. Na hlavní stránce vyberte tlačítko **Povolit doplněk** .

     <a name="migrate-from-v1"></a>
     > [!NOTE]
     > Pokud je povolené tlačítko **zakázat doplněk** a zobrazí se zpráva s upozorněním na migraci v2, nainstaluje se doplněk V1 a před přiřazením definic zásad v2 se musí odebrat. _Nepoužívané_ doplňky V1 se automaticky nahradí doplňkem v2 od 24. srpna.
     > 2020. Je potřeba přiřadit nové verze V2 definic zásad. Chcete-li upgradovat nyní, postupujte následovně:
     >
     > 1. Ověřte, jestli má cluster AKS nainstalovaný doplněk V1, na stránce **zásady** v clusteru AKS a s tím, že aktuální cluster používá Azure Policy doplněk v1... Zpráva.
     > 1. [Odeberte doplněk](#remove-the-add-on-from-aks).
     > 1. Kliknutím na tlačítko **Povolit doplněk** nainstalujete verzi v2 doplňku.
     > 1. [Přiřaďte verze V2 předdefinovaných zásad zásad v1.](#assign-a-built-in-policy-definition)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

Spusťte následující příkaz, abyste ověřili, že instalace doplňku byla úspěšná a že jsou spuštěné _zásady Azure_ a na _serveru gatekeeper_ .

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

Nakonec ověřte, že je nejnovější doplněk nainstalovaný spuštěním tohoto příkazu Azure CLI a nahraďte `<rg>` názvem vaší skupiny prostředků a `<cluster-name>` názvem vašeho clusteru AKS: `az aks show -g <rg> -n <cluster-name>` . Výsledek by měl vypadat podobně jako následující výstup a **config. verze** by měla být `v2` :

```output
"addonProfiles": {
    "azurepolicy": {
        "config": {
            "version": "v2"
        },
        "enabled": true,
        "identity": null
    },
}
```

## <a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes-preview"></a><a name="install-azure-policy-add-on-for-azure-arc-enabled-kubernetes"></a>Instalace doplňku Azure Policy pro Azure ARC s povoleným Kubernetes (Preview)

Před instalací doplňku Azure Policy nebo povolením kterékoli funkce služby musí vaše předplatné umožňovat poskytovatele prostředků **Microsoft. PolicyInsights** a vytvořit přiřazení role pro instanční objekt služby.

1. Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Pokud chcete poskytovatele prostředků povolit, postupujte podle kroků v části [poskytovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) nebo spusťte příkaz Azure CLI nebo Azure PowerShell:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Cluster Kubernetes musí mít verzi _1,14_ nebo vyšší.

1. Nainstalujte [Helm 3](https://v3.helm.sh/docs/intro/install/).

1. Váš cluster Kubernetes je povolený pro Azure ARC. Další informace najdete v tématu připojení [clusteru Kubernetes ke službě Azure ARC](../../../azure-arc/kubernetes/connect-cluster.md).

1. Máte plně kvalifikované ID prostředku Azure pro cluster Kubernetes s povoleným ARC Azure.

1. Otevřete porty pro doplněk. Azure Policy doplněk používá tyto domény a porty k načtení definic zásad a přiřazení a nahlášení kompatibility clusteru zpátky do Azure Policy.

   |Doména |Port |
   |---|---|
   |`gov-prod-policy-data.trafficmanager.net` |`443` |
   |`raw.githubusercontent.com` |`443` |
   |`login.windows.net` |`443` |
   |`dc.services.visualstudio.com` |`443` |

1. Přiřazení role zapisovače dat služby Application Insights (Preview) přiřaďte clusteru Kubernetes s povoleným ARC Azure. Nahraďte `<subscriptionId>` ID předplatného a `<rg>` skupinou prostředků clusteru Kubernetes s povoleným ARC Azure a `<clusterName>` názvem clusteru Kubernetes s povoleným ARC Azure. Udržujte si přehled o vrácených hodnotách _appId_, _Password_a _tenant_ pro kroky instalace.

   - Azure CLI

     ```azurecli-interactive
     az ad sp create-for-rbac --role "Policy Insights Data Writer (Preview)" --scopes "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     $sp = New-AzADServicePrincipal -Role "Policy Insights Data Writer (Preview)" -Scope "/subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>"

     @{ appId=$sp.ApplicationId;password=[System.Runtime.InteropServices.Marshal]::PtrToStringAuto([System.Runtime.InteropServices.Marshal]::SecureStringToBSTR($sp.Secret));tenant=(Get-AzContext).Tenant.Id } | ConvertTo-Json
     ```

   Ukázkový výstup výše uvedených příkazů:

   ```json
   {
       "appId": "aaaaaaaa-aaaa-aaaa-aaaa-aaaaaaaaaaaa",
       "password": "bbbbbbbb-bbbb-bbbb-bbbb-bbbbbbbbbbbb",
       "tenant": "cccccccc-cccc-cccc-cccc-cccccccccccc"
   }
   ```

Po dokončení výše uvedených požadovaných kroků nainstalujte doplněk Azure Policy v clusteru Kubernetes s povoleným ARC Azure:

1. Přidejte do Helm úložiště doplňku Azure Policy:

   ```bash
   helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
   ```

1. Pomocí grafu Helm nainstalujte doplněk Azure Policy:

   ```bash
   # In below command, replace the following values with those gathered above.
   #    <AzureArcClusterResourceId> with your Azure Arc enabled Kubernetes cluster resource Id. For example: /subscriptions/<subscriptionId>/resourceGroups/<rg>/providers/Microsoft.Kubernetes/connectedClusters/<clusterName>
   #    <ServicePrincipalAppId> with app Id of the service principal created during prerequisites.
   #    <ServicePrincipalPassword> with password of the service principal created during prerequisites.
   #    <ServicePrincipalTenantId> with tenant of the service principal created during prerequisites.
   helm install azure-policy-addon azure-policy/azure-policy-addon-arc-clusters \
       --set azurepolicy.env.resourceid=<AzureArcClusterResourceId> \
       --set azurepolicy.env.clientid=<ServicePrincipalAppId> \
       --set azurepolicy.env.clientsecret=<ServicePrincipalPassword> \
       --set azurepolicy.env.tenantid=<ServicePrincipalTenantId>
   ```

   Další informace o tom, co se instaluje graf doplňku Helm, najdete v tématu [Azure Policy definice grafu Helm](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-arc-clusters) na GitHubu.

Spusťte následující příkaz, abyste ověřili, že instalace doplňku byla úspěšná a že jsou spuštěné _zásady Azure_ a na _serveru gatekeeper_ .

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="install-azure-policy-add-on-for-aks-engine-preview"></a><a name="install-azure-policy-add-on-for-aks-engine"></a>Instalace doplňku Azure Policy pro modul AKS (Preview)

Před instalací doplňku Azure Policy nebo povolením kterékoli funkce služby musí vaše předplatné umožňovat poskytovatele prostředků **Microsoft. PolicyInsights** a vytvořit přiřazení role pro instanční objekt služby.

1. Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.62 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Pokud chcete poskytovatele prostředků povolit, postupujte podle kroků v části [poskytovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) nebo spusťte příkaz Azure CLI nebo Azure PowerShell:

   - Azure CLI

     ```azurecli-interactive
     # Log in first with az login if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     az provider register --namespace 'Microsoft.PolicyInsights'
     ```

   - Azure PowerShell

     ```azurepowershell-interactive
     # Log in first with Connect-AzAccount if you're not using Cloud Shell

     # Provider register: Register the Azure Policy provider
     Register-AzResourceProvider -ProviderNamespace 'Microsoft.PolicyInsights'
     ```

1. Vytvořte přiřazení role pro objekt služby Cluster Service.

   - Pokud neznáte ID aplikace instančního objektu služby, vyhledejte ji pomocí následujícího příkazu.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system

     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Přiřazení role zapisovače dat služby Application Insights (Preview) přiřaďte ID aplikace služby Cluster (hodnota _aadClientID_ z předchozího kroku) pomocí rozhraní příkazového řádku Azure CLI. Nahraďte `<subscriptionId>` ID předplatného a `<aks engine cluster resource group>` skupinou prostředků, ve které se cluster AKS Engine Kubernetese pro správu používá samostatně.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

Po dokončení výše uvedených požadovaných kroků nainstalujte doplněk Azure Policy. Instalace může být během cyklu vytváření nebo aktualizace modulu AKS nebo jako nezávislá akce v existujícím clusteru.

- Nainstalovat během vytváření nebo aktualizačního cyklu

  Pokud chcete povolit Azure Policy doplněk během vytváření nového clusteru, který je samostatně spravovaný, nebo jako Aktualizace existujícího clusteru, zahrňte do definice clusteru vlastností [Doplňky](https://github.com/Azure/aks-engine/tree/master/examples/addons/azure-policy) pro modul AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true
  }]
  ```

  Další informace o naleznete v tématu [definice clusteru modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)pro externí průvodce.

- Instalace v existujícím clusteru s Helm grafy

  K přípravě clusteru a instalaci doplňku použijte následující postup:

  1. Nainstalujte [Helm 3](https://v3.helm.sh/docs/intro/install/).

  1. Přidejte úložiště Azure Policy do Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Další informace najdete v tématu [Průvodce rychlým startem pro Helm graf](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Nainstalujte doplněk pomocí grafu Helm. Nahraďte `<subscriptionId>` ID předplatného a `<aks engine cluster resource group>` skupinou prostředků, ve které se cluster AKS Engine Kubernetese pro správu používá samostatně.

     ```bash
     helm install azure-policy-addon azure-policy/azure-policy-addon-aks-engine --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Další informace o tom, co se instaluje graf doplňku Helm, najdete v tématu [Azure Policy definice grafu Helm](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts/azure-policy-addon-aks-engine) na GitHubu.

     > [!NOTE]
     > Vzhledem k tomu, že vztah mezi doplňkem Azure Policy a ID skupiny prostředků Azure Policy podporuje pro každou skupinu prostředků pouze jeden cluster AKS Engine.

Spusťte následující příkaz, abyste ověřili, že instalace doplňku byla úspěšná a že jsou spuštěné _zásady Azure_ a na _serveru gatekeeper_ .

```bash
# azure-policy pod is installed in kube-system namespace
kubectl get pods -n kube-system

# gatekeeper pod is installed in gatekeeper-system namespace
kubectl get pods -n gatekeeper-system
```

## <a name="policy-language"></a>Jazyk zásad

Struktura Azure Policy jazyka pro správu Kubernetes se řídí existujícími definicemi zásad. Při použití [režimu poskytovatele prostředků](./definition-structure.md#resource-provider-modes) se `Microsoft.Kubernetes.Data` pro správu clusterů Kubernetes používají [audit](./effects.md#audit) a [zamítnutí](./effects.md#deny) efektů. _Audit_ a _zamítnutí_ musí poskytovat **podrobné** vlastnosti, které jsou specifické pro práci s [neprů omezením](https://github.com/open-policy-agent/frameworks/tree/master/constraint) a gatekeeper v3.

Jako součást _Details. constraintTemplate_ a _Details._ properties v definici zásad Azure Policy předá do doplňku identifikátory URI těchto [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD). Rego je jazyk, který podporuje NEPRŮ a gatekeeper, aby ověřil požadavek na cluster Kubernetes. Díky podpoře stávajícího standardu pro správu Kubernetes Azure Policy umožňuje znovu použít stávající pravidla a párovat je Azure Policy pro jednotné prostředí generování sestav dodržování předpisů cloudu. Další informace najdete v tématu [co je Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="assign-a-built-in-policy-definition"></a>Přiřazení definice předdefinované zásady

Pokud chcete přiřadit definici zásady ke clusteru Kubernetes, musíte mu přiřadit příslušné operace přiřazení zásad řízení přístupu na základě role Azure (RBAC). Tyto operace mají v předdefinovaných rolích **zásady prostředků Přispěvatel** a **vlastník** Azure. Další informace najdete v tématu [oprávnění Azure RBAC v Azure Policy](../overview.md#azure-rbac-permissions-in-azure-policy).

V následujících krocích najdete předdefinované definice zásad pro správu clusteru pomocí Azure Portal:

1. Spusťte službu Azure Policy v Azure Portal. V levém podokně vyberte **všechny služby** a pak vyhledejte a vyberte **zásady**.

1. V levém podokně stránky Azure Policy vyberte **definice**.

1. V rozevíracím seznamu kategorie použijte **možnost Vybrat vše** , pokud chcete filtr vymazat, a pak vyberte **Kubernetes**.

1. Vyberte definici zásady a pak klikněte na tlačítko **přiřadit** .

1. Nastavte **obor** na skupinu pro správu, předplatné nebo skupinu prostředků clusteru Kubernetes, kde se bude přiřazení zásady vztahovat.

   > [!NOTE]
   > Při přiřazování Azure Policy pro definici Kubernetes musí **obor** zahrnovat prostředek clusteru. V případě clusteru AKS Engine musí být **obor** skupinou prostředků clusteru.

1. Udělte přiřazení zásad **název** a **Popis** , který můžete použít k jeho snadnému identifikaci.

1. Nastavte [vynucování zásady](./assignment-structure.md#enforcement-mode) na jednu z následujících hodnot.

   - **Povoleno** – vynutilo zásady v clusteru. Žádosti o přístup Kubernetes s porušením jsou odepřeny.

   - **Zakázáno** – neuplatněte zásady v clusteru. Žádosti o přístup Kubernetes s porušením nejsou odepřeny. Výsledky hodnocení dodržování předpisů jsou stále k dispozici. Při zavedení nových definic zásad do spuštěných clusterů je možnost _disabled_ užitečná pro testování definice zásady, protože žádosti o přístup s porušením nejsou zamítnuté.

1. Vyberte **Další**.

1. Nastavení **hodnot parametrů**

   - Pokud chcete vyloučit obory názvů Kubernetes ze vyhodnocení zásad, zadejte seznam oborů názvů v **vyloučeních oboru názvů**parametrů. Doporučuje se vyloučit: _Kube-System_, _gatekeeper-System_a _Azure-ARC_.

1. Vyberte **Zkontrolovat a vytvořit**.

Případně můžete k vyhledání a přiřazení zásad Kubernetes použít rychlý Start [portálu zásad](../assign-policy-portal.md) . Místo ukázky "Auditovat virtuální počítače" vyhledejte definici zásady Kubernetes.

> [!IMPORTANT]
> Předdefinované definice zásad jsou k dispozici pro clustery Kubernetes ve skupině **Kubernetes**. Seznam předdefinovaných definic zásad najdete v tématu [Kubernetes Samples](../samples/built-in-policies.md#kubernetes).

## <a name="policy-evaluation"></a>Vyhodnocení zásad

Doplněk se vrátí se změnami v rámci služby Azure Policy pro změny v přiřazení zásad každých 15 minut.
Během tohoto cyklu aktualizace doplněk kontroluje změny. Tyto změny triggeru vytvoří, aktualizuje nebo odstraní šablony omezení a omezení.

Pokud má obor názvů v clusteru Kubernetes některý z následujících popisků, nejsou žádosti o přístup s narušením odepřeny. Výsledky hodnocení dodržování předpisů jsou stále k dispozici.

- `control-plane`
- `admission.policy.azure.com/ignore`

> [!NOTE]
> Správce clusterů může mít oprávnění k vytváření a aktualizaci šablon omezení a prostředků, které instalují Azure Policy doplněk. Tyto scénáře nejsou podporované, protože se přepíší ruční aktualizace. Server Gatekeeper nadále vyhodnocuje zásady, které existovaly před instalací doplňku a přiřazením Azure Policy definice zásad.

Každých 15 minut doplněk volá úplnou kontrolu clusteru. Po shromáždění podrobností o úplné kontrole a všech vyhodnocení v reálném čase pomocí serveru gatekeeper na základě změn v clusteru hlásí doplněk výsledky zpět do Azure Policy pro zahrnutí v [podrobnostech o dodržování předpisů](../how-to/get-compliance-data.md) , jako je jakékoli přiřazení Azure Policy. Během cyklu auditu se vrátí jenom výsledky aktivních přiřazení zásad. Výsledky auditu se taky můžou považovat za [porušení](https://github.com/open-policy-agent/gatekeeper#audit) uvedená v poli stav u neúspěšného omezení. Podrobnosti o _nekompatibilních_ prostředcích najdete v [podrobnostech o komponentách pro režimy poskytovatele prostředků](../how-to/determine-non-compliance.md#component-details-for-resource-provider-modes).

> [!NOTE]
> Každá sestava dodržování předpisů v Azure Policy pro clustery Kubernetes zahrnuje všechna porušení během posledních 45 minut. Časové razítko indikuje, kdy došlo k porušení.

## <a name="logging"></a>protokolování

Jako Kubernetes Controller nebo kontejner se v clusteru Kubernetes udržují protokoly _Azure-Policy_ i _gatekeeper_ . Protokoly se dají zveřejnit na stránce **Přehled** v clusteru Kubernetes.
Další informace najdete v tématu [monitorování výkonu clusteru Kubernetes s využitím Azure monitor pro kontejnery](../../../azure-monitor/insights/container-insights-analyze.md).

Chcete-li zobrazit protokoly doplňku, použijte `kubectl` :

```bash
# Get the azure-policy pod name installed in kube-system namespace
kubectl logs <azure-policy pod name> -n kube-system

# Get the gatekeeper pod name installed in gatekeeper-system namespace
kubectl logs <gatekeeper pod name> -n gatekeeper-system
```

Další informace najdete v tématu [ladění serveru gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) v dokumentaci k serveru gatekeeper.

## <a name="remove-the-add-on"></a>Odebrání doplňku

### <a name="remove-the-add-on-from-aks"></a>Odebrání doplňku z AKS

Pokud chcete odebrat doplněk Azure Policy z clusteru AKS, použijte Azure Portal nebo rozhraní příkazového řádku Azure:

- portál Azure

  1. Spusťte službu AKS v Azure Portal tím, že vyberete **všechny služby**a pak vyhledáte a vyberete **služby Kubernetes**.

  1. Vyberte cluster AKS, ve kterém chcete zakázat doplněk Azure Policy.

  1. Na levé straně stránky služby Kubernetes vyberte **zásady** .

  1. Na hlavní stránce vyberte tlačítko **zakázat doplněk** .

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="remove-the-add-on-from-azure-arc-enabled-kubernetes"></a>Odebrání doplňku z Kubernetes s povoleným ARC Azure

Pokud chcete odebrat Azure Policy doplněk a gatekeeper z clusteru Kubernetes s povoleným obloukem Azure, spusťte následující příkaz Helm:

```bash
helm uninstall azure-policy-addon
```

### <a name="remove-the-add-on-from-aks-engine"></a>Odebrání doplňku z AKS Engine

Chcete-li odebrat Azure Policy doplněk a server gatekeeper z clusteru AKS Engine, použijte metodu, která se zarovnává s tím, jak byl doplněk nainstalován:

- Pokud je nainstalován, nastavte vlastnost **Doplňky** v definici clusteru pro modul AKS:

  Po změně vlastnosti **Doplňky** pro _Azure_ na hodnotu false znovu nasaďte definici clusteru na modul AKS:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

  Další informace najdete v tématu [AKS Engine – zakázání doplňku Azure Policy](https://github.com/Azure/aks-engine/blob/master/examples/addons/azure-policy/README.md#disable-azure-policy-add-on).

- Pokud je nainstalovaná s Helm grafy, spusťte následující příkaz Helm:

  ```bash
  helm uninstall azure-policy-addon
  ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostická data shromážděná pomocí doplňku Azure Policy

Doplněk Azure Policy pro Kubernetes shromažďuje omezená diagnostická data clusteru. Tato diagnostická data jsou podstatná technická data týkající se softwaru a výkonu. Používá se následujícími způsoby:

- Udržování Azure Policyho doplňku v aktuálním stavu
- Udržování Azure Policy zabezpečených, spolehlivých a výkonných doplňků
- Vylepšení Azure Policyho doplňku agregovanou analýzou použití doplňku

Informace shromažďované doplňkem nejsou osobní údaje. V současné době jsou shromažďovány následující podrobnosti:

- Azure Policy verze agenta doplňku
- Typ clusteru
- Oblast clusteru
- Skupina prostředků clusteru
- ID prostředku clusteru
- ID předplatného clusteru
- Operační systém clusteru (příklad: Linux)
- Město clusteru (příklad: Seattle)
- Stav nebo provincie clusteru (příklad: Washington)
- Země nebo oblast clusteru (příklad: USA)
- Výjimky/chyby zjištěné Azure Policym doplňku během instalace agenta při vyhodnocování zásad
- Počet definic zásad pro gatekeeper, které nejsou nainstalované pomocí doplňku Azure Policy

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic zásad](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).