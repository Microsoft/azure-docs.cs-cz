---
title: Osvědčené postupy operátora – zabezpečení clusteru ve službách Azure Kubernetes Services (AKS)
description: Seznamte se s doporučenými postupy pro operátorclusteru, jak spravovat zabezpečení clusteru a upgrady ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: f02f6588946e2b63a1a092aba15603d1685e8207
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594800"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Doporučené postupy pro zabezpečení clusteru a upgrady ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) je zabezpečení vašich úloh a dat klíčovým aspektem. Zejména při spuštění clusterů s více tenanty pomocí logické izolace, je třeba zabezpečit přístup k prostředkům a úlohám. Chcete-li minimalizovat riziko útoku, musíte také použít nejnovější aktualizace zabezpečení Kubernetes a node OS.

Tento článek se zaměřuje na zabezpečení clusteru AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití Azure Active Directory a ovládacích prvků přístupu založených na rolích k zabezpečení přístupu k serveru API
> * Zabezpečený přístup kontejneru k prostředkům uzlu
> * Upgrade clusteru AKS na nejnovější verzi Kubernetes
> * Udržovat uzly aktuální a automaticky používat opravy zabezpečení

Můžete si také přečíst osvědčené postupy pro [správu image kontejneru][best-practices-container-image-management] a pro [zabezpečení podu][best-practices-pod-security].

Můžete také použít [azure kubernetes services integrace s Security Center][security-center-aks] pomoci odhalit hrozby a zobrazit doporučení pro zabezpečení clusterů AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpečený přístup k uzlem serveru api a clusteru

**Pokyny pro osvědčené postupy** – zabezpečení přístupu k serveru Api-Server Kubernetes je jednou z nejdůležitějších věcí, které můžete udělat pro zabezpečení clusteru. Integrujte řízení přístupu na základě rolí Kubernetes (RBAC) s Azure Active Directory a řidíte přístup k serveru API. Tyto ovládací prvky umožňují zabezpečit AKS stejným způsobem, jakým zabezpečete přístup k vašim předplatným Azure.

Server rozhraní API Kubernetes poskytuje jeden bod připojení pro požadavky k provádění akcí v rámci clusteru. Chcete-li zabezpečit a auditovat přístup k serveru rozhraní API, omezte přístup a poskytněte požadovaná nejméně privilegovaná přístupová oprávnění. Tento přístup není jedinečný pro Kubernetes, ale je obzvláště důležité, když cluster AKS je logicky izolované pro použití více klienty.

Azure Active Directory (AD) poskytuje řešení pro správu identit připravené pro podniky, které se integruje s clustery AKS. Vzhledem k tomu, že Kubernetes neposkytuje řešení správy identit, může být jinak obtížné poskytnout podrobný způsob, jak omezit přístup k serveru rozhraní API. S clustery integrované s Azure AD v AKS, můžete použít stávající uživatelské a skupinové účty k ověření uživatelů na serveru rozhraní API.

![Integrace Azure Active Directory pro clustery AKS](media/operator-best-practices-cluster-security/aad-integration.png)

K zabezpečení serveru API pomocí Kubernetes RBAC a integrace Azure AD zajistěte server rozhraní API a zadejte nejmenší počet oprávnění požadovaných pro sadu prostředků s vymezeným oborem, jako je například jeden obor názvů. Různým uživatelům nebo skupinám ve službě Azure AD mohou být uděleny různé role RBAC. Tato podrobná oprávnění umožňují omezit přístup k serveru rozhraní API a poskytují jasnou stopu auditu provedených akcí.

Doporučeným osvědčeným postupem je použití skupin k poskytování přístupu k souborům a složkám versus jednotlivým identitám, použití členství *ve skupinách* Azure AD k navázání uživatelů na role RBAC spíše než na jednotlivé *uživatele*. Při změně členství ve skupině uživatele by se odpovídajícím způsobem změnila jejich přístupová oprávnění v clusteru AKS. Pokud svážete uživatele přímo s rolí, může se jeho pracovní funkce změnit. Členství ve skupinách Azure AD by se aktualizovalo, ale oprávnění v clusteru AKS by to neodrážela. V tomto scénáři uživatel i nadále udělena více oprávnění, než uživatel vyžaduje.

