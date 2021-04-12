---
title: Koncepty – základy Kubernetes pro služby Azure Kubernetes (AKS)
description: Seznamte se se základními komponentami clusterů a úloh Kubernetes a s tím, jak se vztahují k funkcím ve službě Azure Kubernetes Service (AKS).
services: container-service
ms.topic: conceptual
ms.date: 03/05/2020
ms.openlocfilehash: 5e505ed44d221b20178ea5ffb1d9125fb2bddd4c
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105931"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)

Vývoj aplikací se i nadále přesouvá směrem k přístupu založenému na kontejneru. tím se zvyšuje potřeba orchestrovat a spravovat prostředky. V rámci špičkové platformy Kubernetes poskytuje spolehlivé plánování úloh aplikací odolných proti chybám. Služba Azure Kubernetes Service (AKS), spravovaná nabídka Kubernetes, dále zjednodušuje nasazování a správu aplikací založených na kontejnerech.

Tento článek obsahuje následující informace:
* Základní komponenty infrastruktury Kubernetes:
    * *rovina ovládacího prvku*
    * *sortiment*
    * *fondy uzlů*
* Prostředky úlohy: 
    * *podů*
    * *nasazení*
    * *obnovení* 
* Postup seskupení prostředků do *oborů názvů*

## <a name="what-is-kubernetes"></a>Co je Kubernetes?

Kubernetes je rychle se vyvíjející platforma, která spravuje aplikace založené na kontejnerech a jejich přidružené síťové komponenty a součásti úložiště. Kubernetes se zaměřuje na úlohy aplikace, ne na základní součásti infrastruktury. Kubernetes poskytuje deklarativní přístup k nasazením zajištěné robustní sadou rozhraní API pro operace správy.

Pomocí Kubernetes můžete sestavovat a spouštět moderní a přenosné aplikace založené na mikroslužbách, které slouží k orchestraci a správě dostupnosti komponent aplikace. Kubernetes podporuje bezstavové a stavové aplikace jako průběh týmů prostřednictvím přijetí aplikací založených na mikroslužbách.

Jako otevřená platforma vám Kubernetes umožňuje sestavovat aplikace s preferovaným programovacím jazykem, operačním systémem, knihovnami nebo sběrnicí pro zasílání zpráv. Stávající nástroje pro průběžnou integraci a průběžné doručování (CI/CD) můžete integrovat s Kubernetes k naplánování a nasazení vydaných verzí.

AKS poskytuje spravovanou službu Kubernetes, která snižuje složitost nasazení a základní úlohy správy, jako je koordinace upgradu. Platforma Azure spravuje rovinu ovládacího prvku AKS a platíte jenom za uzly AKS, na kterých běží vaše aplikace. AKS je postaven na Open Source modulu Azure Kubernetes Service: [AKS-Engine][aks-engine].

## <a name="kubernetes-cluster-architecture"></a>Architektura clusteru Kubernetes

Cluster Kubernetes je rozdělen do dvou součástí:

- *Rovina ovládacího prvku*: poskytuje základní služby Kubernetes a orchestraci úloh aplikací.
- *Uzly*: spouštění úloh aplikací.

![Rovina ovládacího prvku Kubernetes a součásti uzlu](media/concepts-clusters-workloads/control-plane-and-nodes.png)

## <a name="control-plane"></a>Řídicí rovina

Při vytváření clusteru AKS se automaticky vytvoří a nakonfiguruje rovina ovládacího prvku. Tato Řídicí rovina se poskytuje zdarma jako spravovaný prostředek Azure, který je z uživatele abstraktní. Platíte jenom za uzly připojené ke clusteru AKS. Rovina ovládacího prvku a její prostředky se nachází pouze v oblasti, ve které jste cluster vytvořili.

Rovina ovládacího prvku zahrnuje následující základní komponenty Kubernetes:

