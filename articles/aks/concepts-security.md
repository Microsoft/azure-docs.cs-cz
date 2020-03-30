---
title: Koncepty – zabezpečení ve službách Azure Kubernetes Services (AKS)
description: Další informace o zabezpečení ve službě Azure Kubernetes Service (AKS), včetně hlavní komunikace a komunikace uzlů, zásad y sítě a tajných kódů Kubernetes.
services: container-service
ms.topic: conceptual
ms.date: 03/01/2019
ms.openlocfilehash: 7238e6cd7ab3625e2953a4408c82802d43372256
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77595939"
---
# <a name="security-concepts-for-applications-and-clusters-in-azure-kubernetes-service-aks"></a>Koncepty zabezpečení pro aplikace a clustery ve službě Azure Kubernetes Service (AKS)

Chcete-li chránit vaše zákaznická data při spouštění úloh aplikací ve službě Azure Kubernetes Service (AKS), je zabezpečení vašeho clusteru klíčovým aspektem. Kubernetes obsahuje součásti zabezpečení, jako jsou *zásady sítě* a *tajné kódy*. Azure pak přidá součásti, jako jsou skupiny zabezpečení sítě a řízené upgrady clusteru. Tyto součásti zabezpečení jsou kombinovány tak, aby váš cluster AKS běžel s nejnovějšími aktualizacemi zabezpečení operačního systému a verzemi Kubernetes a se zabezpečeným provozem podu a přístupem k citlivým přihlašovacím údajům.

Tento článek představuje základní koncepty, které zabezpečují vaše aplikace v AKS:

