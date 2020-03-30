---
title: Naučte se zásady Azure pro AKS Engine
description: Zjistěte, jak zásady Azure používají vlastní definice prostředků a agenta otevřených zásad z gatekeeperu v3 ke správě clusterů pomocí Modulu AKS.
ms.date: 11/04/2019
ms.topic: conceptual
ms.openlocfilehash: c41a9d84dfe43e356e9a4a17af523a37209c2933
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75436429"
---
# <a name="understand-azure-policy-for-aks-engine"></a>Principy zásad Azure pro Modul AKS

Azure Policy se integruje se [systémem AKS Engine](https://github.com/Azure/aks-engine/blob/master/docs/README.md), který poskytuje pohodlné nástroje pro rychlé zavádění samoobslužného clusteru Kubernetes v Azure. Tato integrace umožňuje vynucování a zabezpečení ve vašem clusteru AKS Engine spravovanou centralizovaným a konzistentním způsobem. Rozšířením využití [Open Policy Agent](https://www.openpolicyagent.org/) (OPA) [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3 (beta), _vstupní řadič webhook_ pro Kubernetes, Azure Policy umožňuje spravovat a podávat zprávy o stavu dodržování vašich prostředků Azure a AKS Engine samoobslužné clustery z jednoho místa.

> [!NOTE]
> Zásady Azure pro AKS Engine je ve verzi Public Preview a nemá žádnou sla. Gatekeeper v3 je v Beta verzi a je podporován open source komunitou. Služba podporuje pouze předdefinované definice zásad a jeden cluster Modul AKS pro každou skupinu prostředků nakonfigurovanou pomocí instančního objektu.

> [!IMPORTANT]
> Chcete-li získat podporu pro zásady Azure pro AKS Engine, AKS Engine nebo Gatekeeper v3, vytvořte [nový problém](https://github.com/Azure/aks-engine/issues/new/choose) v úložišti AKS Engine GitHub.

## <a name="overview"></a>Přehled

Pokud chcete povolit a používat Azure Policy for AKS Engine s vlastním clusterem Kubernetes v Azure, provázte následující akce:

- [Požadavky](#prerequisites)
- [Instalace doplňku zásad Azure](#installing-the-add-on)
- [Přiřazení definice zásad pro modul AKS](#built-in-policies)
- [Počkejte na ověření](#validation-and-reporting-frequency)

## <a name="prerequisites"></a>Požadavky

Před instalací doplňku zásad Azure nebo povolením některé z funkcí služby musí vaše předplatné povolit poskytovatele prostředků **Microsoft.PolicyInsights** a vytvořit přiřazení role pro objekt zabezpečení clusterové služby. 

1. Pokud chcete povolit poskytovatele prostředků, postupujte podle pokynů v [části Zprostředkovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal) nebo spusťte příkaz Azure CLI nebo Azure PowerShell:

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

1. Vytvoření přiřazení role pro objekt zabezpečení clusterové služby

   - Pokud neznáte ID hlavního objektu clusterové služby, vyhledejte jej pomocí následujícího příkazu.

     ```bash
     # Get the kube-apiserver pod name
     kubectl get pods -n kube-system
   
     # Find the aadClientID value
     kubectl exec <kube-apiserver pod name> -n kube-system cat /etc/kubernetes/azure.json
     ```

   - Přiřazení role Zápisník dat (Preview) zásad přiřaďte k ID hlavní ho aplikace clusterové služby (hodnota _aadClientID_ z předchozího kroku) pomocí příkazového příkazu k příkazu k příkazu k řešení Azure. Nahraďte `<subscriptionId>` ID `<aks engine cluster resource group>` předplatného a skupinou prostředků, ve které se nachází samoobslužný cluster Kubernetes modulu AKS.

     ```azurecli-interactive
     az role assignment create --assignee <cluster service principal app ID> --scope "/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>" --role "Policy Insights Data Writer (Preview)"
     ```

## <a name="azure-policy-add-on"></a>Doplněk zásad Azure

Doplněk _zásad Azure_ pro Kubernetes propojuje službu Zásad Azure s řadičem přijetí Gatekeeper. Doplněk, který je nainstalován do oboru názvů _kube systému,_ uzákoní následující funkce:

- Zkontroluje pomocí zásad Azure přiřazení do clusteru Modul AKS
- Stahuje a instaluje podrobnosti zásad, šablony omezení a omezení.
- Spustí úplnou kontrolu dodržování předpisů v clusteru Modulu AKS.
- Sestavy auditování a dodržování předpisů podrobnosti zpět do zásad Azure

### <a name="installing-the-add-on"></a>Instalace doplňku

Po dokončení požadavků lze nainstalovat doplněk zásad Azure. Instalace může být během cyklu vytváření nebo aktualizace modulu AKS nebo jako nezávislá akce na existujícím clusteru.

- Instalace během vytváření nebo aktualizace cyklu

  Chcete-li povolit doplněk zásad Azure během vytváření nového clusteru s vlastním správou nebo jako aktualizaci existujícího clusteru, zahrňte definici clusteru **vlastností addons** pro Modul AKS.

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

  Další informace naleznete v externí příručce [Definice clusteru modulu AKS](https://github.com/Azure/aks-engine/blob/master/docs/topics/clusterdefinitions.md).

- Instalace v existujícím clusteru pomocí kormidelních grafů

  Pomocí následujících kroků připravte cluster a nainstalujte doplněk:

  1. Nainstalujte gatekeeper do oboru názvů _gatekeeper-system._

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```
  1. Přidejte popisek _roviny ovládacího prvku_ do _kube-system_. Tento popisek vylučuje auditování podů a služeb _kube systému_ gatekeeper a doplněk zásad Azure.

     ```bash
     kubectl label namespaces kube-system control-plane=controller-manager
     ```

  1. Synchronizujte data Kubernetes (Obor názvů, Pod, Ingress, Služba) s OPA.

     ```bash
     kubectl apply -f https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/gatekeeper-opa-sync.yaml
     ```

     Další informace naleznete v tématu [OPA - Replikace dat](https://github.com/open-policy-agent/gatekeeper#replicating-data).

  1. Přidejte repo zásad Azure do helmu.

     ```bash
     helm repo add azure-policy https://raw.githubusercontent.com/Azure/azure-policy/master/extensions/policy-addon-kubernetes/helm-charts
     ```

     Další informace naleznete v [tématu Helm Chart - Quickstart Guide](https://helm.sh/docs/using_helm/#quickstart-guide).

  1. Nainstalujte doplněk pomocí helmového grafu. Nahraďte `<subscriptionId>` ID `<aks engine cluster resource group>` předplatného a skupinou prostředků, ve které se nachází samoobslužný cluster Kubernetes modulu AKS.

     ```bash
     helm install azure-policy/azure-policy-addon-aks-engine --name azure-policy-addon --set azurepolicy.env.resourceid="/subscriptions/<subscriptionId>/resourceGroups/<aks engine cluster resource group>"
     ```

     Další informace o tom, co nainstalovat doplněk Helm Chart, najdete v [tématu Azure Policy Addon Helm Chart definice](https://github.com/Azure/azure-policy/tree/master/extensions/policy-addon-kubernetes/helm-charts) na GitHubu.

     > [!NOTE]
     > Vzhledem k vztahu mezi doplňkem zásad Azure a ID skupiny prostředků podporuje Zásady Azure pouze jeden cluster AKS Engine pro každou skupinu prostředků.

Chcete-li ověřit, zda byla instalace doplňku úspěšná a zda je spuštěn pod _zásad azure,_ spusťte následující příkaz:

```bash
kubectl get pods -n kube-system
```

### <a name="validation-and-reporting-frequency"></a>Četnost ověřování a podávání zpráv

Doplněk se změnami v zásadách Azure pro změny v přiřazení zásad každých 5 minut. Během tohoto cyklu aktualizace doplněk zkontroluje změny. Tyto změny aktivační události vytvoří, aktualizace nebo odstranění šablony omezení a omezení.

> [!NOTE]
> Zatímco _správce clusteru_ může mít oprávnění k provádění změn šablon omezení a omezení, není doporučeno ani podporováno provádět změny šablon omezení nebo omezení vytvořených zásadami Azure. Všechny provedené ruční změny budou během cyklu aktualizace ztraceny.

Každých 5 minut doplněk volá úplné prohledávací clusteru. Po shromáždění podrobností o úplném prohledávači a všech hodnoceních gatekeeperu v reálném čase o pokusech o změny v clusteru, doplněk hlásí výsledky zpět do zásad Azure pro zahrnutí [do podrobností o dodržování předpisů,](../how-to/get-compliance-data.md) jako je jakékoli přiřazení zásad Azure. Během cyklu auditu jsou vráceny pouze výsledky pro aktivní přiřazení zásad. Výsledky auditu lze také považovat za porušení uvedené v poli stavu neúspěšného omezení.

## <a name="policy-language"></a>Jazyk zásad

Jazyková struktura zásad Azure pro správu AKS Engine se řídí existujícími zásadami. Efekt _EnforceOPAConstraint_ se používá ke správě clusterů modulu AKS a přebírá _vlastnosti podrobnosti_ specifické pro práci s [architekturou omezení OPA](https://github.com/open-policy-agent/frameworks/tree/master/constraint) a Gatekeeper v3. Podrobnosti a příklady naleznete v efektu [EnforceOPAConstraint.](effects.md#enforceopaconstraint)

Jako součást _details.constraintTemplate_ a _details.constraint_ vlastnosti v definici zásady Azure předá URI těchto [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) na doplněk. Rego je jazyk, který OPA a Gatekeeper podporují k ověření požadavku na cluster Kubernetes. Podporou existujícího standardu pro správu Kubernetes umožňuje Azure Policy znovu použít stávající pravidla a spárovat je s Azure Policy pro jednotné prostředí pro vytváření přehledů dodržování předpisů v cloudu. Další informace naleznete v tématu [Co je Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Předdefinované zásady

Integrované zásady pro správu clusteru AKS Engine pomocí portálu Azure najdete takto:

1. Spusťte službu Zásad azure na webu Azure Portal. V levém podokně vyberte **Všechny služby** a pak vyhledejte a vyberte **zásady**.

1. V levém podokně stránky Zásady Azure vyberte **Definice**.

1. V rozevíracím seznamu Kategorie použijte k vymazání filtru výběr **všech** a pak vyberte **Kubernetes**.

1. Vyberte definici zásad a pak vyberte tlačítko **Přiřadit.**

> [!NOTE]
> Při přiřazování zásad Azure pro definici modulu AKS musí být **obor** skupinou prostředků clusteru ModulAA.

Alternativně použijte [přiřadit zásadu - portál](../assign-policy-portal.md) rychlý start najít a přiřadit zásady AKS Engine. Vyhledejte definici zásad modulu AKS namísto ukázkových "auditních virtuálních modulů".

> [!IMPORTANT]
> Vestavěné zásady v kategorii **Kubernetes** jsou určeny pouze pro použití s AKS Engine.

## <a name="logging"></a>protokolování

### <a name="azure-policy-add-on-logs"></a>Protokoly doplňků zásad Azure

Jako řadič/kontejner Kubernetes udržuje doplněk zásad Azure protokoly v clusteru Modulu AKS.

Chcete-li zobrazit protokoly doplňku `kubectl`zásad Azure, použijte :

```bash
# Get the Azure Policy Add-on pod name
kubectl -n kube-system get pods -l app=azure-policy --output=name

# Get the logs for the add-on
kubectl logs <Azure Policy Add-on pod name> -n kube-system
```

### <a name="gatekeeper-logs"></a>Gatekeeper protokoly

Pod Gatekeeper, _gatekeeper-controller-manager-0_, je `gatekeeper-system` `kube-system` obvykle v oboru názvů nebo, ale může být v jiném oboru názvů v závislosti na tom, jak je nasazen.

Chcete-li zobrazit protokoly `kubectl`Gatekeeper, použijte :

```bash
NAMESPACE=<namespace of gatekeeper>
kubectl logs gatekeeper-controller-manager-0 -n $NAMESPACE
```

Další informace naleznete v [tématu Ladění gatekeeper](https://github.com/open-policy-agent/gatekeeper#debugging) v dokumentaci OPA.

## <a name="remove-the-add-on"></a>Odebrání doplňku

Chcete-li odebrat doplněk zásad Azure a gatekeeper z clusteru Modulu AKS, použijte metodu, která je v souladu s tím, jak byl doplněk nainstalován:

- Pokud je nainstalována nastavením **vlastnosti addons** v definici clusteru pro Modul AKS:

  Znovu nasadit definici clusteru do AKS Engine po změně **addons** vlastnost pro _azure-policy_ na false:


  ```json
  "addons": [{
      "name": "azure-policy",
      "enabled": false
  }]
  ```

- Pokud je nainstalován s helm grafy:

  1. Odebrání starých omezení

     V současné době mechanismus odinstalace pouze odebere systém Gatekeeper, neodebere žádné _constrainttemplate_, _omezení_nebo _config_ prostředky, které byly vytvořeny uživatelem, ani neodebere jejich doprovodné _CRDs_.

     Když gatekeeper běží, je možné odstranit nežádoucí omezení:

     - Odstranění všech instancí prostředku omezení
     - Odstranění prostředku _ConstraintTemplate,_ který by měl automaticky vyčistit _crd_
     - Odstranění prostředku _Config_ odebere finalizační metody u synchronizovaných prostředků.

  1. Odinstalace doplňku zásad Azure
  
     ```bash
     helm del --purge azure-policy-addon
     ```

  1. Odinstalace gatekeeperu
  
     ```bash
     kubectl delete -f https://raw.githubusercontent.com/open-policy-agent/gatekeeper/master/deploy/gatekeeper.yaml
     ```

## <a name="diagnostic-data-collected-by-azure-policy-add-on"></a>Diagnostická data shromážděná doplňkem zásad Azure

Doplněk zásad Azure pro Kubernetes shromažďuje omezená diagnostická data clusteru. Tato diagnostická data jsou důležitými technickými údaji týkajícími se softwaru a výkonu. Používá se následujícími způsoby:

- Udržujte doplňky zásad Azure aktuální
- Zabezpečení, spolehlivost a výkonná podpora zásad Azure
- Vylepšete doplněk zásad Azure – prostřednictvím souhrnné analýzy použití doplňku

Informace shromážděné doplňkem nejsou osobními údaji. V současné době jsou shromažďovány následující podrobnosti:

- Verze agenta pro doplňky zásad Azure
- Typ clusteru
- Oblast clusteru
- Skupina prostředků clusteru
- ID prostředku clusteru
- ID předplatného clusteru
- Operační systém clusteru (příklad: Linux)
- Cluster město (Příklad: Seattle)
- Stát nebo provincie clusteru (příklad: Washington)
- Země nebo oblast clusteru (Příklad: Spojené státy)
- Výjimky nebo chyby, které při instalaci agenta při vyhodnocení zásad zjistil doplněk zásad Azure
- Počet zásad Gatekeeper, které nejsou nainstalovány doplňkem zásad Azure

## <a name="next-steps"></a>Další kroky

- Projděte si příklady na [ukázkách zásad Azure](../samples/index.md).
- Projděte si [strukturu definic zásad](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Pochopit, jak [programově vytvářet zásady](../how-to/programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](../how-to/get-compliance-data.md).
- Přečtěte si, jak [napravit nekompatibilní prostředky](../how-to/remediate-resources.md).
- Zkontrolujte, co je skupina pro správu [pomocí organizace Uspořádat prostředky pomocí skupin pro správu Azure](../../management-groups/overview.md).