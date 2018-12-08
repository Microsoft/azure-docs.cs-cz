---
title: Operátor osvědčené postupy – zabezpečení clusteru ve službě Azure Kubernetes služby (AKS)
description: Podívejte se operátor clusteru osvědčené postupy pro Správa clusteru zabezpečení a inovace ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: iainfou
ms.openlocfilehash: 5acabc8381422b9202b041cf849af3b35809a3c0
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53109651"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení clusteru a inovace ve službě Azure Kubernetes Service (AKS)

Jak budete spravovat clustery ve službě Azure Kubernetes Service (AKS), zabezpečení úloh a dat je důležitým aspektem. Zejména v případě, že spustíte víceklientské clustery pomocí logické izolace, budete muset zabezpečený přístup k prostředkům a úlohy. Chcete-li minimalizovat riziko útoku, musíte také zkontrolujte, zda že použít nejnovější Kubernetes a aktualizace zabezpečení operačního systému uzlu.

Tento článek se zaměřuje na zabezpečení clusteru AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití Azure Active Directory a řízení přístupu na základě rolí k zabezpečení přístupu k serveru rozhraní API
> * Zabezpečení kontejneru přístup k prostředkům uzlu
> * Upgrade clusteru AKS na nejnovější verzi Kubernetes
> * Zachovat aktualizace uzlů na datum a automatické opravy zabezpečení

Můžete si také přečíst osvědčené postupy pro [Správa imagí kontejnerů] [ best-practices-container-image-management] a [pod zabezpečení][best-practices-pod-security].

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpečený přístup k rozhraní API serveru a clusterovým uzlem

**Osvědčené postupy pro moduly** – zabezpečení přístupu pro Kubernetes rozhraní API – Server je jedním z nejdůležitějších věcí, vám pomůžou zabezpečit váš cluster. Integrace s Azure Active Directory pro řízení přístupu k serveru rozhraní API Kubernetes řízení přístupu na základě role (RBAC). Tyto ovládací prvky umožňují zabezpečit AKS stejným způsobem jako zabezpečený přístup k předplatným Azure.

Na serveru Kubernetes API poskytuje jeden bod připojení pro žádosti o provádění akcí v rámci clusteru. K zabezpečení a auditování přístupu k rozhraní API serveru, přístup a poskytují nejnižším oprávněním přístupová oprávnění vyžaduje. Tento přístup není jedinečný pro Kubernetes, ale je zvlášť důležité při clusteru AKS je logicky izolovaná pro použití více tenantů.

Azure Active Directory (AD) poskytuje řešení pro správu identit připraveno pro podniky, která se integruje s clustery AKS. Jak Kubernetes neposkytuje řešení pro správu identit, v opačném případě lze obtížně detailní způsob, jak omezit přístup k rozhraní API serveru. S Azure integrované s Active Directory clustery ve službě AKS použijete k ověřování uživatelů k rozhraní API serveru vaše existující účty uživatelů a skupin.

