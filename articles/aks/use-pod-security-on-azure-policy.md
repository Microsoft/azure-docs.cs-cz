---
title: Zabezpečení lusků pomocí Azure Policy ve službě Azure Kubernetes (AKS)
description: Naučte se zabezpečit lusky pomocí Azure Policy ve službě Azure Kubernetes (AKS).
services: container-service
ms.topic: article
ms.date: 09/22/2020
author: jluk
ms.openlocfilehash: a1fafdf1db29917982bbf136de45237459712bcd
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92073457"
---
# <a name="secure-pods-with-azure-policy"></a>Zabezpečené pody s využitím Azure Policy

Pokud chcete zlepšit zabezpečení clusteru AKS, můžete určit, jaké funkce se mají přidělovat, a pokud cokoli běží na zásadách společnosti. Tento přístup je definovaný prostřednictvím předdefinovaných zásad, které poskytuje [doplněk Azure Policy pro AKS][kubernetes-policy-reference]. Díky lepší kontrole nad aspekty zabezpečení specifikace vašeho zařízení, jako jsou například oprávnění ke kořenu, umožňuje přísnější dodržování zabezpečení a přehled o tom, co je ve vašem clusteru nasazené. Pokud parametr pod nesplňuje podmínky zadané v zásadě, Azure Policy může zakázat ovládacímu poli pod, aby mohl spustit nebo označit porušení. V tomto článku se dozvíte, jak pomocí Azure Policy omezit nasazení lusků v AKS.

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte existující cluster AKS. Pokud potřebujete cluster AKS, přečtěte si rychlý Start AKS a [použijte Azure CLI][aks-quickstart-cli] nebo [Azure Portal][aks-quickstart-portal].

### <a name="install-the-azure-policy-add-on-for-aks"></a>Instalace doplňku Azure Policy pro AKS

Chcete-li zabezpečit AKS po Azure Policy, je nutné nainstalovat Azure Policy doplněk pro AKS do clusteru AKS. Pomocí těchto [kroků nainstalujte doplněk Azure Policy](../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks).

V tomto dokumentu se předpokládá, že máte následující, které jsou nasazené v předchozím odkazu.

* Zaregistrovali `Microsoft.ContainerService` jste `Microsoft.PolicyInsights` poskytovatele prostředků a pomocí `az provider register`
* Azure CLI 2,12 nebo vyšší
* Cluster AKS ve verzi 1,15 nebo vyšší nainstalovaný s doplňkem Azure Policy

## <a name="overview-of-securing-pods-with-azure-policy-for-aks"></a>Přehled zabezpečení lusků pomocí Azure Policy pro AKS

