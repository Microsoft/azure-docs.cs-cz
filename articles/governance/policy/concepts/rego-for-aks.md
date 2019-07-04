---
title: Pochopit, jak auditovat obsah virtuálního počítače
description: Zjistěte, jak Azure Policy využívá Rego a otevřete agenta zásad ke správě clusterů ve službě Azure Kubernetes Service.
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: fdb392533e28df1d50e90c842d0117385afb254b
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453902"
---
# <a name="understand-azure-policy-for-azure-kubernetes-service"></a>Principy Azure Policy pro službu Azure Kubernetes

Se integruje s Azure Policy [Azure Kubernetes Service](../../../aks/intro-kubernetes.md) (AKS) pro použití enforcements ve velkém měřítku a bezpečnostní opatření na své clustery centralizované, konzistentním způsobem.
Tím, že rozšíří použití [vrátný](https://github.com/open-policy-agent/gatekeeper), _jejich příchodu řadič webhooku_ pro [otevřete agenta zásad](https://www.openpolicyagent.org/) (NEPRŮ), služba Azure Policy umožňuje spravovat a tvorba sestav o dodržování předpisů stav vašich prostředků Azure a AKS clusterů na jednom místě.

> [!NOTE]
> Služba Azure Policy pro AKS je ve verzi Limited Preview a podporuje pouze integrované definice zásad.

## <a name="overview"></a>Přehled

K povolení a používání služby Azure Policy pro AKS s vaším clusterem AKS, postupujte takto:

- [Vyjádřit výslovný souhlas pro funkce ve verzi preview](#opt-in-for-preview)
- [Nainstalujte doplněk Azure Policy](#installation-steps)
- [Přiřazení definice zásady pro AKS](#built-in-policies)
- [Vyčkat, než ověření](#validation-and-reporting-frequency)

## <a name="opt-in-for-preview"></a>Vyjádřit výslovný souhlas pro verzi preview

Před instalací doplňku zásad Azure nebo některou z funkcí služby povolíte, musíte povolit předplatné **Microsoft.ContainerService** poskytovatele prostředků a **Microsoft.policyinsights do**poskytovatele prostředků, pak schválení pro verzi Preview. Získat verzi preview, nebo použijte následující postup na portálu Azure pomocí Azure CLI:

- Azure portal:

  1. Zaregistrujte **Microsoft.ContainerService** a **Microsoft.policyinsights do** poskytovatele prostředků. Pokyny najdete v tématu [poskytovatelé a typy prostředků](../../../azure-resource-manager/resource-manager-supported-services.md#azure-portal).

  1. Spusťte službu Azure Policy na webu Azure Portal tak, že kliknete na **Všechny služby** a pak vyhledáte a vyberete **Zásady**.

     ![Hledání zásad ve všech služeb](../media/rego-for-aks/search-policy.png)

  1. Vyberte **připojte se k verzi Preview** na levé straně na stránku služby Azure Policy.

     ![Zásady pro AKS ve verzi preview pro připojení](../media/rego-for-aks/join-aks-preview.png)

  1. Vyberte řádek odběru, který má být přidána do verze preview.

  1. Vyberte **Opt-in** tlačítko v horní části seznamu předplatných.

- Azure CLI:

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  # Provider register: Register the Azure Kubernetes Services provider
  az provider register --namespace Microsoft.ContainerService

  # Provider register: Register the Azure Policy provider
  az provider register --namespace Microsoft.PolicyInsights

  # Feature register: enables installing the add-on
  az feature register --namespace Microsoft.ContainerService --name AKS-AzurePolicyAutoApprove

  # Feature register: enables the add-on to call the Azure Policy resource provider
  az feature register --namespace Microsoft.PolicyInsights --name AKS-DataplaneAutoApprove
  ```

## <a name="azure-policy-add-on"></a>Doplněk Azure Policy

_Doplněk zásad Azure_ pro Kubernetes propojí službu Azure Policy ke kontroleru jejich příchodu vrátný. Doplněk, který je nainstalovaný do _zásad azure_ obor názvů, představuje následující funkce:

- Kontroly službou Azure Policy pro přiřazení do clusteru AKS
- Soubory ke stažení a ukládá do mezipaměti zásad podrobnosti, včetně _rego_ definice zásad, jako **configmaps**
- Spustí kontrolu dodržování předpisů úplnou kontrolu v clusteru AKS
- Sestavy auditování a podrobnosti o dodržování předpisů zpět do Azure Policy

### <a name="installing-the-add-on"></a>Instalace doplňku

#### <a name="prerequisites"></a>Požadavky

Před instalací doplňku ve vašem clusteru AKS, musí být nainstalována rozšíření ve verzi preview. Tento krok se provádí pomocí Azure CLI:

1. Musí mít Azure CLI verze 2.0.62 nebo později nainstalována a nakonfigurována. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI](/cli/azure/install-azure-cli).

1. AKS cluster, musí být verze _1.10_ nebo vyšší. K ověření vaší verze clusteru AKS pomocí následujícího skriptu:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Look for the value in kubernetesVersion
   az aks list
   ```

1. Nainstalovat verzi _0.4.0_ rozhraní příkazového řádku Azure rozšíření ve verzi preview pro AKS, `aks-preview`:

   ```azurecli-interactive
   # Log in first with az login if you're not using Cloud Shell

   # Install/update the preview extension
   az extension add --name aks-preview

   # Validate the version of the preview extension
   az extension show --name aks-preview --query [version]
   ```

   > [!NOTE]
   > Pokud jste dříve nainstalovali _aks ve verzi preview_ rozšíření, některé aktualizace, pomocí instalace `az extension update --name aks-preview` příkazu.

#### <a name="installation-steps"></a>Postup instalace

Po dokončení požadavků, nainstalujte doplněk Azure Policy v clusteru AKS, který chcete spravovat.

- portál Azure

  1. Spuštění služby AKS na portálu Azure portal kliknutím **všechny služby**, vyhledáte a vyberete **služby Kubernetes**.

  1. Vyberte jednu z vašich clusterů AKS.

  1. Vyberte **zásady (preview)** na levé straně stránky služby Kubernetes.

     ![Zásady z clusteru AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na hlavní stránce vyberte **povolit doplněk** tlačítko.

     ![Povolit Azure Policy pro doplněk AKS](../media/rego-for-aks/enable-policy-add-on.png)

     > [!NOTE]
     > Pokud **povolit doplněk** tlačítko nejde aktivovat, předplatné ještě nejsou přidané do verze preview. Zobrazit [Opt-in pro aplikaci ve verzi preview](#opt-in-for-preview) pro požadované kroky.

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks enable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

### <a name="validation-and-reporting-frequency"></a>Ověření a četnost hlášení

Doplněk ověří pomocí Azure Policy pro změny v přiřazení zásad každých 5 minut. Během tohoto cyklu aktualizace doplněk odebere všechny _configmaps_ v _zásad azure_ potom znovu vytvoří obor názvů _configmaps_ pro použití vrátný.

> [!NOTE]
> Zatímco _Správce clusteru_ může mít oprávnění k _zásad azure_ obor názvů, není vhodné nebo dělat změny do oboru názvů. Všechny ručně prováděné změny se ztratí během cyklu aktualizace.

Každých 5 minut, doplněk vyžaduje úplnou kontrolu clusteru. Po shromáždění podrobností úplnou kontrolu a všechna hodnocení v reálném čase podle změn pokus o vrátný do clusteru, doplněk hlásí výsledky zpět ke službě Azure Policy pro zařazení [podrobnosti o dodržování předpisů](../how-to/get-compliance-data.md) všechny zásady Azure, jako jsou přiřazení. Během cyklu auditů se vrátí pouze výsledky pro aktivní zásada přiřazení.

## <a name="policy-language"></a>Jazyk zásad

Struktury jazyka Azure Policy pro správu AKS následně existujících zásad. Účinek _EnforceRegoPolicy_ slouží ke správě vašich clusterů AKS a trvá _podrobnosti_ vlastnosti specifické pro práci s NEPRŮ a vrátný. Podrobnosti a příklady najdete v tématu [EnforceRegoPolicy](effects.md#enforceregopolicy) vliv.

Jako součást _details.policy_ vlastnost v definici zásady Azure Policy předá URI zásady rego doplněk. Rego je jazyk, ve kterém NEPRŮ a vrátný podporu pro ověření nebo mutovat žádost do clusteru Kubernetes. Díky podpoře současných standardů pro řízení Kubernetes, Azure Policy umožňuje znovu použít existující pravidla a spárovat se službou Azure Policy pro dodržování předpisů jednotné cloudové prostředí pro vytváření sestav. Další informace najdete v tématu [novinky Rego?](https://www.openpolicyagent.org/docs/how-do-i-write-policies.html#what-is-rego).

## <a name="built-in-policies"></a>Předdefinované zásady

Najít integrované zásady pro správu AKS pomocí webu Azure portal, postupujte podle těchto kroků:

1. Spusťte službu Azure Policy na webu Azure Portal. Vyberte **všechny služby** v levém podokně a pak vyhledejte a vyberte **zásady**.

1. V levém podokně na stránku služby Azure Policy vyberte **definice**.

1. Z rozevíracího seznamu kategorií pomocí **Vybrat vše** vymažte filtr a potom vyberte **služby Kubernetes**.

1. Vyberte Definice zásad a potom **přiřadit** tlačítko.

> [!NOTE]
> Při přiřazování zásady Azure pro definici AKS, **oboru** musí obsahovat prostředek clusteru AKS.

Nebo můžete použít [přiřazení zásad – portál](../assign-policy-portal.md) rychlý start k vyhledání a přiřaďte zásady AKS. Vyhledejte definici zásady Kubernetes místo vzorek 'audit virtuálních počítačů".

## <a name="logging"></a>Protokolování

### <a name="azure-policy-add-on-logs"></a>Protokoly doplněk Azure zásad

Doplněk Azure Policy zajišťuje jako kontroler Kubernetes nebo kontejner, protokoly clusteru AKS. Protokoly jsou přístupné **Insights** stránky clusteru AKS. Další informace najdete v tématu [cluster AKS pochopit výkon díky Azure monitoru pro kontejnery](../../../azure-monitor/insights/container-insights-analyze.md).

### <a name="gatekeeper-logs"></a>Protokoly serveru gateKeeper

Povolení protokolů vrátný pro nové žádosti o prostředek, postupujte podle kroků v [povolit a zkontrolovat Kubernetes ve službě AKS protokoly hlavní uzel](../../../aks/view-master-logs.md).
Tady je příklad dotazu zobrazíte odepření události na nové požadavky prostředků:

```kusto
| where Category == "kube-audit"
| where log_s contains "admission webhook"
| limit 100
```

Chcete-li zobrazit protokoly z kontejnerů vrátný, postupujte podle kroků v [povolit a zkontrolovat Kubernetes ve službě AKS protokoly hlavní uzel](../../../aks/view-master-logs.md) a zkontrolujte _kube apiserver_ možnost **nastavení diagnostiky** podokně.

## <a name="remove-the-add-on"></a>Odebrat doplněk

Odebrání clusteru AKS doplněk Azure Policy, použijte webu Azure portal nebo rozhraní příkazového řádku Azure:

- portál Azure

  1. Spuštění služby AKS na portálu Azure portal kliknutím **všechny služby**, vyhledáte a vyberete **služby Kubernetes**.

  1. Vyberte svůj cluster AKS, ve které chcete zakázat doplněk Azure Policy.

  1. Vyberte **zásady (preview)** na levé straně stránky služby Kubernetes.

     ![Zásady z clusteru AKS](../media/rego-for-aks/policies-preview-from-aks-cluster.png)

  1. Na hlavní stránce vyberte **zakázat doplněk** tlačítko.

     ![Zakázat Azure Policy pro doplněk AKS](../media/rego-for-aks/disable-policy-add-on.png)

- Azure CLI

  ```azurecli-interactive
  # Log in first with az login if you're not using Cloud Shell

  az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
  ```

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md).
- Projděte si [strukturu definic zásad](definition-structure.md).
- Projděte si [Vysvětlení efektů zásad](effects.md).
- Pochopit postup [programové vytváření zásad](../how-to/programmatically-create.md).
- Zjistěte, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md).
- Zjistěte, jak [nápravě nekompatibilních prostředků](../how-to/remediate-resources.md).
- Zkontrolujte, jaké skupiny pro správu je s [uspořádání prostředků se skupinami pro správu Azure](../../management-groups/index.md).