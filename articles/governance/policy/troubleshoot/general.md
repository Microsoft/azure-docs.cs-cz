---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy s vytvářením definic zásad, různých sad SDK a doplňku pro Kubernetes.
ms.date: 01/26/2021
ms.topic: troubleshooting
ms.openlocfilehash: 0a64346188696cc7cc16d832474ec4ee6befdae2
ms.sourcegitcommit: 436518116963bd7e81e0217e246c80a9808dc88c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/27/2021
ms.locfileid: "98917739"
---
# <a name="troubleshoot-errors-with-using-azure-policy"></a>Řešení chyb pomocí Azure Policy

Když vytváříte definice zásad, pracujete se sadami SDK nebo nastavíte [Azure Policy pro doplněk Kubernetes](../concepts/policy-for-kubernetes.md) , může dojít k chybám. Tento článek popisuje různé obecné chyby, ke kterým může dojít, a navrhuje způsoby, jak je vyřešit.

## <a name="find-error-details"></a>Najít podrobnosti o chybě

Umístění podrobností o chybě závisí na tom, s jakými aspekty Azure Policy pracujete.

- Pokud pracujete s vlastními zásadami, přejděte na Azure Portal a získejte zpětnou vazbu ke schématu nebo si přečtěte výsledná [data o dodržování předpisů](../how-to/get-compliance-data.md) , abyste viděli, jak byly vyhodnoceny prostředky.
- Pokud pracujete s některou z různých sad SDK, poskytuje sada SDK podrobné informace o tom, proč se funkce nezdařila.
- Pokud pracujete s doplňkem pro Kubernetes, začněte s [protokolováním](../concepts/policy-for-kubernetes.md#logging) v clusteru.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-alias-not-found"></a>Scénář: alias nebyl nalezen.

#### <a name="issue"></a>Problém

V definici zásad se používá nesprávný nebo neexistující alias. Azure Policy používá k mapování Azure Resource Manager vlastností [aliasy](../concepts/definition-structure.md#aliases) .

#### <a name="cause"></a>Příčina

V definici zásad se používá nesprávný nebo neexistující alias.

#### <a name="resolution"></a>Řešení

Nejprve ověřte, zda má vlastnost Správce prostředků alias. Pokud chcete vyhledat dostupné aliasy, přečtěte si [Azure Policy rozšíření pro Visual Studio Code](../how-to/extension-for-vscode.md) nebo sadu SDK.
Pokud alias pro vlastnost Správce prostředků neexistuje, vytvořte lístek podpory.

### <a name="scenario-evaluation-details-arent-up-to-date"></a>Scénář: podrobnosti o vyhodnocení nejsou aktuální.

#### <a name="issue"></a>Problém

Prostředek je ve stavu _Nezahájeno_ nebo podrobnosti o dodržování předpisů nejsou aktuální.

#### <a name="cause"></a>Příčina

Nové přiřazení zásad nebo iniciativ trvá přibližně 30 minut. Nové nebo aktualizované prostředky v rozsahu stávajícího přiřazení budou k dispozici přibližně po dobu 15 minut. Standardní kontrola dodržování předpisů probíhá každých 24 hodin. Další informace najdete v tématu [aktivační události vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Řešení

Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí, a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo sadě SDK. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-isnt-as-expected"></a>Scénář: dodržování předpisů není podle očekávání

#### <a name="issue"></a>Problém

Prostředek není v _odpovídajícím_ nebo _nekompatibilním_ stavu vyhodnocení, který je pro tento prostředek očekávaný.

#### <a name="cause"></a>Příčina

Prostředek není ve správném oboru pro přiřazení zásady, jinak definice zásady nefunguje tak, jak má.

#### <a name="resolution"></a>Řešení

Pokud chcete řešit potíže s definicí zásad, udělejte toto:

1. Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí, a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo SDK. 

1. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Ujistěte se, že parametry přiřazení a rozsah přiřazení jsou nastaveny správně.
1. Zkontrolujte [režim definice zásad](../concepts/definition-structure.md#mode):
   - Režim by měl být `all` pro všechny typy prostředků.
   - Tento režim by měl být v `indexed` případě, že definice zásad kontroluje značky nebo umístění.
1. Ujistěte se, že obor prostředku není vyloučený nebo [je](../concepts/exemption-structure.md) [vyloučený](../concepts/assignment-structure.md#excluded-scopes) .
1. Pokud dodržování předpisů pro přiřazení zásady zobrazuje `0/0` prostředky, neurčily se v rámci oboru přiřazení žádné prostředky, které by bylo možné použít. Ověřte definici zásad i obor přiřazení.
1. U nekompatibilního prostředku, u kterého se očekávalo dodržování předpisů, najdete informace v tématu [určení důvodů nedodržení předpisů](../how-to/determine-non-compliance.md). Porovnání definice s hodnotou vyhodnocené vlastnosti indikuje, proč prostředek nebyl kompatibilní.
   - Pokud je **cílová hodnota** špatná, upravte definici zásady.
   - Pokud je **aktuální hodnota** chybná, ověřte datovou část prostředku prostřednictvím `resources.azure.com` .
1. Další běžné problémy a řešení najdete v tématu věnovaném [řešení potíží: vynucování neočekávaným způsobem](#scenario-enforcement-not-as-expected).

Pokud stále máte problém s vámi vytvořenou duplicitou a přizpůsobenou definicí předdefinované zásady nebo vlastní definice, vytvořte lístek podpory v části **vytváření zásad** pro správné směrování problému.

### <a name="scenario-enforcement-not-as-expected"></a>Scénář: vynucování není podle očekávání

#### <a name="issue"></a>Problém

Prostředek, na který očekáváte, Azure Policy pracovat, není v [protokolu aktivit Azure](../../../azure-monitor/platform/platform-logs-overview.md)žádný záznam.

#### <a name="cause"></a>Příčina

Přiřazení zásad bylo nakonfigurováno pro _zakázané_ nastavení [**enforcementMode**](../concepts/assignment-structure.md#enforcement-mode) .
I když je **enforcementMode** zakázaný, není uplatnění zásad a v protokolu aktivit není žádný záznam.

#### <a name="resolution"></a>Řešení

Řešení potíží s vynucováním zásad pomocí následujícího postupu:

1. Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo sadě SDK. 

1. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Ujistěte se, že parametry přiřazení a rozsah přiřazení jsou správně nastaveny a že je _povolená_ možnost **enforcementMode** .
1. Zkontrolujte [režim definice zásad](../concepts/definition-structure.md#mode):
   - Režim by měl být `all` pro všechny typy prostředků.
   - Tento režim by měl být v `indexed` případě, že definice zásad kontroluje značky nebo umístění.
1. Ujistěte se, že obor prostředku není vyloučený nebo [je](../concepts/exemption-structure.md) [vyloučený](../concepts/assignment-structure.md#excluded-scopes) .
1. Ověřte, zda datová část prostředku odpovídá logice zásad. To se dá udělat [zachycením trasování http (Har)](../../../azure-portal/capture-browser-trace.md) nebo kontrolou vlastností šablony pro Azure Resource Manager (šablona ARM).
1. Další běžné problémy a řešení najdete v tématu věnovaném [řešení potíží: dodržování předpisů neočekávaným způsobem](#scenario-compliance-isnt-as-expected).

Pokud stále máte problém s vámi vytvořenou duplicitou a přizpůsobenou definicí předdefinované zásady nebo vlastní definice, vytvořte lístek podpory v části **vytváření zásad** pro správné směrování problému.

### <a name="scenario-denied-by-azure-policy"></a>Scénář: zamítnutý Azure Policy

#### <a name="issue"></a>Problém

Vytvoření nebo aktualizace prostředku se zamítly.

#### <a name="cause"></a>Příčina

Přiřazení zásady k oboru nového nebo aktualizovaného prostředku splňuje kritéria definice zásady s efektem [odepření](../concepts/effects.md#deny) . Prostředky, které splňují tyto definice, se nebudou vytvářet ani aktualizovat.

#### <a name="resolution"></a>Řešení

Chybová zpráva z přiřazení zásady odepření zahrnuje ID přiřazení definice zásad a zásad. Pokud informace o chybě ve zprávě chybí, je také k dispozici v [protokolu aktivit](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Tyto informace slouží k získání dalších informací, které vám pomohou pochopit omezení prostředků a upravit vlastnosti prostředku v žádosti tak, aby odpovídaly povoleným hodnotám.

## <a name="template-errors"></a>Chyby šablon

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scénář: funkce podporované zásadou zpracovávané šablonou

#### <a name="issue"></a>Problém

Azure Policy podporuje řadu funkcí šablon ARM a funkcí, které jsou k dispozici pouze v definici zásady. Správce prostředků tyto funkce zpracovává jako součást nasazení, ne jako součást definice zásady.

#### <a name="cause"></a>Příčina

Použití podporovaných funkcí, jako je například `parameter()` nebo `resourceGroup()` , vede ke zpracování výsledku funkce v době nasazení místo toho, aby se povolila funkce pro definici zásad a Azure Policymu modulu.

#### <a name="resolution"></a>Řešení

Pokud chcete funkci předat jako součást definice zásady, vydejte celý řetězec `[` tak, aby vlastnost vypadala jako `[[resourceGroup().tags.myTag]` . Řídicí znak způsobí, že Správce prostředků při zpracovávání šablony zacházet s hodnotou jako s řetězcem. Azure Policy pak funkci umístí do definice zásady, která umožňuje její dynamické fungování podle očekávání. Další informace najdete v tématu [syntaxe a výrazy v šablonách Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Chyba instalace doplňku pro Kubernetes

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-of-a-password-error"></a>Scénář: instalace pomocí grafu Helm se nezdařila kvůli chybě hesla.

#### <a name="issue"></a>Problém

`helm install azure-policy-addon`Příkaz se nezdařil a vrátí jednu z následujících chyb:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Příčina

Vygenerované heslo obsahuje čárku ( `,` ), na které se rozděluje graf Helm.

#### <a name="resolution"></a>Řešení

Když spouštíte `helm install azure-policy-addon` , můžete `,` v hodnotě hesla zadat znak čárky () pomocí zpětného lomítka ( `\` ).

### <a name="scenario-installation-by-using-a-helm-chart-fails-because-the-name-already-exists"></a>Scénář: instalace pomocí grafu Helm se nezdařila, protože tento název už existuje.

#### <a name="issue"></a>Problém

`helm install azure-policy-addon`Příkaz se nezdařil a vrátí následující chybu:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Příčina

Graf Helm s názvem `azure-policy-addon` již byl nainstalován nebo částečně nainstalován.

#### <a name="resolution"></a>Řešení

Podle pokynů [odeberte Azure Policy pro doplněk Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)a pak znovu spusťte `helm install azure-policy-addon` příkaz.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scénář: uživatelsky přiřazené identity virtuálních počítačů Azure se nahrazují spravovanými identitami přiřazenými systémem.

#### <a name="issue"></a>Problém

Po přiřazení iniciativ zásad konfigurace hosta k auditování nastavení v počítači již nejsou přiřazeny uživatelem přiřazené spravované identity, které byly přiřazeny k počítači. Přiřadí se jenom spravovaná identita přiřazená systémem.

#### <a name="cause"></a>Příčina

Definice zásad, které se dřív používaly v definicích konfigurace hosta DeployIfNotExists, musí mít k počítači přiřazenou identitu přiřazenou systémem, ale také odebrali přiřazení identity přiřazená uživatelem.

#### <a name="resolution"></a>Řešení

Definice, které tento problém dříve způsobily, se jeví jako _\[ zastaralé \]_ a nahrazují definicemi zásad, které spravují požadavky bez nutnosti odebírat spravované identity přiřazené uživatelem. Je vyžadován ruční krok. Odstraňte všechna existující přiřazení zásad, která jsou označená jako _\[ zastaralá \]_, a nahraďte je aktualizovanými iniciativami zásad požadovaných součástí a definicemi zásad, které mají stejný název jako původní.

Podrobný popis najdete v blogovém příspěvku důležité změny, které byly [vydány pro zásady auditu konfigurace hostů](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316).

## <a name="add-on-for-kubernetes-general-errors"></a>Doplněk pro obecné chyby Kubernetes

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-egress-restrictions"></a>Scénář: doplněk se nemůže připojit ke koncovému bodu služby Azure Policy kvůli omezením na odchozí data

#### <a name="issue"></a>Problém

Doplněk se nemůže připojit ke koncovému bodu služby Azure Policy a vrátí jednu z následujících chyb:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Příčina

K tomuto problému dochází, když je výstup clusteru uzamčený.

#### <a name="resolution"></a>Řešení

Ujistěte se, že jsou otevřené domény a porty uvedené v následujících článcích:

- [Požadovaná odchozí síťová pravidla a plně kvalifikované názvy domény (FQDN) pro clustery AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Instalace doplňku Azure Policy pro Kubernetes s podporou ARC Azure (Preview)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-the-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-because-of-the-aad-pod-identity-configuration"></a>Scénář: doplněk se nemůže spojit s koncovým bodem služby Azure Policy, protože konfigurace AAD-pod-identity

#### <a name="issue"></a>Problém

Doplněk se nemůže připojit ke koncovému bodu služby Azure Policy a vrátí jednu z následujících chyb:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Příčina

K této chybě dochází, pokud je v clusteru nainstalovaný příkaz _Add-pod-identity_ a _Kube – systémová_ lusky nejsou vyloučené v _AAD-pod-identity_.

Lusky NMI (uzel komponenty _AAD-pod-identity_ Managed identity) upraví uzly softwaru iptables tak, aby zachytil volání koncového bodu metadat instance Azure. Tato instalace znamená, že všechny žádosti, které se provedou na koncový bod metadat, jsou zachyceny NMI, a to i v případě, že bod pod nepoužívá _identitu AAD-pod-identity_.
_AzurePodIdentityException_ CUSTOMRESOURCEDEFINITION (CRD) je možné nakonfigurovat tak, aby informovala _AAD-pod-identity_ , že všechny požadavky na koncový bod metadat, které pocházejí z pod, odpovídají popiskům definovaným v CRD by měly být proxy bez jakéhokoli zpracování v NMI.

#### <a name="resolution"></a>Řešení

Pomocí konfigurace AzurePodIdentityException CRD vylučte systémové lusky, které mají `kubernetes.azure.com/managedby: aks` popisek v oboru názvů _Kube-System_ v _AAD-pod-identity_ . 

Další informace najdete v tématu [zakázání identity Azure Active Directory (Azure AD) pod identitou pro konkrétní pod/aplikaci](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Chcete-li konfigurovat výjimku, postupujte podle tohoto příkladu:

```yaml
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: mic-exception
  namespace: default
spec:
  podLabels:
    app: mic
    component: mic
---
apiVersion: "aadpodidentity.k8s.io/v1"
kind: AzurePodIdentityException
metadata:
  name: aks-addon-exception
  namespace: kube-system
spec:
  podLabels:
    kubernetes.azure.com/managedby: aks
```

### <a name="scenario-the-resource-provider-isnt-registered"></a>Scénář: poskytovatel prostředků není zaregistrovaný.

#### <a name="issue"></a>Problém

Doplněk se může připojit ke koncovému bodu služby Azure Policy, ale v protokolech doplňku se zobrazí jedna z následujících chyb:

- `The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See
  https://aka.ms/policy-register-subscription for how to register subscriptions.`

- `policyinsightsdataplane.BaseClient#CheckDataPolicyCompliance: Failure responding to request:
  StatusCode=500 -- Original Error: autorest/azure: Service returned an error. Status=500
  Code="InternalServerError" Message="Encountered an internal server error.`

#### <a name="cause"></a>Příčina

Poskytovatel prostředků Microsoft. PolicyInsights není zaregistrován. Aby mohl doplněk získat definice zásad a vracet data o dodržování předpisů, musí být zaregistrován.

#### <a name="resolution"></a>Řešení

Zaregistrujte poskytovatele prostředků Microsoft. PolicyInsights v rámci předplatného clusteru. Pokyny najdete v tématu [registrace poskytovatele prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscription-is-disabled"></a>Scénář: předplatné je zakázané.

#### <a name="issue"></a>Problém

Doplněk se může připojit ke koncovému bodu služby Azure Policy, ale zobrazí se následující chyba:

`The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.`

#### <a name="cause"></a>Příčina

Tato chyba znamená, že předplatné bylo zjištěno problematické a přidal se příznak funkce `Microsoft.PolicyInsights/DataPlaneBlocked` k blokování předplatného.

#### <a name="resolution"></a>Řešení

Pokud chcete tento problém prozkoumat a vyřešit, [obraťte se na tým funkcí](mailto:azuredg@microsoft.com).

## <a name="next-steps"></a>Další kroky

Pokud váš problém není uvedený v tomto článku nebo ho nemůžete vyřešit, Získejte podporu na jednom z následujících kanálů:

- Získejte odpovědi od odborníků prostřednictvím [Microsoft Q&A](/answers/topics/azure-policy.html).
- Připojte se pomocí [@AzureSupport](https://twitter.com/azuresupport) . Tento oficiální Microsoft Azure prostředek na Twitteru pomáhá zlepšit uživatelské prostředí tím, že propojuje komunitu Azure se správnými odpověďmi, podporou a odborníky.
- Pokud stále potřebujete pomoc, navštivte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **Odeslat žádost o podporu**.
