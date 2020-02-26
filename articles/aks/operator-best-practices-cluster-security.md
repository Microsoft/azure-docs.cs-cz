---
title: Operátor osvědčené postupy – zabezpečení clusteru ve službě Azure Kubernetes služby (AKS)
description: Podívejte se operátor clusteru osvědčené postupy pro Správa clusteru zabezpečení a inovace ve službě Azure Kubernetes Service (AKS)
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f02f6588946e2b63a1a092aba15603d1685e8207
ms.sourcegitcommit: 99ac4a0150898ce9d3c6905cbd8b3a5537dd097e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77594800"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení clusteru a inovace ve službě Azure Kubernetes Service (AKS)

Jak budete spravovat clustery ve službě Azure Kubernetes Service (AKS), zabezpečení úloh a dat je důležitým aspektem. Zejména v případě, že spustíte víceklientské clustery pomocí logické izolace, budete muset zabezpečený přístup k prostředkům a úlohy. Chcete-li minimalizovat riziko útoku, musíte také zkontrolujte, zda že použít nejnovější Kubernetes a aktualizace zabezpečení operačního systému uzlu.

Tento článek se zaměřuje na zabezpečení clusteru AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití Azure Active Directory a řízení přístupu na základě rolí k zabezpečení přístupu k serveru rozhraní API
> * Zabezpečení kontejneru přístup k prostředkům uzlu
> * Upgrade clusteru AKS na nejnovější verzi Kubernetes
> * Udržování uzlů v aktuálním stavu a automatické použití oprav zabezpečení

Můžete si také přečíst osvědčené postupy pro [správu imagí kontejnerů][best-practices-container-image-management] a pro [zabezpečení pod][best-practices-pod-security].

[Integraci služeb Azure Kubernetes Services s Security Center][security-center-aks] můžete také využít k detekci hrozeb a zobrazení doporučení pro zabezpečení clusterů AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpečený přístup k rozhraní API serveru a clusterovým uzlem

**Pokyny k osvědčeným postupům** – zabezpečení přístupu k rozhraní Kubernetes API – Server je jedním z nejdůležitějších věcí, které můžete provést pro zabezpečení clusteru. Integrace s Azure Active Directory pro řízení přístupu k serveru rozhraní API Kubernetes řízení přístupu na základě role (RBAC). Tyto ovládací prvky umožňují zabezpečit AKS stejným způsobem jako zabezpečený přístup k předplatným Azure.

Na serveru Kubernetes API poskytuje jeden bod připojení pro žádosti o provádění akcí v rámci clusteru. K zabezpečení a auditování přístupu k rozhraní API serveru, přístup a poskytují nejnižším oprávněním přístupová oprávnění vyžaduje. Tento přístup není jedinečný pro Kubernetes, ale je zvlášť důležité při clusteru AKS je logicky izolovaná pro použití více tenantů.

Azure Active Directory (AD) poskytuje řešení pro správu identit připraveno pro podniky, která se integruje s clustery AKS. Jak Kubernetes neposkytuje řešení pro správu identit, v opačném případě lze obtížně detailní způsob, jak omezit přístup k rozhraní API serveru. S Azure integrované s Active Directory clustery ve službě AKS použijete k ověřování uživatelů k rozhraní API serveru vaše existující účty uživatelů a skupin.