- [Zabezpečení hlavních součástí](#master-security)
- [Zabezpečení uzlu](#node-security)
- [Upgrady clusteru](#cluster-upgrades)
- [Zabezpečení sítě](#network-security)
- [Tajné klíče Kubernetes](#kubernetes-secrets)

## <a name="master-security"></a>Hlavní zabezpečení

V AKS jsou hlavní součásti Kubernetes součástí spravované služby poskytované společností Microsoft. Každý cluster AKS má svůj vlastní jeden tenantský, vyhrazený hlavní server Kubernetes, který poskytuje server ROZHRANÍ API, plánovač atd. Tento předloha je spravován a spravován společností Microsoft.

Ve výchozím nastavení používá server Api Kubernetes veřejnou IP adresu a plně kvalifikovaný název domény (FQDN). Přístup k serveru rozhraní API můžete řídit pomocí ovládacích prvků přístupu založených na rolích Kubernetes a služby Azure Active Directory. Další informace najdete [v tématu integrace Azure AD s AKS][aks-aad].

## <a name="node-security"></a>Zabezpečení uzlu

AKS uzly jsou virtuální počítače Azure, které spravujete a spravujete. Linuxové uzly běží optimalizovanou distribuci Ubuntu pomocí runtime kontejneru Moby. Uzly Windows Serveru (aktuálně ve verzi Preview v AKS) spouštějí optimalizovanou verzi Windows Serveru 2019 a také používají runtime kontejneru Moby. Při vytvoření nebo škálování clusteru AKS se uzly automaticky nasadí s nejnovějšími aktualizacemi a konfiguracemi zabezpečení operačního systému.

Platforma Azure automaticky aplikuje opravy zabezpečení operačního systému na linuxové uzly na noční bázi. Pokud aktualizace zabezpečení operačního systému Linux vyžaduje restartování hostitele, restartování se neprovádí automaticky. Můžete ručně restartovat linuxové uzly, nebo společný přístup je použití [Kured][kured], open-source reboot daemon pro Kubernetes. Kured běží jako [DaemonSet][aks-daemonsets] a monitoruje každý uzel pro přítomnost souboru označující, že je nutné restartovat počítač. Restartování jsou spravována v rámci clusteru pomocí stejného [procesu vyprazdňování a vypouštění](#cordon-and-drain) jako upgrade clusteru.

U uzlů systému Windows Server (aktuálně ve verzi Preview v AKS) se služba Windows Update automaticky nespustí a nepoužije nejnovější aktualizace. V pravidelných intervalech kolem cyklu vydávání služby Windows Update a vlastního procesu ověřování byste měli provést upgrade ve fondu uzlů systému Windows Server v clusteru AKS. Tento proces upgradu vytvoří uzly, které spustí nejnovější bitovou kopii a opravy systému Windows Server, a potom odebere starší uzly. Další informace o tomto procesu naleznete [v tématu Upgrade fondu uzlů v AKS][nodepool-upgrade].

Uzly se nasazují do podsítě privátní virtuální sítě bez přiřazených veřejných IP adres. Pro účely řešení potíží a správy je ssh ve výchozím nastavení povolena. Tento přístup SSH je k dispozici pouze pomocí interní IP adresy.

K zajištění úložiště používají uzly spravované disky Azure. Pro většinu velikostí uzlů virtuálních počítačů se jedná o disky Premium podporované vysoce výkonnými disky SSD. Data uložená na spravovaných discích se automaticky zašifrují v klidovém stavu v rámci platformy Azure. Pro zlepšení redundance se tyto disky také bezpečně replikují v rámci datového centra Azure.

Prostředí Kubernetes, v AKS nebo jinde, v současné době nejsou zcela bezpečné pro nepřátelské použití více klientů. Další funkce zabezpečení, jako jsou *zásady zabezpečení podnebo* podrobnější ovládací prvky přístupu založené na rolích (RBAC) pro uzly ztěžují zneužití. Pro skutečné zabezpečení při spuštění nepřátelských úloh s více klienty je však hypervisor jedinou úrovní zabezpečení, které byste měli důvěřovat. Doména zabezpečení pro Kubernetes se stane celým clusterem, nikoli jednotlivým uzlem. Pro tyto typy nepřátelských víceklientských úloh byste měli použít fyzicky izolované clustery. Další informace o způsobech izolace úloh naleznete v [tématu Doporučené postupy pro izolaci clusteru v AKS][cluster-isolation],

## <a name="cluster-upgrades"></a>Upgrady clusteru

Pro zabezpečení a dodržování předpisů nebo použití nejnovějších funkcí poskytuje Azure nástroje pro orchestraci upgradu clusteru AKS a komponent. Tato orchestrace upgradu zahrnuje součásti hlavního serveru Kubernetes i součásti agenta. Můžete zobrazit [seznam dostupných verzí Kubernetes](supported-kubernetes-versions.md) pro váš cluster AKS. Chcete-li spustit proces upgradu, zadejte jednu z těchto dostupných verzí. Azure pak bezpečně kordony a vyprázdní každý uzel AKS a provede upgrade.

### <a name="cordon-and-drain"></a>Kordon a odtok

Během procesu upgradu jsou uzly AKS jednotlivě uzavřeny z clusteru, takže na nich nejsou naplánovány nové pody. Uzly jsou pak vyprázdněny a upgradovány takto:

- Nový uzel je nasazen do fondu uzlů. Tento uzel spouští nejnovější image operačního systému a opravy.
- Jeden z existujících uzlů je určen pro upgrade. Pody na tomto uzlu jsou řádně ukončeny a naplánovány na ostatní uzly ve fondu uzlů.
- Tento existující uzel je odstraněn z clusteru AKS.
- Další uzel v clusteru je uzavřen a vyprázdněn stejným procesem, dokud nebudou všechny uzly úspěšně nahrazeny jako součást procesu upgradu.

Další informace naleznete [v tématu Upgrade clusteru AKS][aks-upgrade-cluster].

## <a name="network-security"></a>Zabezpečení sítě

Pokud jde o připojení a zabezpečení s místními sítěmi, můžete svůj cluster AKS nasadit do existujících podsítí virtuálnísítě Azure. Tyto virtuální sítě mohou mít připojení VPN Azure site-to-Site nebo Express Route zpět do místní sítě. Řadiče příchozího přenosu dat kubernetes lze definovat pomocí privátních interních IP adres, takže služby jsou přístupné pouze prostředně prostředně tohoto interního síťového připojení.

### <a name="azure-network-security-groups"></a>Skupiny zabezpečení sítě Azure

Azure používá pravidla skupiny zabezpečení sítě, aby filtrovat tok provozu ve virtuálních sítích. Tato pravidla definují rozsahy ip adres zdroje a cíle, porty a protokoly, kterým je povolen nebo odepřen přístup k prostředkům. Výchozí pravidla jsou vytvořena tak, aby umožnila přenos TLS na server rozhraní API Kubernetes. Při vytváření služeb s vykladače zatížení, mapování portů nebo příchozích tras, AKS automaticky upraví skupinu zabezpečení sítě pro přenos toku odpovídajícím způsobem.

## <a name="kubernetes-secrets"></a>Tajné klíče Kubernetes

Kubernetes *Secret* se používá k vkládání citlivých dat do podů, jako jsou například přístupová pověření nebo klíče. Nejprve vytvoříte tajný klíč pomocí rozhraní API Kubernetes. Při definování pod u. nebo nasazení, konkrétní tajný klíč lze požadovat. Tajné klíče jsou k dispozici pouze uzly, které mají naplánované pod, který to vyžaduje, a tajný klíč je uložen v *tmpfs*, není zapsán na disk. Při odstranění posledního podu v uzlu, který vyžaduje tajný klíč, je tajný klíč odstraněn z tmpfs uzlu. Tajné klíče jsou uloženy v rámci daného oboru názvů a lze přistupovat pouze pody ve stejném oboru názvů.

Použití tajných kódů snižuje citlivé informace, které jsou definovány v pod nebo služby YAML manifest. Místo toho požadujete tajný klíč uložený na serveru Rozhraní API Kubernetes jako součást manifestu YAML. Tento přístup poskytuje pouze přístup k přístupu podu k tajnému klíči. Upozornění: nezpracované tajné soubory manifestu obsahují tajná data ve formátu base64 (další podrobnosti naleznete v [oficiální dokumentaci).][secret-risks] Proto tento soubor by měl být považován za citlivé informace a nikdy potvrzena správy zdrojového kódu.

## <a name="next-steps"></a>Další kroky

Chcete-li začít se zabezpečením clusterů AKS, přečtěte [si informace o upgradu clusteru AKS][aks-upgrade-cluster].

Doporučené postupy najdete v [tématu Doporučené postupy pro zabezpečení clusteru a upgrady v AKS][operator-best-practices-cluster-security] a [Doporučené postupy pro zabezpečení podu v AKS][developer-best-practices-pod-security].

Další informace o základních konceptech Kubernetes a AKS naleznete v následujících článcích:

- [Kubernetes / AKS clustery a úlohy][aks-concepts-clusters-workloads]
- [Kubernetes / AKS identita][aks-concepts-identity]
- [Kubernetes / AKS virtuální sítě][aks-concepts-network]
- [Kubernetes / AKS úložiště][aks-concepts-storage]
- [Kubernetes / AKS váha][aks-concepts-scale]

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
[developer-best-practices-pod-security]:developer-best-practices-pod-security.md
[nodepool-upgrade]: use-multiple-node-pools.md#upgrade-a-node-pool