>[!NOTE]
> Tento dokument popisuje, jak použít Azure Policy k zabezpečení lusků, což je nástupce [funkce zásad zabezpečení Kubernetes pod ve verzi Preview](use-pod-security-policies.md).
> **Obě zásady zabezpečení (Preview) a Azure Policy doplňku pro AKS nelze povolit současně.**
> 
> Pokud instalujete doplněk Azure Policy do clusteru s povolenými zásadami zabezpečení pod, [vypněte pomocí těchto kroků zásady zabezpečení pod tímto postupem](use-pod-security-policies.md#enable-pod-security-policy-on-an-aks-cluster).

V clusteru AKS se k zachycení požadavků na server rozhraní API používá řadič pro přístup, když se prostředek vytvoří a aktualizuje. Řadič pro přijímání pak může *ověřit* požadavek prostředku na základě sady pravidel podle toho, jestli se má vytvořit.

Dříve byla tato funkce [(Preview)](use-pod-security-policies.md) povolena prostřednictvím projektu Kubernetes, aby se omezilo, které z nich je možné nasadit.

Pomocí Azure Policyho doplňku může cluster AKS používat integrované zásady Azure, které v předchozích zásadách zabezpečení a dalších prostředků Kubernetes mají stejné zabezpečení. Doplněk Azure Policy pro AKS nainstaluje spravovanou instanci [serveru gatekeeper](https://github.com/open-policy-agent/gatekeeper)a ověří řadič přístupu. Azure Policy pro Kubernetes je postavená na Open Source otevřeném agentem zásad, který spoléhá na [Jazyk zásad Rego](../governance/policy/concepts/policy-for-kubernetes.md#policy-language).

Tento dokument popisuje, jak použít Azure Policy k zabezpečení lusků v clusteru AKS a pokyny k migraci ze zásad zabezpečení (Preview).

## <a name="limitations"></a>Omezení

Na Azure Policy doplňku pro clustery Kubernetes se vztahují následující obecná omezení:

- Azure Policy doplněk pro Kubernetes je podporován na Kubernetes verze **1,14** nebo vyšší.
- Azure Policy doplňku pro Kubernetes lze nasadit pouze do fondů uzlů systému Linux.
- Podporují se jenom předdefinované definice zásad.
- Maximální počet nevyhovujících záznamů na zásadu na cluster: **500**
- Maximální počet nevyhovujících záznamů na předplatné: **1 000 000**
- Instalace serveru gatekeeper mimo Azure Policy doplňky se nepodporují. Než povolíte doplněk Azure Policy, odinstalujte všechny součásti nainstalované předchozí instalací serveru gatekeeper.
- [Důvody nedodržení předpisů](../governance/policy/how-to/determine-non-compliance.md#compliance-reasons) nejsou pro tento [režim poskytovatele prostředků](../governance/policy/concepts/definition-structure.md#resource-provider-modes) k dispozici.

Následující omezení platí pouze pro Azure Policy doplněk pro AKS:

- [Zásada zabezpečení AKS pod (Preview)](use-pod-security-policies.md) a doplněk Azure Policy pro AKS nelze povolit současně. 
- Obory názvů automaticky vyloučené Azure Policy doplněk pro vyhodnocení: _Kube-System_, _gatekeeper-System_a _AKS-Periscope_.

### <a name="recommendations"></a>Doporučení

Níže jsou uvedená obecná doporučení pro používání doplňku Azure Policy:

- Doplněk Azure Policy vyžaduje 3 součásti gatekeeper, které se mají spustit: 1 audit pod a 2 Webhook pod replikami. Tyto komponenty spotřebují více prostředků, protože počet prostředků Kubernetes a přiřazení zásad roste v clusteru, který vyžaduje operace auditu a vynucení.

  - Pro méně než 500 lusků v jednom clusteru s maximálním počtem 20 omezení: 2 vCPU a 350 MB paměti na součást.
  - Pro více než 500 lusků v jednom clusteru s maximálním počtem 40 omezení: 3 vCPU a 600 MB paměti na součást.

Následující doporučení platí pouze pro AKS a doplněk Azure Policy:

- Použijte fond uzlů systému s příchodem `CriticalAddonsOnly` k naplánování na serveru gatekeeper lusky. Další informace najdete v tématu [použití fondů systémových uzlů](use-system-pools.md#system-and-user-node-pools).
- Zabezpečte odchozí přenosy z clusterů AKS. Další informace najdete v tématu [řízení provozu odchozích dat pro uzly clusteru](limit-egress-traffic.md).
- Pokud je cluster `aad-pod-identity` povolený, lusky NMI (Node Managed identity) upraví uzly na softwaru iptables tak, aby zachytil volání koncového bodu metadat instance Azure. Tato konfigurace znamená, že všechny požadavky na koncový bod metadat jsou zachyceny NMI i v případě, že pole pod nepoužívá `aad-pod-identity` . AzurePodIdentityException CRD je možné nakonfigurovat tak, aby informovala `aad-pod-identity` , že všechny požadavky na koncový bod metadat pocházející z objektu pod, který odpovídá popiskům definovaným v CRD, by měly být proxy bez jakéhokoli zpracování v NMI. Systém lusky s `kubernetes.azure.com/managedby: aks` návěštím v oboru názvů _Kube-System_ by měl být vyloučený v `aad-pod-identity` konfiguraci AzurePodIdentityException CRD. Další informace najdete v tématu [zakázání identity AAD-pod-identity pro konkrétního pod nebo aplikaci](https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md).
  Chcete-li konfigurovat výjimku, nainstalujte [YAML s výjimkou mikrofonu](https://github.com/Azure/aad-pod-identity/blob/master/deploy/infra/mic-exception.yaml).

Doplněk Azure Policy vyžaduje, aby fungovaly prostředky procesoru a paměti. Tyto požadavky se zvyšují, protože se zvyšuje velikost clusteru. Obecné pokyny k používání doplňku Azure Policy najdete v tématu [Azure Policy doporučení][policy-recommendations] .

## <a name="azure-policies-to-secure-kubernetes-pods"></a>Zásady Azure pro zabezpečení Kubernetes lusky

Po instalaci doplňku Azure Policy se ve výchozím nastavení neuplatní žádné zásady.

K dispozici je 11 předdefinovaných individuálních zásad Azure a dvou integrovaných iniciativ, které konkrétně zabezpečují lusky v clusteru AKS.
Jednotlivé zásady je možné přizpůsobit pomocí efektu. [Tady je uvedený úplný seznam zásad AKS a jejich podporovaných efektů][policy-samples]. Přečtěte si další informace o [Azure Policych efektech](../governance/policy/concepts/effects.md).

Zásady Azure je možné použít na úrovni skupiny pro správu, předplatného nebo skupiny prostředků. Když přiřadíte zásadu na úrovni skupiny prostředků, ujistěte se, že je v oboru zásad vybraná skupina prostředků cílového clusteru AKS. Každý cluster v přiřazeném oboru s nainstalovaným doplňkem Azure Policy je v oboru pro zásady.

Pokud používáte [zásadu zabezpečení pod (Preview) ](use-pod-security-policies.md), zjistěte, jak [migrovat na Azure Policy a o dalších rozdílech v různých chováních](#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

### <a name="built-in-policy-initiatives"></a>Integrované iniciativy zásad

Iniciativa v Azure Policy je kolekce definic zásad, které jsou přizpůsobené pro dosažení cíle přeplňování jednotného přihlašování. Použití iniciativ může zjednodušit správu a přiřazování zásad napříč AKS clustery. Iniciativa existuje jako jeden objekt, další informace o [Azure Policy iniciativách](../governance/policy/overview.md#initiative-definition).

Azure Policy pro Kubernetes nabízí dvě integrované iniciativy, které mají bezpečnostní lusky, [základní](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) a [omezené](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00).

Obě předdefinované iniciativy jsou sestavené z definic používaných v [pod zásadami zabezpečení z Kubernetes](https://github.com/kubernetes/website/blob/master/content/en/examples/policy/baseline-psp.yaml).

|[Řízení zásad zabezpečení pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Odkaz na definici Azure Policy| [Základní iniciativa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) | [Omezená iniciativa](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) |
|---|---|---|---|
|Zakázat spouštění privilegovaných kontejnerů|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F95edb821-ddaf-4404-9732-666045e056b4)| Yes | Yes
|Zakázat sdílené použití oborů názvů hostitele|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F47a1ee2f-2a2a-4576-bf2a-e0e36709c2b8)| Yes | Yes
|Omezit veškeré využití hostitelských sítí a portů|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F82985f06-dc18-4a48-bc1c-b9f4f0098cfe)| Yes | Yes
|Omezení využití hostitelského systému souborů|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F098fc59e-46c7-4d99-9b16-64990e543d75)| Yes | Yes
|Omezení možností pro Linux na [výchozí sadu](https://docs.docker.com/engine/reference/run/#runtime-privilege-and-linux-capabilities)|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fc26596ff-4d70-4e6a-9a30-c2506bd2f80c) | Yes | Yes
|Omezit využití definovaných typů svazků|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F16697877-1118-4fb1-9b65-9898ec2509ec)| - | Ano – povolené typy svazků jsou `configMap` , `emptyDir` , `projected` , `downwardAPI` , `persistentVolumeClaim`|
|Eskalace oprávnění do kořenového adresáře|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F1c6e92c9-99f0-4e55-9cf2-0c234dc48f99) | - | Yes |
|Omezení ID uživatelů a skupin kontejneru|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Yes|
|Omezte přidělení FSGroup, které vlastní svazky pod.|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff06ddb64-5fa3-4b77-b166-acb36f7f6042) | - | Ano – povolená pravidla jsou `runAsUser: mustRunAsNonRoot` , `supplementalGroup: mustRunAs 1:65536` , `fsGroup: mustRunAs 1:65535` , `runAsGroup: mustRunAs 1:65535` .  |
|Vyžaduje profil seccomp|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F975ce327-682c-4f2e-aa46-b9598289b86c) | - | Ano, allowedProfiles jsou * `docker/default` nebo `runtime/default` |

\* Docker/default se v Kubernetes nepoužívá, protože v 1.11

### <a name="additional-optional-policies"></a>Další volitelné zásady

Existují další zásady Azure, které se dají jednotlivě použít mimo použití iniciativy. Zvažte možnost připojit tyto zásady kromě iniciativ, pokud vaše požadavky nejsou splněné v rámci integrovaných iniciativ.

|[Řízení zásad zabezpečení pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)| Odkaz na definici Azure Policy| Použití kromě základní iniciativy | Použít společně s omezenou iniciativou |
|---|---|---|---|
|Definování profilu AppArmor používaného kontejnery|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F511f5417-5d12-434d-ab2e-816901e72a5e) | Volitelné | Volitelné |
|Povoluje připojení, která nejsou jen pro čtení.|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Fdf49d893-a74c-421d-bc95-c663042e5b80) | Volitelné | Volitelné |
|Omezit na konkrétní ovladače FlexVolume|[Veřejný cloud](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2Ff4a8fce0-2dd5-4c21-9a36-8f0ec809d663) | Volitelné – použijte, pokud chcete jenom ovladače FlexVolume omezit, ale ne jiné nastavit pomocí omezení využití definovaných typů svazků. | Netýká se – omezená iniciativa zahrnuje "omezení využití definovaných typů svazků", což zakáže všechny ovladače FlexVolume |