![Integrace s Azure Active Directory pro clustery AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Použití Kubernetes RBAC a Azure integrace AD k zabezpečení rozhraní API serveru a poskytují nejmenší počet oprávnění potřebná k oboru sadu prostředků, jako je například jednoho oboru názvů. Různí uživatelé nebo skupiny ve službě Azure AD lze udělit různé role RBAC. Tyto podrobné oprávnění umožňují omezit přístup k serveru rozhraní API a poskytnout jasné revizní záznam akcí prováděných.

Doporučený postup je použít skupiny k poskytnutí přístupu k souborům a složkám proti jednotlivým identitám. členství ve *skupině* Azure AD můžete použít ke svázání uživatelů s rolemi RBAC, nikoli jednotlivými *uživateli*. Jako uživatele změn členství ve skupinách bude odpovídajícím způsobem měnit jejich přístupových oprávnění na clusteru AKS. Pokud svážete přímo k roli uživatele, se může změnit svoje pracovní funkci. Členství ve skupinách Azure AD by aktualizoval, ale nebude odrážel oprávnění v clusteru AKS. V tomto scénáři uživatel skončilo udělením více oprávnění, než uživatel požaduje.

Další informace o integraci a RBAC služby Azure AD najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Zabezpečení kontejneru přístup k prostředkům

**Doprovodné materiály k osvědčeným postupům** – omezení přístupu k akcím, které mohou kontejnery provádět. Zadejte minimální počet oprávnění a vyhnout se použít kořenovém / privilegovaného eskalace.

Stejným způsobem, že byste měli udělit uživatelům nebo skupinám nejmenší počet oprávnění vyžadovaných, kontejnery by měla být omezené jenom na akce a procesy, které potřebují. Chcete-li minimalizovat riziko útoku, nekonfigurujte aplikací a kontejnerů, které vyžadují eskalované oprávnění nebo kořenový přístup. Například nastavte `allowPrivilegeEscalation: false` v manifestu pod. Tyto položky *zabezpečení* jsou integrovány do Kubernetes a umožňují definovat další oprávnění, jako je například uživatel nebo skupina, které se mají spustit jako nebo jaké možnosti systému Linux mají být vystavení. Další doporučené postupy najdete v tématu [zabezpečení pod přístupem k prostředkům][pod-security-contexts].

Pro podrobnější kontrolu nad akcemi kontejneru můžete použít také integrované funkce zabezpečení pro Linux, jako je *AppArmor* a *seccomp*. Tyto funkce jsou definovány na úrovni uzlu a pak implementované pomocí pod manifestu. Integrované funkce zabezpečení pro Linux jsou dostupné jenom v uzlech a luskech systému Linux.

> [!NOTE]
> Prostředí Kubernetes, v AKS nebo jinde, nejsou zcela bezpečná pro nepřátelský využití více tenantů. Další funkce zabezpečení, jako jsou *AppArmor*, *seccomp*, *pod, zásady zabezpečení*nebo podrobnější řízení přístupu na základě rolí (RBAC) pro uzly, se obtížně využívají. Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty však je hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery.

### <a name="app-armor"></a>Armor aplikace

K omezení akcí, které mohou kontejnery provádět, můžete použít modul zabezpečení jádra systému [AppArmor][k8s-apparmor] Linux. AppArmor je k dispozici jako součást základní uzlů AKS operačního systému a je ve výchozím nastavení povolené. Vytváření AppArmor profily, které omezují akce, jako čtení, zápis nebo spustit nebo systémové funkce, jako je například připojení systémy souborů. Výchozí profily AppArmor omezují přístup k různým `/proc` a `/sys` umístění a poskytují prostředky pro logickou izolaci kontejnerů z podkladového uzlu. AppArmor funguje pro každou aplikaci, která běží na systému Linux, ne jenom podů Kubernetes.

![Profily AppArmor nepoužívá v clusteru AKS k omezení akcí kontejneru](media/operator-best-practices-container-security/apparmor.png)

AppArmor v akci najdete v následujícím příkladu vytvoří profil, který neumožňuje zápis do souborů. [SSH][aks-ssh] na uzel AKS a pak vytvořte soubor s názvem *Deny-Write. Profile* a vložte následující obsah:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Profily AppArmor se přidávají pomocí příkazu `apparmor_parser`. Přidat profil do AppArmor a zadejte název profilu, vytvořili v předchozím kroku:

```console
sudo apparmor_parser deny-write.profile
```

Neexistuje žádný výstup vrácena, pokud je profil správně analyzovat a použít na AppArmor. Vrátíte se do příkazového řádku.

Z místního počítače teď vytvořte manifest pod názvem *AKS-AppArmor. yaml* a vložte následující obsah. Tento manifest definuje anotaci pro `container.apparmor.security.beta.kubernetes` přidat odkazy na profil pro *odepření a zápis* vytvořený v předchozích krocích:

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

Nasaďte ukázku pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f aks-apparmor.yaml
```

Po nasazení pod nasazeným příkazem použijte příkaz [kubectl exec][kubectl-exec] k zápisu do souboru. Příkaz nejde provést, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Další informace o AppArmor najdete v tématu [profily AppArmor v Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Zabezpečení výpočetních

I když AppArmor funguje pro libovolnou aplikaci pro Linux, na úrovni procesu funguje [seccomp (*sec*urovat *comp*uting)][seccomp] . Seccomp je také modul zabezpečení jádra systému Linux a má nativní podporu Docker modul runtime používá uzlů AKS. S seccomp procesní volání, které mohou provádět kontejnery jsou omezené. Vytvořit filtry, které definují, jaká opatření je třeba povolit nebo odepřít a pak pomocí poznámek v rámci manifestu YAML pod přidružit k filtru seccomp. To odpovídá jenom udělíte minimální oprávnění, které jsou potřeba ke spuštění kontejneru a žádné další doporučené postupy.

Pokud chcete vidět v seccomp v akci, vytvořte filtr, který zabrání změně oprávnění k souboru. [SSH][aks-ssh] na uzel AKS a pak vytvořte filtr seccomp s názvem */var/lib/kubelet/seccomp/Prevent-chmod* a vložte následující obsah:

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

Z místního počítače teď vytvořte manifest pod názvem *AKS-seccomp. yaml* a vložte následující obsah. Tento manifest definuje anotaci pro `seccomp.security.alpha.kubernetes.io` a odkazuje na filtr *prevence – chmod* vytvořený v předchozím kroku:

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

Nasaďte ukázku pomocí příkazu [kubectl Apply][kubectl-apply] :

```console
kubectl apply -f ./aks-seccomp.yaml
```

Stav lusků zobrazíte pomocí příkazu [kubectl získat lusky][kubectl-get] . Pokud chcete pod nahlásí chybu. `chmod` příkazu je znemožněno spouštěním filtru seccomp, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Další informace o dostupných filtrech najdete v tématu [Seccomp Security profiles for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Pravidelně aktualizujte na nejnovější verzi Kubernetes

**Doprovodné materiály k osvědčeným postupům** – aktuální informace o nových funkcích a opravách chyb pravidelně inovujte na verzi Kubernetes v clusteru AKS.

Nové funkce rychleji než tradiční infrastruktury platformy tempem vydaných verzí Kubernetes. Kubernetes aktualizace obsahují nové funkce a opravy chyb a zabezpečení. Nové funkce obvykle přecházejí přes *alfa* a pak na stav *beta verze* , než se stanou *stabilní* a jsou všeobecně dostupné a doporučené pro použití v produkčním prostředí. Tohoto cyklu vydávání verzí by bylo možné Kubernetes aktualizovat bez nutnosti pravidelně dochází k rozbíjející změny nebo nastavení nasazení a šablony.

AKS podporuje čtyři dílčí verze Kubernetes. To znamená, že při je zavedení nové verze menší opravy, byly ukončeny nejstarší vedlejší verze a opravy verze nepodporuje. Méně závažné aktualizace Kubernetes dojít v pravidelných intervalech. Ujistěte se, že máte zásady správného řízení procesu pro kontrolu a podle potřeby, takže nejsou dodrženy podporu upgradovat. Další informace najdete v tématu [podporované verze KUBERNETES AKS][aks-supported-versions]

Chcete-li ověřit verze, které jsou pro cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Následně můžete upgradovat cluster AKS pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Proces upgradu bezpečně cordons pozastavuje jednoho uzlu současně, naplánuje podů na zbývající uzly a pak nasadí nový uzel s nejnovější verzí OS a Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Další informace o upgradech v AKS najdete v tématu [podporované verze Kubernetes v AKS][aks-supported-versions] a [upgrade clusteru AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Zpracování aktualizací a restartování uzlu Linux pomocí kured

**Doprovodné materiály k osvědčeným postupům** – AKS automaticky stáhne a nainstaluje opravy zabezpečení na jednotlivé uzly Linux, ale v případě potřeby se nerestartuje automaticky. Použijte `kured` ke sledování nedokončených restartování, pak bezpečně Cordon a vyprázdněte uzel, aby bylo možné uzel restartovat, použít aktualizace a co nejbezpečněji pro operační systém. Pro uzly Windows serveru (v současné době ve verzi Preview v AKS) pravidelně provádějte operaci upgradu AKS a bezpečně Cordon a vyprázdněte a nasaďte aktualizované uzly.

Každý večer, uzly Linux v AKS získá opravy zabezpečení dostupné prostřednictvím kanálu aktualizace distribuce. Toto chování je automaticky nakonfigurovaný, jako jsou nasazené uzly v clusteru AKS. Chcete-li minimalizovat narušení a možnému dopadu na běžící úlohu, uzly nejsou restartuje automaticky pokud úroveň opravy zabezpečení nebo aktualizace jádra vyžaduje.

Projekt Open source [kured (KUbernetes restart Daemon)][kured] tím, že Weaveworks sleduje čekání na restartování uzlu. Pokud uzel Linux používá aktualizace, které vyžadují restartování, uzel se bezpečně uzavřené a vyprázdní, aby se přesunuly a naplánovaly lusky na jiných uzlech v clusteru. Jakmile se uzel restartuje, přidá se zpátky do clusteru a plánování podů na něm obnoví Kubernetes. Aby se minimalizovalo přerušení, dá se restartovat jenom jeden uzel v čase `kured`.

![Proces restartování uzlů AKS pomocí kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Pokud chcete při restartování provést jemnější kontrolu, `kured` se může integrovat s Prometheus, aby se zabránilo restartování, pokud dojde k jiným událostem údržby nebo problémům s clusterem. Tato integrace minimalizuje další komplikace restartováním uzly, když se aktivně řešení jiných problémů.

Další informace o tom, jak zpracovat restartování uzlu, najdete v tématu [použití aktualizací zabezpečení a jádra na uzlech v AKS][aks-kured].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na tom, jak zabezpečit AKS cluster. K provedení některých z těchto oblastí, naleznete v následujících článcích:

* [Integrace Azure Active Directory s AKS][aks-aad]
* [Upgrade clusteru AKS na nejnovější verzi nástroje Kubernetes][aks-upgrade]
* [Zpracování aktualizací zabezpečení a restartování uzlu pomocí kured][aks-kured]

<!-- EXTERNAL LINKS -->
[kured]: https://github.com/weaveworks/kured
[k8s-apparmor]: https://kubernetes.io/docs/tutorials/clusters/apparmor/
[seccomp]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#seccomp
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
[aks-aad]: azure-ad-integration.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: /azure/security-center/azure-kubernetes-service-integration
