---
title: Koncepty – zabezpečení ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o zabezpečení ve službě Azure Kubernetes (AKS), včetně komunikace mezi hlavním a uzlem, síťové zásady a Kubernetes tajné klíče.
services: container-service
author: mlearned
ms.topic: conceptual
ms.date: 03/11/2021
ms.author: mlearned
ms.openlocfilehash: 3fafbe3f4b1c53f929682f4ca160fb19a5e91918
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105302"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes (AKS)

Zabezpečení clusteru chrání zákaznická data při spouštění úloh aplikací ve službě Azure Kubernetes Service (AKS). 

Kubernetes zahrnuje bezpečnostní komponenty, jako jsou třeba *zásady sítě* a *tajné klíče*. Do té doby Azure zahrnuje komponenty, jako jsou skupiny zabezpečení sítě a Orchestrované upgrady clusterů. AKS kombinuje tyto součásti zabezpečení s:
* Udržujte svůj cluster AKS s nejnovějšími aktualizacemi zabezpečení operačního systému a Kubernetes verzemi.
* Poskytněte zabezpečený provoz pod a přístup k citlivým přihlašovacím údajům.

V tomto článku se seznámíte se základními koncepty, které zabezpečují vaše aplikace v AKS:

- [Koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes (AKS)](#security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks)
  - [Hlavní zabezpečení](#master-security)
  - [Zabezpečení uzlů](#node-security)
    - [Izolace výpočtů](#compute-isolation)
  - [Upgrady clusteru](#cluster-upgrades)
    - [Cordon a vyprázdnění](#cordon-and-drain)
  - [Zabezpečení sítě](#network-security)
    - [Skupiny zabezpečení sítě Azure](#azure-network-security-groups)
  - [Tajné kódy Kubernetes](#kubernetes-secrets)
  - [Další kroky](#next-steps)

## <a name="master-security"></a>Hlavní zabezpečení

V AKS jsou hlavní komponenty Kubernetes součástí poskytované spravované služby, kterou spravuje a udržuje společnost Microsoft. Každý cluster AKS má vlastní jeden tenant vyhrazený Kubernetes hlavní server, který poskytuje Server API, Plánovač atd.

Ve výchozím nastavení používá server Kubernetes API veřejnou IP adresu a plně kvalifikovaný název domény (FQDN). Přístup ke koncovému bodu serveru rozhraní API můžete omezit pomocí [rozsahů povolených IP][authorized-ip-ranges]adres. Můžete také vytvořit plně [soukromý cluster][private-clusters] pro omezení přístupu serveru API k vaší virtuální síti.

Přístup k serveru rozhraní API můžete řídit pomocí Kubernetes řízení přístupu založeného na rolích (Kubernetes RBAC) a Azure RBAC. Další informace najdete v tématu věnovaném [integraci Azure AD s AKS][aks-aad].

## <a name="node-security"></a>Zabezpečení uzlů

AKS uzly jsou virtuální počítače Azure, které spravujete a udržujete. 
* Uzly Linux spouštějí optimalizovanou distribuci Ubuntu pomocí `containerd` modulu runtime kontejneru nebo Moby. 
* Uzly Windows serveru spouštějí optimalizovanou verzi Windows serveru 2019 pomocí `containerd` modulu runtime kontejneru nebo Moby. 

Při vytváření nebo navýšení kapacity clusteru AKS se uzly automaticky nasazují s nejnovějšími aktualizacemi a konfiguracemi zabezpečení operačního systému.

> [!NOTE]
> AKS clustery pomocí:
> * Fondy uzlů Kubernetes verze 1,19 a větší použití `containerd` jako modul runtime kontejneru. 
> * Kubernetes před fondy uzlů v 1.19 používají jako modul runtime kontejneru [Moby](https://mobyproject.org/) (nadřazený Docker).

### <a name="node-security-patches"></a>Opravy zabezpečení uzlů

#### <a name="linux-nodes"></a>Uzly Linux
Platforma Azure automaticky aplikuje opravy zabezpečení operačního systému na uzly se systémem Linux na noční bázi. Pokud aktualizace zabezpečení operačního systému Linux vyžaduje restart hostitele, nebude automaticky restartován. Máte tyto možnosti:
* Ručně restartujte uzly se systémem Linux.
* Použijte [Kured][kured]démon typu open-source restart pro Kubernetes. Kured běží jako [DaemonSet][aks-daemonsets] a monitoruje každý uzel pro soubor, který indikuje, že je potřeba restartovat počítač. 

Restartování se v rámci clusteru spravují pomocí stejného [procesu Cordon a vyprázdnění](#cordon-and-drain) jako upgrade clusteru.

#### <a name="windows-server-nodes"></a>Uzly Windows serveru

V případě uzlů Windows serveru web Windows Update automaticky nespouštět a instalovat nejnovější aktualizace. Naplánujte upgrady fondu uzlů Windows serveru v clusteru AKS kolem web Windows Update pravidelného cyklu vydávání verzí a vlastního procesu ověřování. Tento proces upgradu vytvoří uzly, na kterých běží nejnovější image a opravy Windows serveru, a pak odebere starší uzly. Další informace o tomto procesu najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

### <a name="node-deployment"></a>Nasazení uzlu
Uzly jsou nasazeny do podsítě privátní virtuální sítě bez přiřazených veřejných IP adres. Pro účely řešení potíží a správy je SSH ve výchozím nastavení povolený a přístupný jenom pomocí interní IP adresy.

### <a name="node-storage"></a>Úložiště uzlů
K poskytnutí úložiště používají uzly Azure Managed Disks. Pro většinu velikostí uzlů virtuálních počítačů jsou Azure Managed Disks disky Premium zajištěné vysokým výkonem SSD. Data uložená na spravovaných discích se v rámci platformy Azure automaticky šifrují v klidovém stavu. Za účelem zlepšení redundance jsou Azure Managed Disks bezpečně replikovány v datovém centru Azure.

### <a name="hostile-multi-tenant-workloads"></a>Nepřátelský víceklientské zatížení

V současné době není prostředí Kubernetes bezpečné pro více než nepřátelských využívání více tenantů. Další funkce zabezpečení, jako jsou například *zásady zabezpečení* nebo Kubernetes RBAC pro uzly, efektivně blokují zneužití. Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty Důvěřujte pouze hypervisoru. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. 

U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery. Další informace o způsobech izolace úloh najdete v tématu [osvědčené postupy pro izolaci clusteru v AKS][cluster-isolation].

### <a name="compute-isolation"></a>Izolace výpočtů

Kvůli dodržování předpisů nebo zákonných požadavků můžou některé úlohy vyžadovat vysokou úroveň izolace od ostatních zákaznických úloh. Pro tyto úlohy Azure poskytuje [izolovaným virtuálním](../virtual-machines/isolation.md) počítačům, které se použijí jako uzly agentů v clusteru AKS. Tyto virtuální počítače jsou izolované na konkrétní typ hardwaru a vyhrazené pro jediného zákazníka. 

Při vytváření clusteru AKS nebo přidávání fondu uzlů vyberte [jeden z izolovaných počítačů velikosti](../virtual-machines/isolation.md) jako **Velikost uzlu** .

## <a name="cluster-upgrades"></a>Upgrady clusteru

Azure poskytuje nástroje pro orchestraci upgradu pro upgrade clusteru a komponent AKS, udržování zabezpečení a dodržování předpisů a přístup k nejnovějším funkcím. Tato orchestrace upgradu zahrnuje hlavní komponenty i agenta Kubernetes. 

Pokud chcete spustit proces upgradu, zadejte jednu z [uvedených dostupných verzí Kubernetes](supported-kubernetes-versions.md). Azure pak bezpečně cordons a vyprázdní každý uzel AKS a upgrady.

### <a name="cordon-and-drain"></a>Cordon a vyprázdnění

Během procesu upgradu se uzly AKS jednotlivě uzavřené z clusteru, aby nedocházelo k tomu, aby se nové lusky naplánovaly. Uzly se pak vyprázdní a upgradují takto:

1.  Do fondu uzlů je nasazen nový uzel. 
    * V tomto uzlu se spouští nejnovější bitová kopie operačního systému a opravy.
1. Pro upgrade je identifikován jeden z existujících uzlů. 
1. Lusky v identifikovaném uzlu se řádně ukončí a naplánují na ostatních uzlech ve fondu uzlů.
1. Vyprázdnit uzel se odstraní z clusteru AKS.
1. Kroky 1-4 se opakují, dokud nebudou všechny uzly úspěšně nahrazeny součástí procesu upgradu.

Další informace najdete v tématu [upgrade clusteru AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Zabezpečení sítě

V případě připojení a zabezpečení pomocí místních sítí můžete nasadit cluster AKS do stávajících podsítí virtuální sítě Azure. Tyto virtuální sítě se k místní síti připojují zpátky pomocí sítě VPN typu Site-to-site nebo Express Route. Definujte Kubernetes příchozí řadiče s privátními interními IP adresami, abyste omezili přístup ke službám na připojení k interní síti.

### <a name="azure-network-security-groups"></a>Skupiny zabezpečení sítě Azure

Pro filtrování toku provozu virtuální sítě Azure používá pravidla skupiny zabezpečení sítě. Tato pravidla definují zdrojové a cílové rozsahy IP adres, porty a protokoly povolený nebo odepřený přístup k prostředkům. Vytvoří se výchozí pravidla, která povolí přenos TLS do serveru rozhraní Kubernetes API. Vytvoříte služby pomocí nástrojů pro vyrovnávání zatížení, mapování portů nebo směrování příchozích dat. AKS automaticky upraví skupinu zabezpečení sítě pro přenosový tok.

Pokud pro cluster AKS zadáte vlastní podsíť, neupravujte skupinu zabezpečení **sítě na úrovni** podsítě SPRAVOVANOU nástrojem AKS. Místo toho vytvořte další skupiny zabezpečení sítě na úrovni podsítě a upravte tok přenosů. Ujistěte se, že nejsou v konfliktu s nezbytným provozem, který spravuje cluster, jako je například přístup k nástroji pro vyrovnávání zatížení, komunikace s řídicí rovinou a [výstup][aks-limit-egress-traffic].

### <a name="kubernetes-network-policy"></a>Zásady sítě Kubernetes

Pro omezení síťového provozu mezi lusky v clusteru nabízí AKS podporu pro [zásady sítě Kubernetes][network-policy]. Pomocí zásad sítě můžete povolit nebo odepřít konkrétní síťové cesty v rámci clusteru založené na oborech názvů a selektorech popisků.

## <a name="kubernetes-secrets"></a>Tajné klíče Kubernetes

Pomocí *tajného klíče* Kubernetes vložíte citlivé údaje do lusků, jako jsou přihlašovací údaje nebo klíče pro přístup. 
1. Vytvořte tajný klíč pomocí rozhraní Kubernetes API. 
1. Definice nebo nasazení a vyžádání konkrétního tajného klíče. 
    * Tajné kódy se poskytují pouze uzlům s naplánovaným uzlem, který je vyžaduje.
    * Tajný kód je uložený v *tmpfs* a nezapisuje na disk. 
1. Když odstraníte poslední uzel pod uzlem, který vyžaduje tajný klíč, bude tajný kód odstraněn z tmpfs uzlu. 
   * Tajné kódy jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze v rámci stejného oboru názvů.

Použití tajných klíčů snižuje citlivé informace definované v manifestu pod nebo Service YAML. Místo toho vyžádáte tajný klíč uložený v Kubernetes API serveru jako součást manifestu YAML. Tento přístup poskytuje pouze konkrétní přístup k tajnému kódu pod. 

> [!NOTE]
> Soubory manifestu nezpracovaných tajných kódů obsahují tajná data ve formátu Base64 (Další informace najdete v [oficiální dokumentaci][secret-risks] ). Tyto soubory považovat za citlivé informace a nikdy je nepotvrzují do správy zdrojových kódů.

Tajné kódy Kubernetes jsou uložené v etcd a v úložišti hodnot distribuovaných klíčů. Úložiště Etcd je plně spravované pomocí AKS a [data se v rámci platformy Azure šifrují v klidovém][encryption-atrest]stavu. 

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s zabezpečením clusterů AKS, přečtěte si téma [upgrade clusteru AKS][aks-upgrade-cluster].

Související osvědčené postupy najdete v tématu [osvědčené postupy pro zabezpečení clusteru a upgrady v AKS][operator-best-practices-cluster-security] a [osvědčené postupy pro zabezpečení v AKS][developer-best-practices-pod-security].

Další informace o základních konceptech Kubernetes a AKS najdete v tématech:

- [Clustery a úlohy Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identita Kubernetes/AKS][aks-concepts-identity]
- [Virtuální sítě Kubernetes/AKS][aks-concepts-network]
- [Úložiště Kubernetes/AKS][aks-concepts-storage]
- [Škálování Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks
[encryption-atrest]: ../security/fundamentals/encryption-atrest.md

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: ./managed-aad.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[aks-limit-egress-traffic]: limit-egress-traffic.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
[authorized-ip-ranges]: api-server-authorized-ip-ranges.md
[private-clusters]: private-clusters.md
[network-policy]: use-network-policies.md