![Integrace s Azure Active Directory pro clustery AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Použití Kubernetes RBAC a Azure integrace AD k zabezpečení rozhraní API serveru a poskytují nejmenší počet oprávnění potřebná k oboru sadu prostředků, jako je například jednoho oboru názvů. Různí uživatelé nebo skupiny ve službě Azure AD lze udělit různé role RBAC. Tyto podrobné oprávnění umožňují omezit přístup k serveru rozhraní API a poskytnout jasné revizní záznam akcí prováděných.

Doporučené osvědčeným postupem je použití skupin pro poskytnutí přístupu k souborům a složkám a jednotlivé identity, použijte Azure AD *skupiny* členství pro vazbu uživatelů pro role RBAC spíše než jednotlivé *uživatelé*. Jako uživatele změn členství ve skupinách bude odpovídajícím způsobem měnit jejich přístupových oprávnění na clusteru AKS. Pokud svážete přímo k roli uživatele, se může změnit svoje pracovní funkci. Členství ve skupinách Azure AD by aktualizoval, ale nebude odrážel oprávnění v clusteru AKS. V tomto scénáři uživatel skončilo udělením více oprávnění, než uživatel požaduje.

Další informace o integraci služby Azure AD a RBAC najdete v tématu [osvědčené postupy pro ověřování a autorizace ve službě AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Zabezpečení kontejneru přístup k prostředkům

**Osvědčené postupy pro moduly** -omezit přístup na akce, které můžete provádět kontejnery. Zadejte minimální počet oprávnění a vyhnout se použít kořenovém / privilegovaného eskalace.

Stejným způsobem, že byste měli udělit uživatelům nebo skupinám nejmenší počet oprávnění vyžadovaných, kontejnery by měla být omezené jenom na akce a procesy, které potřebují. Chcete-li minimalizovat riziko útoku, nekonfigurujte aplikací a kontejnerů, které vyžadují eskalované oprávnění nebo kořenový přístup. Například nastavte `allowPrivilegeEscalation: false` v manifestu pod. Tyto *pod kontexty zabezpečení* jsou integrované do Kubernetes a vám umožňují definovat další oprávnění, jako je například uživatel nebo skupina spustit jako, nebo jaké možnosti Linux ke zveřejnění. Osvědčené postupy, najdete v článku [pod zabezpečený přístup k prostředkům][pod-security-contexts].

Pro podrobnější řízení kontejneru akcí, můžete použít také integrované funkce zabezpečení systému Linux, jako *AppArmor* a *seccomp*. Tyto funkce jsou definovány na úrovni uzlu a pak implementované pomocí pod manifestu.

### <a name="app-armor"></a>Armor aplikace

K omezení akcí, které mohou provádět kontejnerů, můžete použít [AppAmour] [ k8s-apparmor] modulu zabezpečení jádra systému Linux. AppArmor je k dispozici jako součást základní uzlů AKS operačního systému a je ve výchozím nastavení povolené. Vytváření AppArmor profily, které omezují akce, jako čtení, zápis nebo spustit nebo systémové funkce, jako je například připojení systémy souborů. Výchozí profily AppArmor omezit přístup k různým `/proc` a `/sys` umístění a poskytují způsob logicky izolovat kontejnery ze základní uzel. AppArmor funguje pro každou aplikaci, která běží na systému Linux, ne jenom podů Kubernetes.

![Profily AppArmor nepoužívá v clusteru AKS k omezení akcí kontejneru](media/operator-best-practices-container-security/apparmor.png)

AppArmor v akci najdete v následujícím příkladu vytvoří profil, který neumožňuje zápis do souborů. [SSH] [ aks-ssh] uzlu AKS, vytvořte soubor s názvem *odepřít write.profile* a vložte následující obsah:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

AppArmor profily jsou přidány pomocí `apparmor_parser` příkazu. Přidat profil do AppArmor a zadejte název profilu, vytvořili v předchozím kroku:

```console
sudo apparmor_parser deny-write.profile
```

Neexistuje žádný výstup vrácena, pokud je profil správně analyzovat a použít na AppArmor. Vrátíte se do příkazového řádku.

Z místního počítače, teď vytvoření manifestu pod názvem *aks apparmor.yaml* a vložte následující obsah. Tento manifest definuje anotaci pro `container.apparmor.security.beta.kubernetes` přidat odkazy *odepřít zápisu* profil vytvořený v předchozích krocích:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: hello-apparmor
  annotations:
    container.apparmor.security.beta.kubernetes.io/hello: localhost/k8s-apparmor-example-deny-write
spec:
  containers:
  - name: hello
    image: busybox
    command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
```

Nasadit ukázkový pod pomocí [použití kubectl] [ kubectl-apply] příkaz:

```console
kubectl apply -f aks-apparmor.yaml
```

Pomocí podu nasazené [kubectl exec] [ kubectl-exec] příkaz pro zápis do souboru. Příkaz nejde provést, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Další informace o AppArmor najdete v tématu [AppArmor profily v Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Zabezpečení výpočetních

Zatímco AppArmor funguje pro aplikace pro Linux, [seccomp (*sekundu*selhání *kompozice*TNG postup)] [ seccomp] funguje na úrovni procesu. Seccomp je také modul zabezpečení jádra systému Linux a má nativní podporu Docker modul runtime používá uzlů AKS. S seccomp procesní volání, které mohou provádět kontejnery jsou omezené. Vytvořit filtry, které definují, jaká opatření je třeba povolit nebo odepřít a pak pomocí poznámek v rámci manifestu YAML pod přidružit k filtru seccomp. To odpovídá jenom udělíte minimální oprávnění, které jsou potřeba ke spuštění kontejneru a žádné další doporučené postupy.

Pokud chcete vidět v seccomp v akci, vytvořte filtr, který zabrání změně oprávnění k souboru. [SSH] [ aks-ssh] uzlu AKS, vytvořte seccomp filtru s názvem */var/lib/kubelet/seccomp/prevent-chmod* a vložte následující obsah:

```
{
  "defaultAction": "SCMP_ACT_ALLOW",
  "syscalls": [
    {
      "name": "chmod",
      "action": "SCMP_ACT_ERRNO"
    }
  ]
}
```

Z místního počítače, teď vytvoření manifestu pod názvem *aks seccomp.yaml* a vložte následující obsah. Tento manifest definuje anotaci pro `seccomp.security.alpha.kubernetes.io` a odkazuje *zabránit chmod* Filtr vytvořený v předchozím kroku:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: chmod-prevented
  annotations:
    seccomp.security.alpha.kubernetes.io/pod: localhost/prevent-chmod
spec:
  containers:
  - name: chmod
    image: busybox
    command:
      - "chmod"
    args:
     - "777"
     - /etc/hostname
  restartPolicy: Never
```

Nasadit ukázkový pod pomocí [použití kubectl] [ kubectl-apply] příkaz:

```console
kubectl apply -f ./aks-seccomp.yaml
```

Zobrazení stavu podů pomocí [kubectl get pods] [ kubectl-get] příkazu. Pokud chcete pod nahlásí chybu. `chmod` Příkaz je zabráněno spuštění filtrem seccomp, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Další informace o dostupných filtrech najdete v tématu [Seccomp zabezpečení profilů pro Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Pravidelně aktualizujte na nejnovější verzi Kubernetes

**Osvědčené postupy pro moduly** – Pokud chcete aktuální informace o nové funkce a opravy chyb, pravidelně upgrade na Kubernetes verze ve vašem clusteru AKS.

Nové funkce rychleji než tradiční infrastruktury platformy tempem vydaných verzí Kubernetes. Kubernetes aktualizace obsahují nové funkce a opravy chyb a zabezpečení. Nové funkce obvykle procházení *alfa* a potom *beta* stav dřív, než narostou *stabilní* a jsou obecně dostupné a doporučuje se pro použití v produkčním prostředí. Tohoto cyklu vydávání verzí by bylo možné Kubernetes aktualizovat bez nutnosti pravidelně dochází k rozbíjející změny nebo nastavení nasazení a šablony.

AKS podporuje čtyři dílčí verze Kubernetes. To znamená, že při je zavedení nové verze menší opravy, byly ukončeny nejstarší vedlejší verze a opravy verze nepodporuje. Méně závažné aktualizace Kubernetes dojít v pravidelných intervalech. Ujistěte se, že máte zásady správného řízení procesu pro kontrolu a podle potřeby, takže nejsou dodrženy podporu upgradovat. Další informace najdete v tématu [verze nepodporuje Kubernetes AKS][aks-supported-versions]

Pokud chcete zkontrolovat verze, které jsou k dispozici pro váš cluster, použijte [az aks get upgrady] [ az-aks-get-upgrades] příkaz, jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Potom můžete upgradovat pomocí clusteru AKS [az aks upgrade] [ az-aks-upgrade] příkazu. Proces upgradu bezpečně cordons pozastavuje jednoho uzlu současně, naplánuje podů na zbývající uzly a pak nasadí nový uzel s nejnovější verzí OS a Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version 1.11.3
```

Další informace o upgradech ve službě AKS najdete v tématu [verze nepodporuje Kubernetes v AKS] [ aks-supported-versions] a [Upgrade clusteru AKS][aks-upgrade].

## <a name="process-node-updates-and-reboots-using-kured"></a>Uzel proces aktualizuje a restartuje pomocí kured

**Osvědčené postupy pro moduly** – AKS automaticky stáhne a nainstaluje zabezpečení řeší na všech pracovních uzlů, ale nejsou automaticky restartuje v případě potřeby. Použití `kured` čekající restartování počítače, podívejte se pak bezpečně kordon a výpusť uzlu povolit uzel restartovat, se aktualizace nainstalovaly a být tak bezpečné jako možné s ohledem na operační systém.

Každý večer uzlů AKS získat opravy zabezpečení, které jsou k dispozici prostřednictvím kanálu aktualizace jejich distribuce. Toto chování je automaticky nakonfigurovaný, jako jsou nasazené uzly v clusteru AKS. Chcete-li minimalizovat narušení a možnému dopadu na běžící úlohu, uzly nejsou restartuje automaticky pokud úroveň opravy zabezpečení nebo aktualizace jádra vyžaduje.

Open source [kured (KUbernetes restartování démona)] [ kured] projektu Weaveworks sleduje pro čekající restartování uzlu. Pokud uzel provede aktualizace vyžadující restart počítače, uzel bezpečně uzavřené a Vyprázdněné přesunout a plán podů na jiných uzlech v clusteru. Jakmile se uzel restartuje, přidá se zpátky do clusteru a plánování podů na něm obnoví Kubernetes. Pokud chcete přerušení minimalizovat, je povoleno pouze jednoho uzlu současně restartovat `kured`.

![Proces restartování uzlů AKS pomocí kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Pokud chcete lepší kontrolu intervalem přes při restartování dochází, `kured` lze integrovat s Prometheus zabránit restartování počítače, pokud existují další události údržby nebo clusteru problémy v průběhu. Tato integrace minimalizuje další komplikace restartováním uzly, když se aktivně řešení jiných problémů.

Další informace o tom, jak zpracovat restartování uzlu najdete v tématu [použití aktualizací zabezpečení a jádra pro uzly ve službě AKS][aks-kured].

## <a name="next-steps"></a>Další postup

Tento článek se zaměřuje na tom, jak zabezpečit AKS cluster. K provedení některých z těchto oblastí, naleznete v následujících článcích:

* [Integrace služby Azure Active Directory s AKS][aks-aad]
* [Upgrade clusteru AKS na nejnovější verzi Kubernetes][aks-upgrade]
* [Proces aktualizace a uzel se restartuje s kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://docs.docker.com/engine/security/seccomp/
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-exec]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#exec
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get

<!-- INTERNAL LINKS -->
[az-aks-get-upgrades]: /cli/azure/aks#az-aks-get-upgrades
[az-aks-upgrade]: /cli/azure/aks#az-aks-upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: aad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
