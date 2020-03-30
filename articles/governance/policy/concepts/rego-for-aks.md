---
title: Naučte se zásady Azure pro službu Azure Kubernetes
description: Zjistěte, jak zásady Azure používají agenta zásad Rego a Open ke správě clusterů ve službě Azure Kubernetes.
ms.date: 03/27/2020
ms.topic: conceptual
ms.openlocfilehash: d77c5cf94a8239f4617e563961cbe1cc40e48fe0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80372645"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Principy služby Azure Policy pro službu Azure Kubernetes Service

Azure Policy se integruje se [službou Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) a aplikuje na škálovací vynucení a ochranná opatření na vaše clustery centralizovaným a konzistentním způsobem.
Rozšířením využití [Gatekeeper](https://github.com/open-policy-agent/gatekeeper) v3, _přijímací řadič webhook_ pro [Open Policy Agent](https://www.openpolicyagent.org/) (OPA), Azure Policy umožňuje spravovat a podávat zprávy o stavu dodržování vašich prostředků Azure a AKS clustery z jednoho místa.

> [!IMPORTANT]
> Azure Policy for AKS je ve verzi Preview a podporuje jenom integrované definice zásad. Integrované zásady jsou v kategorii **Kubernetes.** _Zastaralá_zásady **effectu EnforceRegoPolicy** a související **chodkařské služby** Jsou zastaralé . Místo toho použijte aktualizovaný efekt [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)

> [!WARNING]
> Tato funkce ještě není dostupná ve všech oblastech. Stav při zavedení naleznete v tématu [Problémy AKS – nejnovější změny doplňku zásad](https://github.com/Azure/AKS/issues/1529).

## <a name="overview"></a>Přehled

Pokud chcete povolit a používat Zásady Azure pro AKS s clusterem AKS, provázte následující akce:

- [Výslovný souhlas s funkcemi ve verzi Preview](#opt-in-for-preview)
- [Instalace doplňku zásad Azure](#installation-steps)
- [Přiřazení definice zásad pro AKS](#built-in-policies)
- [Počkejte na ověření](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Přihlášení pro náhled

Před instalací doplňku zásad Azure nebo povolením některé z funkcí služby musí vaše předplatné povolit poskytovatele prostředků **Microsoft.ContainerService** a poskytovatele prostředků **Microsoft.PolicyInsights** a poté získat schválení pro připojení k verzi Preview. Pokud se chcete připojit k předběžné verzi, postupujte takto na webu Azure Portal nebo pomocí azure cli:

- Portál Azure:

  1. Zaregistrujte poskytovatele prostředků **Microsoft.ContainerService** a **Microsoft.PolicyInsights.** Postup naleznete v tématu [Zprostředkovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

     ![Hledání zásad ve všech službách](../media/rego-for-aks/search-policy.png)

  1. Na levé straně stránky Zásadazure vyberte **Připojit se k verzi Preview.**

     ![Připojte se k verzi Policy for AKS preview](../media/rego-for-aks/join-aks-preview.png)

  1. Vyberte řádek předplatného, které chcete přidat do náhledu.

  1. V horní části seznamu předplatných vyberte tlačítko **Odhlásit** se.

- Cli Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Service provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  ```

## <a name="azure-policy-add-on"></a>Doplněk zásad Azure

Doplněk _zásad Azure_ pro Kubernetes propojuje službu Zásad Azure s řadičem přijetí Gatekeeper. Doplněk, který je nainstalován do oboru názvů _kube systému,_ uzákoní následující funkce:

- Zkontroluje pomocí služby Zásad Azure přiřazení do clusteru.
- Nasazuje zásady v clusteru jako vlastní prostředky [šablony omezení](https://github.com/open-policy-agent/gatekeeper#constraint-templates) a [omezení.](https://github.com/open-policy-agent/gatekeeper#constraints)
- Sestavy auditování a dodržování předpisů podrobnosti zpět do služby Zásad Azure.

### <a name="installing-the-add-on"></a>Instalace doplňku

#### <a name="prerequisites"></a>Požadavky

Před instalací doplňku do clusteru AKS je nutné nainstalovat rozšíření náhledu. Tento krok se provádí pomocí azure cli:

1. Pokud byly nainstalovány zásady Gatekeeper v2, odeberte doplněk s tlačítkem **Zakázat** v clusteru AKS na stránce **Zásady (náhled).**

1. Potřebujete nainstalované a nakonfigurované a nakonfigurované azure CLI verze 2.0.62 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Cluster AKS musí být verze _1.14_ nebo vyšší. K ověření verze clusteru AKS použijte následující skript:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Nainstalujte verzi _0.4.0_ rozšíření Azure CLI preview `aks-preview`pro AKS:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Pokud jste rozšíření _aks-preview_ nainstalovali dříve, nainstalujte všechny aktualizace pomocí příkazu. `az extension update --name aks-preview`

#### <a name="installation-steps"></a>Kroky instalace

Po dokončení požadavků nainstalujte doplněk zásad Azure do clusteru AKS, který chcete spravovat.

- portál Azure

  1. Spusťte službu AKS na webu Azure Portal kliknutím na **Všechny služby**a vyhledáním a výběrem **služeb Kubernetes**.

  1. Vyberte jeden z clusterů AKS.

  1. Na levé straně stránky služby Kubernetes vyberte **Zásady (náhled).**

     ![Zásady z clusteru AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na hlavní stránce vyberte tlačítko **Povolit doplněk.**

     ![Povolení doplňku Zásady Azure pro AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Pokud je tlačítko **Povolit doplněk** šedé, předplatné ještě nebylo přidáno do náhledu. Viz [Opt-in pro náhled](#opt-in-for-preview) pro požadované kroky. Pokud je k dispozici tlačítko **Zakázat,** Gatekeeper v2 je stále nainstalován a musí být odebrán.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Četnost ověřování a podávání zpráv

Doplněk se změnami pomocí služby Zásad Azure pro změny v přiřazení zásad každých 15 minut.
Během tohoto cyklu aktualizace doplněk zkontroluje změny. Tyto změny aktivační události vytvoří, aktualizace nebo odstranění šablony omezení a omezení.

> [!NOTE]
> Zatímco správce clusteru může mít oprávnění k vytváření a aktualizaci šablon omezení a prostředků omezení, tyto scénáře nejsou podporovány, protože ruční aktualizace budou přepsány.

Každých 15 minut doplněk volá úplné prohledávací clusteru. Po shromáždění podrobností o úplném prohledávači a všech hodnoceních gatekeeperu v reálném čase o pokusech o změny v clusteru, doplněk hlásí výsledky zpět do služby Zásad Azure pro zahrnutí [do podrobností o dodržování předpisů,](../how-to/get-compliance-data.md#portal) jako je jakékoli přiřazení zásad Azure. Během cyklu auditu jsou vráceny pouze výsledky pro aktivní přiřazení zásad. Výsledky auditu lze také považovat za [porušení](https://github.com/open-policy-agent/gatekeeper#audit) uvedené v poli stavu neúspěšného omezení.

## <a name="policy-language"></a>Jazyk zásad

Jazyková struktura zásad Azure pro správu Kubernetes se řídí existujícími zásadami. Efekt _EnforceOPAConstraint_ se používá ke správě clusterů Kubernetes a přebírá vlastnosti podrobnosti specifické pro práci s [opa rozhraní mantinelu](https://github.com/open-policy-agent/frameworks/tree/master/constraint) a Gatekeeper v3. Podrobnosti a příklady naleznete v efektu [EnforceOPAConstraint.](./effects.md#enforceopaconstraint)
  
Jako součást _details.constraintTemplate_ a _details.constraint_ vlastnosti v definici zásady Azure předá URI těchto [CustomResourceDefinitions](https://github.com/open-policy-agent/gatekeeper#constraint-templates) (CRD) na doplněk. Rego je jazyk, který OPA a Gatekeeper podporují k ověření požadavku na cluster Kubernetes. Podporou existujícího standardu pro správu Kubernetes umožňuje Azure Policy znovu použít stávající pravidla a spárovat je s Azure Policy pro jednotné prostředí pro vytváření přehledů dodržování předpisů v cloudu. Další informace naleznete v tématu [Co je Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Předdefinované zásady

Pokud chcete najít integrované zásady pro správu clusteru pomocí portálu Azure, postupujte takto:

1. Spusťte službu Zásad azure na webu Azure Portal. V levém podokně vyberte Všechny služby a pak vyhledejte a vyberte **zásady**.

1. V levém podokně stránky Zásady Azure vyberte **Definice**.

1. V rozevíracím seznamu Kategorie použijte k vymazání filtru výběr všech a pak vyberte **Kubernetes**.

1. Vyberte definici zásad a pak vyberte tlačítko **Přiřadit.**

1. Nastavte **obor** na skupinu pro správu, předplatné nebo skupinu prostředků clusteru Kubernetes, kde se bude vztahovat přiřazení zásad.

   > [!NOTE]
   > Při přiřazování zásad Azure pro definici AKS musí **obor** obsahovat prostředek clusteru AKS.

1. Pojmenujte **a** **popis,** který můžete snadno identifikovat.

1. Nastavte [vynucení zásad](./assignment-structure.md#enforcement-mode) na jednu z níže uvedených hodnot.

   - **Povoleno** – vynucujte zásady v clusteru. Kubernetes žádosti o přijetí s porušením jsou zamítnuty.
   
   - **Zakázáno** – Nevynucujte zásady v clusteru. Kubernetes žádá o přijetí s porušením nejsou zamítnuty. Výsledky hodnocení souladu jsou stále k dispozici. Při zavádění nových zásad pro spouštění clusterů je možnost _Zakázáno_ užitečná pro testování zásad, protože žádosti o přijetí s porušením nejsou odepřeny.

1. Vyberte **další**.

1. Nastavení **hodnot parametrů**
   
   - Chcete-li vyloučit obory názvů Kubernetes z vyhodnocení zásad, zadejte seznam oborů názvů v **parametru Vyloučení jmenného prostoru**. Doporučuje se vyloučit: _kube-systém_

1. Vyberte **Zkontrolovat a vytvořit**.

Alternativně použijte [přiřadit zásadu - portál](../assign-policy-portal.md) rychlý start najít a přiřadit zásady AKS. Vyhledejte definici zásad Kubernetes namísto vzorku "audit vms".

> [!IMPORTANT]
> Integrované zásady v kategorii **Kubernetes** jsou určeny pouze pro použití s AKS. Seznam předdefinovaných zásad naleznete v [tématu Kubernetes ovy ukázky](../samples/built-in-policies.md#kubernetes).

## <a name="logging"></a>protokolování

### <a name="azure-policy-add-on-logs"></a>Protokoly doplňků zásad Azure

Jako řadič/kontejner Kubernetes uchovávejte protokoly v clusteru AKS. Protokoly jsou vystaveny na stránce **Insights** clusteru AKS. Další informace najdete [v tématu Principy výkonu clusteru AKS pomocí Azure Monitor pro kontejnery](../../../azure-monitor/insights/container-insights-analyze.md).

## <a name="remove-the-add-on"></a>Odebrání doplňku

Pokud chcete doplněk zásad Azure odebrat z clusteru AKS, použijte buď portál Azure, nebo příkazové příkazové příkazové příkazy Azure:

- portál Azure

  1. Spusťte službu AKS na webu Azure Portal kliknutím na **Všechny služby**a vyhledáním a výběrem **služeb Kubernetes**.

  1. Vyberte cluster AKS, kde chcete zakázat doplněk zásad Azure.

  1. Na levé straně stránky služby Kubernetes vyberte **Zásady (náhled).**

     ![Zásady z clusteru AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na hlavní stránce vyberte tlačítko **Zakázat doplněk.**

     ![Zakázání doplňku Zásady Azure pro AKS](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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
