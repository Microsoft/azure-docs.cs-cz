---
title: O sítích služby
description: Získejte přehled sítí, jejich architektury a možností a kritéria, která byste měli zvážit při výběru jednoho k nasazení.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77594307"
---
# <a name="about-service-meshes"></a>O sítích služby

Síť nabízí možnosti, jako je Správa provozu, odolnost, zásady, zabezpečení, silná identita a pozorování vašich úloh. Vaše aplikace je oddělená od těchto provozních schopností a síť je přesouvá z aplikační vrstvy, a to až do vrstvy infrastruktury.

## <a name="scenarios"></a>Scénáře

Toto jsou některé z scénářů, které se dají pro vaše úlohy povolit, když používáte síť služby:

- **Šifrovat veškerý provoz v clusteru** – POVOLÍ vzájemné TLS mezi zadanými službami v clusteru. Tato možnost se dá rozšířit na příchozí a odchozí data na hraniční síti. Poskytuje zabezpečenou možnost zabezpečení ve výchozím nastavení bez nutnosti změn pro kód aplikace a infrastrukturu.

- **Kanárské a dvoufázové uvádění** – zadejte podmínky pro podmnožinu provozu, která se má směrovat do sady nových služeb v clusteru. Po úspěšném testování zkušební verze odeberte podmíněné směrování a fázi postupně zvyšováním% veškerého provozu do nové služby. Nakonec bude veškerý provoz směrován na novou službu.

- **Správa provozu a manipulace** – vytvořte zásadu pro službu, která bude omezovat veškerý provoz na verzi služby od určitého původu. Nebo zásadu, která aplikuje strategii opakování na třídy selhání mezi zadanými službami. Zrcadlení živého provozu do nových verzí služeb během migrace nebo ladění problémů. K otestování odolnosti můžete vložit chyby mezi službami v testovacím prostředí.

- **Pozorování** – získáte přehled o tom, jak jsou vaše služby propojené s přenosem dat. Získejte metriky, protokoly a trasování pro veškerý provoz v clusteru a příchozí a odchozí přenosy. Přidejte do svých aplikací možnosti distribuovaného trasování.

## <a name="architecture"></a>Architektura

Síť se obvykle skládá z řídicí roviny a roviny dat.

**Rovina ovládacího prvku** má několik komponent, které podporují správu sítě. Obvykle se bude jednat o rozhraní pro správu, které by mohlo být uživatelské rozhraní nebo rozhraní API. K dispozici jsou také komponenty, které spravují pravidla a definice zásad definující způsob, jakým má síť služby implementovat konkrétní možnosti. K dispozici jsou také komponenty, které spravují aspekty zabezpečení, jako je silná identita a certifikáty pro mTLS. Sítě budou mít také typicky metriky nebo komponenty pro pozorování, které shromažďují a agreguje metriky a telemetrii z úloh.

**Rovina dat** se obvykle skládá z proxy serveru, který je transparentně vložen jako postranní vozík pro vaše úlohy. Tento proxy server je nakonfigurován pro řízení veškerého síťového provozu v rámci, který obsahuje vaše zatížení. To umožňuje konfiguraci proxy serveru pro zabezpečení provozu prostřednictvím mTLS, dynamického směrování provozu, uplatnění zásad na provoz a shromažďování metrik a trasovacích informací. 

