---
title: Principy – základy Kubernetes pro služby Azure Kubernetes (AKS)
description: Naučte se základní cluster a zatížení součástí Kubernetes a jak souvisejí s funkcí ve službě Azure Kubernetes Service (AKS)
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: f5695e52528c3384c46c49c5c5ec2e451bd0be7c
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2018
ms.locfileid: "52998097"
---
# <a name="kubernetes-core-concepts-for-azure-kubernetes-service-aks"></a>Základní koncepty Kubernetes pro Azure Kubernetes Service (AKS)

Jako vývoj aplikací se přesunul směrem k přístupu založených na kontejnerech, je potřeba orchestrovat a spravovat propojených prostředků důležitá. Kubernetes je přední platforma, která poskytuje schopnost poskytovat spolehlivé plánování úloh aplikace odolné proti chybám. Azure Kubernetes Service (AKS) je spravované platformy Kubernetes nabízející další zjednodušuje nasazení a správu založených na kontejnerech aplikace.

Tento článek představuje základní komponenty infrastruktury Kubernetes, jako *předlohy clusteru*, *uzly*, a *fondy uzlů*. Prostředky úlohy, jako *podů*, *nasazení*, a *nastaví* jsou rovněž popsány spolu s postupy skupiny prostředků do *obory názvů*.

## <a name="what-is-kubernetes"></a>Co je Kubernetes?

Kubernetes je rychle se vyvíjejícím platforma, která spravuje kontejnerových aplikací a jejich přidružených součásti sítě a úložiště. Zaměřuje se na úloh aplikací, nikoli základní komponenty infrastruktury. Kubernetes poskytuje deklarativní přístup k nasazení se opírá o robustní sadu rozhraní API pro operace správy.

Můžete sestavit a spustit moderních přenosné mikroslužbových aplikací, které využívají samosprávné Kubernetes Orchestrace a Správa dostupnosti těchto součástí aplikace. Kubernetes podporuje bezstavových a stavových aplikací jako průběh týmy prostřednictvím přijetí aplikací založených na mikroslužbách.

Jako otevřenou platformu Kubernetes umožňuje sestavovat aplikace s Upřednostňovaný programovací jazyk, operační systém, knihovny nebo zasílání zpráv Service bus. Existující průběžnou integraci a nástroje pro průběžné doručování (CI/CD) můžete integrovat s využitím Kubernetes k plánování a nasazení vydání.

Azure Kubernetes Service (AKS) nabízí spravované služby Kubernetes, která snižuje složitost pro nasazení a základní úlohy správy, včetně koordinace upgrady. Hlavní servery clusteru AKS spravuje platformy Azure a budete platit jenom za uzlů AKS, na kterých běží vaše aplikace. AKS je postavený na modul open source Azure Kubernetes Service (aks-engine).

## <a name="kubernetes-cluster-architecture"></a>Architektura clusteru Kubernetes

Kubernetes cluster je rozdělena na dvě součásti:

- *Hlavní clusteru* uzly poskytují základní služby Kubernetes a Orchestrace úloh aplikací.
- *Uzly* spouštěli aplikaci.

![Součásti hlavní větev a uzlu clusteru Kubernetes](media/concepts-clusters-workloads/cluster-master-and-nodes.png)

## <a name="cluster-master"></a>Hlavní server clusteru

Při vytváření clusteru AKS předlohy clusteru je automaticky vytvořen a nakonfigurován. Tento hlavní cluster se poskytuje jako spravovaných prostředků Azure odvozená z uživatele. Se neúčtují žádné poplatky pro hlavní uzel clusteru, pouze uzly, které jsou součástí clusteru AKS.

Hlavní clusteru zahrnuje následující součásti core Kubernetes:

- *kube apiserver* – rozhraní API serveru je, jak jsou vystaveny základní rozhraní Kubernetes API. Tato součást poskytuje interakce pro nástroje pro správu, například `kubectl` nebo řídicí panel Kubernetes.
- *etcd* – Pokud chcete zachovat stav clusteru Kubernetes a konfiguraci s vysokou dostupností *etcd* je úložiště hodnot klíče v Kubernetes.
- *Plánovač kube* – při vytváření nebo škálování aplikací, Plánovač Určuje, jaké uzly mohli spouštět úlohy a spustí je.
- *Správce kontroléru kube* – The správce Kontroléru dohlíží na několik menších řadiče, které provádějí akce, jako je replikace podů a zpracování uzlu operace.