| Komponenta | Popis |  
| ----------------- | ------------- |  
| *kube-apiserver*                                                                                 | Server API je způsob zpřístupnění základních rozhraní Kubernetes API. Tato součást poskytuje interakci pro nástroje pro správu, například `kubectl` nebo řídicí panel Kubernetes.                                                        |  
| *etcd* | Aby se zachoval stav clusteru a konfigurace Kubernetes, vysoce dostupný *etcd* je klíčovým úložištěm hodnot v rámci Kubernetes.                                      |  
| *Kube – Scheduler*                                                                            | Když vytváříte nebo škálovat aplikace, Plánovač určí, které uzly můžou úlohu spouštět a spustí je.                                                                                    |  
| *Kube-Controller-Manager*                                                                            | Správce kontroléru se dohlíží na počet menších řadičů, které provádějí akce jako replikace lusků a zpracování operací uzlu.                                                                  |  

AKS poskytuje rovinu ovládacího prvku s jedním klientem a vyhrazeným serverem API, plánovačem atd. Definujete počet a velikost uzlů a platforma Azure nakonfiguruje zabezpečenou komunikaci mezi řídicí rovinou a uzly. Interakce s rovinou ovládacího prvku probíhá prostřednictvím rozhraní API Kubernetes, jako je `kubectl` nebo řídicí panel Kubernetes.

I když nemusíte konfigurovat komponenty (jako úložiště *etcd* s vysokou dostupností) s touto rovinou spravovaného ovládacího prvku, nemůžete přímo získat přístup k rovině ovládacího prvku. Kubernetes Řídicí rovina a upgrady uzlů se orchestrují prostřednictvím rozhraní příkazového řádku Azure CLI nebo Azure Portal. Pokud chcete řešit možné problémy, můžete zkontrolovat protokoly roviny ovládacího prvku prostřednictvím protokolů Azure Monitor.

Ke konfiguraci nebo přímému přístupu k rovině ovládacího prvku můžete nasadit vlastní cluster Kubernetes pomocí [AKS-Engine][aks-engine].

Související osvědčené postupy najdete [v tématu osvědčené postupy pro zabezpečení a upgrady clusterů v AKS][operator-best-practices-cluster-security].

## <a name="nodes-and-node-pools"></a>Fondy uzlů a uzlů

Chcete-li spustit aplikace a podpůrné služby, potřebujete *uzel* Kubernetes. Cluster AKS má minimálně jeden uzel, virtuální počítač Azure, který spouští součásti uzlu Kubernetes a modul runtime kontejneru.

