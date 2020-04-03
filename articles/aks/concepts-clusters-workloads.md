---
title: Koncepty – základy Kubernetes pro služby Azure Kubernetes Services (AKS)
description: Naučte se základní součásti clusteru a pracovního vytížení Kubernetes a jejich vztah k funkcím ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: 019c886aba1c8fe34211e73e4d960b14e79303b9
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80617443"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Základní koncepty Kubernetes pro službu Azure Kubernetes Service (AKS)

Vývoj aplikací se pohybuje směrem k přístupu založenému na kontejnerech, je důležité, aby bylo důležité organizovat a spravovat prostředky. Kubernetes je přední platforma, která poskytuje možnost poskytovat spolehlivé plánování úloh aplikací odolných proti chybám. Azure Kubernetes Service (AKS) je spravovaná nabídka Kubernetes, která dále zjednodušuje nasazení a správu aplikací založených na kontejnerech.

Tento článek představuje základní součásti infrastruktury Kubernetes, jako je *například řídicí rovina*, *uzly*a *fondy uzlů*. Jsou také zavedeny prostředky pracovního vytížení, jako *jsou pody*, *nasazení*a *sady,* spolu s postupem seskupit prostředky do *oborů názvů*.

## <a name="what-is-kubernetes"></a>Co je Kubernetes?

Kubernetes je rychle se vyvíjející platforma, která spravuje aplikace založené na kontejnerech a jejich přidružené síťové a úložné komponenty. Důraz je kladen na úlohy aplikace, nikoli základní součásti infrastruktury. Kubernetes poskytuje deklarativní přístup k nasazením, podporovaný robustní sadu api pro operace správy.

Můžete vytvářet a spouštět moderní, přenosné aplikace založené na mikroslužbách, které těží z orchestrace kubernetes a správu dostupnosti těchto součástí aplikace. Kubernetes podporuje bezstavové i stavové aplikace jako týmy postupují prostřednictvím přijetí aplikací založených na mikroslužbách.

Jako otevřená platforma vám Kubernetes umožňuje vytvářet aplikace s preferovaným programovacím jazykem, osem, knihovnami nebo sběrnicí pro zasílání zpráv. Stávající nástroje průběžné integrace a průběžného doručování (CI/CD) se mohou integrovat s Kubernetes a naplánovat a nasadit vydání.

Služba Azure Kubernetes Service (AKS) poskytuje spravovanou službu Kubernetes, která snižuje složitost nasazení a úloh správy jádra, včetně koordinace upgradů. Rovina řízení AKS je spravována platformou Azure a platíte jenom za uzly AKS, které spouštějí vaše aplikace. AKS je postaven na open source Azure Kubernetes Service Engine[(aks-engine).][aks-engine]

## <a name="kubernetes-cluster-architecture"></a>Architektura clusteru Kubernetes

Kubernetesův cluster je rozdělen do dvou složek:

- *Řídicí rovina* uzly poskytují základní služby Kubernetes a orchestraci úloh aplikací.
- *Uzly* spouštějí úlohy aplikací.

