---
title: Principy – zabezpečení služby Azure Kubernetes (AKS)
description: Další informace o zabezpečení ve službě Azure Kubernetes Service (AKS), včetně komunikace master a uzel, zásad sítě a tajné klíče Kubernetes.
services: container-service
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 10/16/2018
ms.author: iainfou
ms.openlocfilehash: 2c6569d92913a3cff9ee51529dd381386ed2a792
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55818987"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes Service (AKS)

Zabezpečení clusteru chráníme vaše zákaznická data, jako jste spouštěli aplikaci ve službě Azure Kubernetes Service (AKS), je důležitým aspektem. Kubernetes zahrnuje zabezpečení součásti, například *zásady sítě, které* a *tajných kódů*. Azure pak přidá do komponent, jako jsou skupiny zabezpečení sítě a orchestrované upgrady clusteru. Tyto součásti zabezpečení se spojí dohromady a udržovat svůj cluster AKS během nejnovější aktualizace zabezpečení operačního systému a verzí Kubernetes a s zabezpečení pod provoz a přístup k citlivé přihlašovací údaje.

Tento článek představuje základní koncepty, které zabezpečení aplikací ve službě AKS:

- [Hlavní součásti zabezpečení](#master-security)
- [Zabezpečení uzlu](#node-security)
- [Upgrade clusteru](#cluster-upgrades)
- [Zabezpečení sítě](#network-security)
- Tajné klíče Kubernetes

## <a name="master-security"></a>Hlavní zabezpečení

Hlavní součásti Kubernetes ve službě AKS, jsou součástí spravované služby poskytované společností Microsoft. Každý cluster AKS má své vlastní hlavní Kubernetes jedním klienty, vyhrazené poskytnout serveru rozhraní API, Plánovač atd. Tento hlavní je spravovaný a udržovaný microsoftem

Ve výchozím nastavení na serveru Kubernetes API používá veřejnou IP adresu a s plně kvalifikovaný název (plně kvalifikovaný název domény). Můžete řídit přístup k rozhraní API serveru pomocí řízení přístupu na základě rolí Kubernetes a Azure Active Directory. Další informace najdete v tématu [integrace Azure AD s AKS][aks-aad].

## <a name="node-security"></a>Zabezpečení uzlu

Uzly AKS jsou virtuální počítače s Azure, které můžete spravovat a udržovat. V uzlech je spuštěný optimalizované distribuce systému Ubuntu Linux pomocí Dockeru kontejner modulu runtime. Při vytváření nebo škálování AKS cluster je nejnovější aktualizace zabezpečení operačního systému a konfigurace se automaticky nasadí uzly.

Platforma Azure automaticky použije opravy zabezpečení operačního systému pro uzly přes noc. Pokud aktualizace zabezpečení operačního systému, vyžaduje restartování hostitele, že restartování se neprovádí. Můžete ručně restartovat uzly, nebo běžným přístupem je použití [Kured][kured], open source restartování démona pro Kubernetes. Kured běží jako [DaemonSet] [aks daemonset] a sleduje každý uzel na přítomnost souboru označující, že je vyžadován restart. Restartování se spravují v clusteru pomocí stejných [kordon a výpusť procesu](#cordon-and-drain) jako upgradu clusteru.

Uzly jsou nasazené do privátní virtuální síť podsíť s žádné veřejné IP adresy přiřazené. Pro účely řešení potíží a správu SSH ve výchozím nastavení zapnutá. Tento přístup přes SSH je dostupný pouze pomocí interní IP adresa. Pro další omezení přístupu rozsah IP pro uzly AKS. je možné pravidla skupiny zabezpečení sítě Azure. Odstraňuje se pravidlo výchozí skupiny zabezpečení sítě SSH a zakázat službu SSH na uzlech brání v provádění úloh údržby na platformě Azure.

K poskytování úložiště, uzly pomocí Azure Managed Disks. Pro většinu velikostí virtuálních počítačů uzel jsou založená na jednotkách SSD výkonné disky úrovně Premium. Data uložená na spravovaných discích se automaticky šifrují při nečinnosti v rámci platformy Azure. Ke zlepšení redundance, jsou zároveň se bezpečně tyto disky replikovat v rámci datového centra Azure.

Prostředí Kubernetes v AKS nebo jinde, aktuálně nejsou zcela bezpečný pro použití v nehostinném prostředí více tenantů. Další bezpečnostní funkce, jako *zásady zabezpečení Pod* nebo další prvky velice přesně kontrolovat přístup na základě rolí (RBAC) pro uzly ztížit zneužití. True zabezpečení při spouštění úloh v nehostinném prostředí více tenantů, je hypervisor pouze úroveň zabezpečení, které byste měli věřit. Domény zabezpečení pro Kubernetes se změní celý cluster, nikoli jednotlivých uzlů. Pro tyto typy úloh nehostinném prostředí více tenantů měli byste použít fyzicky izolované clustery. Další informace o způsobech k izolaci úloh najdete v tématu [osvědčené postupy pro zajištění izolace clusteru ve službě AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Upgrade clusteru

Zabezpečení a dodržování předpisů, nebo pokud chcete používat nejnovější funkce Azure poskytuje nástroje k orchestraci upgrade clusteru AKS a komponent. Tento upgrade Orchestrace zahrnuje obě Kubernetes hlavní server a agent součásti. Zobrazit seznam dostupných verzí Kubernetes pro váš cluster AKS. Chcete-li zahájit proces upgradu, zadáte některou z těchto dostupných verzí. Azure pak bezpečně cordons pozastavuje jednotlivých uzlů AKS a provádí upgrade.

### <a name="cordon-and-drain"></a>Cordon a vyprazdňování

Během procesu upgradu jsou uzly AKS jednotlivě kapacity z clusteru tak nových podů nejsou na ně. Uzly jsou pak Vyprázdněné a upgradovat takto:

- Existující podů se řádně ukončit a naplánováno na zbývajících uzlech.
- Po restartování uzlu, na dokončení procesu upgradu a pak spojení zpět do clusteru AKS.
- Podů jsou naplánovány ke spuštění na nich znovu.
- Další uzel v clusteru je uzavřené a Vyprázdněné pomocí stejného procesu, dokud všechny uzly jsou úspěšně upgradoval.

Další informace najdete v tématu [upgradu a AKS clusteru][aks-upgrade-cluster].

## <a name="network-security"></a>Zabezpečení sítě

Pro připojení a zabezpečení v sítích na pracovišti můžete nasadit cluster AKS do existující podsítě virtuální sítě Azure. Tyto virtuální sítě může mít Azure připojení Site-to-Site VPN nebo Express Route zpět do místní sítě. Řadiče příchozího přenosu dat Kubernetes může být definované s soukromé, interní IP adresy, takže služby jsou dostupné jenom přes toto připojení k interní síti.

### <a name="azure-network-security-groups"></a>Skupiny zabezpečení sítě Azure

Chcete-li filtrovat toku provozu ve virtuálních sítích, Azure využívá pravidla skupiny zabezpečení sítě. Tato pravidla definují zdrojové a cílové rozsahy IP adres, porty a protokoly, které se povoluje nebo odepírá přístup k prostředkům. Výchozí pravidla se vytvářejí pro povolení provozu TLS na serveru Kubernetes API a pro přístup přes SSH k uzlům. Při vytváření služby pomocí nástroje pro vyrovnávání zatížení, mapování portů nebo příchozího přenosu dat trasy, AKS automaticky změní skupina zabezpečení sítě pro provoz volný průběh.

## <a name="kubernetes-secrets"></a>Tajné klíče Kubernetes

Kubernetes *tajný kód* slouží k vložení citlivá data do podů, jako je například přístup k přihlašovacím údajům nebo klíčům. Nejprve vytvoříte tajného kódu pomocí rozhraní API Kubernetes. Při definování pod nebo nasazení mohou být požadována určitého tajného klíče. Tajné kódy jsou poskytovány pouze pro uzly, které mají naplánované pod, kterého je vyžadováno, a tajný kód je uložen v *tmpfs*není napsaných na disk. Při odstranění posledního podu na uzel, který se vyžaduje tajný kód. tajný kód je odstraněn z tmpfs uzlu. Tajné klíče jsou uložené v daném oboru názvů a je přístupný pouze pomocí podů v rámci stejného oboru názvů.

Použití tajných kódů snižuje citlivých informací, která je definována v podu nebo service YAML manifest. Místo toho si vyžádáte tajného klíče uložené v serveru Kubernetes API jako součást YAML manifestu. Tento přístup poskytuje pouze konkrétní pod přístup k tajný kód.

## <a name="next-steps"></a>Další postup

Začínáme se zabezpečením vašich clusterů AKS najdete v článku [Upgrade clusteru AKS][aks-upgrade-cluster].

Další informace o základní Kubernetes a AKS koncepty najdete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / identitě AKS][aks-concepts-identity]
- [Kubernetes / virtuální sítě AKS][aks-concepts-network]
- [Kubernetes / AKS storage][aks-concepts-storage]
- [Kubernetes snížit nebo navýšit AKS][aks-concepts-scale]

<!-- LINKS - External -->
[kured]: https://github.com/weaveworks/kured
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/

<!-- LINKS - Internal -->
[aks-daemonsets]: concepts-clusters-workloads.md#daemonsets
[aks-upgrade-cluster]: upgrade-cluster.md
[aks-aad]: aad-integration.md
[aks-concepts-clusters-workloads]: concepts-clusters-workloads.md
[aks-concepts-identity]: concepts-identity.md
[aks-concepts-scale]: concepts-scale.md
[aks-concepts-storage]: concepts-storage.md
[aks-concepts-network]: concepts-network.md
[cluster-isolation]: operator-best-practices-cluster-isolation.md
