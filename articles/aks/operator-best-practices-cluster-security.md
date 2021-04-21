---
title: Osvědčené postupy pro zabezpečení clusteru
titleSuffix: Azure Kubernetes Service
description: Seznamte se s osvědčenými postupy pro postup správy zabezpečení a upgradů clusteru ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 04/07/2021
ms.openlocfilehash: 5cb103d843aafbb7f72c03d65b45fe3a84f8d1cd
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107782976"
---
# <a name="best-practices-for-cluster-security-and-upgrades-in-azure-kubernetes-service-aks"></a>Osvědčené postupy pro zabezpečení a upgrady clusterů ve službě Azure Kubernetes Service (AKS)

Při správě clusterů ve službě Azure Kubernetes Service (AKS) je klíčovým aspektem důležité zabezpečení úloh a dat. Když spouštíte víceklientské clustery s využitím logické izolace, obzvláště potřebujete zabezpečit přístup k prostředkům a úlohám. K minimalizaci rizika útoku použijte nejnovější aktualizace zabezpečení operačního systému Kubernetes a Node.

Tento článek se zaměřuje na zabezpečení clusteru AKS. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * K zabezpečení přístupu k serveru rozhraní API používejte Azure Active Directory a Kubernetes řízení přístupu na základě role (Kubernetes RBAC).
> * Zabezpečte přístup kontejneru k prostředkům uzlu.
> * Upgradujte cluster AKS na nejnovější verzi Kubernetes.
> * Udržování uzlů v aktuálním stavu a automatické použití oprav zabezpečení

Můžete si také přečíst osvědčené postupy pro [správu imagí kontejnerů][best-practices-container-image-management] a pro [zabezpečení pod][best-practices-pod-security].

[Integraci služeb Azure Kubernetes Services s Security Center][security-center-aks] můžete také využít k detekci hrozeb a zobrazení doporučení pro zabezpečení clusterů AKS.

## <a name="secure-access-to-the-api-server-and-cluster-nodes"></a>Zabezpečený přístup k serveru rozhraní API a uzlům clusteru

> **Osvědčené postupy** 
>
> Jedním z nejdůležitějších způsobů zabezpečení clusteru je zabezpečení přístupu k serveru rozhraní Kubernetes API. Pokud chcete řídit přístup k serveru rozhraní API, Integrujte Kubernetes RBAC pomocí Azure Active Directory (Azure AD). Pomocí těchto ovládacích prvků můžete zabezpečit AKS stejným způsobem jako při zabezpečení přístupu k předplatným Azure.

Server Kubernetes API poskytuje jeden spojovací bod pro požadavky na provádění akcí v rámci clusteru. Pro zabezpečení a audit přístupu k serveru rozhraní API omezte přístup a poskytněte nejnižší možné úrovně oprávnění. i když tento přístup není jedinečný pro Kubernetes, je obzvláště důležité, když logicky izolujete cluster AKS pro použití s více klienty.

Azure AD poskytuje řešení pro správu identit připravené na podnik, které se integruje s AKS clustery. Vzhledem k tomu, že Kubernetes neposkytuje řešení pro správu identit, může být pro podrobné omezení přístupu k serveru rozhraní API pevně vylisovaný. S integrovanými clustery Azure AD v AKS můžete použít stávající účty uživatelů a skupin k ověřování uživatelů na serveru rozhraní API.

![Integrace Azure Active Directory pro clustery AKS](media/operator-best-practices-cluster-security/aad-integration.png)

Pomocí Kubernetes RBAC a Azure AD-Integration můžete zabezpečit Server rozhraní API a poskytnout minimální oprávnění požadovaná pro sadu prostředků s vymezeným oborem, jako je jeden obor názvů. Můžete udělit různým uživatelům Azure AD nebo skupinám různé role Kubernetes. S podrobnými oprávněními můžete omezit přístup k serveru rozhraní API a poskytnout jasný záznam pro audit provedených akcí.

Doporučeným postupem je použití *skupin* k poskytnutí přístupu k souborům a složkám namísto individuálních identit. Můžete například použít členství ve *skupině* Azure AD k navázání uživatelů na role Kubernetes, a ne na jednotlivé *uživatele*. Jako změny členství ve skupinách uživatelů se příslušná oprávnění pro přístup ke clusteru AKS mění. 

Mezitím řekněme, že svážete jednotlivé uživatele přímo s rolí a jejich pracovními funkcemi. I když se členství ve skupině Azure AD aktualizuje, jejich oprávnění v clusteru AKS by ne. V tomto scénáři se uživateli ukončí více oprávnění, než vyžaduje.

