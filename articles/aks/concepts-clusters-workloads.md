---
title: Koncepty – základy Kubernetes pro služby Azure Kubernetes (AKS)
description: Seznamte se se základními komponentami clusterů a úloh Kubernetes a s tím, jak se vztahují k funkcím ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 12/07/2020
ms.openlocfilehash: 7485631660395e03c558167c321e6091c6fac755
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373228"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)

Vzhledem k tomu, že vývoj aplikací se přesouvá směrem k přístupu založenému na kontejneru, je potřeba orchestrovat a spravovat prostředky, které jsou důležité. Kubernetes je přední platforma, která poskytuje možnost poskytovat spolehlivé plánování úloh aplikací odolných proti chybám. Služba Azure Kubernetes Service (AKS) je spravovaná Kubernetes nabídka, která dále zjednodušuje nasazování a správu aplikací založených na kontejnerech.

V tomto článku se seznámíte se základními komponentami infrastruktury Kubernetes, jako je například *řídicí plocha*, *uzly* a *fondy uzlů*. Společně s postupem seskupení prostředků do *oborů názvů* se zavádějí i prostředky úlohy, jako jsou *lusky*, *nasazení* a *sady* .

## <a name="what-is-kubernetes"></a>Co je Kubernetes?

Kubernetes je rychle se vyvíjející platforma, která spravuje aplikace založené na kontejnerech a jejich přidružené síťové komponenty a součásti úložiště. Zaměřuje se na úlohy aplikace, nikoli na základní součásti infrastruktury. Kubernetes poskytuje deklarativní přístup k nasazením zajištěné robustní sadou rozhraní API pro operace správy.

Můžete vytvářet a spouštět moderní a přenosné aplikace založené na mikroslužbách, které využívají Kubernetes Orchestrace a správu dostupnosti těchto komponent aplikace. Kubernetes podporuje bezstavové a stavové aplikace jako průběh týmů prostřednictvím přijetí aplikací založených na mikroslužbách.

Jako otevřená platforma vám Kubernetes umožňuje sestavovat aplikace s preferovaným programovacím jazykem, operačním systémem, knihovnami nebo sběrnicí pro zasílání zpráv. Stávající nástroje pro průběžnou integraci a průběžné doručování (CI/CD) můžete integrovat s Kubernetes k naplánování a nasazení vydaných verzí.

Služba Azure Kubernetes Service (AKS) poskytuje spravovanou službu Kubernetes, která snižuje složitost pro úlohy správy nasazení a jádra, včetně koordinačních upgradů. Rovina ovládacího prvku AKS je spravovaná platformou Azure a platíte jenom za uzly AKS, na kterých běží vaše aplikace. AKS je postaven na Open Source modulu Azure Kubernetes Service Engine ([AKS-Engine][aks-engine]).

## <a name="kubernetes-cluster-architecture"></a>Architektura clusteru Kubernetes

Cluster Kubernetes je rozdělen do dvou součástí:

- *Rovina ovládacího prvku* poskytuje základní služby Kubernetes a orchestraci úloh aplikací.
- *Uzly* , které spouštějí úlohy vaší aplikace.

