---
title: Informace Azure Policy pro službu Azure Kubernetes
description: Přečtěte si, jak Azure Policy používá Rego a Open Agent zásad ke správě clusterů ve službě Azure Kubernetes.
ms.date: 03/18/2020
ms.topic: conceptual
ms.openlocfilehash: f6c70d676914cf861ecc378efc4ec23a78879f6e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "82187711"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Principy služby Azure Policy pro službu Azure Kubernetes Service

Azure Policy se integruje se [službou Azure Kubernetes](../../../aks/intro-kubernetes.md) (AKS), která se použije při vynuceném škálování a ochraně vašich clusterů v centralizovaném, konzistentním způsobem.
Rozšířením použití [serveru gatekeeper](https://github.com/open-policy-agent/gatekeeper/tree/master/deprecated) v2 _Webhook kontroleru pro přístup_ k [otevřenému agentu zásad](https://www.openpolicyagent.org/) (neprů) umožňuje Azure Policy spravovat a hlásit stav dodržování předpisů vašich prostředků Azure a clusterů AKS z jednoho místa.

> [!NOTE]
> Azure Policy pro AKS je ve verzi omezené verze Preview a podporuje jenom integrované definice zásad.

## <a name="overview"></a>Přehled

Pokud chcete povolit a používat Azure Policy pro AKS s clusterem AKS, proveďte následující akce:

- [Výslovný souhlas s funkcemi ve verzi Preview](#opt-in-for-preview)
- [Instalace doplňku Azure Policy](#installation-steps)
- [Přiřazení definice zásady pro AKS](#built-in-policies)
- [Počkat na ověření](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Výslovný souhlas pro verzi Preview

Před instalací doplňku Azure Policy nebo povolením kterékoli funkce služby musí vaše předplatné umožňovat poskytovatele prostředků **Microsoft. ContainerService** a poskytovatele prostředků **Microsoft. PolicyInsights** a pak být schválené pro připojení k verzi Preview. Pokud se chcete připojit k verzi Preview, postupujte podle těchto kroků v Azure Portal nebo pomocí rozhraní příkazového řádku Azure CLI:

- Azure Portal:

  1. Zaregistrujte poskytovatele prostředků **Microsoft. ContainerService** a **Microsoft. PolicyInsights** . Postup najdete v tématu [poskytovatelé a typy prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#azure-portal).

  1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

     :::image type="content" source="../media/rego-for-aks/search-policy.png" alt-text="Vyhledat zásady ve všech službách" border="false":::

  1. Na levé straně stránky Azure Policy vyberte **Join Preview** .

     :::image type="content" source="../media/rego-for-aks/join-aks-preview.png" alt-text="Připojte se k zásadě pro AKS ve verzi Preview" border="false":::

  1. Vyberte řádek předplatného, které chcete přidat do verze Preview.

  1. V horní části seznamu předplatných vyberte tlačítko pro **výslovný souhlas** .

- Rozhraní příkazového řádku Azure:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AKS-AzurePolicyAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.ContainerService
  
  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataPlaneAutoApprove
  
  # Use the following to confirm the feature has registered
  az feature list -o table --query "[?contains(name, 'Microsoft.PolicyInsights/AKS-DataPlaneAutoApprove')].{Name:name,State:properties.state}"
  
  # Once the above shows 'Registered' run the following to propagate the update
  az provider register -n Microsoft.PolicyInsights
  
  ```

## <a name="azure-policy-add-on"></a>Doplněk Azure Policy

_Doplněk Azure Policy_ pro Kubernetes připojuje službu Azure Policy k řadiči pro přístup k serveru gatekeeper. Doplněk, který je nainstalovaný v oboru názvů _zásad Azure_ , doplňuje následující funkce:

- Kontroluje Azure Policy přiřazení do clusteru AKS.
- Podrobnosti o zásadách ke stažení a ukládání do mezipaměti, včetně definice zásad _Rego_ , jako **configmaps**
- Spustí kontrolu dodržování předpisů s úplnou kontrolou v clusteru AKS.
- Hlásí informace o auditování a dodržování předpisů zpátky na Azure Policy

### <a name="installing-the-add-on"></a>Instalace doplňku

#### <a name="prerequisites"></a>Požadavky

Před instalací doplňku v clusteru AKS je nutné nainstalovat rozšíření Preview. Tento krok se provádí pomocí Azure CLI:

1. Potřebujete nainstalovanou a nakonfigurovanou verzi Azure CLI 2.0.62 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI](/cli/azure/install-azure-cli).

1. Cluster AKS musí mít verzi _1,10_ nebo vyšší. K ověření verze clusteru AKS použijte následující skript:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Instalace verze _0.4.0_ rozšíření Azure CLI Preview pro AKS `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Pokud jste dříve nainstalovali rozšíření _AKS-Preview_ , nainstalujte všechny aktualizace pomocí `az extension update --name aks-preview` příkazu.

#### <a name="installation-steps"></a>Kroky instalace

Až se požadavky dokončí, nainstalujte doplněk Azure Policy v clusteru AKS, který chcete spravovat.

- portál Azure

  1. Spusťte v Azure Portal službu AKS kliknutím na **všechny služby**a pak vyhledejte a vyberte **služby Kubernetes**.

  1. Vyberte jeden z clusterů AKS.

  1. Na levé straně stránky služby Kubernetes vyberte **zásady (Preview)** .

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Zásady z clusteru AKS" border="false":::

  1. Na hlavní stránce vyberte tlačítko **Povolit doplněk** .

     :::image type="content" source="../media/rego-for-aks/enable-policy-add-on.png" alt-text="Povolení Azure Policy pro doplněk AKS" border="false":::

     > [!NOTE]
     > Pokud je tlačítko **Povolit doplněk** šedě zobrazené, předplatné ještě není přidané do verze Preview. Požadované kroky najdete v tématu [výslovný souhlas pro verzi Preview](#opt-in-for-preview) .

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Frekvence ověřování a generování sestav

Doplněk se vrátí se změnami Azure Policy pro změny v přiřazení zásad každých 5 minut. Během tohoto cyklu aktualizace doplněk odebere všechny _configmaps_ v oboru názvů _zásad Azure_ a pak znovu vytvoří _Configmaps_ pro použití pomocí serveru gatekeeper.

> [!NOTE]
> I když _Správce clusteru_ může mít oprávnění k oboru názvů _Azure-Policy_ , nedoporučuje se ani není podporován, aby provedl změny v oboru názvů. Jakékoli ručně provedené změny budou ztraceny během aktualizačního cyklu.

Každých 5 minut doplněk volá úplnou kontrolu clusteru. Po shromáždění podrobností o úplné kontrole a všech vyhodnocení v reálném čase pomocí serveru gatekeeper na základě změn v clusteru hlásí doplněk výsledky zpět do Azure Policy pro zahrnutí v [podrobnostech o dodržování předpisů](../how-to/get-compliance-data.md) , jako je jakékoli přiřazení Azure Policy. Během cyklu auditu se vrátí jenom výsledky aktivních přiřazení zásad.

## <a name="policy-language"></a>Jazyk zásad

Struktura Azure Policy jazyka pro správu AKS se řídí existujícími zásadami. Efekt _EnforceRegoPolicy_ se používá ke správě clusterů AKS a má _podrobné_ vlastnosti, které jsou specifické pro práci s neprů a gatekeeper v2. Podrobnosti a příklady najdete v [EnforceRegoPolicy](effects.md#enforceregopolicy) efektu.

Jako součást vlastnosti _Details. Policy_ v definici zásady Azure Policy předá do DOPLŇKU identifikátor URI zásady Rego. Rego je jazyk, který NEPRŮ a gatekeeper podporuje k ověření nebo splnění požadavku na cluster Kubernetes. Díky podpoře stávajícího standardu pro správu Kubernetes Azure Policy umožňuje znovu použít stávající pravidla a párovat je Azure Policy pro jednotné prostředí generování sestav dodržování předpisů cloudu. Další informace najdete v tématu [co je Rego?](https://www.openpolicyagent.org/docs/latest/policy-language/#what-is-rego).

## <a name="built-in-policies"></a>Předdefinované zásady

Pokud chcete najít předdefinované zásady pro správu AKS pomocí Azure Portal, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy v Azure Portal. V levém podokně vyberte **všechny služby** a pak vyhledejte a vyberte **zásady**.

1. V levém podokně stránky Azure Policy vyberte **definice**.

1. V rozevíracím seznamu kategorie použijte **možnost Vybrat vše** , pokud chcete filtr vymazat, a pak vyberte **Kubernetes Service**.

1. Vyberte definici zásady a pak klikněte na tlačítko **přiřadit** .

> [!NOTE]
> Při přiřazování Azure Policy pro definici AKS musí **obor** zahrnovat prostředek clusteru AKS.

Případně můžete k vyhledání a přiřazení zásad AKS použít rychlý Start [portálu zásad](../assign-policy-portal.md) . Místo ukázky "Auditovat virtuální počítače" vyhledejte definici zásady Kubernetes.

> [!IMPORTANT]
> Předdefinované zásady ve **službě kategorie Kubernetes** se používají jenom pro AKS.

## <a name="logging"></a>protokolování

### <a name="azure-policy-add-on-logs"></a>Azure Policy protokoly doplňků

Jako Kubernetes Controller nebo kontejner, Azure Policy doplněk udržuje protokoly v clusteru AKS. Protokoly se zveřejňují na stránce **Přehled** v clusteru AKS. Další informace najdete v tématu [vysvětlení výkonu clusteru AKS s Azure monitor pro kontejnery](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Protokoly gatekeeper

Pokud chcete povolit protokoly gatekeeper pro nové požadavky na prostředky, postupujte podle kroků v části [povolení a kontrola protokolů hlavního uzlu Kubernetes v AKS](../../../aks/view-master-logs.md).
Tady je příklad dotazu pro zobrazení zamítnutých událostí pro nové požadavky na prostředky:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Pokud chcete zobrazit protokoly z kontejnerů gatekeeper, postupujte podle kroků v části [povolení a kontrola protokolů hlavního uzlu Kubernetes v AKS](../../../aks/view-master-logs.md) a v podokně **nastavení diagnostiky** zaškrtněte možnost _Kube-apiserver_ .

## <a name="remove-the-add-on"></a>Odebrání doplňku

Pokud chcete odebrat doplněk Azure Policy z clusteru AKS, použijte Azure Portal nebo rozhraní příkazového řádku Azure:

- portál Azure

  1. Spusťte v Azure Portal službu AKS kliknutím na **všechny služby**a pak vyhledejte a vyberte **služby Kubernetes**.

  1. Vyberte cluster AKS, ve kterém chcete zakázat doplněk Azure Policy.

  1. Na levé straně stránky služby Kubernetes vyberte **zásady (Preview)** .

     :::image type="content" source="../media/rego-for-aks/policies-preview-from-aks-cluster.png" alt-text="Zásady z clusteru AKS" border="false":::

  1. Na hlavní stránce vyberte tlačítko **zakázat doplněk** .

     :::image type="content" source="../media/rego-for-aks/disable-policy-add-on.png" alt-text="Zakázat doplněk Azure Policy pro AKS" border="false":::

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
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