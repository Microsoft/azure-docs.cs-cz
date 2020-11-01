---
title: Odstraňování běžných chyb
description: Naučte se řešit problémy s vytvářením definic zásad, různých SDK a doplňku pro Kubernetes.
ms.date: 10/30/2020
ms.topic: troubleshooting
ms.openlocfilehash: 74b622dd41fb28e845a35780e5d06588189ec029
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/01/2020
ms.locfileid: "93146275"
---
# <a name="troubleshoot-errors-using-azure-policy"></a>Řešení chyb pomocí Azure Policy

Při vytváření definic zásad, práci se sadou SDK nebo nastavení [Azure Policy pro doplněk Kubernetes](../concepts/policy-for-kubernetes.md) můžete narazit na chyby. Tento článek popisuje různé obecné chyby, ke kterým může dojít, a způsob jejich řešení.

## <a name="finding-error-details"></a>Hledání podrobností o chybě

Umístění podrobností o chybě závisí na akci, která způsobuje chybu.

- Pokud pracujete s vlastními zásadami, zkuste to v Azure Portal, abyste získali zpětnou vazbu ke schématu nebo zkontrolovali výsledná [data dodržování předpisů](../how-to/get-compliance-data.md) , abyste viděli, jak byly vyhodnoceny prostředky.
- Při práci s různými SDK poskytuje sada SDK podrobné informace o tom, proč se funkce nezdařila.
- Při práci s doplňkem pro Kubernetes začněte [protokolováním](../concepts/policy-for-kubernetes.md#logging) clusteru.

## <a name="general-errors"></a>Obecné chyby

### <a name="scenario-alias-not-found"></a>Scénář: alias nebyl nalezen.

#### <a name="issue"></a>Problém

Azure Policy používá k mapování Azure Resource Manager vlastností [aliasy](../concepts/definition-structure.md#aliases) .

#### <a name="cause"></a>Příčina

V definici zásad se používá nesprávný nebo neexistující alias.

#### <a name="resolution"></a>Řešení

Nejprve ověřte, zda má vlastnost Správce prostředků alias. K vyhledání dostupných aliasů použijte [rozšíření Azure Policy pro Visual Studio Code](../how-to/extension-for-vscode.md), [Azure Resource Graph](../../resource-graph/samples/starter.md#distinct-alias-values)nebo SDK. Pokud alias pro vlastnost Správce prostředků neexistuje, vytvořte lístek podpory.

### <a name="scenario-evaluation-details-not-up-to-date"></a>Scénář: podrobnosti vyhodnocení nejsou aktuální.

#### <a name="issue"></a>Problém

Prostředek je ve stavu "nespuštěno" nebo podrobnosti o dodržování předpisů nejsou aktuální.

#### <a name="cause"></a>Příčina

Použití nového přiřazení zásady nebo iniciativy trvá přibližně 30 minut. Nové nebo aktualizované prostředky v rozsahu stávajícího přiřazení budou k dispozici přibližně 15 minut později. Standardní kontrola dodržování předpisů proběhne každých 24 hodin. Další informace najdete v tématu [aktivační události vyhodnocení](../how-to/get-compliance-data.md#evaluation-triggers).

#### <a name="resolution"></a>Řešení

Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí, a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo SDK. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).

### <a name="scenario-compliance-not-as-expected"></a>Scénář: dodržování předpisů podle očekávání

#### <a name="issue"></a>Problém

Prostředek není ve stavu vyhodnocení, buď _kompatibilní_ , nebo _nekompatibilní_ , který je pro tento prostředek očekávaný.

#### <a name="cause"></a>Příčina

Prostředek není ve správném oboru pro přiřazení zásady nebo definice zásady nefunguje tak, jak má.

#### <a name="resolution"></a>Řešení

Pomocí těchto kroků můžete vyřešit potíže s definicí zásad:

1. Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí, a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo SDK. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Ověřte, zda jsou parametry přiřazení a rozsah přiřazení nastaveny správně.
1. Zkontrolujte [režim definice zásad](../concepts/definition-structure.md#mode):
   - Režim All pro všechny typy prostředků.
   - Režim "indexovaný", pokud definice zásad kontroluje značky nebo umístění.
1. Ověřte, že obor prostředku není [vyloučený](../concepts/assignment-structure.md#excluded-scopes) nebo má [výjimku](../concepts/exemption-structure.md).
1. Pokud dodržování předpisů pro přiřazení zásady zobrazuje `0/0` prostředky, neurčily se v rámci oboru přiřazení žádné prostředky, které by bylo možné použít. Ověřte definici zásad i obor přiřazení.
1. U nekompatibilního prostředku, u kterého se očekává, že bude kompatibilní, ověřte, že [nedodržujete důvody pro nedodržení předpisů](../how-to/determine-non-compliance.md). Porovnání definice s hodnotou vyhodnocené vlastnosti indikuje, proč prostředek nebyl kompatibilní.
   - Pokud je **cílová hodnota** špatná, upravte definici zásady.
   - Pokud je **aktuální hodnota** chybná, ověřte datovou část prostředku prostřednictvím `resources.azure.com` .
1. Podívejte se na [řešení potíží: vynucování není podle očekávání](#scenario-enforcement-not-as-expected) pro jiné běžné problémy a řešení.

Pokud stále máte problém s vámi vytvořenou duplicitou a přizpůsobenou definicí předdefinované zásady nebo vlastní definice, vytvořte lístek podpory v části **vytváření zásad** pro správné směrování problému.

### <a name="scenario-enforcement-not-as-expected"></a>Scénář: vynucování není podle očekávání

#### <a name="issue"></a>Problém

Prostředek, na kterém se očekává, Azure Policy není a v [protokolu aktivit Azure](../../../azure-monitor/platform/platform-logs-overview.md)neexistuje žádný záznam.

#### <a name="cause"></a>Příčina

Přiřazení zásad bylo nakonfigurováno pro [EnforcementMode](../concepts/assignment-structure.md#enforcement-mode) _zakázané_ . V době, kdy je režim vynucení zakázaný, není účinek zásad vynucený a v protokolu aktivit není žádný záznam.

#### <a name="resolution"></a>Řešení

Pomocí těchto kroků můžete vyřešit vynucení přiřazení zásad:

1. Nejdřív počkejte odpovídající dobu, než se vyhodnocení dokončí, a výsledky dodržování předpisů budou k dispozici v Azure Portal nebo SDK. Chcete-li zahájit novou zkušební kontrolu pomocí Azure PowerShell nebo REST API, přečtěte si téma [Kontrola vyhodnocení na vyžádání](../how-to/get-compliance-data.md#on-demand-evaluation-scan).
1. Ověřte, zda jsou parametry přiřazení a rozsah přiřazení správně nastaveny a zda je _povolená_ možnost **enforcementMode** . 
1. Zkontrolujte [režim definice zásad](../concepts/definition-structure.md#mode):
   - Režim All pro všechny typy prostředků.
   - Režim "indexovaný", pokud definice zásad kontroluje značky nebo umístění.
1. Ověřte, že obor prostředku není [vyloučený](../concepts/assignment-structure.md#excluded-scopes) nebo má [výjimku](../concepts/exemption-structure.md).
1. Ověřte, že datová část prostředku odpovídá logice zásad. To se dá udělat [zachycením trasování Har](../../../azure-portal/capture-browser-trace.md) nebo kontrolou vlastností šablony ARM.
1. Podívejte [se na řešení potíží: dodržování předpisů](#scenario-compliance-not-as-expected) pro další běžné problémy a řešení podle očekávání.

Pokud stále máte problém s vámi vytvořenou duplicitou a přizpůsobenou definicí předdefinované zásady nebo vlastní definice, vytvořte lístek podpory v části **vytváření zásad** pro správné směrování problému.

### <a name="scenario-denied-by-azure-policy"></a>Scénář: zamítnutý Azure Policy

#### <a name="issue"></a>Problém

Vytvoření nebo aktualizace prostředku se zamítly.

#### <a name="cause"></a>Příčina

Přiřazení zásady do oboru, ve kterém je nový nebo aktualizovaný prostředek, splňuje kritéria definice zásady s efektem [odepření](../concepts/effects.md#deny) . Zdroje informací o těchto definicích je znemožněno vytvářet ani aktualizovat.

#### <a name="resolution"></a>Řešení

Chybová zpráva z přiřazení zásady odepření zahrnuje ID přiřazení definice zásad a zásad. Pokud informace o chybě ve zprávě chybí, je také k dispozici v [protokolu aktivit](../../../azure-monitor/platform/activity-log.md#view-the-activity-log). Tyto informace slouží k získání dalších informací, které vám pomohou pochopit omezení prostředků a upravit vlastnosti prostředku v žádosti tak, aby odpovídaly povoleným hodnotám.

## <a name="template-errors"></a>Chyby šablon

### <a name="scenario-policy-supported-functions-processed-by-template"></a>Scénář: funkce podporované zásadou zpracovávané šablonou

#### <a name="issue"></a>Problém

Azure Policy podporuje řadu funkcí a funkcí šablony Azure Resource Manager (šablony ARM), které jsou k dispozici pouze v definici zásady. Správce prostředků tyto funkce zpracovává jako součást nasazení, ne jako součást definice zásady.

#### <a name="cause"></a>Příčina

Použití podporovaných funkcí, jako je například `parameter()` nebo `resourceGroup()` , vede ke zpracování výsledku funkce v době nasazení místo ukončení funkce pro účely definice zásad a Azure Policyho stroje.

#### <a name="resolution"></a>Řešení

Chcete-li předat funkci až po součást definice zásady, vydejte celý řetězec `[` tak, aby vlastnost vypadala jako `[[resourceGroup().tags.myTag]` . Řídicí znak způsobí, že Správce prostředků při zpracování šablony zacházet s hodnotou jako s řetězcem. Azure Policy pak funkci umístí do definice zásady, což umožní její dynamické fungování podle očekávání. Další informace najdete v tématu [syntaxe a výrazy v šablonách Azure Resource Manager](../../../azure-resource-manager/templates/template-expressions.md).

## <a name="add-on-for-kubernetes-installation-errors"></a>Chyba instalace doplňku pro Kubernetes

### <a name="scenario-install-using-helm-chart-fails-on-password"></a>Scénář: instalace pomocí grafu Helm v hesle se nezdařila

#### <a name="issue"></a>Problém

`helm install azure-policy-addon`Příkaz se nezdařil s jednou z následujících zpráv:

- `!: event not found`
- `Error: failed parsing --set data: key "<key>" has no value (cannot end with ,)`

#### <a name="cause"></a>Příčina

Vygenerované heslo obsahuje čárku ( `,` ), na které se Helm graf.

#### <a name="resolution"></a>Řešení

`,`Při spuštění `helm install azure-policy-addon` s zpětným lomítkem () zařídí čárku () v hodnotě hesla `\` .

### <a name="scenario-install-using-helm-chart-fails-as-name-already-exists"></a>Scénář: instalace pomocí grafu Helm se nezdařila, protože název už existuje.

#### <a name="issue"></a>Problém

`helm install azure-policy-addon`Příkaz se nezdařil s následující zprávou:

- `Error: cannot re-use a name that is still in use`

#### <a name="cause"></a>Příčina

Graf Helm s názvem `azure-policy-addon` již byl nainstalován nebo částečně nainstalován.

#### <a name="resolution"></a>Řešení

Postupujte podle pokynů k [odebrání Azure Policy pro doplněk Kubernetes](../concepts/policy-for-kubernetes.md#remove-the-add-on)a pak znovu spusťte `helm install azure-policy-addon` příkaz.

### <a name="scenario-azure-virtual-machine-user-assigned-identities-are-replaced-by-system-assigned-managed-identities"></a>Scénář: uživatelsky přiřazené identity virtuálních počítačů Azure se nahrazují spravovanými identitami přiřazenými systémem.

#### <a name="issue"></a>Problém

Po přiřazení iniciativ zásad konfigurace hosta k auditování nastavení v počítačích se už nepřiřazují uživatelsky spravované identity přiřazené k počítači. Přiřadí se jenom spravovaná identita přiřazená systémem.

#### <a name="cause"></a>Příčina

Definice zásad, které se dřív používaly v definicích konfigurace hosta DeployIfNotExists, zajistí, že se počítači přiřadí identita přiřazená systémem, ale také odebrala přiřazení identit přiřazená uživatelem.

#### <a name="resolution"></a>Řešení

Definice, které tento problém dříve způsobily, se jeví jako \[ zastaralé \] a nahrazují definicemi zásad, které spravují požadavky bez nutnosti odebrání spravované identity přiřazené uživatelem. Je vyžadován ruční krok. Odstraňte všechna existující přiřazení zásad, která jsou označena jako \[ zastaralá, \] a nahraďte je aktualizovanými iniciativami zásad požadovaných součástí a definicemi zásad, které mají stejný název jako původní.

Podrobný popis najdete v tomto blogovém příspěvku:

[Důležitá změna vydaná pro zásady auditu konfigurace hosta](https://techcommunity.microsoft.com/t5/azure-governance-and-management/important-change-released-for-guest-configuration-audit-policies/ba-p/1655316)

## <a name="add-on-for-kubernetes-general-errors"></a>Doplněk pro obecné chyby Kubernetes

### <a name="scenario-add-on-doesnt-work-with-aks-clusters-on-version-119-preview"></a>Scénář: doplněk nefunguje s clustery AKS ve verzi 1,19 (Preview).

#### <a name="issue"></a>Problém

Clustery verze 1,19 vrátí tuto chybu prostřednictvím řadiče serveru gatekeeper a Webhooku zásad:

```
2020/09/22 20:06:55 http: TLS handshake error from 10.244.1.14:44282: remote error: tls: bad certificate
```

#### <a name="cause"></a>Příčina

AKS clusers na verzi 1,19 (Preview) ještě není kompatibilní s doplňkem Azure Policy.

#### <a name="resolution"></a>Řešení

Nepoužívejte Kubernetes 1,19 (Preview) s doplňkem Azure Policy. Doplněk lze použít s libovolnou podporovanou všeobecně dostupnou verzí, například 1,16, 1,17 nebo 1,18.

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-egress-restrictions"></a>Scénář: doplněk se nemůže spojit s koncovým bodem služby Azure Policy, protože neplatí omezení pro výstup.

#### <a name="issue"></a>Problém

Doplněk se nemůže připojit ke koncovému bodu služby Azure Policy a vrátí jednu z následujících chyb:

- `failed to fetch token, service not reachable`
- `Error getting file "Get https://raw.githubusercontent.com/Azure/azure-policy/master/built-in-references/Kubernetes/container-allowed-images/template.yaml: dial tcp 151.101.228.133.443: connect: connection refused`

#### <a name="cause"></a>Příčina

K těmto potížím dochází, když dojde k uzamčení odchozího clusteru.

#### <a name="resolution"></a>Řešení

Ujistěte se, že jsou otevřené domény a porty v následujících článcích:

- [Požadovaná odchozí síťová pravidla a plně kvalifikované názvy domény pro clustery AKS](../../../aks/limit-egress-traffic.md#required-outbound-network-rules-and-fqdns-for-aks-clusters)
- [Instalace doplňku Azure Policy pro Azure ARC s povoleným Kubernetes (Preview)](../concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-azure-arc-enabled-kubernetes)

### <a name="scenario-add-on-is-unable-to-reach-the-azure-policy-service-endpoint-due-to-aad-pod-identity-configuration"></a>Scénář: doplněk se nemůže spojit s koncovým bodem služby Azure Policy z důvodu konfigurace AAD-pod-identity.

#### <a name="issue"></a>Problém

Doplněk se nemůže připojit ke koncovému bodu služby Azure Policy a vrátí jednu z následujících chyb:

- `azure.BearerAuthorizer#WithAuthorization: Failed to refresh the Token for request to https://gov-prod-policy-data.trafficmanager.net/checkDataPolicyCompliance?api-version=2019-01-01-preview: StatusCode=404`
- `adal: Refresh request failed. Status Code = '404'. Response body: getting assigned identities for pod kube-system/azure-policy-8c785548f-r882p in CREATED state failed after 16 attempts, retry duration [5]s, error: <nil>`

#### <a name="cause"></a>Příčina

K této chybě dochází, když je v clusteru nainstalovaný příkaz _Add-pod-identity_ a _Kube – systémová_ lusky nejsou vyloučené v _AAD-pod-identity_ .

Lusky spravované identity (NMI) v poli _AAD-pod-identity_ mění uzly softwaru iptables tak, aby zachytil volání koncového bodu metadat instance Azure. Tato instalace znamená, že všechny požadavky na koncový bod metadat jsou zachyceny NMI i v případě, že pole pod nepoužívá _identitu AAD-pod-identity_ .
**AzurePodIdentityException** CRD je možné nakonfigurovat tak, aby informovala _AAD-pod-identity_ , že všechny požadavky na koncový bod metadat pocházející z objektu pod, který odpovídá popiskům definovaným v CRD, by měly být proxy bez jakéhokoli zpracování v NMI.

#### <a name="resolution"></a>Řešení

Pomocí konfigurace AzurePodIdentityException CRD vylučte systém lusky pomocí `kubernetes.azure.com/managedby: aks` popisku v oboru názvů _Kube-System_ v _AAD-pod-identity_ . **AzurePodIdentityException**

Další informace najdete v tématu [zakázání identity AAD pod u konkrétního typu pod/aplikací](https://azure.github.io/aad-pod-identity/docs/configure/application_exception).

Chcete-li konfigurovat výjimku, přečtěte si tento příklad:

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

Doplněk se může připojit ke koncovému bodu služby Azure Policy, ale zobrazí se následující chyba:

```
The resource provider 'Microsoft.PolicyInsights' is not registered in subscription '{subId}'. See https://aka.ms/policy-register-subscription for how to register subscriptions.
```

#### <a name="cause"></a>Příčina

`Microsoft.PolicyInsights`Poskytovatel prostředků není zaregistrován a musí být zaregistrován pro doplněk, aby získal definice zásad a vracel data o dodržování předpisů.

#### <a name="resolution"></a>Řešení

Zaregistrujte `Microsoft.PolicyInsights` poskytovatele prostředků. Pokyny najdete v tématu [registrace poskytovatele prostředků](../../../azure-resource-manager/management/resource-providers-and-types.md#register-resource-provider).

### <a name="scenario-the-subscript-is-disabled"></a>Scénář: dolní index je zakázaný.

#### <a name="issue"></a>Problém

Doplněk se může připojit ke koncovému bodu služby Azure Policy, ale zobrazí se následující chyba:

```
The subscription '{subId}' has been disabled for azure data-plane policy. Please contact support.
```

#### <a name="cause"></a>Příčina

Tato chyba znamená, že předplatné bylo zjištěno problematické a přidal se příznak funkce `Microsoft.PolicyInsights/DataPlaneBlocked` k blokování předplatného.

#### <a name="resolution"></a>Řešení

Obraťte se na tým funkcí, `azuredg@microsoft.com` aby mohl tento problém prozkoumat a vyřešit. 

## <a name="next-steps"></a>Další kroky

Pokud jste se nedostali k problému nebo jste nedokázali problém vyřešit, přejděte k jednomu z následujících kanálů, kde najdete další podporu:

- Získejte odpovědi od odborníků prostřednictvím [Microsoft Q&A](/answers/topics/azure-policy.html).
- Spojte se s nástrojem [@AzureSupport](https://twitter.com/azuresupport) – oficiální Microsoft Azure účet pro zlepšení zkušeností zákazníků tím, že propojíte komunitu Azure s správnými zdroji: odpověďmi, podporou a odborníky.
- Pokud potřebujete další pomoc, můžete zasouborovat incident podpory Azure. Přejít na [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte **získat podporu** .