Další informace o integraci Azure AD, Kubernetes RBAC a Azure RBAC najdete v tématu [osvědčené postupy pro ověřování a autorizaci v AKS][aks-best-practices-identity].

## <a name="secure-container-access-to-resources"></a>Zabezpečený přístup k prostředkům kontejneru

> **Osvědčené postupy** 
> 
> Omezte přístup k akcím, které mohou kontejnery provádět. Zadejte nejnižší počet oprávnění a vyhněte se použití kořenového přístupu nebo privilegovaného eskalace.

Stejným způsobem, jako byste měli udělit uživatelům nebo skupinám minimální požadovaná oprávnění, byste měli také omezit kontejnery jenom na nezbytné akce a procesy. Abyste minimalizovali riziko útoku, nekonfigurujte aplikace a kontejnery, které vyžadují zvýšení oprávnění nebo root Access. 

Například nastavte `allowPrivilegeEscalation: false` v manifestu pod. Tyto integrované Kubernetes *pod zabezpečením* umožňují definovat další oprávnění, jako je například uživatel nebo skupina, které se mají spustit, nebo možnosti Linux, které se mají zveřejnit. Další doporučené postupy najdete v tématu [zabezpečení pod přístupem k prostředkům][pod-security-contexts].

Pro ještě podrobnější kontrolu nad akcemi kontejneru můžete použít taky integrované funkce zabezpečení pro Linux, jako je *AppArmor* a *seccomp*. 
1. Definujte funkce zabezpečení pro Linux na úrovni uzlu.
1. Implementujte funkce pomocí manifestu pod. 

Integrované funkce zabezpečení pro Linux jsou dostupné jenom v uzlech a luskech systému Linux.

> [!NOTE]
> V současné době není prostředí Kubernetes zcela bezpečná pro nepřátelský využití více tenantů. Další funkce zabezpečení, jako jsou *AppArmor*, *seccomp*,*pod, zásady zabezpečení* nebo Kubernetes RBAC pro uzly, efektivně blokují zneužití. 
>
>Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty Důvěřujte pouze hypervisoru. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. 
>
> U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery.

### <a name="app-armor"></a>Obraně aplikace

Chcete-li omezit akce kontejneru, můžete použít modul zabezpečení jádra systému [AppArmor][k8s-apparmor] Linux. AppArmor je k dispozici jako součást základního operačního systému AKS node a je ve výchozím nastavení povolená. Vytvoříte profily AppArmor, které omezují operace čtení, zápisu nebo provádění, nebo systémové funkce, jako je třeba připojování systémů souborů. Výchozí profily AppArmor omezují přístup k různým `/proc` `/sys` umístěním a poskytují prostředky pro logickou izolaci kontejnerů z podkladového uzlu. AppArmor funguje pro všechny aplikace, které běží na Linux, nejen Kubernetes lusky.

![AppArmor profily používané v clusteru AKS k omezení akcí kontejneru](media/operator-best-practices-container-security/apparmor.png)

Chcete-li zobrazit AppArmor v akci, následující příklad vytvoří profil, který zabrání v zápisu do souborů. 
1. [SSH][aks-ssh] pro uzel AKS.
1. Vytvořte soubor s názvem *Deny-Write. Profile*.
1. Vložte následující obsah:

    ```
    #include <tunables/global>
    profile k8s-apparmor-example-deny-write flags=(attach_disconnected) {
      #include <abstractions/base>
  
      file,
      # Deny all file writes.
      deny /** w,
    }
    ```

Profily AppArmor se přidávají pomocí `apparmor_parser` příkazu. 
1. Přidejte profil do AppArmor.
1. Zadejte název profilu vytvořeného v předchozím kroku:

    ```console
    sudo apparmor_parser deny-write.profile
    ```

    Pokud se profil správně analyzuje a použije na AppArmor, neuvidíte žádný výstup a vrátíte se do příkazového řádku.

