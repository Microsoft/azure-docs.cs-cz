---
title: Koncepty – zabezpečení ve službě Azure Kubernetes Services (AKS)
description: Přečtěte si o zabezpečení ve službě Azure Kubernetes (AKS), včetně komunikace mezi hlavním a uzlem, síťové zásady a Kubernetes tajné klíče.
services: container-service
author: mlearned
ms.service: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.author: mlearned
ms.openlocfilehash: 1d100f17130594ace6169f5840915c88435cb9a8
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/26/2019
ms.locfileid: "67615778"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Koncepce zabezpečení pro aplikace a clustery ve službě Azure Kubernetes (AKS)

Pokud chcete chránit zákaznická data při spouštění úloh aplikací ve službě Azure Kubernetes Service (AKS), je zabezpečení clusteru bezpečnostním aspektem. Kubernetes zahrnuje bezpečnostní komponenty, jako jsou třeba *zásady sítě* a *tajné klíče*. Azure pak přidá komponenty, jako jsou skupiny zabezpečení sítě a Orchestrované upgrady clusteru. Tyto součásti zabezpečení jsou zkombinovány, aby cluster AKS používal nejnovější aktualizace zabezpečení operačního systému a verze Kubernetes, a se zabezpečeným provozem pod a přístupem k citlivým přihlašovacím údajům.

V tomto článku se seznámíte se základními koncepty, které zabezpečují vaše aplikace v AKS:

