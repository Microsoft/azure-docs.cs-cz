---
title: Osvědčené postupy pro zabezpečení clusteru
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro postup správy zabezpečení a upgradů clusteru ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 12/06/2018
ms.openlocfilehash: 5f249a7e6e7fac13301f0d2717336651b171b422
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91776302"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení a upgrady clusterů ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) je důležité zvážit zabezpečení vašich úloh a dat. Zvláště když spouštíte víceklientské clustery s využitím logické izolace, musíte zabezpečit přístup k prostředkům a úlohám. Abyste minimalizovali riziko útoku, musíte také zajistit, abyste použili nejnovější aktualizace zabezpečení operačního systému Kubernetes a Node.

Tento článek se zaměřuje na zabezpečení clusteru AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Použití Azure Active Directory a řízení přístupu na základě role (RBAC) k zabezpečení přístupu k serveru rozhraní API
> * Zabezpečený přístup k prostředkům uzlů v kontejneru
> * Upgrade clusteru AKS na nejnovější verzi Kubernetes
> * Udržování uzlů v aktuálním stavu a automatické použití oprav zabezpečení

Můžete si také přečíst osvědčené postupy pro [správu imagí kontejnerů][best-practices-container-image-management] a pro [zabezpečení pod][best-practices-pod-security].

[Integraci služeb Azure Kubernetes Services s Security Center][security-center-aks] můžete také využít k detekci hrozeb a zobrazení doporučení pro zabezpečení clusterů AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpečený přístup k serveru rozhraní API a uzlům clusteru

**Pokyny k osvědčeným postupům** – zabezpečení přístupu k rozhraní Kubernetes API – Server je jedním z nejdůležitějších věcí, které můžete provést pro zabezpečení clusteru. Integrujte Kubernetes řízení přístupu na základě role (RBAC) s Azure Active Directory k řízení přístupu k serveru rozhraní API. Tyto ovládací prvky vám umožní zabezpečit AKS stejným způsobem jako při zabezpečení přístupu k předplatným Azure.

Server Kubernetes API poskytuje jeden spojovací bod pro požadavky na provádění akcí v rámci clusteru. Pokud chcete zabezpečit a auditovat přístup k serveru rozhraní API, omezte přístup a poskytněte minimální požadovaná oprávnění přístupu. Tento přístup není jedinečný pro Kubernetes, ale je obzvláště důležitý, když je cluster AKS logicky izolovaný pro víceklientské použití.

Azure Active Directory (AD) poskytuje řešení pro správu identit připravené na podnik, které se integruje s clustery AKS. Vzhledem k tomu, že Kubernetes neposkytuje řešení pro správu identit, může jinak být obtížné poskytnout podrobný způsob, jak omezit přístup k serveru rozhraní API. S integrovanými clustery Azure AD v AKS můžete použít stávající účty uživatelů a skupin k ověřování uživatelů na serveru rozhraní API.