### <a name="unsupported-built-in-policies-for-managed-aks-clusters"></a>Nepodporované předdefinované zásady pro spravované clustery AKS

> [!NOTE]
> Následující 3 zásady se **v AKS nepodporují** kvůli přizpůsobení aspektů spravovaných a zabezpečených pomocí AKS jako spravované služby. Tyto zásady jsou vytvořené speciálně pro clustery připojené ke službě Azure ARC s nespravovanými rovinami řízení.

|[Řízení zásad zabezpečení pod](https://kubernetes.io/docs/concepts/policy/pod-security-policy/#what-is-a-pod-security-policy)|
|---|
|Definování vlastního kontextu SELinux kontejneru|
|Omezení sysctl profilu používaného kontejnery|
|Výchozí typy připojení procedury jsou definované tak, aby se snížila plocha pro útoky.|

<!---
# Removing until custom initiatives are supported the week after preview

#### Custom initiative

If the built-in initiatives to address pod security do not match your requirements, you can choose your own policies to exist in a custom initiative. Read more about [building custom initatives in Azure Policy](../governance/policy/tutorials/create-and-manage#create-and-assign-an-initiative-definition).

--->

### <a name="namespace-exclusion"></a>Vyloučení oboru názvů

> [!WARNING]
> Lusky v názvových prostorech správců, jako je Kube-System, musí běžet, aby cluster zůstal v pořádku. odebrání požadovaného oboru názvů ze seznamu výchozích vyloučených oborů názvů může vystavit porušení zásad z důvodu požadovaného systému pod.

AKS vyžaduje, aby se systémové lusky spouštěly na clusteru a poskytovaly důležité služby, jako je například překlad DNS. Zásady, které omezují funkčnost, můžou ovlivnit stabilitu systému pod. V důsledku toho jsou během **žádosti o přijetí při vytváření, aktualizaci a auditování zásad vyloučené z vyhodnocení zásad**tyto obory názvů. To vynutí, aby se nová nasazení do těchto oborů názvů vyloučila ze zásad Azure.

1. Kube – systém
1. Server Gatekeeper
1. Azure – ARC
1. AKS – Periscope

Další vlastní obory názvů je možné z vyhodnocení vyloučit během vytváření, aktualizace a auditu. Tato vyloučení by měla být použita, pokud máte specializované lusky, které jsou spuštěny ve schváleném oboru názvů a chcete se vyhnout aktivaci narušení auditu.

## <a name="apply-the-baseline-initiative"></a>Použití základní iniciativy

> [!TIP]
> Všechny zásady se ve výchozím nastavení projeví pro audit. Účinky lze kdykoli aktualizovat na odepřít Azure Policy.

Aby bylo možné použít směrný plán, můžeme přiřazovat prostřednictvím Azure Portal.
<!--
1. Navigate to the Policy service in Azure portal
1. In the left pane of the Azure Policy page, select **Definitions**
1. Search for "Baseline Profile" on the search pane to the right of the page
1. Select `Kubernetes Pod Security Standards Baseline Profile for Linux-based workloads` from the `Kubernetes` category
-->
1. Pomocí [tohoto odkazu na Azure Portal](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) si Projděte základní iniciativu zabezpečení pod.
1. Nastavte **Rozsah** na úroveň předplatného nebo jenom skupinu prostředků obsahující clustery AKS s povoleným doplňkem Azure Policy.
1. Vyberte stránku **parametry** a aktualizujte **efekt** z `audit` na na `deny` , pokud chcete zablokovat nová nasazení porušující základní iniciativu.
1. Přidání dalších oborů názvů, které se mají vyloučit z vyhodnocení během vytváření, aktualizace a auditu, [se některé obory názvů vyloučí z vyhodnocení zásad.](#namespace-exclusion) 
 ![ aktualizovat efekt](media/use-pod-security-on-azure-policy/update-effect.png)
1. Vyberte možnost **zkontrolovat + vytvořit** a odešlete zásady.

Potvrzení zásad se aplikují na cluster spuštěním `kubectl get constrainttemplates` .

> [!NOTE]
> Synchronizace zásad může trvat [až 20 minut, než se synchronizuje](../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition) do každého clusteru.

Výstup by měl vypadat přibližně takto:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              30m
k8sazureblockhostnamespace               30m
k8sazurecontainernoprivilege             30m
k8sazurehostfilesystem                   30m
k8sazurehostnetworkingports              30m
```

## <a name="validate-rejection-of-a-privileged-pod"></a>Ověření odmítnutí privilegovaného pod

Pojďme nejdřív otestovat, co se stane, když naplánujete pod, pomocí kontextu zabezpečení `privileged: true` . Tento kontext zabezpečení přestupňování oprávnění pod. Základní iniciativa nepovoluje privilegované lusky, takže požadavek bude odepřen v případě zamítnutí nasazení.

Vytvořte soubor s názvem `nginx-privileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx
      securityContext:
        privileged: true
```

Vytvořte pod příkazem [kubectl Apply][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Podle očekávání se nepovedlo naplánovat na, jak je znázorněno v následujícím příkladu výstupu:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

V poli se nedosáhnou fáze plánování, takže před přesunutím na neexistují žádné prostředky, které by bylo možné odstranit.

## <a name="test-creation-of-an-unprivileged-pod"></a>Vytvoření testu neprivilegovaného pod

V předchozím příkladu se image kontejneru automaticky pokusila použít kořen k navázání NGINX na port 80. Tuto žádost zamítla iniciativa zásad směrného plánu, takže se pod ní nepovede spustit. Zkusíme teď spustit stejný NGINX pod bez privilegovaného přístupu.

Vytvořte soubor s názvem `nginx-unprivileged.yaml` a vložte následující YAML manifest:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx
```

Vytvořte pod pomocí příkazu [kubectl Applu][kubectl-apply] a zadejte název manifestu YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

V části se úspěšně naplánovalo. Když zkontrolujete stav pod, pomocí příkazu [kubectl Get lusky][kubectl-get] je *spuštěný*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

Tento příklad ukazuje základní iniciativu ovlivňující jenom nasazení, která porušují zásady v kolekci. Povolená nasazení budou nadále fungovat.

Pomocí příkazu [kubectl Delete][kubectl-delete] odstraňte Nginx s neprivilegovaným příkazem a zadejte název vašeho manifestu YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-policies-and-the-azure-policy-add-on"></a>Zakázat zásady a doplněk Azure Policy

Odebrání základní iniciativy:

1. Přejděte do podokna zásady na Azure Portal
1. V levém podokně vyberte **přiřazení** .
1. Klikněte na "..." tlačítko vedle profilu standardních hodnot
1. Vyberte Odstranit přiřazení.

![Odstranit přiřazení](media/use-pod-security-on-azure-policy/delete-assignment.png)

Doplněk Azure Policy zakážete pomocí příkazu [AZ AKS Disable-addons][az-aks-disable-addons] .

```azurecli
az aks disable-addons --addons azure-policy --name MyAKSCluster --resource-group MyResourceGroup
```

Naučte se, jak odebrat [doplněk Azure Policy z Azure Portal](../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks).

## <a name="migrate-from-kubernetes-pod-security-policy-to-azure-policy"></a>Migrace ze zásad zabezpečení Kubernetes pod na Azure Policy

Chcete-li provést migraci ze zásad zabezpečení pod, je třeba provést následující akce v clusteru.

1. [Zakázat zásadu zabezpečení pod](use-pod-security-policies.md#clean-up-resources) v clusteru
1. Povolení [doplňku Azure Policy][kubernetes-policy-reference]
1. Povolit požadované zásady Azure z [dostupných integrovaných zásad][policy-samples]

Níže je souhrn změn chování mezi zásadami zabezpečení a Azure Policy.

|Scénář| Zásady zabezpečení pod | Azure Policy |
|---|---|---|
|Instalace|Funkce zásady zabezpečení Povolit pod |Povolit Azure Policy doplněk
|Nasadit zásady| Prostředek nasazení pod zásadou zabezpečení| Přiřaďte zásady Azure k oboru skupiny prostředků nebo předplatnému. Pro aplikace prostředků Kubernetes je vyžadován doplněk Azure Policy.
| Výchozí zásady | Když je v AKS povolené zásady zabezpečení, aplikují se výchozí privilegované a neomezená zásada. | Povolením doplňku Azure Policy nepoužijete žádné výchozí zásady. Zásady musíte explicitně povolit v Azure Policy.
| Kdo může vytvářet a přiřazovat zásady | Správce clusteru vytvoří prostředek zásad zabezpečení pod. | Uživatelé musí mít ve skupině prostředků clusteru AKS minimální roli oprávnění "vlastník" nebo "Přispěvatel zásad prostředků". -Prostřednictvím rozhraní API můžou uživatelé přiřazovat zásady v oboru prostředků clusteru AKS. Uživatel by měl mít minimálně oprávnění "vlastník" nebo "Přispěvatel zásad prostředků" na prostředku clusteru AKS. -V Azure Portal lze zásady přiřadit na úrovni skupiny pro správu nebo předplatného nebo skupiny prostředků.
| Autorizace zásad| Uživatelé a účty služeb vyžadují explicitní oprávnění k používání zásad zabezpečení pod. | K autorizaci zásad není nutné žádné další přiřazení. Až se zásady přiřadí v Azure, můžou tyto zásady používat všichni uživatelé clusteru.
| Použitelnost zásad | Uživatel s rolí správce obchází vynucování zásad zabezpečení pod. | Všichni uživatelé (Správci & nepoužívají správce) uvidí stejné zásady. Na základě uživatelů neexistují žádná speciální velká písmena. Aplikaci zásad lze vyloučit na úrovni oboru názvů.
| Rozsah zásad | Zásady zabezpečení pod oborem názvů nejsou. | Šablony omezení používané Azure Policy nejsou obor názvů.
| Akce odepřít/audit/mutace | Zásady zabezpečení pod podporují jenom akce Deny. Mutace se dají udělat s výchozími hodnotami pro žádosti o vytvoření. Ověřování lze provést během požadavků na aktualizaci.| Azure Policy podporuje akce zakázat &. Mutace se ještě nepodporují, ale byly plánované.
| Dodržování předpisů zásad zabezpečení pod | Neexistují žádné informace o dodržování předpisů lusky, které existovaly před povolením zásad zabezpečení. Neodpovídající lusky vytvořené po povolení zásad zabezpečení v případě odepření. | Neodpovídající lusky, které existovaly před použitím zásad Azure, se budou zobrazovat v porušení zásad. Neodpovídající lusky vytvořené po povolení zásad Azure se odepře, pokud jsou zásady nastavené s použitím efektu odepření.
| Postup zobrazení zásad v clusteru | `kubectl get psp` | `kubectl get constrainttemplate` – Vrátí se všechny zásady.
| Pod standardem zásady zabezpečení – privilegované | Při povolení této funkce se ve výchozím nastavení vytvoří prostředek zásad zabezpečení s oprávněním pod. | Privilegovaný režim nezahrnuje žádné omezení. Výsledkem je, že nemusíte mít žádné Azure Policy přiřazení.
| [Standard zásad zabezpečení/Standardní – Standardní hodnota](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Uživatel nainstaluje základní zdroj zásad zabezpečení. | Azure Policy poskytuje [integrovanou iniciativu podle směrného plánu](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) , která se mapuje na základní zásady zabezpečení podle směrného plánu.
| [V případě zásad zabezpečení s omezením úrovně Standard](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Uživatel nainstaluje prostředek pod omezením zásad zabezpečení. | Azure Policy poskytuje [integrovaný s omezenou iniciativou](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) , která se mapuje na zásadu zabezpečení s omezením pod.

## <a name="next-steps"></a>Další kroky

Tento článek ukazuje, jak použít zásadu Azure, která omezuje nasazování privilegovaných lusků, aby nedocházelo k použití privilegovaného přístupu. Existuje mnoho zásad, které je možné použít, například zásady, které omezují použití svazků. Další informace o dostupných možnostech najdete v článku [Azure Policy pro referenční dokumenty Kubernetes][kubernetes-policy-reference].

Další informace o omezování síťového provozu najdete v tématu [zabezpečení provozu mezi lusky pomocí zásad sítě v AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[aad-pod-identity]: https://github.com/Azure/aad-pod-identity
[aad-pod-identity-exception]: https://github.com/Azure/aad-pod-identity/blob/master/docs/readmes/README.app-exception.md

<!-- LINKS - internal -->
[policy-recommendations]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-limitations]: ../governance/policy/concepts/policy-for-kubernetes.md?#limitations
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[az-aks-disable-addons]: /cli/azure/aks#az-aks-disable-addons
[az-policy-assignment-delete]: /cli/azure/policy/assignment#az-policy-assignment-delete