![Typická architektura sítě služby](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Možnosti

Každá síť sítě má přirozené přizpůsobení a zaměřuje se na podporu konkrétních scénářů, ale obvykle zjistíte, že většina z následujících možností implementuje celou řadu, pokud ne.

### <a name="traffic-management"></a>Správa provozu 

- **Protokol** – vrstva 7 (http, grpc)
- **Dynamické směrování** – podmíněné, váhy, zrcadlení
- **Odolnost** – vypršení časových limitů, opakování, přepínacích okruhů
- **Zásady** – řízení přístupu, omezení přenosové rychlosti, kvóty
- **Testování** – vkládání chyb

### <a name="security"></a>Zabezpečení

- **Šifrování** – mTLS, Správa certifikátů, externí certifikační autorita
- **Silná identita** – SPIFFE nebo podobná
- **Ověřování** – ověřování, autorizace

### <a name="observability"></a>Pozorovatelnost

- **Metriky** – zlatá metrika, Prometheus, grafana
- **Trasování** – trasování napříč úlohami
- **Provoz** – cluster, příchozí/odchozí

### <a name="mesh"></a>Smíšená (mesh)

- **Podporované výpočetní** – Kubernetes, virtuální počítače
- **Vícenásobný cluster** – brány, federace

## <a name="selection-criteria"></a>Kritéria výběru

Než vyberete síť služby, ujistěte se, že rozumíte vašim požadavkům a důvodům pro instalaci sítě. Zkuste se zeptat na následující otázky.

- **Je adaptér příchozího přenosu dostačující pro moje potřeby?** – Někdy je potřeba mít možnost jako testování/b nebo rozdělování provozu na příchozí přenos dat, aby podporovala požadovaný scénář. Nedělejte si nesložitou složitost do vašeho prostředí, a to bez jakýchkoli stran.

- **Můžou Moje úlohy a prostředí tolerovat další režijní náklady?** – Všechny další součásti, které jsou potřebné k podpoře sítě, vyžadují další prostředky, jako je CPU a paměť. Kromě toho všechny proxy a jejich přidružené zásady kontrolují latenci v provozu. Pokud máte úlohy, které jsou velmi citlivé na latenci, nebo nemůžete poskytnout další prostředky pro pokrytí součástí sítě a pak je znovu zvážit.

- **Je to zbytečně nové složitost?** – Pokud je důvodem pro instalaci sítě pro službu možnost získat funkci, která není nutně důležitá pro obchodní nebo provozní týmy, zvažte, jestli je to další složitost instalace, údržby a konfigurace.

- **Dá se přijmout při přírůstkovém přístupu?** – Některé z sítí služby, které poskytují spoustu možností, se dají přijmout přírůstkovým přístupem. Nainstalujte jenom komponenty, které potřebujete, abyste zajistili úspěch. Jakmile budete mít větší jistotu a potřebujete další možnosti, prozkoumejte je. Naodporuje, že se doporučuje nainstalovat *všechno* od začátku.

Pokud se po pečlivém zvážení rozhodnete, že potřebujete síť pro poskytování požadovaných možností, pak je vaše další rozhodnutí, *které z nich je síť?*

Vezměte v úvahu následující oblasti, které jsou v souladu s vašimi požadavky. To vám umožní dosáhnout nejlepšího přizpůsobení pro vaše prostředí a úlohy. V části [Další kroky](#next-steps) přejdete na další podrobné informace o konkrétních oblastech služby a o tom, jak se mapují na tyto oblasti.

- **Technická** Správa provozu, zásady, zabezpečení a pozorování

- **Business** -Commercial support, Foundation (CNCF), licence OSS, zásady správného řízení

- **Provozní** – instalace/upgrady, požadavky na prostředky, požadavky na výkon, integrace (metriky, telemetrie, řídicí panely, nástroje, SMI), smíšené úlohy (systémy Linux a Windows Node pool), COMPUTE (Kubernetes, virtuální počítače), více clusterů

- **Zabezpečení** – ověřování, identita, Správa certifikátů a rotace, připojitelná externí certifikační autorita


## <a name="next-steps"></a>Další kroky

V následující dokumentaci najdete další informace o sítích, které můžete vyzkoušet v Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Další informace o Istio...][istio-about]

> [!div class="nextstepaction"]
> [Další informace o linkeru...][linkerd-about]

> [!div class="nextstepaction"]
> [Další informace o Consul...][consul-about]

Také můžete chtít prozkoumat rozhraní sítě pro připojení k síti (SMI), což je standardní rozhraní pro sítě sítí na Kubernetes:

- [Rozhraní SMI (Service-Interface)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md