![Integrace Azure Active Directory pro clustery AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Použijte Kubernetes RBAC a Azure AD – Integration Server rozhraní API a poskytněte minimální počet oprávnění vyžadovaných pro množinu prostředků, jako je například jeden obor názvů. Různým uživatelům nebo skupinám ve službě Azure AD se dají udělit různé role RBAC. Tato podrobná oprávnění umožňují omezit přístup k serveru rozhraní API a poskytnout jasný záznam pro audit provedených akcí.

Doporučený postup je použít skupiny k poskytnutí přístupu k souborům a složkám proti jednotlivým identitám. členství ve *skupině* Azure AD můžete použít ke svázání uživatelů s rolemi RBAC, nikoli jednotlivými *uživateli*. Jako změny členství ve skupinách uživatelů se jejich přístupová oprávnění v clusteru AKS mění odpovídajícím způsobem. Pokud uživatele svážete přímo s rolí, může se změnit jeho funkce úlohy. Členství ve skupině Azure AD by se aktualizovalo, ale oprávnění v clusteru AKS to nereflektují. V tomto scénáři se uživateli ukončí udělování dalších oprávnění, než kolik jich uživatel vyžaduje.

Další informace o integraci a RBAC služby Azure AD najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Zabezpečený přístup k prostředkům kontejneru

**Doprovodné materiály k osvědčeným postupům** – omezení přístupu k akcím, které mohou kontejnery provádět. Zadejte nejnižší počet oprávnění a vyhněte se použití kořenového/privilegovaného eskalace.

Stejným způsobem, jako byste měli udělit uživatelům nebo skupinám nejmenší počet požadovaných oprávnění, by měly být kontejnery také omezené jenom na akce a procesy, které potřebují. Chcete-li minimalizovat riziko útoku, nekonfigurujte aplikace a kontejnery, které vyžadují zvýšení oprávnění nebo root Access. Například nastavte `allowPrivilegeEscalation: false` v manifestu pod. Tyto položky *zabezpečení* jsou integrovány do Kubernetes a umožňují definovat další oprávnění, jako je například uživatel nebo skupina, které se mají spustit jako nebo jaké možnosti systému Linux mají být vystavení. Další doporučené postupy najdete v tématu [zabezpečení pod přístupem k prostředkům][pod-security-contexts].

Pro podrobnější kontrolu nad akcemi kontejneru můžete použít také integrované funkce zabezpečení pro Linux, jako je *AppArmor* a *seccomp*. Tyto funkce jsou definovány na úrovni uzlu a následně implementovány prostřednictvím manifestu pod. Integrované funkce zabezpečení pro Linux jsou dostupné jenom v uzlech a luskech systému Linux.

> [!NOTE]
> Prostředí Kubernetes, v AKS nebo jinde, nejsou zcela bezpečná pro nepřátelský využití více tenantů. Další funkce zabezpečení, jako jsou *AppArmor*, *seccomp*, *pod, zásady zabezpečení*nebo podrobnější řízení přístupu na základě role (RBAC) pro uzly, jsou zneužití obtížnější. Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty však je hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery.

### <a name="app-armor"></a>Obraně aplikace

K omezení akcí, které mohou kontejnery provádět, můžete použít modul zabezpečení jádra systému [AppArmor][k8s-apparmor] Linux. AppArmor je k dispozici jako součást základního operačního systému AKS node a je ve výchozím nastavení povolená. Vytvoříte profily AppArmor, které omezují akce, jako jsou čtení, zápis nebo spouštění nebo systémové funkce, jako jsou třeba montážní systémy. Výchozí profily AppArmor omezují přístup k různým `/proc` `/sys` umístěním a poskytují prostředky pro logickou izolaci kontejnerů z podkladového uzlu. AppArmor funguje pro všechny aplikace, které běží na Linux, nejen Kubernetes lusky.

![AppArmor profily používané v clusteru AKS k omezení akcí kontejneru](media/operator-best-practices-container-security/apparmor.png)

Chcete-li zobrazit AppArmor v akci, následující příklad vytvoří profil, který zabrání v zápisu do souborů. [SSH][aks-ssh] na uzel AKS a pak vytvořte soubor s názvem *Deny-Write. Profile* a vložte následující obsah:

```
#include <tunables/global>
profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
  #include <abstractions/base>
  
  file,
  # Deny all file writes.
  deny /** w,
}
```

Profily AppArmor se přidávají pomocí `apparmor_parser` příkazu. Přidejte profil do AppArmor a zadejte název profilu, který jste vytvořili v předchozím kroku:

```console
sudo apparmor_parser deny-write.profile
```

Pokud se profil správně analyzuje a použije na AppArmor, nevrátí se žádný výstup. Budete vráceni do příkazového řádku.

Z místního počítače teď vytvořte manifest pod názvem *AKS-AppArmor. yaml* a vložte následující obsah. Tento manifest definuje anotaci pro `container.apparmor.security.beta.kubernetes` Přidání odkazů na profil pro *odepření a zápis* vytvořený v předchozích krocích:

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

Po nasazení pod nasazeným příkazem použijte příkaz [kubectl exec][kubectl-exec] k zápisu do souboru. Příkaz nelze provést, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl exec hello-apparmor touch /tmp/test

touch: /tmp/test: Permission denied
command terminated with exit code 1
```

Další informace o AppArmor najdete v tématu [profily AppArmor v Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Zabezpečené výpočetní prostředí

I když AppArmor funguje pro libovolnou aplikaci pro Linux, na úrovni procesu funguje [seccomp (*sec*urovat *comp*uting)][seccomp] . Seccomp je také modul zabezpečení jádra pro Linux a nativně se podporuje za běhu Docker, který používají uzly AKS. V seccomp proces volá tyto kontejnery, které mohou být provedeny. Vytvoříte filtry, které definují akce, které se mají povolit nebo zakázat, a pak pomocí poznámek v rámci manifestu pod YAML přiřadíte k filtru seccomp. To je v souladu s osvědčeným postupem pouze pro udělení minimálních oprávnění, která jsou potřeba ke spuštění, a žádné další.

Chcete-li zobrazit seccomp v akci, vytvořte filtr, který zabrání změně oprávnění k souboru. [SSH][aks-ssh] na uzel AKS a pak vytvořte filtr seccomp s názvem */var/lib/kubelet/seccomp/Prevent-chmod* a vložte následující obsah:

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

Stav lusků zobrazíte pomocí příkazu [kubectl získat lusky][kubectl-get] . Pod ní nahlásí chybu. `chmod`Příkazu je znemožněno spouštěním filtru seccomp, jak je znázorněno v následujícím příkladu výstupu:

```
$ kubectl get pods

NAME                      READY     STATUS    RESTARTS   AGE
chmod-prevented           0/1       Error     0          7s
```

Další informace o dostupných filtrech najdete v tématu [Seccomp Security profiles for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Pravidelná aktualizace na nejnovější verzi Kubernetes

**Doprovodné materiály k osvědčeným postupům** – aktuální informace o nových funkcích a opravách chyb pravidelně aktualizují verzi Kubernetes v clusteru AKS.

Kubernetes uvolňuje nové funkce rychlejšího tempa než tradičních platforem infrastruktury. Aktualizace Kubernetes zahrnují nové funkce a opravy chyb nebo zabezpečení. Nové funkce obvykle přecházejí přes *alfa* a pak na stav *beta verze* , než se stanou *stabilní* a jsou všeobecně dostupné a doporučené pro použití v produkčním prostředí. Tento cyklus vydaných verzí by vám měl umožňovat aktualizovat Kubernetes bez pravidelného zaznamenání nejnovějších změn nebo přizpůsobení nasazení a šablon.

AKS podporuje tři dílčí verze Kubernetes. To znamená, že při zavedení nové dílčí verze opravy jsou vyřazení nejstarší podverze a verze patch. Drobné aktualizace k Kubernetes dochází v pravidelných intervalech. Ujistěte se, že máte proces zásad správného řízení pro kontrolu a upgrade podle potřeby, abyste nemuseli zaklesnout na podporu. Další informace najdete v tématu [podporované verze KUBERNETES AKS][aks-supported-versions].

Chcete-li ověřit verze, které jsou pro cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Následně můžete upgradovat cluster AKS pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Proces upgradu bezpečně cordons a vyprázdní jeden uzel v čase, plánuje na zbývajících uzlech a pak nasadí nový uzel s nejnovějšími verzemi operačního systému a Kubernetes.

Důrazně doporučujeme otestovat nové podverze ve vývojovém testovacím prostředí, abyste mohli ověřit, že vaše úlohy pokračuje v dobrém provozu s novou verzí Kubernetes. Kubernetes může vyřadit rozhraní API, například ve verzi 1,16, na které se můžou spoléhat vaše úlohy. Při zavedení nových verzí do produkčního prostředí zvažte použití [několika fondů uzlů v samostatných verzích](use-multiple-node-pools.md) a jednotlivé fondy upgradujte postupně, aby se aktualizace provedla v clusteru. Pokud spouštíte více clusterů, upgradujte v jednom okamžiku jeden cluster na postupně monitorující dopad nebo změny.

```azurecli-interactive
az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
```

Další informace o upgradech v AKS najdete v tématu [podporované verze Kubernetes v AKS][aks-supported-versions] a [upgrade clusteru AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Zpracování aktualizací a restartování uzlu Linux pomocí kured

**Doprovodné materiály k osvědčeným postupům** – AKS automaticky stáhne a nainstaluje opravy zabezpečení na jednotlivé uzly Linux, ale v případě potřeby se nerestartuje automaticky. Použijte `kured` ke sledování čeká na restartování, pak bezpečně Cordon a vyprázdněte uzel, aby bylo možné uzel restartovat, použít aktualizace a co nejbezpečněji pro operační systém. Pro uzly Windows serveru pravidelně provádějte operaci upgradu AKS a bezpečně Cordon a vyprázdněte a nasaďte aktualizované uzly.

Každý večer, uzly Linux v AKS získá opravy zabezpečení dostupné prostřednictvím kanálu aktualizace distribuce. Toto chování se konfiguruje automaticky při nasazení uzlů v clusteru AKS. Aby se minimalizovalo přerušení a potenciální dopad na spuštěné úlohy, uzly se automaticky restartují, pokud je vyžaduje Oprava zabezpečení nebo aktualizace jádra.

Projekt Open source [kured (KUbernetes restart Daemon)][kured] tím, že Weaveworks sleduje čekání na restartování uzlu. Pokud uzel Linux používá aktualizace, které vyžadují restartování, uzel se bezpečně uzavřené a vyprázdní, aby se přesunuly a naplánovaly lusky na jiných uzlech v clusteru. Jakmile se uzel restartuje, přidá se zpátky do clusteru a Kubernetes obnoví v něm v části plánování lusky. Aby se minimalizovalo přerušení, může restartovat pouze jeden uzel v čase `kured` .

![Proces restartování uzlu AKS pomocí kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Pokud chcete, aby se při restartování prováděla jemnější kontrola, je `kured` možné ji integrovat s Prometheus a zabránit tak restartování, pokud dojde k jiným událostem údržby nebo problémům s clusterem. Tato integrace minimalizuje další komplikace tím, že je restartuje uzly, když aktivně řešíte jiné problémy.

Další informace o tom, jak zpracovat restartování uzlu, najdete v tématu [použití aktualizací zabezpečení a jádra na uzlech v AKS][aks-kured].

## <a name="next-steps"></a>Další kroky

Tento článek se zaměřuje na zabezpečení clusteru AKS. Chcete-li implementovat některé z těchto oblastí, přečtěte si následující články:

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
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/azure-kubernetes-service-integration.md