- [Zabezpečení hlavních součástí](#master-security)
- [Zabezpečení uzlů](#node-security)
- [Upgrade clusteru](#cluster-upgrades)
- [Zabezpečení sítě](#network-security)
- [Tajné kódy Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Hlavní zabezpečení

V AKS jsou hlavní komponenty Kubernetes součástí spravované služby, kterou poskytuje Microsoft. Každý cluster AKS má vlastní jeden tenant vyhrazený Kubernetes hlavní server, který poskytuje Server API, Plánovač atd. Tento hlavní server spravuje a udržuje společnost Microsoft.

Ve výchozím nastavení používá server Kubernetes API veřejnou IP adresu a s plně kvalifikovaným názvem domény (FQDN). Přístup k serveru rozhraní API můžete řídit pomocí Kubernetes řízení přístupu založeného na rolích a Azure Active Directory. Další informace najdete v tématu věnovaném [integraci Azure AD s AKS][aks-aad].

## <a name="node-security"></a>Zabezpečení uzlů

AKS uzly jsou virtuální počítače Azure, které spravujete a udržujete. Uzly Linux spouštějí optimalizovanou distribuci Ubuntu pomocí modulu runtime kontejneru Moby. Uzly Windows serveru (v současnosti ve verzi Preview v AKS) spouští optimalizovanou verzi Windows serveru 2019 a také používají modul runtime kontejneru Moby. Při vytváření nebo navýšení kapacity clusteru AKS se uzly automaticky nasazují s nejnovějšími aktualizacemi a konfiguracemi zabezpečení operačního systému.

Platforma Azure automaticky aplikuje opravy zabezpečení operačního systému na uzly se systémem Linux na noční bázi. Pokud aktualizace zabezpečení operačního systému Linux vyžaduje restart hostitele, neprovádí se automatické restartování. Uzly se systémem Linux můžete restartovat ručně, nebo běžným přístupem je použití [Kured][kured]a open-source démon pro Kubernetes. Kured běží jako [DaemonSet][aks-daemonsets] a monitoruje každý uzel pro přítomnost souboru, který indikuje, že je potřeba restartovat počítač. Restartování se v rámci clusteru spravují pomocí stejného [procesu Cordon a vyprázdnění](#cordon-and-drain) jako upgrade clusteru.

V případě uzlů Windows serveru (aktuálně ve verzi Preview v AKS) web Windows Update nespustí automaticky a použije nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání web Windows Update a vlastního procesu ověřování byste měli provést upgrade ve fondech uzlů Windows serveru v clusteru AKS. Tento proces upgradu vytvoří uzly, na kterých běží nejnovější image a opravy Windows serveru, a pak odebere starší uzly. Další informace o tomto procesu najdete v tématu [upgrade fondu uzlů v AKS][nodepool-upgrade].

Uzly jsou nasazeny do podsítě privátní virtuální sítě bez přiřazených veřejných IP adres. Pro účely řešení potíží a správy je ve výchozím nastavení povolený SSH. Přístup přes SSH je k dispozici pouze pomocí interní IP adresy.

K poskytnutí úložiště používají uzly Azure Managed Disks. Pro většinu velikostí uzlů virtuálních počítačů se jedná o prémiové disky zajištěné vysokým výkonem SSD. Data uložená na spravovaných discích se v rámci platformy Azure automaticky šifrují v klidovém stavu. Kvůli vylepšení redundance jsou tyto disky také bezpečně replikovány v datovém centru Azure.

Prostředí Kubernetes, v AKS nebo jinde, aktuálně nejsou zcela bezpečná pro nepřátelský využití více tenantů. Další funkce zabezpečení, jako jsou *zásady zabezpečení* nebo pokročilejší řízení přístupu na základě role (RBAC) pro uzly, se obtížně využívají. Pro skutečné zabezpečení při spouštění nepřátelských úloh s více klienty však je hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se bude nacházet v celém clusteru, nikoli v jednotlivých uzlech. U těchto typů nepřátelských úloh s více klienty byste měli použít fyzicky izolované clustery. Další informace o způsobech izolace úloh najdete v tématu osvědčené [postupy pro izolaci clusteru v AKS][cluster-isolation].

## <a name="cluster-upgrades"></a>Upgrady clusteru

V případě zabezpečení a dodržování předpisů nebo použití nejnovějších funkcí poskytuje Azure nástroje k orchestraci upgradu clusteru a komponent AKS. Tato orchestrace upgradu zahrnuje hlavní komponenty i agenta Kubernetes. [Seznam dostupných verzí Kubernetes](supported-kubernetes-versions.md) můžete zobrazit pro cluster AKS. Chcete-li spustit proces upgradu, zadejte jednu z těchto dostupných verzí. Azure pak bezpečně cordons a vyprázdní každý uzel AKS a provede upgrade.

### <a name="cordon-and-drain"></a>Cordon a vyprázdnění

Během procesu upgradu se uzly AKS jednotlivě uzavřené z clusteru, takže se na ně neplánují nové lusky. Uzly se pak vyprázdní a upgradují takto:

- Do fondu uzlů je nasazen nový uzel. V tomto uzlu se spouští nejnovější bitová kopie operačního systému a opravy.
- Pro upgrade je identifikován jeden z existujících uzlů. Lusky v tomto uzlu se řádně ukončí a naplánují na ostatních uzlech ve fondu uzlů.
- Tento existující uzel se odstraní z clusteru AKS.
- Další uzel v clusteru je uzavřené a vyprázdněný pomocí stejného procesu, dokud všechny uzly nebudou úspěšně nahrazeny součástí procesu upgradu.

Další informace najdete v tématu [upgrade clusteru AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Zabezpečení sítě

V případě připojení a zabezpečení pomocí místních sítí můžete nasadit cluster AKS do stávajících podsítí virtuální sítě Azure. Tyto virtuální sítě můžou mít připojení VPN typu Site-to-Site VPN nebo Express Route ke své místní síti. Kubernetes příchozí řadiče můžou být definované pomocí privátních interních IP adres, aby služby byly přístupné jenom přes toto interní síťové připojení.

### <a name="azure-network-security-groups"></a>Skupiny zabezpečení sítě Azure

Pokud chcete filtrovat tok provozu ve virtuálních sítích, Azure používá pravidla skupiny zabezpečení sítě. Tato pravidla definují zdrojové a cílové rozsahy IP adres, porty a protokoly, které mají povolený nebo odepřený přístup k prostředkům. Vytvoří se výchozí pravidla, která povolí přenos TLS do serveru rozhraní Kubernetes API. Když vytváříte služby pomocí nástrojů pro vyrovnávání zatížení, mapování portů nebo tras příchozího přenosu dat, AKS automaticky upraví skupinu zabezpečení sítě, aby se provoz správně Flow.

## <a name="kubernetes-secrets"></a>Tajné klíče Kubernetes

*Tajný kód* Kubernetes se používá pro vkládání citlivých dat do lusků, jako jsou přihlašovací údaje nebo klíče pro přístup. Nejdřív vytvoříte tajný klíč pomocí rozhraní Kubernetes API. Pokud definujete pod nebo nasazením, může se požadovat konkrétní tajný klíč. Tajné kódy se poskytují pouze uzlům, které mají naplánovanou hodnotu typu, která ji vyžaduje, a tajný klíč je uložený v *tmpfs*, který není zapsaný na disk. Když se poslední uzel pod uzlem, který vyžaduje tajný klíč, odstraní, tajný kód se odstraní z tmpfs uzlu. Tajné kódy jsou uloženy v daném oboru názvů a lze k nim přistupovat pouze v rámci stejného oboru názvů.

Použití tajných kódů snižuje citlivé informace, které jsou definovány v manifestu pod nebo Service YAML. Místo toho vyžádáte tajný klíč uložený v Kubernetes API serveru jako součást manifestu YAML. Tento přístup poskytuje pouze konkrétní přístup k tajnému kódu pod. Upozorňujeme na to, že soubory manifestu nezpracovaných tajných kódů obsahují tajná data ve formátu Base64 (Další informace najdete v [oficiální dokumentaci][secret-risks] ). Proto by se tento soubor měl považovat za citlivé informace a nikdy se nezavazuje ke správě zdrojového kódu.

## <a name="next-steps"></a>Další postup

Pokud chcete začít s zabezpečením clusterů AKS, přečtěte si téma [upgrade clusteru AKS][aks-upgrade-cluster].

Související osvědčené postupy najdete [v tématu osvědčené postupy pro zabezpečení a upgrady clusterů v AKS][operator-best-practices-cluster-security].

Další informace o základních konceptech Kubernetes a AKS najdete v následujících článcích:

- [Clustery a úlohy Kubernetes/AKS][aks-concepts-clusters-workloads]
- [Identita Kubernetes/AKS][aks-concepts-identity]
- [Virtuální sítě Kubernetes/AKS][aks-concepts-network]
- [Úložiště Kubernetes/AKS][aks-concepts-storage]
- [Škálování Kubernetes/AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[secret-risks]: https://kubernetes.io/docs/concepts/configuration/secret/#risks

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: azure-ad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
[operator-best-practices-cluster-security]: operator-best-practices-cluster-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