![Rovina ovládacího prvku Kubernetes a součásti uzlu](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Řídicí rovina

Při vytváření clusteru AKS se automaticky vytvoří a nakonfiguruje rovina ovládacího prvku. Tato Řídicí rovina je k dispozici jako spravovaný prostředek Azure, který je z uživatele abstraktní. Pro plochu ovládacího prvku se neúčtují žádné náklady, jenom uzly, které jsou součástí clusteru AKS. Rovina ovládacího prvku a její prostředky se nachází pouze v oblasti, ve které jste cluster vytvořili.

Rovina ovládacího prvku zahrnuje následující základní komponenty Kubernetes:

- *Kube-apiserver* – Server rozhraní API je způsob, jakým jsou vystavená základní rozhraní API Kubernetes. Tato součást poskytuje interakci pro nástroje pro správu, například `kubectl` nebo řídicí panel Kubernetes.
- *etcd* – Chcete-li zachovat stav clusteru a konfigurace Kubernetes, vysoce dostupný *etcd* je klíčovým úložištěm hodnot v rámci Kubernetes.
- *Kube-Scheduler* – když vytváříte nebo škálovat aplikace, Plánovač určí, které uzly můžou úlohu spouštět, a spustí je.
- *Kube-Controller-Manager* – správce kontroléru se dohlíží na počet menších řadičů, které provádějí akce jako replikace lusků a zpracování operací uzlu.

AKS poskytuje rovinu ovládacího prvku s jedním klientem a vyhrazeným serverem API, plánovačem atd. Definujete počet a velikost uzlů a platforma Azure nakonfiguruje zabezpečenou komunikaci mezi řídicí rovinou a uzly. Interakce s rovinou ovládacího prvku probíhá prostřednictvím rozhraní API Kubernetes, jako je `kubectl` nebo řídicí panel Kubernetes.

Tato spravovaná rovina řízení znamená, že nemusíte konfigurovat komponenty jako vysoce dostupné úložiště *etcd* , ale také to znamená, že nemůžete přímo přistupovat k rovině ovládacího prvku. Upgrady na Kubernetes se orchestrují prostřednictvím Azure CLI nebo Azure Portal, který upgraduje rovinu ovládacího prvku a pak uzly. Pokud chcete řešit možné problémy, můžete zkontrolovat protokoly roviny ovládacího prvku prostřednictvím protokolů Azure Monitor.

Pokud potřebujete řídicí plochu nakonfigurovat určitým způsobem nebo potřebujete k ní přímý přístup, můžete nasadit vlastní cluster Kubernetes pomocí [AKS-Engine][aks-engine].

Související osvědčené postupy najdete [v tématu osvědčené postupy pro zabezpečení a upgrady clusterů v AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Fondy uzlů a uzlů

Chcete-li spustit aplikace a podpůrné služby, potřebujete *uzel* Kubernetes. Cluster AKS má jeden nebo více uzlů, což je virtuální počítač Azure, který spouští součásti uzlu Kubernetes a modul runtime kontejneru:

- `kubelet`Je agent Kubernetes, který zpracovává požadavky orchestrace z řídicí roviny a plánuje spouštění požadovaných kontejnerů.
- Virtuální sítě zpracovává *Kube-proxy* na každém uzlu. Proxy směruje síťový provoz a spravuje přidělování IP adres pro služby a lusky.
- *Modul runtime kontejneru* je komponenta, která umožňuje spuštění kontejnerových aplikací a interakce s dalšími prostředky, jako je například virtuální síť a úložiště. V AKS se jako modul runtime kontejneru používá Moby.

![Virtuální počítač Azure a podpůrné prostředky pro uzel Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Velikost virtuálního počítače Azure pro vaše uzly definuje, kolik procesorů, kolik paměti a velikost a typ úložiště je k dispozici (například vysoce výkonné SSD nebo běžný pevný disk). Pokud předpokládáte potřebu aplikací, které vyžadují velké množství procesoru a paměti nebo vysoce výkonné úložiště, naplánujte odpovídající velikost uzlu. Můžete také škálovat počet uzlů v clusteru AKS tak, aby splňovaly požadavky.

V AKS je image virtuálního počítače pro uzly v clusteru v současné době založená na Ubuntu Linux nebo Windows serveru 2019. Při vytváření clusteru AKS nebo při horizontálním navýšení kapacity počtu uzlů vytvoří platforma Azure požadovaný počet virtuálních počítačů a nakonfiguruje je. Neexistuje žádná ruční konfigurace, kterou byste mohli provést. Uzly agentů se účtují jako standardní virtuální počítače, takže se automaticky aplikují všechny slevy, které používáte pro velikost virtuálního počítače (včetně [rezervací Azure][reservation-discounts]).

Pokud potřebujete použít jiný hostitelský operační systém, modul runtime kontejneru nebo zahrnout vlastní balíčky, můžete nasadit vlastní cluster Kubernetes pomocí [AKS-Engine][aks-engine]. Funkce pro odesílání dat `aks-engine` a poskytuje možnosti konfigurace, než jsou oficiálně podporované v clusterech AKS. Pokud například chcete použít modul runtime kontejneru jiný než Moby, můžete použít `aks-engine` ke konfiguraci a nasazení clusteru Kubernetes, který splňuje vaše aktuální potřeby.

### <a name="resource-reservations"></a>Rezervace prostředků

Prostředky uzlů využívají AKS k zajištění funkce uzlu jako součásti clusteru. Toto využití může vytvořit nesoulad mezi celkovými prostředky vašeho uzlu a prostředky ALLOCATABLE při použití v AKS. Tyto informace jsou důležité při nastavování požadavků a omezení pro uživatele nasazené lusky.

Chcete-li najít prostředky ALLOCATABLE uzlu, spusťte příkaz:
```kubectl
kubectl describe node [NODE_NAME]
```

Aby bylo možné udržovat výkon a funkce uzlu, jsou prostředky rezervovány na jednotlivých uzlech pomocí AKS. V případě, že uzel roste větší množství prostředků, rezervace prostředků roste v důsledku většího počtu uživatelů nasazených v části s potřebnou správou lusků.

>[!NOTE]
> Použití doplňků AKS, jako je Container Insights (OMS), bude spotřebovávat další prostředky uzlů.

Jsou rezervované dva typy prostředků:

- PROCESOR vyhrazený pro **procesor** závisí na typu uzlu a konfiguraci clusteru, což může způsobit, že by ALLOCATABLE procesor byl v důsledku spuštění dalších funkcí.

   | Jádra procesoru na hostiteli | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube – rezervováno (millicores)|60|100|140|180|260|420|740|

- **Paměť,** kterou využívá AKS, zahrnuje součet dvou hodnot.

   1. Démon kubelet je nainstalován na všech uzlech agenta Kubernetes za účelem správy vytváření a ukončování kontejneru. Ve výchozím nastavení má démon následující pravidlo vyřazení: *paměť. k dispozici<750Mi*, což znamená, že uzel musí vždy mít alespoň 750 mi ALLOCATABLE.  Když je hostitel pod touto prahovou hodnotou dostupné paměti, kubelet ukončí jednu z běžících lusků, aby uvolnil paměť na hostitelském počítači a chránil ji. Tato akce se aktivuje až po snížení dostupné paměti za prahovou hodnotu 750Mi.

   2. Druhá hodnota je regresivní míra rezervací paměti pro správnou funkci démona kubelet (Kube – rezervováno).
      - 25% prvních 4 GB paměti
      - 20% z dalších 4 GB paměti (až 8 GB)
      - 10% z dalších 8 GB paměti (až 16 GB)
      - 6% z dalších 112 GB paměti (až 128 GB)
      - 2% libovolné paměti nad 128 GB

Výše uvedená pravidla pro paměť a přidělení procesoru se používají k udržení dobrých uzlů agentů, včetně některých z nich hostujících prostředí, které jsou pro stav clusteru zásadní. Tato pravidla přidělení také způsobí, že uzel hlásí méně ALLOCATABLE paměť a procesor, než by normálně neměl být součástí clusteru Kubernetes. Výše uvedené rezervace prostředků se nedají změnit.

Například pokud uzel nabízí 7 GB, bude hlásit 34% paměti, která není ALLOCATABLE, včetně prahové hodnoty pro pevné vyřazení 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Kromě rezervací pro samotný Kubernetes vyhradí základní operační systém Node také množství prostředků procesoru a paměti pro udržení funkcí operačního systému.

Související osvědčené postupy najdete v tématu [osvědčené postupy pro základní funkce nástroje Scheduler v AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Fondy uzlů

Uzly stejné konfigurace jsou seskupeny dohromady do *fondů uzlů*. Cluster Kubernetes obsahuje jeden nebo více fondů uzlů. Počáteční počet uzlů a velikost je definován při vytváření clusteru AKS, který vytváří *výchozí fond uzlů*. Tento výchozí fond uzlů v AKS obsahuje základní virtuální počítače, které spouštějí vaše uzly agentů.

> [!NOTE]
> Chcete-li zajistit spolehlivou činnost clusteru, měli byste spustit alespoň 2 (dva) uzly ve výchozím fondu uzlů.

Při škálování nebo upgradu clusteru AKS je akce prováděna s výchozím fondem uzlů. Můžete se také rozhodnout škálovat nebo upgradovat určitý fond uzlů. V případě operací upgradu jsou spuštěné kontejnery naplánovány na jiných uzlech ve fondu uzlů, dokud nebudou všechny uzly úspěšně upgradovány.

Další informace o použití více fondů uzlů v AKS najdete v tématu [Vytvoření a Správa fondů více uzlů pro cluster v AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selektory uzlů

V clusteru AKS, který obsahuje víc fondů uzlů, možná budete muset říct, aby se pro daný prostředek používal příslušný fond uzlů pro Kubernetes Scheduler. Například řadiče příchozího přenosu dat by se neměly spouštět na uzlech Windows serveru. Selektory uzlů umožňují definovat různé parametry, jako je například uzel OS, aby bylo možné určit, kde má být uzel pod naplánován.

Následující základní příklad naplánuje instanci NGINX na uzlu Linux pomocí voliče uzlů *"beta.Kubernetes.IO/OS": Linux*:

```yaml
kind: Pod
apiVersion: v1
metadata:
  name: nginx
spec:
  containers:
    - name: myfrontend
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.12-alpine
  nodeSelector:
    "beta.kubernetes.io/os": linux
```

Další informace o tom, jak určit, kde mají být lusky, najdete v tématu [osvědčené postupy pro pokročilé funkce plánovače v AKS][operator-best-practices-advanced-scheduler].

## <a name="pods"></a>Podů

Kubernetes používá *lusky* ke spuštění instance aplikace. Pod představuje jednu instanci vaší aplikace. Lusky mají typicky mapování 1:1 s kontejnerem, i když existují pokročilé scénáře, kde objekt pod může obsahovat více kontejnerů. Tyto lomené lusky jsou naplánované společně na stejném uzlu a umožňují kontejnerům sdílet související prostředky.

Když vytvoříte pod, můžete definovat *požadavky* na prostředky pro vyžádání určitého množství prostředků procesoru nebo paměti. Plánovač Kubernetes se pokusí naplánovat lusky, aby běžely na uzlu s dostupnými prostředky pro splnění požadavku. Můžete také zadat maximální omezení prostředků, které brání danému zařízení v využívání příliš velkého množství výpočetních prostředků ze základního uzlu. Osvědčeným postupem je zahrnout do všech lusků omezení prostředků, které pomůžou plánovači Kubernetes pochopit, které prostředky jsou potřeba a povolené.

Další informace najdete v tématu životní cyklus [Kubernetes lusky][kubernetes-pods] a [Kubernetes pod][kubernetes-pod-lifecycle].

Pod je logický prostředek, ale kontejnery jsou spuštěny úlohami aplikace. Lusky jsou obvykle dočasné a nepoužívané prostředky a samostatně naplánované lusky neposkytují některé z funkcí vysoké dostupnosti a redundance Kubernetes. Místo toho jsou lusky nasazeny a spravovány Kubernetes *řadiči*, jako je například kontrolér nasazení.

## <a name="deployments-and-yaml-manifests"></a>Nasazení a manifesty YAML

*Nasazení* představuje jednu nebo více identické lusky spravované kontrolérem nasazení Kubernetes. Nasazení definuje počet *replik* (lusky), které se mají vytvořit, a Plánovač Kubernetes zajišťuje, aby v případě, že dojde k rozchodu nebo uzlů, bylo naplánováno další lusky na uzlech v pořádku.

Nasazení můžete aktualizovat, aby se změnila konfigurace lusků, použitého obrazu kontejneru nebo připojeného úložiště. Řadič nasazení vyprázdní a ukončí daný počet replik, vytvoří repliky z nové definice nasazení a pokračuje v procesu, dokud nebudou všechny repliky v nasazení aktualizovány.

Většina bezstavových aplikací v AKS by měla místo plánování jednotlivých lusků používat model nasazení. Kubernetes může monitorovat stav a stav nasazení, aby se zajistilo, že se v clusteru spustí požadovaný počet replik. Když naplánujete pouze jednotlivé lusky, lusky se nerestartují, pokud dojde k potížím a nedojde k jejich přeplánování na funkčních uzlech, pokud jejich aktuální uzel narazí na problém.

Pokud aplikace vyžaduje, aby byly kvora instancí vždy k dispozici pro rozhodování o správě, nechcete, aby proces aktualizace narušil tuto schopnost. *Rozpočty přerušení pod* mohou být použity k definování toho, kolik replik v nasazení lze v průběhu aktualizace nebo upgradu uzlu považovat za nefunkční. Například pokud máte ve svém nasazení *pět (5)* replik, můžete definovat přerušení v případě *4* , aby bylo možné v jednom okamžiku odstranit nebo přeplánovat pouze jednu repliku. Stejně jako u omezení prostředků pod je osvědčeným postupem definování rozpočtů přerušení v aplikacích vyžadujících minimální počet replik, které mají být vždy k dispozici.

Nasazení se obvykle vytváří a spravují pomocí `kubectl create` nebo `kubectl apply` . Nasazení vytvoříte tak, že definujete soubor manifestu ve formátu YAML (YAML Ain't Markup Language). Následující příklad vytvoří základní nasazení webového serveru NGINX. Nasazení určuje *tři (3)* repliky, které mají být vytvořeny, a vyžaduje, aby byl na kontejneru otevřen port *80* . Pro procesor a paměť se definují taky požadavky na prostředky a omezení.

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
        image: mcr.microsoft.com/oss/nginx/nginx:1.15.2-alpine
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

Složitější aplikace se dají vytvářet taky včetně služeb, jako jsou například nástroje pro vyrovnávání zatížení v manifestu YAML.

Další informace najdete v tématu [nasazení Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Správa balíčků pomocí Helm

Běžným přístupem ke správě aplikací v Kubernetes je [Helm][helm]. Můžete sestavit a použít stávající veřejné Helm *grafy* , které obsahují zabalenou verzi kódu aplikace a manifesty YAML Kubernetes pro nasazení prostředků. Tyto Helm grafy se můžou ukládat lokálně nebo často ve vzdáleném úložišti, jako je třeba [úložiště grafu Azure Container Registry Helm][acr-helm].

Chcete-li použít Helm, nainstalujte do počítače klienta Helm nebo použijte klienta Helm v [Azure Cloud Shell][azure-cloud-shell]. Pomocí klienta můžete vyhledat nebo vytvořit grafy Helm a pak je nainstalovat do clusteru Kubernetes. Další informace najdete v tématu [instalace existujících aplikací pomocí Helm v AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets a DaemonSets

Řadič nasazení používá Plánovač Kubernetes ke spuštění určitého počtu replik na jakémkoli dostupném uzlu s dostupnými prostředky. Tento přístup k použití nasazení může být pro bezstavové aplikace dostačující, ale ne pro aplikace, které vyžadují trvalou konvenci pojmenování nebo úložiště. Pro aplikace, které vyžadují, aby existovala replika na každém uzlu nebo vybrané uzly v rámci clusteru, kontroler nasazení se nedívá na to, jak jsou repliky distribuované napříč uzly.

Existují dva Kubernetes prostředky, které umožňují spravovat tyto typy aplikací:

- *StatefulSets* – Udržujte stav aplikací nad rámec individuálního životního cyklu, například úložiště.
- *DaemonSets* – zajistěte spuštěnou instanci na každém uzlu, a to nejdříve v procesu Bootstrap Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Moderní vývoj aplikací se často zaměřuje na bezstavové aplikace, ale *StatefulSets* se dají použít pro stavové aplikace, jako jsou například aplikace, které obsahují databázové součásti. StatefulSet se podobá nasazení v tom, že jedna nebo více identických lusků se vytváří a spravují. Repliky v StatefulSet sledují řádný a sekvenční přístup k nasazení, škálování, upgradům a ukončením. U StatefulSet (jako repliky se přejmenovává) konvence pojmenování, názvy sítí a uchování úložiště.

Aplikaci definujete ve formátu YAML pomocí `kind: StatefulSet` a kontroler StatefulSet pak zpracovává nasazení a správu požadovaných replik. Data se zapisují do trvalého úložiště, které poskytuje Azure Managed Disks nebo soubory Azure. V StatefulSets zůstane příslušné trvalé úložiště i v případě, že se StatefulSet odstraní.

Další informace najdete v tématu [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliky ve StatefulSet se naplánují a spouštějí napříč libovolným dostupným uzlem v clusteru AKS. Pokud potřebujete zajistit, aby na uzlu běžela aspoň jedna pod v sadě, můžete místo toho použít DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Pro konkrétní účely shromažďování protokolů nebo monitorování možná budete muset spustit dané zařízení na všech nebo vybraných uzlech. *DaemonSet* se znovu používá k nasazení jedné nebo více identické lusky, ale kontroler DaemonSet zajišťuje, že každý zadaný uzel spustí instanci uzlu pod.

Řadič DaemonSet může naplánovat lusky na uzlech na začátku v procesu spouštění clusteru, než se spustí výchozí Plánovač Kubernetes. Tato možnost zajistí, že lusky ve DaemonSet začínají před tradičními lusky v nasazení nebo v StatefulSet.

Podobně jako StatefulSets je DaemonSet definován jako součást definice YAML pomocí `kind: DaemonSet` .

Další informace najdete v tématu [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Pokud se používá [doplněk virtuálních uzlů](virtual-nodes-cli.md#enable-virtual-nodes-addon), DaemonSets ve virtuálním uzlu nevytvoří lusky.

## <a name="namespaces"></a>Obory názvů

Prostředky Kubernetes, například lusky a nasazení, jsou logicky seskupeny do *oboru názvů*. Tato seskupení poskytují způsob, jak logicky rozdělit cluster AKS a omezit přístup k vytváření, zobrazení nebo správě prostředků. Můžete například vytvořit obory názvů pro oddělení obchodních skupin. Uživatelé můžou pracovat jenom s prostředky v rámci svých přiřazených oborů názvů.

![Kubernetes obory názvů pro logicky dělit prostředky a aplikace](media/concepts-clusters-workloads/namespaces.png)

Při vytváření clusteru AKS jsou k dispozici následující obory názvů:

- *výchozí* – tento obor názvů je tam, kde jsou ve výchozím nastavení vytvářeny lusky a nasazení, když žádné není k dispozici. V menších prostředích můžete nasazovat aplikace přímo do výchozího oboru názvů, aniž byste museli vytvářet další logické separace. Při interakci s rozhraním API Kubernetes, jako je například s `kubectl get pods` , je použit výchozí obor názvů, pokud není zadán žádný.
- *Kube-System* – tento obor názvů je tam, kde existují základní prostředky, jako jsou například síťové funkce jako DNS a proxy nebo řídicí panel Kubernetes. Do tohoto oboru názvů obvykle nesadíte vlastní aplikace.
- *Kube – Public* – tento obor názvů se obvykle nepoužívá, ale dá se použít k zobrazení prostředků v celém clusteru a může ho zobrazit libovolný uživatel.

Další informace najdete v tématu [obory názvů Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Další kroky

Tento článek popisuje některé základní komponenty Kubernetes a jejich použití v clusterech AKS. Další informace o základních konceptech Kubernetes a AKS najdete v následujících článcích:

- [Přístup a identita Kubernetes/AKS][aks-concepts-identity]
- [Zabezpečení Kubernetes/AKS][aks-concepts-security]
- [Virtuální sítě Kubernetes/AKS][aks-concepts-network]
- [Úložiště Kubernetes/AKS][aks-concepts-storage]
- [Škálování Kubernetes/AKS][aks-concepts-scale]

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