Poskytuje předlohu jednoho tenanta clusteru AKS pomocí vyhrazeného serveru rozhraní API, Plánovač atd. Definování počtu a velikosti uzlů a platformy Azure nakonfiguruje zabezpečenou komunikaci mezi hlavní clusteru a uzlů. Interakce s hlavní větví clusteru dojde k přes rozhraní API Kubernetes, jako například `kubectl` nebo řídicí panel Kubernetes.

Tento hlavní spravovaný cluster znamená, že není potřeba konfigurovat součásti, jako jsou s vysokou dostupností *etcd* úložiště, ale také znamená, že hlavní clusteru nelze přímý přístup. Upgrade na Kubernetes jsou orchestrované prostřednictvím rozhraní příkazového řádku Azure nebo webu Azure portal, který provede upgrade hlavní clusteru a pak uzly. Řešení potíží s informace o možných problémech, můžete zkontrolovat hlavní protokoly clusteru pomocí Azure Log Analytics.

Pokud je potřeba nakonfigurovat hlavní clusteru specifickým způsobem nebo potřebujete přímý přístup k nim, můžete nasadit vlastní cluster Kubernetes pomocí [aks-engine][aks-engine].

## <a name="nodes-and-node-pools"></a>Uzly a fondy uzlů

Ke spuštění vaší aplikace a podpůrných služeb, budete potřebovat Kubernetes *uzel*. AKS cluster má jeden nebo více uzlů, které je virtuální počítač Azure (VM), na kterém běží uzel součástí Kubernetes a modul runtime kontejneru:

- `kubelet` Je agent Kubernetes, který zpracovává požadavky Orchestrace z clusteru hlavní a plánování spouštění požadovanou kontejnery.
- Virtuální síť se zpracovává souborem *kube proxy* na každém uzlu. Trasy proxy serveru síťový provoz a spravuje přidělování IP adres pro služby a tyto pody.
- *Runtime kontejneru* je komponenta, která umožňuje spouštět a interakci s dalšími materiály, jako je například virtuální síť a úložiště kontejnerizovaných aplikací. Ve službě AKS Docker slouží jako kontejner modulu runtime.

![Virtuální počítač Azure a podpůrné prostředky pro uzel Kubernetes](media/concepts-clusters-workloads/aks-node-resource-interactions.png)

Velikost virtuálního počítače Azure pro uzly definuje počet procesorů, kolik paměti a velikosti a typu úložiště (jako třeba vysoce výkonné SSD nebo HDD pravidelných). Pokud očekáváte, třeba pro aplikace, které vyžadují kopírování velkých objemů procesoru a paměti nebo vysoce výkonné úložiště, podle toho naplánujte velikost uzlu. Můžete také škálovat počet uzlů ve vašem clusteru AKS podle potřeby.

Ve službě AKS image virtuálního počítače pro uzly ve vašem clusteru momentálně založené na Ubuntu Linuxu. Při vytváření clusteru AKS nebo vertikálně navýšit kapacitu počtu uzlů, Platforma Azure vytvoří požadovaný počet virtuálních počítačů a nakonfiguruje je. Neexistuje žádná ruční konfigurace do mezipaměti.

Pokud je potřeba pomocí jiného hostitele operačního systému, modul runtime kontejneru, nebo použít vlastní balíčky, můžete nasadit vlastní cluster Kubernetes pomocí [aks-engine][aks-engine]. Nadřazeného `aks-engine` uvolní funkce a poskytnout informace o možnostech konfigurace předtím, než se oficiálně podporuje v clusteru AKS. Například pokud chcete používat kontejnery Windows nebo modul runtime kontejneru než Dockeru, můžete použít `aks-engine` můžete nakonfigurovat a nasadit cluster Kubernetes, který bude vyhovovat vašim aktuálním potřebám.

### <a name="resource-reservations"></a>Rezervace prostředků

Není nutné spravovat základní součásti Kubernetes na každém uzlu, jako *kubelet*, *kube proxy*, a *kube-dns*, ale některé z dostupných spotřebují výpočetní prostředky. Pokud chcete zachovat uzel výkon a funkčnost, jsou vyhrazené následujících výpočetních prostředků na každém uzlu:

- **Procesor** – 60ms
- **Paměť** – 20 % až 4 GB

Tyto rezervace znamená, že množství dostupné procesoru a paměti pro vaše aplikace může zobrazit menší než samotný uzel obsahuje. Pokud existují omezení zdrojů vzhledem k počtu aplikací, které spouštíte, tyto rezervace zkontrolujte využití procesoru a paměť zůstane k dispozici pro součásti jádra Kubernetes. Rezervace prostředků se nedá změnit.

Příklad:

- **Standard DS2 v2** velikost uzlu obsahuje 2 virtuálních procesorů a 7 GB paměti
    - 20 % 7 paměti GiB = 1,4 GB
    - Celkem *(7 1.4) = 5.6 GiB* paměti je k dispozici pro uzel
    
- **Standardní v3 E4s** velikost uzlu obsahuje 4 virtuální procesory a 32 GiB paměti
    - 20 % paměti 32 GiB = 6.4 GiB, ale AKS pouze rezervuje maximálně 4 GB
    - Celkem *(32-4) = 28 GiB* je k dispozici pro uzel
    
Základní uzel operačního systému také vyžaduje určitý objem prostředků procesoru a paměti k dokončení vlastní základních funkcí.

### <a name="node-pools"></a>Fondy uzlů

Uzlů se stejnou konfigurací jsou seskupeny do *fondy uzlů*. Kubernetes cluster obsahuje jeden nebo více fondy uzlů. Počáteční počet uzlů a velikosti jsou definována při vytvoření clusteru AKS, který vytvoří *výchozí fond uzlů*. Tento výchozí fond uzlů ve službě AKS obsahuje základní virtuální počítače, na kterých běží vaše agenta uzly.

Při škálování nebo upgrade clusteru AKS, akce se provádí na výchozí fond uzlů. Pro upgrade operace naplánováno spuštěné kontejnery na jiných uzlech ve fondu uzlů, dokud se úspěšně upgradoval na všech uzlech.

## <a name="pods"></a>Podů

Používá Kubernetes *podů* ke spuštění instance vaší aplikace. Pod představuje jednu instanci vaší aplikace. Podů obvykle mají mapování 1:1 s kontejnerem, i když existuje pokročilé scénáře kde pod může obsahovat několik kontejnerů. Tyto pody více kontejnerů jsou naplánovány společně na stejném uzlu a povolit kontejnery sdílet související prostředky.

Při vytváření podu můžete definovat *omezení prostředků* požádat o určitý objem prostředků procesoru nebo paměti. Plánovač Kubernetes pokusí se naplánovat podů pro spuštění na uzel s prostředky pro splnění požadavku. Můžete také zadat omezení maximální prostředků, které brání v dané pod ve využívání příliš mnoho výpočetních prostředků z podkladové uzlu. Osvědčeným postupem je zahrnují omezení prostředků pro všechny podů umožňující plánovači Kubernetes pochopit, jaké prostředky jsou potřebné a povolené.

Další informace najdete v tématu [podů Kubernetes] [ kubernetes-pods] a [Kubernetes pod životního cyklu][kubernetes-pod-lifecycle].

Pod je logický prostředků, ale kontejnery jsou, ve kterém se spouští úlohy aplikací. Podů jsou obvykle dočasné, uvolnitelné prostředky a jednotlivě naplánované podů neproběhly některé vysokou dostupnost a redundance funkcí, které poskytuje Kubernetes. Místo toho se nasazují a spravují pomocí Kubernetes podů obvykle *řadiče*, jako je například nasazení Kontroleru.

## <a name="deployments-and-yaml-manifests"></a>Manifesty nasazení a YAML

A *nasazení* představuje jeden nebo více identické podů spravuje adaptérem nasazení Kubernetes. Nasazení definuje počet *repliky* (pod) Pokud chcete vytvořit, a Plánovač Kubernetes se zajistí, že pokud podů nebo uzly, dojde k potížím, další pody jsou naplánovány na uzly v dobrém stavu.

Můžete aktualizovat nasazení měnit konfiguraci podů, použít image kontejneru, nebo připojené úložiště. Nasazení řadiče pozastavuje a ukončí daný počet replik, vytvoří repliky z definice nového nasazení a proces pokračuje, dokud nebudou aktualizovány všechny repliky v nasazení.

