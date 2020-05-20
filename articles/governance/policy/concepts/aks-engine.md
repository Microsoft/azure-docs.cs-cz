---
title: Další Azure Policy pro modul AKS
description: Přečtěte si, jak Azure Policy používá CustomResourceDefinitions a Open Agent zásad z gatekeeper V3 ke správě clusterů pomocí stroje AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: f1d6f08f07e015b1e7fe2886746bf9e8b43fdce0
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/19/2020
ms.locfileid: "83654964"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Vysvětlení Azure Policy pro modul AKS

Azure Policy se integruje s [modulem AKS](https://github.com/Azure/aks-engine/blob/master/docs/README.md), což je systém, který poskytuje praktické nástroje pro rychlé spuštění samoobslužně spravovaného clusteru Kubernetes v Azure. Tato integrace umožňuje v rámci samoobslužného a konzistentního výkonu provádět vynucování a zabezpečení AKSch clusterů v modulu pro správu. Rozšířením používání [otevřeného agenta zásad](https://www.openpolicyagent.org/) (Neprů) [gatekeeper](https://github.com/open-policy-agent/gatekeeper) V3 (beta) se _Webhook kontroleru_ připojení pro Kubernetes Azure Policy umožňuje spravovat a nahlásit stav dodržování předpisů vašich prostředků Azure a samoobslužně spravovaných clusterů AKS Engine z jednoho místa.

> [!NOTE]
> Azure Policy pro modul AKS je v Public Preview a nemá žádnou smlouvu SLA. Server Gatekeeper v3 je ve verzi beta a je podporovaný komunitou open source. Služba podporuje pouze předdefinované definice zásad, fondy uzlů pro Linux a jeden cluster AKS Engine pro každou skupinu prostředků nakonfigurovanou s instančním objektem.

> [!IMPORTANT]
> Pokud chcete získat podporu pro Azure Policy modul AKS, AKS Engine nebo gatekeeper v3, vytvořte [Nový problém](https://github.com/Azure/aks-engine/issues/new/choose) v úložišti GitHubu AKS Engine.

## <a name="overview"></a>Přehled

Pokud chcete povolit a používat Azure Policy pro modul AKS s vaším samoobslužným clusterem Kubernetes v Azure, proveďte následující akce:

- [Požadavky](#prerequisites)
- [Instalace doplňku Azure Policy](#installing-the-add-on)
- [Přiřazení definice zásady pro modul AKS](#built-in-policies)
- [Počkat na ověření](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Požadavky

Před instalací doplňku Azure Policy nebo povolením kterékoli funkce služby musí vaše předplatné umožňovat poskytovatele prostředků **Microsoft. PolicyInsights** a vytvořit přiřazení role pro instanční objekt služby. 

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

1. Vytvoření přiřazení role pro objekt služby clusteru

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

## <a name="azure-policy-add-on"></a>Doplněk Azure Policy

_Doplněk Azure Policy_ pro Kubernetes připojuje službu Azure Policy k řadiči pro přístup k serveru gatekeeper. Doplněk, který je nainstalován do oboru názvů _Kube-System_ , doplňuje následující funkce:

- Kontroluje Azure Policy přiřazení k clusteru modulu AKS.
- Stažení a instalace podrobností zásad, šablon omezení a omezení
- Spustí kontrolu dodržování předpisů s úplnou kontrolou v clusteru AKS Engine.
- Hlásí informace o auditování a dodržování předpisů zpátky na Azure Policy

### <a name="installing-the-add-on"></a>Instalace doplňku

Až se požadavky dokončí, můžete nainstalovat doplněk Azure Policy. Instalace může být během cyklu vytváření nebo aktualizace modulu AKS nebo jako nezávislá akce v existujícím clusteru.

- Nainstalovat během vytváření nebo aktualizačního cyklu

  Pokud chcete povolit Azure Policy doplněk během vytváření nového clusteru, který je samostatně spravovaný, nebo jako Aktualizace existujícího clusteru, zahrňte do definice clusteru vlastností **Doplňky** pro modul AKS.

  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": true,
      "config": {
          "auditInterval": "30",
          "constraintViolationsLimit": "20"
      }
  }]
  ```

  Další informace o naleznete v tématu [definice clusteru modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md)pro externí průvodce.

- Instalace v existujícím clusteru s Helm grafy

  K přípravě clusteru a instalaci doplňku použijte následující postup:

  1. Nainstalujte server gatekeeper do oboru názvů _systému gatekeeper_ .

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Přidejte popisek _Control-rovina_ do _Kube-System_. Tento popisek vylučuje auditování _Kube_ a služeb pomocí služby Gatekeeper a Azure Policy doplňku.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchronizujte data Kubernetes (obor názvů, příchozí, příchozí, služba) pomocí NEPRŮ.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Další informace najdete v tématu [neprů data o replikaci](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Přidejte úložiště Azure Policy do Helm.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Další informace najdete v tématu [Průvodce rychlým startem pro Helm graf](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Nainstalujte doplněk pomocí grafu Helm. Nahraďte `<subscriptionId>` ID předplatného a `<aks engine cluster resource group>` skupinou prostředků, ve které se cluster AKS Engine Kubernetese pro správu používá samostatně.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Další informace o tom, co se instaluje graf doplňku Helm, najdete v tématu [Azure Policy definice grafu Helm](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) na GitHubu.

     > [!NOTE]
     > Vzhledem k tomu, že vztah mezi doplňkem Azure Policy a ID skupiny prostředků Azure Policy podporuje pro každou skupinu prostředků pouze jeden cluster AKS Engine.

Chcete-li ověřit, zda byla instalace doplňku úspěšná a zda je spuštěná _zásada Azure-_ pod, spusťte následující příkaz:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Frekvence ověřování a generování sestav

Doplněk se vrátí se změnami Azure Policy pro změny v přiřazení zásad každých 5 minut. Během tohoto cyklu aktualizace doplněk kontroluje změny. Tyto změny triggeru vytvoří, aktualizuje nebo odstraní šablony omezení a omezení.

> [!NOTE]
> I když _Správce clusteru_ může mít oprávnění k provádění změn v šablonách omezení a omezeních, nedoporučuje se ani se nepodporuje, aby provedl změny v šablonách omezení nebo omezeních vytvořených nástrojem Azure Policy. Jakékoli ručně provedené změny budou ztraceny během aktualizačního cyklu.

Každých 5 minut doplněk volá úplnou kontrolu clusteru. Po shromáždění podrobností o úplné kontrole a všech vyhodnocení v reálném čase pomocí serveru gatekeeper na základě změn v clusteru hlásí doplněk výsledky zpět do Azure Policy pro zahrnutí v [podrobnostech o dodržování předpisů](../how-to/get-compliance-data.md) , jako je jakékoli přiřazení Azure Policy. Během cyklu auditu se vrátí jenom výsledky aktivních přiřazení zásad. Výsledky auditu se taky můžou považovat za porušení uvedená v poli stav u neúspěšného omezení.

## <a name="policy-language"></a>Jazyk zásad

Struktura Azure Policy jazyka pro správu modulu AKS se řídí existujícími zásadami. Efekt _EnforceOPAConstraint_ se používá ke správě clusterů AKS Engine a má _podrobné_ vlastnosti, které jsou specifické pro práci s [omezeními neprů](https://github.com/open-policy-agent/frameworks/tree/master/constraint) a gatekeeper v3. Podrobnosti a příklady najdete v [EnforceOPAConstraint](effects.md#enforceopaconstraint) efektu.

Jako součást _Details. constraintTemplate_ a _Details._ properties v definici zásad Azure Policy předá do doplňku identifikátory URI těchto [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD). Rego je jazyk, který podporuje NEPRŮ a gatekeeper, aby ověřil požadavek na cluster Kubernetes. Díky podpoře stávajícího standardu pro správu Kubernetes Azure Policy umožňuje znovu použít stávající pravidla a párovat je Azure Policy pro jednotné prostředí generování sestav dodržování předpisů cloudu. Další informace najdete v tématu [co je Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Předdefinované zásady

Pokud chcete najít předdefinované zásady pro správu clusteru AKS Engine pomocí Azure Portal, postupujte takto:

1. Spusťte službu Azure Policy v Azure Portal. V levém podokně vyberte **všechny služby** a pak vyhledejte a vyberte **zásady**.

1. V levém podokně stránky Azure Policy vyberte **definice**.

1. V rozevíracím seznamu kategorie použijte **možnost Vybrat vše** , pokud chcete filtr vymazat, a pak vyberte **Kubernetes**.

1. Vyberte definici zásady a pak klikněte na tlačítko **přiřadit** .

> [!NOTE]
> Při přiřazování Azure Policy pro definici modulu AKS musí být **obor** skupinou prostředků clusteru AKS Engine.

Případně můžete k vyhledání a přiřazení zásad AKS Engine použít průvodce [přiřazením portálu zásad](../assign-policy-portal.md) . Místo ukázky "Auditovat virtuální počítače" vyhledejte definici zásad pro modul AKS.

> [!IMPORTANT]
> Předdefinované zásady v kategorii **Kubernetes** se používají jenom pro modul AKS.

## <a name="logging"></a>Protokolování

### <a name="azure-policy-add-on-logs"></a>Azure Policy protokoly doplňků

Jako Kubernetes Controller nebo kontejner, Azure Policy doplněk udržuje protokoly v clusteru AKS Engine.

Chcete-li zobrazit Azure Policy protokoly doplňku, použijte `kubectl` :

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Protokoly gatekeeper

Gatekeeper pod, _gatekeeper-Controller-Manager-0_je obvykle v `gatekeeper-system` `kube-system` oboru názvů nebo, ale může být v jiném oboru názvů v závislosti na tom, jak je nasazený.

Chcete-li zobrazit protokoly gatekeeper, použijte `kubectl` :

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Další informace najdete v tématu [ladění serveru gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) v dokumentaci k neprů.

## <a name="remove-the-add-on"></a>Odebrání doplňku

Chcete-li odebrat Azure Policy doplněk a server gatekeeper z clusteru AKS Engine, použijte metodu, která se zarovnává s tím, jak byl doplněk nainstalován:

- Pokud je nainstalován, nastavte vlastnost **Doplňky** v definici clusteru pro modul AKS:

  Po změně vlastnosti **Doplňky** pro _Azure_ na hodnotu false znovu nasaďte definici clusteru na modul AKS:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Pokud je nainstalován s Helm grafy:

  1. Odebrat stará omezení

     V současné době odinstalační mechanizmus odebere jenom systém gatekeeper, neodebere žádné prostředky _ConstraintTemplate_, _omezení_nebo _Konfigurace_ , které vytvořil uživatel, ani neodebere jejich doprovodné _CRDs_.

     Když je server gatekeeper spuštěný, je možné odebrat nežádoucí omezení:

     - Odstraňují se všechny instance prostředku omezení.
     - Odstranění prostředku _ConstraintTemplate_ , který by měl automaticky vyčistit _CRD_
     - Odstranění prostředku _Konfigurace_ odebere finalizační metody u synchronizovaných prostředků.

  1. Odinstalace doplňku Azure Policy
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Odinstalace serveru gatekeeper
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
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
- Počet zásad serveru gatekeeper, které nejsou nainstalované pomocí doplňku Azure Policy

## <a name="next-steps"></a>Další kroky

- Přečtěte si příklady na [Azure Policy Samples](../samples/index.md).
- Projděte si [strukturu definic zásad](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Zjistěte, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [opravit prostředky, které nedodržují předpisy](../how-to/remediate-resources.md).
- Seznamte se s tím, co skupina pro správu [organizuje vaše prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).