1. Z místního počítače vytvořte manifest pod názvem *AKS-AppArmor. yaml*. Tento manifest:
    * Definuje anotaci pro `container.apparmor.security.beta.kubernetes` .
    * Odkazuje na profil pro *odepření a zápis* vytvořený v předchozích krocích.

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
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command: [ "sh", "-c", "echo 'Hello AppArmor!' && sleep 1h" ]
    ```

1. S nasazeným pod, použijte příkaz ověřit, zda se zobrazí text *Hello-AppArmor* pod jako *blokované*:

    ```
    $ kubectl get pods

    NAME             READY   STATUS    RESTARTS   AGE
    aks-ssh          1/1     Running   0          4m2s
    hello-apparmor   0/1     Blocked   0          50s
    ```

Další informace o AppArmor najdete v tématu [profily AppArmor v Kubernetes][k8s-apparmor].

### <a name="secure-computing"></a>Zabezpečené výpočetní prostředí

I když AppArmor funguje pro libovolnou aplikaci pro Linux, na úrovni procesu funguje [seccomp (*sec* urovat *comp* uting)][seccomp] . Seccomp je také modul zabezpečení jádra pro Linux a nativně se podporuje za běhu Docker, který používají uzly AKS. Pomocí seccomp můžete omezit volání procesu kontejneru. V souladu s osvědčenými postupy pro udělení minimálního oprávnění kontejneru pouze pro běh:
* Definování s filtry, které akce povolují nebo zakazují.
* Přidávání poznámek v rámci manifestu pod YAML, aby se spojil s filtrem seccomp. 

Chcete-li zobrazit seccomp v akci, vytvořte filtr, který zabrání změně oprávnění k souboru. 
1. [SSH][aks-ssh] pro uzel AKS.
1. Vytvořte filtr seccomp s názvem */var/lib/kubelet/seccomp/Prevent-chmod*.
1. Vložte následující obsah:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "name": "chmod",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "fchmodat",
          "action": "SCMP_ACT_ERRNO"
        },
        {
          "name": "chmodat",
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

    Ve verzi 1,19 a novější je nutné nakonfigurovat následující:

    ```json
    {
      "defaultAction": "SCMP_ACT_ALLOW",
      "syscalls": [
        {
          "names": ["chmod","fchmodat","chmodat"],
          "action": "SCMP_ACT_ERRNO"
        }
      ]
    }
    ```

1. Z místního počítače vytvořte manifest pod názvem *AKS-seccomp. yaml* a vložte následující obsah. Tento manifest:
    * Definuje anotaci pro `seccomp.security.alpha.kubernetes.io` .
    * Odkazuje na filtr *prevence – chmod* vytvořený v předchozím kroku.

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
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

    Ve verzi 1,19 a novější je nutné nakonfigurovat následující:

    ```yaml
    apiVersion: v1
    kind: Pod
    metadata:
      name: chmod-prevented
    spec:
      securityContext:
        seccompProfile:
          type: Localhost
          localhostProfile: prevent-chmod
      containers:
      - name: chmod
        image: mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11
        command:
          - "chmod"
        args:
         - "777"
         - /etc/hostname
      restartPolicy: Never
    ```

1. Nasaďte ukázku pomocí příkazu [kubectl Apply][kubectl-apply] :

    ```console
    kubectl apply -f ./aks-seccomp.yaml
    ```

1. Pomocí příkazu [kubectl Get lusks][kubectl-get] zobrazte stav pod. 
    * Pod ní nahlásí chybu. 
    * `chmod`Příkazu je znemožněno spouštěním filtru seccomp, jak je znázorněno v následujícím příkladu výstupu:    

    ```
    $ kubectl get pods

    NAME                      READY     STATUS    RESTARTS   AGE
    chmod-prevented           0/1       Error     0          7s
    ```

Další informace o dostupných filtrech najdete v tématu [Seccomp Security profiles for Docker][seccomp].

## <a name="regularly-update-to-the-latest-version-of-kubernetes"></a>Pravidelná aktualizace na nejnovější verzi Kubernetes

> **Osvědčené postupy** 
> 
> Pokud chcete mít aktuální informace o nových funkcích a opravách chyb, pravidelně upgradujte verzi Kubernetes v clusteru AKS.

Kubernetes uvolňuje nové funkce rychlejšího tempa než tradičních platforem infrastruktury. Kubernetes aktualizace zahrnují:
* Nové funkce
* Opravy chyb nebo zabezpečení 

Nové funkce obvykle přesouvají stav *alfa* a *beta* , než se stanou *stabilní*. Jakmile je stabilní, jsou všeobecně dostupné a doporučené pro použití v produkčním prostředí. Kubernetes nový cyklus vydání vydaných funkcí umožňuje aktualizovat Kubernetes bez pravidelného zaznamenání nedostatku změn nebo přizpůsobení nasazení a šablon.

AKS podporuje tři dílčí verze Kubernetes. Jakmile je zavedena nová dílčí verze opravy, jsou vyřazení nejstarší dílčí verze a vydání oprav. K vedlejším aktualizacím Kubernetes dochází v pravidelných intervalech. Abyste mohli zůstat v rámci podpory, ujistěte se, že máte proces zásad správného řízení pro kontrolu nezbytných upgradů. Další informace najdete v tématu [podporované verze KUBERNETES AKS][aks-supported-versions].

Chcete-li ověřit verze, které jsou pro cluster k dispozici, použijte příkaz [AZ AKS Get-Upgrades][az-aks-get-upgrades] , jak je znázorněno v následujícím příkladu:

```azurecli-interactive
az aks get-upgrades --resource-group myResourceGroup --name myAKSCluster
```

Následně můžete upgradovat cluster AKS pomocí příkazu [AZ AKS upgrade][az-aks-upgrade] . Zabezpečený proces upgradu:
* Cordons a vyprázdní jeden uzel v jednom okamžiku.
* Plánuje v luskech zbývající uzly.
* Nasadí nový uzel s nejnovějšími verzemi operačního systému a Kubernetes.

>[!IMPORTANT]
> Otestujte nové podverze ve vývojovém testovacím prostředí a ověřte, že vaše úloha zůstane v dobrém stavu s novou verzí Kubernetes. 
>
> Kubernetes může vyřadit rozhraní API (jako ve verzi 1,16), na které vaše úlohy spoléhají. Při zavedení nových verzí do produkčního prostředí zvažte použití [několika fondů uzlů v samostatných verzích](use-multiple-node-pools.md) a jednotlivé fondy upgradujte postupně, aby se aktualizace provedla v clusteru. Pokud spouštíte více clusterů, upgradujte v jednom okamžiku jeden cluster na postupně monitorující dopad nebo změny.
>
>```azurecli-interactive
>az aks upgrade --resource-group myResourceGroup --name myAKSCluster --kubernetes-version KUBERNETES_VERSION
>```

Další informace o upgradech v AKS najdete v tématu [podporované verze Kubernetes v AKS][aks-supported-versions] a [upgrade clusteru AKS][aks-upgrade].

## <a name="process-linux-node-updates-and-reboots-using-kured"></a>Zpracování aktualizací a restartování uzlu Linux pomocí kured

> **Osvědčené postupy** 
> 
> I když AKS automaticky stáhne a nainstaluje opravy zabezpečení na každý uzel Linux, automaticky se nerestartuje. 
> 1. Použijte `kured` ke sledování čeká na restartování.
> 1. Bezpečně Cordon a vyprázdněte uzel, aby bylo možné uzel restartovat.
> 1. Použijte aktualizace.
> 1. Co nejlépe Zabezpečte s ohledem na operační systém. 

Pro uzly Windows serveru pravidelně provádějte operaci upgradu AKS a bezpečně Cordon a vyprázdněte a nasaďte aktualizované uzly.

Každý večer, uzly Linux v AKS získá opravy zabezpečení prostřednictvím kanálu aktualizace distribuce. Toto chování se automaticky nakonfiguruje, protože uzly se nasazují v clusteru AKS. Aby se minimalizovalo přerušení a potenciální dopad na spuštěné úlohy, uzly se automaticky restartují, pokud je vyžaduje Oprava zabezpečení nebo aktualizace jádra.

Projekt Open source [kured (KUbernetes restart Daemon)][kured] tím, že Weaveworks sleduje čekání na restartování uzlu. Pokud uzel Linux používá aktualizace, které vyžadují restartování, uzel se bezpečně uzavřené a vyprázdní, aby se přesunuly a naplánovaly lusky na jiných uzlech v clusteru. Jakmile se uzel restartuje, přidá se zpátky do clusteru a Kubernetes pokračuje pod plánováním. Aby se minimalizovalo přerušení, může restartovat pouze jeden uzel v čase `kured` .

![Proces restartování uzlu AKS pomocí kured](media/operator-best-practices-cluster-security/node-reboot-process.png)

Pokud potřebujete ještě větší kontrolu nad restartováním, můžete je `kured` integrovat s Prometheus a zabránit tak restartování, pokud dojde k jiným událostem údržby nebo problémům s clusterem. Tato integrace snižuje komplikace tím, že je restartuje uzly, když aktivně řešíte jiné problémy.

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
[az-aks-get-upgrades]: /cli/azure/aks#az_aks_get_upgrades
[az-aks-upgrade]: /cli/azure/aks#az_aks_upgrade
[aks-supported-versions]: supported-kubernetes-versions.md
[aks-upgrade]: upgrade-cluster.md
[aks-best-practices-identity]: concepts-identity.md
[aks-kured]: node-updates-kured.md
[aks-aad]: ./azure-ad-integration-cli.md
[best-practices-container-image-management]: operator-best-practices-container-image-management.md
[best-practices-pod-security]: developer-best-practices-pod-security.md
[pod-security-contexts]: developer-best-practices-pod-security.md#secure-pod-access-to-resources
[aks-ssh]: ssh.md
[security-center-aks]: ../security-center/defender-for-kubernetes-introduction.md