![Součásti řídicí roviny a uzlů Kubernetes](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Řídicí rovina

Když vytvoříte cluster AKS, automaticky se vytvoří a nakonfiguruje rovina ovládacího prvku. Tato rovina ovládacího prvku je k dispozici jako spravovaný prostředek Azure abstrahovaný od uživatele. Neexistuje žádné náklady na rovinu ovládacího prvku, pouze uzly, které jsou součástí clusteru AKS.

Řídicí rovina obsahuje následující základní komponenty Kubernetes:

- *kube-apiserver* - Server API je způsob, jakým jsou základní rozhraní API Kubernetes vystavena. Tato součást poskytuje interakci pro nástroje `kubectl` pro správu, jako je například řídicí panel Kubernetes.
- *etcd* - Pro zachování stavu vašeho clusteru Kubernetes a konfigurace je vysoce dostupný *etcd* klíčovým úložištěm hodnot v rámci Kubernetes.
- *kube-scheduler* – při vytváření nebo škálování aplikací plánovač určuje, které uzly můžete spustit úlohu a spustí je.
- *kube-controller-manager* - Správce kontrolorů dohlíží na řadu menších řadičů, které provádějí akce, jako je replikace podů a zpracování operací uzlů.

AKS poskytuje jednoklientské řídicí roviny, s vyhrazeným serverem ROZHRANÍ API, plánovačem atd. Definujete počet a velikost uzlů a platforma Azure konfiguruje zabezpečenou komunikaci mezi rovinou ovládacího prvku a uzly. Interakce s řídicí rovinou probíhá prostřednictvím rozhraní API `kubectl` Kubernetes, jako je například řídicí panel Kubernetes.

Toto spravované řízení roviny znamená, že není nutné konfigurovat součásti, jako je vysoce dostupné *etcd* úložiště, ale také to znamená, že nelze získat přístup k rovině ovládacího prvku přímo. Upgrady na Kubernetes se řídí prostřednictvím rozhraní příkazového příkazu Azure nebo portálu Azure, který upgraduje rovinu ovládacího prvku a pak uzly. Chcete-li vyřešit možné problémy, můžete zkontrolovat protokoly roviny ovládacího prvku prostřednictvím protokolů Azure Monitor.

Pokud potřebujete nakonfigurovat řídicí rovinu určitým způsobem nebo potřebujete k ní mít přímý přístup, můžete nasadit vlastní cluster Kubernetes pomocí [aks-engine][aks-engine].

Doporučené postupy v přidružených tématech najdete [v tématu Doporučené postupy pro zabezpečení clusteru a upgrady v AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Fondy uzlů a uzlů

Chcete-li spouštět aplikace a podpůrné služby, potřebujete *uzel*Kubernetes . Cluster AKS má jeden nebo více uzlů, což je virtuální počítač Azure (VM), který spouští komponenty uzlů Kubernetes a runtime kontejneru:

- Je `kubelet` agent Kubernetes, který zpracovává požadavky orchestrace z řídicí roviny a plánování spuštění požadovaných kontejnerů.
- Virtuální síť je zpracována *kube-proxy* na každém uzlu. Proxy server směruje síťový provoz a spravuje adresování IP adres pro služby a pody.
- *Za běhu kontejneru* je součást, která umožňuje kontejnerizované aplikace ke spuštění a interakci s dalšími prostředky, jako je například virtuální síť a úložiště. V AKS Moby se používá jako zaběhu kontejneru.

![Virtuální počítač Azure a podpůrné prostředky pro uzel Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Velikost virtuálního počítače Azure pro vaše uzly definuje, kolik procesorů, kolik paměti a velikost a typ úložiště k dispozici (například vysoce výkonný SSD nebo běžné HDD). Pokud očekáváte potřebu aplikací, které vyžadují velké množství procesoru a paměti nebo vysoce výkonné úložiště, naplánujte odpovídajícím způsobem velikost uzlu. Můžete také horizontální navýšení kapacity počet uzlů v clusteru AKS k uspokojení poptávky.

V AKS je image virtuálního počítače pro uzly ve vašem clusteru aktuálně založená na Ubuntu Linux nebo Windows Server 2019. Když vytvoříte cluster AKS nebo navštováte počet uzlů, platforma Azure vytvoří požadovaný počet virtuálních počítačů a nakonfiguruje je. Neexistuje žádná ruční konfigurace pro vás provést. Uzly agentů se účtují jako standardní virtuální počítače, takže se automaticky použijí všechny slevy, které máte na velikost virtuálního počítače, kterou používáte (včetně [rezervací Azure).][reservation-discounts]

Pokud potřebujete použít jiný hostitelský operační systém, modul runtime kontejneru nebo zahrnout vlastní balíčky, můžete nasadit vlastní cluster Kubernetes pomocí [aks-engine][aks-engine]. Upstream `aks-engine` vydává funkce a poskytuje možnosti konfigurace dříve, než jsou oficiálně podporovány v clusterech AKS. Například pokud chcete použít za běhu kontejneru než Moby, můžete použít `aks-engine` ke konfiguraci a nasazení clusteru Kubernetes, který splňuje vaše aktuální potřeby.

### <a name="resource-reservations"></a>Rezervace zdrojů

Prostředky uzlu jsou využívány AKS, aby uzel fungoval jako součást clusteru. To může způsobit nesoulad mezi celkovými prostředky uzlu a prostředky, které lze přidělit při použití v AKS. To je důležité si uvědomit při nastavování požadavků a omezení pro uživatelem nasazené pody.

Chcete-li najít alokovatelné prostředky uzlu spustit:
```kubectl
kubectl describe node [NODE_NAME]

```

Chcete-li zachovat výkon uzlu a funkčnost, prostředky jsou vyhrazeny na každém uzlu AKS. Jako uzel zvětšuje prostředky, rezervace prostředků roste z důvodu vyšší množství uživatelů nasazených podů, které potřebují správu.

>[!NOTE]
> Pomocí doplňků AKS, jako je například Container Insights (OMS) bude spotřebovávat další prostředky uzlu.

- **CPU** - vyhrazený procesor je závislý na typu uzlu a konfiguraci clusteru, což může způsobit méně alokovatelný procesor z důvodu spuštění dalších funkcí

| Jádra procesoru na hostiteli | 1 | 2 | 4 | 8 | 16 | 32|64|
|---|---|---|---|---|---|---|---|
|Kube-vyhrazeno (milijádra)|60|100|140|180|260|420|740|

- **Paměť** - paměť využívaná AKS obsahuje součet dvou hodnot.

1. Kubelet daemon je nainstalován na všech uzlech agentka Kubernetes pro správu vytváření a ukončení kontejneru. Ve výchozím nastavení na AKS, tento daemon má následující pravidlo vystěhování: *memory.available<750Mi*, což znamená, že uzel musí mít vždy alespoň 750 Mi alokovatelné za všech okolností.  Pokud je hostitel pod touto prahovou hodnotou dostupné paměti, kubelet ukončí jeden z běžících podů, aby uvolnil paměť v hostitelském počítači a chránil ji. Jedná se o reaktivní akci, jakmile dostupná paměť klesne nad prahovou hodnotu 750Mi.

2. Druhá hodnota je regresivní rychlost rezervace paměti pro kubelet daemon správně fungovat (kube-reserved).
    - 25 % z prvních 4 GB paměti
    - 20 % z následujících 4 GB paměti (až 8 GB)
    - 10 % z dalších 8 GB paměti (až 16 GB)
    - 6 % z následujících 112 GB paměti (až 128 GB)
    - 2% paměti nad 128 GB

Výše uvedená pravidla pro přidělení paměti a procesoru se používají k udržení uzlů agenta v pořádku, včetně některých hostitelských systémových podů, které jsou důležité pro stav clusteru. Tato pravidla přidělení také způsobit uzel sestavy méně alokovatelné paměti a procesoru, než by v případě, že nebyly součástí clusteru Kubernetes. Výše uvedené rezervace zdrojů nelze změnit.

Například pokud uzel nabízí 7 GB, bude hlásit 34 % paměti, které nelze přidělit nad prahovou hodnotu 750Mi pevné vystěhovávky.

`(0.25*4) + (0.20*3) = + 1 GB + 0.6GB = 1.6GB / 7GB = 22.86% reserved`

Kromě rezervací pro samotné kubernetes, základní uzel OS také rezervuje množství prostředků procesoru a paměti pro údržbu funkcí operačního systému.

Doporučené postupy najdete [v tématu Doporučené postupy pro základní funkce plánovače v AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Fondy uzlů

Uzly stejné konfigurace jsou seskupeny do *fondů uzlů*. Cluster Kubernetes obsahuje jeden nebo více fondů uzlů. Počáteční počet uzlů a velikost jsou definovány při vytváření clusteru AKS, který vytvoří *výchozí fond uzlů*. Tento výchozí fond uzlů v AKS obsahuje základní virtuální počítače, které spouštějí uzly agenta.

> [!NOTE]
> Chcete-li zajistit, aby váš cluster fungoval spolehlivě, měli byste spustit alespoň 2 (dva) uzly ve výchozím fondu uzlů.

Při škálování nebo upgradu clusteru AKS se akce provádí proti výchozímu fondu uzlů. Můžete také změnit měřítko nebo upgradovat fond konkrétních uzlů. Pro operace upgradu jsou spuštěné kontejnery naplánovány na jiných uzlech ve fondu uzlů, dokud nebudou všechny uzly úspěšně upgradovány.

Další informace o použití více fondů uzlů v AKS naleznete v [tématu Vytvoření a správa více fondů uzlů pro cluster v AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Voliči uzlů

V clusteru AKS, který obsahuje více fondů uzlů, budete muset sdělit Plánovač kubernetes, který fond uzlů se má použít pro daný prostředek. Například řadiče příchozího přenosu dat by neměly být spuštěny v uzlech Windows Server (aktuálně ve verzi Preview v AKS). Voliči uzlů umožňují definovat různé parametry, například operační modul uzlu, pro řízení, kde by měl být pod naplánován.

Následující základní příklad naplánuje instanci NGINX na linuxovém uzlu pomocí voliče uzlů *"beta.kubernetes.io/os": linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: nginx:1.15.12
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Další informace o tom, jak řídit, kde jsou naplánovány pody, naleznete [v tématu Doporučené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Lusky

Kubernetes používá *pods* ke spuštění instance aplikace. Pod představuje jednu instanci aplikace. Pody mají obvykle mapování 1:1 s kontejnerem, i když existují pokročilé scénáře, kde pod může obsahovat více kontejnerů. Tyto pody s více kontejnery jsou naplánovány společně na stejném uzlu a umožňují kontejnerům sdílet související prostředky.

Při vytváření podu můžete definovat *požadavky na prostředky* a požádat o určité množství prostředků procesoru nebo paměti. Plánovač Kubernetes se pokusí naplánovat pody spustit na uzlu s dostupnými prostředky ke splnění požadavku. Můžete také určit maximální limity prostředků, které brání danému podu spotřebovávat příliš mnoho výpočetního prostředku z podkladového uzlu. Osvědčeným postupem je zahrnout omezení prostředků pro všechny pody pomoci Plánovač Kubernetes pochopit, které prostředky jsou potřebné a povolené.

Další informace naleznete [v tématech Kubernetes lusky][kubernetes-pods] a [Kubernetes pod životní cyklus][kubernetes-pod-lifecycle].

Pod je logický prostředek, ale kontejnery jsou místo, kde se spouštějí úlohy aplikace. Pody jsou obvykle pomíjivé, jednorázové zdroje a individuálně naplánované pody chybí některé funkce vysoké dostupnosti a redundance, které Kubernetes poskytuje. Místo toho pody jsou obvykle nasazeny a *spravovány*kubernetes řadiče , jako je například řadič nasazení.

## <a name="deployments-and-yaml-manifests"></a>Nasazení a manifesty YAML

*Nasazení* představuje jeden nebo více identických podů, které spravuje řadič nasazení Kubernetes. Nasazení definuje počet *replik (podů)* k vytvoření a Plánovač Kubernetes zajišťuje, že pokud pody nebo uzly narazí na problémy, další pody jsou naplánovány na uzly v pořádku.

Můžete aktualizovat nasazení změnit konfiguraci podů, image kontejneru nebo připojené úložiště. Řadič nasazení vyprázdní a ukončí daný počet replik, vytvoří repliky z nové definice nasazení a pokračuje v procesu, dokud nebudou aktualizovány všechny repliky v nasazení.

Většina bezstavových aplikací v AKS by měla používat model nasazení spíše než plánování jednotlivých podů. Kubernetes můžete sledovat stav a stav nasazení a zajistit tak, aby požadovaný počet replik spuštěných v clusteru. Při plánování pouze jednotlivé pody, pody nejsou restartovány, pokud narazí na problém a nejsou přeplánovány na uzly v pořádku, pokud jejich aktuální uzel narazí na problém.

Pokud aplikace vyžaduje kvorum instancí, které mají být vždy k dispozici pro rozhodnutí o správě, které mají být provedeny, nechcete, aby proces aktualizace narušit tuto schopnost. *Rozpočty přerušení podu* lze definovat, kolik replik v nasazení lze vyjmuta během aktualizace nebo upgrade uzlu. Například pokud máte *5* replik y v nasazení, můžete definovat přerušení pod *4* povolit pouze jednu repliku z odstranění nebo přeplánování najednou. Stejně jako u omezení prostředků pod, osvědčeným postupem je definovat rozpočty přerušení pod u aplikací, které vyžadují minimální počet replik být vždy přítomen.

Nasazení se obvykle vytvářejí a `kubectl create` `kubectl apply`spravují pomocí nebo . Chcete-li vytvořit nasazení, definujte soubor manifestu ve formátu YAML (YAML Ain't Markup Language). Následující příklad vytvoří základní nasazení webového serveru NGINX. Nasazení určuje *3* repliky, které mají být vytvořeny a tento port *80* být otevřena v kontejneru. Požadavky na prostředky a omezení jsou také definovány pro procesor a paměť.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: nginx
spec:
  replicas: 3
  selector:
    matchLabels:
      app: nginx
  template:
    metadata:
      labels:
        app: nginx
    spec:
      containers:
      - name: nginx
        image: nginx:1.15.2
        ports:
        - containerPort: 80
        resources:
          requests:
            cpu: 250m
            memory: 64Mi
          limits:
            cpu: 500m
            memory: 256Mi
```

Složitější aplikace lze vytvořit také včetně služeb, jako jsou například vykladače zatížení v rámci manifestu YAML.

Další informace naleznete v [tématu Kubernetes nasazení][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Správa balíčků s helmou

Společný přístup ke správě aplikací v Kubernetes je s [Helmem][helm]. Můžete vytvářet a používat existující veřejné *grafy helmu,* které obsahují zabalenou verzi kódu aplikace a manifesty Kubernetes YAML k nasazení prostředků. Tyto helm grafy mohou být uloženy místně nebo často ve vzdáleném úložišti, jako je například [úložiště grafu Helm registru kontejnerů Azure][acr-helm].

Chcete-li použít Helm, serverová součást s názvem *Tiller* je nainstalována v clusteru Kubernetes. Kultivátor spravuje instalaci grafů v rámci clusteru. Samotný klient Helm je nainstalován místně ve vašem počítači nebo lze použít v rámci [prostředí Azure Cloud Shell][azure-cloud-shell]. Můžete vyhledat nebo vytvořit Helm grafy s klientem a pak je nainstalovat do clusteru Kubernetes.

![Helm obsahuje klientskou komponentu a součást Tiller na straně serveru, která vytváří prostředky uvnitř clusteru Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Další informace najdete [v tématu Instalace aplikací pomocí helmu ve službě Azure Kubernetes Service (AKS).][aks-helm]

## <a name="statefulsets-and-daemonsets"></a>Stavové sady a daemonsets

Řadič nasazení používá Plánovač Kubernetes ke spuštění daného počtu replik na libovolném dostupném uzlu s dostupnými prostředky. Tento přístup pomocí nasazení může být dostatečná pro bezstavové aplikace, ale ne pro aplikace, které vyžadují trvalé konvence pojmenování nebo úložiště. U aplikací, které vyžadují repliku, aby existovala v každém uzlu nebo vybraných uzlech v rámci clusteru, se řadič nasazení nedívá na to, jak jsou repliky distribuovány mezi uzly.

Existují dva prostředky Kubernetes, které umožňují spravovat tyto typy aplikací:

- *Stavové sady* – udržovat stav aplikací mimo životní cyklus jednotlivých podů, jako je například úložiště.
- *DaemonSets* - Zajistěte spuštěnou instanci na každém uzlu v rané fázi procesu zaváděcích pastí Kubernetes.

### <a name="statefulsets"></a>Stavové sady

Vývoj moderních aplikací se často zaměřuje na bezstavové aplikace, ale *stavové sady* lze použít pro stavové aplikace, jako jsou například aplikace, které obsahují součásti databáze. StatefulSet je podobný nasazení v tom, že jeden nebo více identických podů jsou vytvořeny a spravovány. Repliky v StatefulSet postupujte podle řádné, sekvenční přístup k nasazení, škálování, upgrady a ukončení. S StatefulSet (jako repliky jsou přeplánovány) konvence pojmenování, názvy sítí a úložiště přetrvávají.

Aplikaci definujete ve formátu `kind: StatefulSet`YAML pomocí aplikace a řadič StatefulSet pak zpracovává nasazení a správu požadovaných replik. Data se zapisují do trvalého úložiště poskytovaného spravovanými disky Azure nebo soubory Azure. S StatefulSets základní trvalé úložiště zůstane i v případě, že StatefulSet je odstraněn.

Další informace naleznete v tématu [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliky v StatefulSet jsou naplánovány a běží přes všechny dostupné uzla v clusteru AKS. Pokud potřebujete zajistit, že alespoň jeden pod v sadě běží na uzlu, můžete místo toho použít DaemonSet.

### <a name="daemonsets"></a>Sady daemonů

Pro konkrétní potřeby shromažďování nebo monitorování protokolu může být nutné spustit daný pod na všech nebo vybraných uzlech. *DaemonSet* se opět používá k nasazení jednoho nebo více identických podů, ale DaemonSet Controller zajišťuje, že každý uzel zadaný spustí instanci podu.

DaemonSet Controller můžete naplánovat pody na uzly v rané fázi procesu spouštění clusteru, před spuštěním výchozího plánovače Kubernetes. Tato schopnost zajišťuje, že pody v DaemonSet jsou spuštěny před tradiční pody v nasazení nebo StatefulSet jsou naplánovány.

Podobně jako Stavové sady je sada DaemonSet definována `kind: DaemonSet`jako součást definice YAML pomocí .

Další informace naleznete v tématu [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Pokud používáte [doplněk Virtuální uzly](virtual-nodes-cli.md#enable-virtual-nodes-addon), DaemonSets nevytvoří pody na virtuální uzel.

## <a name="namespaces"></a>Jmenné prostory

Kubernetes prostředky, jako jsou pody a nasazení, jsou logicky seskupeny do *oboru názvů*. Tato seskupení poskytují způsob, jak logicky rozdělit cluster AKS a omezit přístup k vytváření, zobrazení nebo správě prostředků. Můžete vytvořit obory názvů pro oddělení obchodních skupin, například. Uživatelé mohou pracovat pouze s prostředky v rámci svých přiřazených oborů názvů.

![Kubernetes obory názvů logicky rozdělit prostředky a aplikace](media/concepts-clusters-workloads/namespaces.png)

Při vytváření clusteru AKS jsou k dispozici následující obory názvů:

- *výchozí* – Tento obor názvů je místo, kde pody a nasazení jsou vytvořeny ve výchozím nastavení, pokud není k dispozici žádný. V menších prostředích můžete nasadit aplikace přímo do výchozího oboru názvů bez vytváření dalších logických oddělení. Při interakci s rozhraním API Kubernetes, například s `kubectl get pods`, výchozí obor názvů se používá, když není zadán žádný.
- *kube-system* - Tento obor názvů je místo, kde existují základní prostředky, jako jsou síťové funkce, jako je DNS a proxy nebo řídicí panel Kubernetes. Obvykle není nasadit vlastní aplikace do tohoto oboru názvů.
- *kube-public* - Tento obor názvů se obvykle nepoužívá, ale lze použít pro prostředky, které mají být viditelné v celém clusteru a může být zobrazen libovolným uživatelem.

Další informace naleznete [v tématu Kubernetes obory názvů][kubernetes-namespaces].

## <a name="next-steps"></a>Další kroky

Tento článek popisuje některé základní komponenty Kubernetes a jak se vztahují na clustery AKS. Další informace o základních konceptech Kubernetes a AKS naleznete v následujících článcích:

- [Kubernetes / AKS přístup a identita][aks-concepts-identity]
- [Kubernetes / AKS zabezpečení][aks-concepts-security]
- [Kubernetes / AKS virtuální sítě][aks-concepts-network]
- [Kubernetes / AKS úložiště][aks-concepts-storage]
- [Kubernetes / AKS váha][aks-concepts-scale]

<!-- EXTERNAL LINKS -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubernetes-pods]: https://kubernetes.io/docs/concepts/workloads/pods/pod-overview/
[kubernetes-pod-lifecycle]: https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/
[kubernetes-deployments]: https://kubernetes.io/docs/concepts/workloads/controllers/deployment/
[kubernetes-statefulsets]: https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/
[kubernetes-daemonset]: https://kubernetes.io/docs/concepts/workloads/controllers/daemonset/
[kubernetes-namespaces]: https://kubernetes.io/docs/concepts/overview/working-with-objects/namespaces/
[helm]: https://helm.sh/
[azure-cloud-shell]: https://shell.azure.com

<!-- INTERNAL LINKS -->
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-security]: concepts-security.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[acr-helm]: ../container-registry/container-registry-helm-repos.md
[aks-helm]: kubernetes-helm.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[operator-best-practices-scheduler]: operator-best-practices-scheduler.md
[use-multiple-node-pools]: use-multiple-node-pools.md
[operator-best-practices-advanced-scheduler]: operator-best-practices-advanced-scheduler.md
[reservation-discounts]:../cost-management-billing/reservations/save-compute-costs-reservations.md