| Komponenta | Popis |  
| ----------------- | ------------- |  
| `kubelet`                                                                                 | Agent Kubernetes, který zpracovává požadavky orchestrace z roviny řízení a plánování spouštění požadovaných kontejnerů.                                                        |  
| *Kube – proxy* | Zpracovává virtuální sítě na každém uzlu. Proxy směruje síťový provoz a spravuje přidělování IP adres pro služby a lusky.                                      |  
| *modul runtime kontejneru*                                                                            | Umožňuje spuštění kontejnerových aplikací a interakce s dalšími prostředky, jako je například virtuální síť a úložiště. Clustery AKS s využitím fondů uzlů Kubernetes verze 1.19 + `containerd` se používají jako jejich modul runtime kontejneru. Clustery AKS používající Kubernetes před fondem uzlů verze 1,19 pro fondy uzlů používají [Moby](https://mobyproject.org/) (nadřazený Docker) jako jejich modul runtime kontejneru.                                                                                    |  


![Virtuální počítač Azure a podpůrné prostředky pro uzel Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Velikost virtuálního počítače Azure pro vaše uzly definuje procesory úložiště, paměť, velikost a typ, který je k dispozici (například vysoce výkonné SSD nebo běžný pevný disk). Naplánujte velikost uzlu kolem toho, jestli vaše aplikace můžou vyžadovat velké objemy procesoru a paměti nebo vysoce výkonné úložiště. Horizontální navýšení kapacity počtu uzlů v clusteru AKS, aby splňovaly požadavky.

V AKS je image virtuálního počítače pro uzly clusteru založená na Ubuntu Linux nebo Windows serveru 2019. Když vytvoříte cluster AKS nebo budete škálovat počet uzlů, platforma Azure automaticky vytvoří a nakonfiguruje požadovaný počet virtuálních počítačů. Uzly agentů se účtují jako standardní virtuální počítače, takže se automaticky uplatní všechny slevy velikosti virtuálních počítačů (včetně [rezervací Azure][reservation-discounts]).

Pokud používáte jiný hostitelský operační systém, kontejnerový modul runtime nebo včetně různých vlastních balíčků, nasaďte vlastní cluster Kubernetes s [AKS modulem][aks-engine] . Funkce pro odesílání nadřazených `aks-engine` verzí nabízí možnosti konfigurace před podporou v clusterech AKS. Pokud tedy chcete použít modul runtime kontejneru jiný než `containerd` nebo [Moby](https://mobyproject.org/), můžete spustit `aks-engine` pro konfiguraci a nasazení clusteru Kubernetes, který splňuje vaše aktuální potřeby.

### <a name="resource-reservations"></a>Rezervace prostředků

AKS používá prostředky uzlů k usnadnění funkce uzlu jako součást clusteru. Toto využití může vytvořit nesoulad mezi celkovými prostředky vašeho uzlu a ALLOCATABLE prostředky v AKS. Tyto informace si pamatujte při nastavování požadavků a omezení pro uživatele nasazené v luskech.

Chcete-li najít prostředky ALLOCATABLE uzlu, spusťte příkaz:
```kubectl
kubectl describe node [NODE_NAME]
```

Aby bylo možné zachovat výkon a funkčnost uzlu, AKS rezervuje prostředky na každém uzlu. V případě, že uzel roste větší množství prostředků, rezervace prostředků roste kvůli větší potřebě správy lusků nasazených uživatelem.

>[!NOTE]
> Použití doplňků AKS, jako je Container Insights (OMS), bude spotřebovávat další prostředky uzlů.

Jsou rezervované dva typy prostředků:

- **Procesor**  
    Vyhrazený procesor závisí na typu uzlu a konfiguraci clusteru, což může způsobit neallocatableý procesor z důvodu spuštění dalších funkcí.

   | Jádra procesoru na hostiteli | 1    | 2    | 4    | 8    | 16 | 32|64|
   |---|---|---|---|---|---|---|---|
   |Kube – rezervováno (millicores)|60|100|140|180|260|420|740|

- **Memory (Paměť)**  
    Paměť využíváná nástrojem AKS zahrnuje součet dvou hodnot.

   1. **`kubelet` proces**   
       `kubelet`Démon je nainstalován na všech uzlech agenta Kubernetes pro správu vytváření a ukončování kontejneru. 
   
        Ve výchozím nastavení `kubelet` má démon *paměť. k dispozici<* pravidlo vyřazení 750Mi, které zajistí, aby měl uzel vždy alespoň 750 mi ALLOCATABLE. Když je hostitel pod tím, že je dostupná prahová hodnota paměti, `kubelet` aktivuje jednu z běžících lusků a uvolní paměť na hostitelském počítači.

   2. **Regresivní míra rezervací paměti** pro funkci démona kubelet na správnou funkci (*Kube – rezervováno*).
      - 25% prvních 4 GB paměti
      - 20% z dalších 4 GB paměti (až 8 GB)
      - 10% z dalších 8 GB paměti (až 16 GB)
      - 6% z dalších 112 GB paměti (až 128 GB)
      - 2% libovolné paměti nad 128 GB

Pravidla pro přidělení paměti a procesoru:
* Udržujte uzly agentů v pořádku, včetně některých hostujících systémů, které jsou pro stav clusteru velmi důležité. 
* Způsobí to, že uzel ohlásí méně ALLOCATABLE paměť a procesor, než by by neměl být součástí clusteru Kubernetes. 

Výše uvedené rezervace prostředků se nedají změnit.

Například pokud uzel nabízí 7 GB, bude hlásit 34% paměti, která není ALLOCATABLE, včetně prahové hodnoty pro pevné vyřazení 750Mi.

`0.75 + (0.25*4) + (0.20*3) = 0.75GB + 1GB + 0.6GB = 2.35GB / 7GB = 33.57% reserved`

Kromě rezervací pro samotný Kubernetes vyhradí základní operační systém Node také množství prostředků procesoru a paměti pro udržení funkcí operačního systému.

Související osvědčené postupy najdete v tématu [osvědčené postupy pro základní funkce nástroje Scheduler v AKS][operator-best-practices-scheduler].

### <a name="node-pools"></a>Fondy uzlů

Uzly stejné konfigurace jsou seskupeny dohromady do *fondů uzlů*. Cluster Kubernetes obsahuje alespoň jeden fond uzlů. Počáteční počet uzlů a velikost je definován při vytváření clusteru AKS, který vytváří *výchozí fond uzlů*. Tento výchozí fond uzlů v AKS obsahuje základní virtuální počítače, které spouštějí vaše uzly agentů.

> [!NOTE]
> Aby se zajistilo, že váš cluster funguje spolehlivě, měli byste spustit aspoň dva (2) uzly ve výchozím fondu uzlů.

Můžete škálovat nebo upgradovat cluster AKS s výchozím fondem uzlů. Můžete si vybrat, že budete chtít škálovat nebo upgradovat určitý fond uzlů. V případě operací upgradu jsou spuštěné kontejnery naplánovány na jiných uzlech ve fondu uzlů, dokud nebudou všechny uzly úspěšně upgradovány.

Další informace o použití více fondů uzlů v AKS najdete v tématu [Vytvoření a Správa fondů více uzlů pro cluster v AKS][use-multiple-node-pools].

### <a name="node-selectors"></a>Selektory uzlů

V clusteru AKS s více fondy uzlů možná budete muset sdělit, které fondu uzlů Kubernetes se má použít pro daný prostředek. Například řadiče příchozího přenosu dat by se neměly spouštět na uzlech Windows serveru. 

Selektory uzlů umožňují definovat různé parametry, jako je třeba Node OS, abyste mohli určit, kde má být uzel pod naplánován.

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

Kubernetes používá *lusky* ke spuštění instance aplikace. Pod představuje jednu instanci vaší aplikace. 

Lusky mají typicky mapování 1:1 s kontejnerem. V pokročilých scénářích může objekt pod obsahovat více kontejnerů. Více kontejnerů je naplánováno společně na stejném uzlu a umožňuje kontejnerům sdílet související prostředky.

Když vytvoříte pod, můžete definovat *požadavky* na prostředky pro vyžádání určitého množství prostředků procesoru nebo paměti. Plánovač Kubernetes se pokusí splnit požadavek naplánováním lusků, aby běžely na uzlu s dostupnými prostředky. Můžete také určit maximální počet prostředků, které zabrání tomu, aby na základě využívala příliš mnoho výpočetních prostředků ze základního uzlu. Osvědčeným postupem je zahrnout do všech lusků omezení prostředků, které pomohou plánovači Kubernetes identifikovat potřebné prostředky.

Další informace najdete v tématu životní cyklus [Kubernetes lusky][kubernetes-pods] a [Kubernetes pod][kubernetes-pod-lifecycle].

Pod je logický prostředek, ale úlohy aplikací běží na kontejnerech. Lusky jsou obvykle dočasné a nepoužívané prostředky. Jednotlivě naplánované lusky nedovolují některé z funkcí vysoké dostupnosti a redundance Kubernetes. Místo toho jsou lusky nasazeny a spravovány Kubernetes *řadiči*, jako je například kontrolér nasazení.

## <a name="deployments-and-yaml-manifests"></a>Nasazení a manifesty YAML

*Nasazení* představuje identické lusky spravované kontrolérem nasazení Kubernetes. Nasazení definuje počet *replik* pod, které se mají vytvořit. Plánovač Kubernetes zajišťuje, aby v uzlech v pořádku byly naplánovány další lusky, pokud dojde k problémům s lusky nebo uzly.

Nasazení můžete aktualizovat, aby se změnila konfigurace lusků, použitého obrazu kontejneru nebo připojeného úložiště. Kontroler nasazení:
* Vyprázdní a ukončí daný počet replik.
* Vytvoří repliky z nové definice nasazení.
* Pokračuje v procesu, dokud nebudou aktualizovány všechny repliky v nasazení.

Většina bezstavových aplikací v AKS by měla místo plánování jednotlivých lusků používat model nasazení. Kubernetes může monitorovat stav nasazení a stav, aby se zajistilo, že se v clusteru spustí požadovaný počet replik. V případě, že se naplánujete jednotlivě, lusky se nerestartují, pokud dojde k problému, a pokud jejich aktuální uzel narazí na problém, nepřeplánujete je v pořádku

Pokud vaše aplikace vyžaduje minimální počet dostupných instancí, nechcete rušit rozhodování o správě pomocí procesu aktualizace. *Rozpočty přerušení pod* , definují, kolik replik v nasazení je možné během upgradu nebo aktualizace uzlu vypnout. Například pokud máte ve svém nasazení *pět (5)* replik, můžete definovat přerušení "na" *4 (čtyři)* , aby bylo možné odstranit nebo přeplánovat pouze jednu repliku. Stejně jako u omezení prostředků pod je osvědčeným postupem definování nepřerušených rozpočtů v aplikacích vyžadujících minimální počet replik, které jsou vždy k dispozici.

Nasazení se obvykle vytváří a spravují pomocí `kubectl create` nebo `kubectl apply` . Vytvořte nasazení definováním souboru manifestu ve formátu YAML. 

Následující příklad vytvoří základní nasazení webového serveru NGINX. Nasazení určuje *tři (3)* repliky, které mají být vytvořeny, a vyžaduje, aby byl na kontejneru otevřen port *80* . Pro procesor a paměť se definují taky požadavky na prostředky a omezení.

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

Složitější aplikace je možné vytvořit zahrnutím služeb (jako jsou nástroje pro vyrovnávání zatížení) v rámci manifestu YAML.

Další informace najdete v tématu [nasazení Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Správa balíčků pomocí Helm

[Helm][helm] se běžně používá ke správě aplikací v Kubernetes. Prostředky můžete nasadit pomocí sestavení a používání stávajících veřejných Helm *grafů* , které obsahují zabalenou verzi kódu aplikace a manifestů YAML Kubernetes. Grafy Helm můžete ukládat buď místně, nebo ve vzdáleném úložišti, jako je například [úložiště grafu Azure Container Registry Helm][acr-helm].

Chcete-li použít Helm, nainstalujte do počítače klienta Helm nebo použijte klienta Helm v [Azure Cloud Shell][azure-cloud-shell]. Vyhledejte nebo vytvořte grafy Helm a pak je nainstalujte do svého clusteru Kubernetes. Další informace najdete v tématu [instalace existujících aplikací pomocí Helm v AKS][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets a DaemonSets

Pomocí plánovače Kubernetes spustí kontrolér nasazení repliky na jakémkoli dostupném uzlu s dostupnými prostředky. I když tento přístup může být pro bezstavové aplikace dostačující, kontroler nasazení není ideální pro aplikace, které vyžadují:
* Trvalá konvence pojmenování nebo úložiště. 
* Replika, která má existovat v každém uzlu SELECT v rámci clusteru.

Dva prostředky Kubernetes však umožňují správu těchto typů aplikací:

- *StatefulSets* udržuje stav aplikací nad rámec individuálního životního cyklu, jako je třeba úložiště.
- *DaemonSets* zajistěte spuštěnou instanci na každém uzlu, a to nejdříve v procesu Bootstrap Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Moderní vývoj aplikací se často zaměřuje na bezstavové aplikace. U stavových aplikací, jako jsou ty, které obsahují databázové komponenty, můžete použít *StatefulSets*. Podobně jako u nasazení vytvoří StatefulSet a spravuje aspoň jedno identické pod. Repliky v StatefulSet sledují řádný a sekvenční přístup k nasazení, škálování, upgradu a ukončení. Konvence pojmenování, názvy sítí a úložiště jsou trvalé, protože repliky se přemění pomocí StatefulSet.

Definujte aplikaci ve formátu YAML pomocí `kind: StatefulSet` . Odtud kontroler StatefulSet zpracovává nasazení a správu požadovaných replik. Data se zapisují do trvalého úložiště, které poskytuje Azure Managed Disks nebo soubory Azure. V StatefulSets zůstává původní trvalé úložiště i v případě, že se StatefulSet odstraní.

Další informace najdete v tématu [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliky ve StatefulSet se naplánují a spouštějí napříč libovolným dostupným uzlem v clusteru AKS. Aby bylo zajištěno, že alespoň jeden uzel v sadě na uzlu běží, použijte místo toho DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Pro konkrétní shromažďování a sledování protokolů možná budete muset spustit pod na všech nebo vybraných uzlech. *DaemonSet* můžete použít k nasazení jednoho nebo více identických lusků, ale kontroler DaemonSet zajišťuje, že každý zadaný uzel spustí instanci uzlu pod.

Řadič DaemonSet může naplánovat lusky na uzlech na začátku v procesu spouštění clusteru, než se spustí výchozí Plánovač Kubernetes. Tato možnost zajistí, že lusky ve DaemonSet začínají před tradičními lusky v nasazení nebo v StatefulSet.

Podobně jako StatefulSets je DaemonSet definován jako součást definice YAML pomocí `kind: DaemonSet` .

Další informace najdete v tématu [Kubernetes DaemonSets][kubernetes-daemonset].

> [!NOTE]
> Pokud se používá [doplněk virtuálních uzlů](virtual-nodes-cli.md#enable-virtual-nodes-addon), DaemonSets ve virtuálním uzlu nevytvoří lusky.

## <a name="namespaces"></a>Obory názvů

Kubernetes prostředky, jako jsou lusky a nasazení, se logicky seskupují do *oboru názvů* a rozdělují cluster AKS a omezují vytváření, zobrazování a správu přístupu k prostředkům. Můžete například vytvořit obory názvů pro oddělení obchodních skupin. Uživatelé můžou pracovat jenom s prostředky v rámci svých přiřazených oborů názvů.

![Kubernetes obory názvů pro logicky dělit prostředky a aplikace](media/concepts-clusters-workloads/namespaces.png)

Při vytváření clusteru AKS jsou k dispozici následující obory názvů:

| Obor názvů | Description |  
| ----------------- | ------------- |  
| *default*                                                                                 | Tam, kde jsou lusky a nasazení vytvářeny ve výchozím nastavení, když žádné nejsou k dispozici. V menších prostředích můžete nasazovat aplikace přímo do výchozího oboru názvů, aniž byste museli vytvářet další logické separace. Při interakci s rozhraním API Kubernetes, jako je například s `kubectl get pods` , je použit výchozí obor názvů, pokud není zadán žádný.                                                        |  
| *kube-system* | Kde existují základní prostředky, jako jsou například síťové funkce jako DNS a proxy nebo řídicí panel Kubernetes. Do tohoto oboru názvů obvykle nesadíte vlastní aplikace.                                      |  
| *kube-public*                                                                            | Obvykle se nepoužívá, ale dá se použít k zobrazení prostředků v celém clusteru a může je zobrazit libovolný uživatel.                                                                                    |  


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