Většina bezstavové aplikace ve službě AKS by měly používat model nasazení namísto plánování jednotlivých pody. Kubernetes můžete monitorovat stav a stav nasazení a zkontrolujte, že požadovaný počet replik spuštěných v rámci clusteru. Při plánování pouze jednotlivé podů, nejsou tyto pody restartovat, pokud dojde k potížím a nemění na uzly v dobrém stavu, pokud jejich aktuálního uzlu dojde k potížím.

Pokud aplikace vyžaduje kvorum instancí mít vždycky k dispozici pro rozhodnutí o správu, nechcete, aby procesu aktualizace narušit tuto možnost. *Pod přerušení rozpočty* umožňuje definovat, kolik repliky v nasazení můžete třeba odstavit během upgradu aktualizace nebo uzel. Pokud máte například *5* repliky ve vašem nasazení, můžete definovat pod přerušení *4* tak, aby povolovala pouze jednu repliku z se odstraní a znovu naplánována v čase. Stejně jako u pod omezení prostředků, osvědčeným postupem je definovat pod přerušení rozpočty na aplikace, které vyžadují minimální počet replik vždy být k dispozici.

Nasazení se obvykle vytváří a spravují s `kubectl create` nebo `kubectl apply`. Chcete-li vytvořit nasazení, definujete soubor manifestu ve formátu YAML (YAML Ain't Markup Language). Následující příklad vytvoří základní nasazení webový server NGINX. Určuje, nasazení *3* repliky, který se má vytvořit a tento port *80* být otevřený v kontejneru. Omezení a požadavky na zdroje jsou také definovány pro procesor a paměť.

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

Složitějších aplikací můžete vytvořit také zahrnutím služeb, jako je nástroje pro vyrovnávání zatížení v rámci manifestu YAML.

Další informace najdete v tématu [nasazení Kubernetes][kubernetes-deployments].

### <a name="package-management-with-helm"></a>Správa balíčků s nástrojem Helm.

Běžným přístupem ke správě aplikací v Kubernetes je s [Helm][helm]. Můžete vytvořit a použít stávající veřejnou Helm *grafy* , které obsahují zabalená verze kódu aplikace a manifestů nasazení prostředků Kubernetes YAML. Tyto grafy Helm může být uložená místně, nebo často ve vzdáleném úložišti, jako například [úložiště grafu helmu registru kontejneru Azure][acr-helm].

Pokud chcete používat Helm, volá komponentu serveru *Tiller* je nainstalován v clusteru Kubernetes. Tiller spravuje instalaci grafy v rámci clusteru. Klienta Helm, samotného je nainstalovaný místně na počítači nebo je možné v rámci [Azure Cloud Shell][azure-cloud-shell]. Vyhledejte nebo vytvářejte grafy Helm klienta a pak je nainstalujte k vašemu clusteru Kubernetes.

![Příkaz Helm zahrnuje komponentu klienta a Tiller komponenty na straně serveru, která vytváří prostředky do clusteru Kubernetes](media/concepts-clusters-workloads/use-helm.png)

Další informace najdete v tématu [instalace aplikací s nástrojem Helm ve službě Azure Kubernetes Service (AKS)][aks-helm].

## <a name="statefulsets-and-daemonsets"></a>StatefulSets a DaemonSets

Nasazení řadiče Kubernetes Plánovač používá ke spuštění daný počet replik na libovolný uzel k dispozici se dostupné prostředky. Tento přístup pomocí nasazení může být dostatečné pro bezstavové aplikace, ale ne pro aplikace, které vyžadují trvalé zásady vytváření názvů nebo úložiště. Pro aplikace, které vyžadují repliky existovat na každém uzlu, nebo vybrané uzly v rámci clusteru nasazení Kontroleru nevypadá na tom, jak repliky jsou distribuovány napříč uzly.

Existují dva prostředky Kubernetesu, které vám umožní spravovat tyto typy aplikací:

- *StatefulSets* – zachovat stav aplikací nad rámec životního cyklu jednotlivých pod, jako je například úložiště.
- *DaemonSets* – zajistěte spuštěné instance na každém uzlu v rané fázi procesu bootstrap Kubernetes.

### <a name="statefulsets"></a>StatefulSets

Vývoj moderních aplikací často cílem pro bezstavové aplikace, ale *StatefulSets* lze použít pro stavových aplikací, jako jsou třeba aplikace, které zahrnují databáze součásti. StatefulSet je podobný nasazení jednoho nebo více identické podů se vytváří a spravují. Repliky v StatefulSet podle řádné sekvenční přístup pro nasazení, škálování, inovace a ukončení. S StatefulSet zachovat zásady vytváření názvů, názvů sítě a úložiště jako repliky jsou znovu naplánována.

Můžete definovat aplikaci pomocí formátu YAML `kind: StatefulSet`, a kontroler StatefulSet pak obsluhuje, nasazení a správu požadované replik. Data se zapisují do trvalého úložiště Azure Managed Disks nebo soubory Azure k dispozici. Díky StatefulSets zůstávají základní trvalého úložiště i při odstranění StatefulSet.

Další informace najdete v tématu [Kubernetes StatefulSets][kubernetes-statefulsets].

Repliky v StatefulSet jsou naplánované a spustit mezi všechny dostupné uzly v clusteru AKS. Pokud je potřeba zajistit tohoto podu alespoň jeden v sady běží na uzlu, můžete místo toho použít DaemonSet.

### <a name="daemonsets"></a>DaemonSets

Pro konkrétní kolekci nebo potřeby monitorování možná bude nutné ke spuštění dané pod na všechny nebo vybrané uzly. A *DaemonSet* je znovu použít k nasazení jednoho nebo více identické podů, ale řadič DaemonSet zajistí, že každý uzel zadán běží instance pod.

Kontroler DaemonSet můžete naplánovat podů na uzlech v rané fázi procesu spuštění clusteru než výchozí, které se má spuštěn Plánovač Kubernetes. Tato možnost zajistí spuštění podů v DaemonSet předtím, než tradiční podů v nasazení nebo StatefulSet jsou naplánovány.

Jako StatefulSets, DaemonSet je definován jako součást definice YAML pomocí `kind: DaemonSet`.

Další informace najdete v tématu [Kubernetes DaemonSets][kubernetes-daemonset].

## <a name="namespaces"></a>Obory názvů

Prostředky Kubernetesu, jako je například podů a nasazení, jsou logicky rozdělena do *obor názvů*. Tato seskupení poskytují způsob, jak logicky rozdělte AKS cluster a omezit přístup k vytváření, zobrazení a správě prostředků. Můžete vytvořit samostatné obchodní skupiny, například obory názvů. Pouze mohou uživatelé komunikovat s prostředky v rámci jejich přiřazené obory názvů.

![Obory názvů Kubernetes a logicky rozdělit prostředky a aplikace](media/concepts-clusters-workloads/namespaces.png)

Při vytváření clusteru AKS následující obory názvů jsou k dispozici:

- *výchozí* -tento obor názvů je, kde podů a nasazení se vytvoří ve výchozím nastavení při žádný nezadá. V menších prostředí můžete nasazovat aplikace přímo do výchozí obor názvů bez vytvoření další logické separace. Pokud pracujete s rozhraní Kubernetes API jako s `kubectl get pods`, výchozí obor názvů je používán, když není zadaný žádný.
- *kube-system* -tento obor názvů je, kde základními prostředky existují, jako jsou síťové funkce, jako jsou DNS a proxy serveru nebo řídicí panel Kubernetes. Obvykle není nasazení vlastních aplikací do tohoto oboru názvů.
- *kube-public* – tento obor názvů se obvykle nepoužívá, ale můžete použít pro prostředky být viditelné v rámci celého clusteru a je zobrazit všichni uživatelé.

Další informace najdete v tématu [obory názvů Kubernetes][kubernetes-namespaces].

## <a name="next-steps"></a>Další postup

Tento článek popisuje některé základní součásti Kubernetes a jak se vztahují k clusteru AKS. Další informace o základní Kubernetes a AKS koncepty najdete v následujících článcích:

- [Kubernetes / AKS přístup a identita][aks-concepts-identity]
- [Kubernetes / zabezpečení AKS][aks-concepts-security]
- [Kubernetes / virtuální sítě AKS][aks-concepts-network]
- [Kubernetes / AKS storage][aks-concepts-storage]
- [Kubernetes snížit nebo navýšit AKS][aks-concepts-scale]

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