Další informace o integraci Azure AD a RBAC, najdete [v tématu Doporučené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Zabezpečený přístup kontejneru k prostředkům

**Pokyny pro osvědčené postupy** – omezte přístup k akcím, které mohou kontejnery provádět. Zadejte nejmenší počet oprávnění a vyhněte se použití kořenové / privilegované eskalace.

Stejným způsobem, že byste měli udělit uživatelům nebo skupiny nejmenší počet požadovaných oprávnění, kontejnery by měly být také omezeny pouze na akce a procesy, které potřebují. Chcete-li minimalizovat riziko útoku, nekonfigurujte aplikace a kontejnery, které vyžadují eskalovaná oprávnění nebo root přístup. Například nastavit `allowPrivilegeEscalation: false` v pod manifestu. Tyto *kontexty zabezpečení podu* jsou integrovány do Kubernetes a umožňují definovat další oprávnění, jako je například uživatel nebo skupina spustit jako nebo jaké linuxové schopnosti vystavit. Další doporučené postupy naleznete v [tématu Zabezpečený pod přístup k prostředkům][pod-security-contexts].

Pro podrobnější kontrolu akcí kontejneru můžete také použít integrované funkce zabezpečení Linuxu, jako je *AppArmor* a *seccomp*. Tyto funkce jsou definovány na úrovni uzlu a pak implementovány prostřednictvím manifestu pod. Vestavěné funkce zabezpečení Linuxu jsou k dispozici pouze na linuxových uzlech a podech.

> [!NOTE]
> Prostředí Kubernetes v AKS nebo jinde nejsou zcela bezpečná pro nepřátelské použití s více tenanty. Další funkce zabezpečení, jako je *Například AppArmor*, *seccomp*, *Pod zásady zabezpečení*nebo více jemně odstupňované ovládací prvky přístupu na základě rolí (RBAC) pro uzly ztížit zneužití. Pro skutečné zabezpečení při spuštění nepřátelských úloh s více klienty je však hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se stane celým clusterem, nikoli jednotlivým uzlem. Pro tyto typy nepřátelských víceklientských úloh byste měli použít fyzicky izolované clustery.

### <a name="app-armor"></a>Brnění aplikace

Chcete-li omezit akce, které kontejnery mohou provádět, můžete použít modul zabezpečení jádra [AppArmor][k8s-apparmor] Linux. AppArmor je k dispozici jako součást základního operačního systému uzlu AKS a je ve výchozím nastavení povolen. Můžete vytvořit Profily AppArmor, které omezují akce, jako je čtení, zápis nebo spuštění nebo systémové funkce, jako je například připojení souborových systémů. Výchozí profily AppArmor `/proc` omezují `/sys` přístup k různým a umístěním a poskytují prostředky k logickému izolátu kontejnerů od základního uzlu. AppArmor funguje pro všechny aplikace, které běží na Linuxu, a to nejen Kubernetes lusky.

![Profily AppArmor, které se používají v clusteru AKS k omezení akcí kontejnerů](media/operator-best-practices-container-security/apparmor.png)

Chcete-li zobrazit AppArmor v akci, následující příklad vytvoří profil, který brání zápisu do souborů. [SSH][aks-ssh] do uzlu AKS, potom vytvořte soubor s názvem *deny-write.profile* a vložte následující obsah:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Profily AppArmor jsou `apparmor_parser` přidány pomocí příkazu. Přidejte profil do AppArmor a zadejte název profilu vytvořeného v předchozím kroku:

```console
sudo apparmor_parser deny-write.profile
```

Neexistuje žádný výstup vrácena, pokud je profil správně analyzována a použita apparmor. Jste vráceni do příkazového řádku.

Z místního počítače nyní vytvořte manifest pod s názvem *aks-apparmor.yaml* a vložte následující obsah. Tento manifest definuje poznámku `container.apparmor.security.beta.kubernetes` pro přidání odkazů profilu *odepření a zápisu* vytvořeného v předchozích krocích:

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

Nasazení ukázkového podu pomocí příkazu [kubectl apply:][kubectl-apply]

```console
kubectl apply -f aks-apparmor.yaml
```

S nasazeným modulem zapisujte příkaz [kubectl exec][kubectl-exec] k zápisu do souboru. Příkaz nelze provést, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Další informace o AppArmor, viz [AppArmor profily v Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Bezpečná výpočetní technika

Zatímco AppArmor funguje pro jakoukoli aplikaci Linux, [seccomp *(sec*ure *comp*uting)][seccomp] funguje na úrovni procesu. Seccomp je také modul zabezpečení jádra Linuxu a je nativně podporován modulem Docker runtime používaným uzly AKS. S seccomp volání procesu, který kontejnery lze provádět jsou omezené. Vytvoříte filtry, které definují, jaké akce povolit nebo odepřít a pak pomocí poznámky v rámci pod YAML manifest udrhnutí s filtrem seccomp. To je zarovnán s osvědčeným postupem pouze udělení kontejneru minimální oprávnění, která jsou potřeba ke spuštění a žádné další.

Chcete-li zobrazit seccomp v akci, vytvořte filtr, který zabrání změně oprávnění k souboru. [SSH][aks-ssh] do uzlu AKS, vytvořte filtr seccomp s názvem */var/lib/kubelet/seccomp/prevent-chmod* a vložte následující obsah:

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

Z místního počítače nyní vytvořte manifest pod s názvem *aks-seccomp.yaml* a vložte následující obsah. Tento manifest definuje poznámku `seccomp.security.alpha.kubernetes.io` a odkazuje na filtr *prevent-chmod* vytvořený v předchozím kroku:

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

Nasazení ukázkového podu pomocí příkazu [kubectl apply:][kubectl-apply]

```console
kubectl apply -f ./aks-seccomp.yaml
```

Zobrazení stavu podů pomocí příkazu [kubectl get pods.][kubectl-get] Pod hlásí chybu. Příkaz `chmod` není spuštěn filtrem seccomp, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Další informace o dostupných filtrech naleznete v [tématu Seccomp security profiles for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Pravidelná aktualizace na nejnovější verzi Kubernetes

**Pokyny pro osvědčené postupy** – Chcete-li mít aktuální informace o nových funkcích a opravách chyb, pravidelně upgradujte na verzi Kubernetes v clusteru AKS.

Kubernetes vydává nové funkce rychlejším tempem než tradiční platformy infrastruktury. Aktualizace Kubernetes zahrnují nové funkce a opravy chyb nebo zabezpečení. Nové funkce se obvykle pohybují přes *alfa* a pak *beta* stav před tím, než se stanou *stabilní* a jsou obecně dostupné a doporučené pro produkční použití. Tento cyklus vydání by vám měl umožnit aktualizovat Kubernetes, aniž byste pravidelně setkávali s nejnovějšími změnami nebo upravovali nasazení a šablony.

AKS podporuje čtyři dílčí verze Kubernetes. To znamená, že když je zavedena nová dílčí verze opravy, nejstarší verze dílčí verze a verze opravy jsou podporovány. Drobné aktualizace Kubernetes se dějí v pravidelných intervalech. Ujistěte se, že máte proces zásad správného řízení ke kontrole a upgradu podle potřeby, takže nemusíte vypadnout z podpory. Další informace naleznete [v tématu Supported Kubernetes versions AKS][aks-supported-versions]

Chcete-li zkontrolovat verze, které jsou k dispozici pro váš cluster, použijte příkaz [az aks get-upgrade,][az-aks-get-upgrades] jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Potom můžete upgradovat cluster AKS pomocí příkazu [az aks upgrade.][az-aks-upgrade] Proces upgradu bezpečně kordony a vyprázdní jeden uzel najednou, naplánuje pody na zbývající uzly a pak nasadí nový uzel s nejnovější verze operačního systému a Kubernetes.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Další informace o inovacích v AKS naleznete v [tématu Podporované verze Kubernetes v AKS][aks-supported-versions] a [Upgrade clusteru AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Zpracování aktualizací uzlů Linuxu a restartování pomocí kured

**Pokyny pro osvědčené postupy** – AKS automaticky stáhne a nainstaluje opravy zabezpečení na každé uzly Linuxu, ale v případě potřeby se automaticky nerestartuje. Slouží `kured` k sledování čekajících restartování, pak bezpečně kordon a vyprázdnění uzlu, aby uzel restartovat, použít aktualizace a být co nejbezpečnější s ohledem na OS. Uzly Windows Server (aktuálně ve verzi Preview v AKS) pravidelně provádějte operaci upgradu AKS, abyste bezpečně uzavřeli a vypouštěli pody a nasazovali aktualizované uzly.

Každý večer linuxové uzly v AKS získají bezpečnostní záplaty dostupné prostřednictvím kanálu distribuce. Toto chování je konfigurováno automaticky, protože uzly jsou nasazeny v clusteru AKS. Chcete-li minimalizovat narušení a potenciální dopad na spuštěné úlohy, uzly nejsou automaticky restartovány, pokud to vyžaduje oprava zabezpečení nebo aktualizace jádra.

Open-source [kured (KUbernetes REboot Daemon)][kured] projekt Weaveworks hodinky pro čekající uzel restartuje. Když uzel Linux uplatní aktualizace, které vyžadují restartování, uzel je bezpečně uzavřen a vyčerpaný přesunout a naplánovat pody na jiných uzlech v clusteru. Po restartování uzlu je přidán zpět do clusteru a Kubernetes pokračuje v plánování podů na něm. Chcete-li minimalizovat narušení, pouze jeden uzel v době, kdy je povoleno restartovat `kured`.

![Proces restartování uzlu AKS pomocí kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Pokud chcete jemnější kontrolu zrnitosti při `kured` restartování, můžete integrovat s Prometheus zabránit restartování, pokud existují jiné události údržby nebo clusteru problémy probíhají. Tato integrace minimalizuje další komplikace restartováním uzlů při aktivním řešení dalších problémů.

Další informace o tom, jak zpracovat restartování uzlů, naleznete [v tématu Použití aktualizací zabezpečení a jádra u uzlů v AKS][aks-kured].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřil na zabezpečení clusteru AKS. Chcete-li implementovat některé z těchto oblastí, naleznete v následujících článcích:

* [Integrace služby Azure Active Directory s AKS][aks-aad]
* [Upgrade clusteru AKS na nejnovější verzi Kubernetes][aks-upgrade]
* [Zpracovat aktualizace zabezpečení a restartovat uzel pomocí funkce kured][aks-kured]

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
