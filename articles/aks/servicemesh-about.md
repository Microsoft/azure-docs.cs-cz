---
title: O servisních kinách
description: Získejte přehled o servisních sítěch, jejich architektuře a možnostech a o kritériích, která byste měli zvážit při výběru sítě, kterou chcete nasadit.
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: eca49a3fac1ea0398ebe1d05bde20fbca3c81232
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594307"
---
# <a name="about-service-meshes"></a>O servisních kinách

Síť služeb poskytuje funkce, jako je správa provozu, odolnost proti chybám, zásady, zabezpečení, silná identita a pozorovatelnost vašich úloh. Vaše aplikace je oddělena od těchto provozních funkcí a síť služeb je přesune z aplikační vrstvy a dolů do vrstvy infrastruktury.

## <a name="scenarios"></a>Scénáře

Toto jsou některé ze scénářů, které lze povolit pro vaše úlohy při použití sítě služby:

- **Šifrování všech přenosů v clusteru** – povolit vzájemné TLS mezi zadanými službami v clusteru. To lze rozšířit na příchozí a odchozí v obvodu sítě. Poskytuje zabezpečenou výchozí možnost bez změn potřebných pro kód aplikace a infrastrukturu.

- **Kanárské a postupné zavádění** – Určete podmínky pro podmnožinu provozu, která má být směrována na sadu nových služeb v clusteru. Při úspěšném testu kanárského uvolnění odstraňte podmíněné směrování a postupně zvyšujte % veškerého provozu na novou službu. Nakonec bude veškerý provoz přesměrován na novou službu.

- **Řízení provozu a manipulace** – vytvořte zásadu služby, která bude osazovat veškerý provoz na verzi služby z určitého původu. Nebo zásady, které používá strategii opakování tříd y selhání mezi zadané služby. Zrcadlit živý provoz na nové verze služeb během migrace nebo ladění problémů. Vstřikujte chyby mezi služby v testovacím prostředí k testování odolnosti.

- **Pozorovatelnost** - Získejte přehled o tom, jak jsou vaše služby propojeny provoz, který mezi nimi proudí. Získat metriky, protokoly a trasování pro všechny přenosy v clusteru a příchozí přenos/odchozí přenos. Přidejte do svých aplikací distribuované schopnosti trasování.

## <a name="architecture"></a>Architektura

Síť služby se obvykle skládá z roviny ovládacího prvku a roviny dat.

**Rovina ovládacího prvku** má řadu součástí, které podporují správu sítě služby. To bude obvykle zahrnovat rozhraní pro správu, které může být uživatelské rozhraní nebo rozhraní API. Obvykle také budou existovat součásti, které spravují definice pravidla a zásad, které definují, jak by měla síť služeb implementovat specifické funkce. Existují také součásti, které spravují aspekty zabezpečení, jako je silná identita a certifikáty pro mTLS. Služby ok bude mít také obvykle metriky nebo pozorovatelnost součást, která shromažďuje a agreguje metriky a telemetrie z úloh.

**Rovina dat** se obvykle skládá z proxy serveru, který je transparentně injektován jako sajdkaři do vašich úloh. Tento proxy server je nakonfigurován tak, aby řídil veškerý síťový provoz v podu a mimo ho, který obsahuje vaše pracovní vytížení. To umožňuje proxy server, který má být nakonfigurován tak, aby zabezpečil provoz prostřednictvím mTLS, dynamicky směrovat provoz, používat zásady pro provoz a shromažďovat metriky a informace o trasování. 

![Typická architektura sítě služeb](media/servicemesh/typical-architecture.png)

## <a name="capabilities"></a>Možnosti

Každá z mše služby mají přirozené přizpůsobení a zaměřit se na podporu konkrétních scénářů, ale obvykle zjistíte, že většina bude implementovat řadu, ne-li všechny následující funkce.

### <a name="traffic-management"></a>Řízení dopravy 

- **Protokol** – vrstva 7 (http, grpc)
- **Dynamické směrování** – podmíněné, vážení, zrcadlení
- **Odolnost vůči chybám** – časové výstky, opakování, jističe
- **Zásady** – řízení přístupu, omezení rychlosti, kvóty
- **Testování** - vstřikování poruch

### <a name="security"></a>Zabezpečení

- **Šifrování** – mTLS, správa certifikátů, externí CA
- **Silná identita** – SPIFFE nebo podobné
- **Auth** – autentizace, autorizace

### <a name="observability"></a>Pozorovatelnost

- **Metriky** – zlaté metriky, prometheus, grafana
- **Trasování** – trasování napříč úlohami
- **Provoz** – cluster, příchozí přenos/odchozí přenos

### <a name="mesh"></a>Smíšená (mesh)

- **Podporované výpočetní prostředky** - Kubernetes, virtuální počítače
- **Vícecluster -** brány, federace

## <a name="selection-criteria"></a>Kritéria výběru

Před výběrem sítě služeb se ujistěte, že rozumíte svým požadavkům a důvodům instalace sítě služby. Zkuste položit následující otázky.

- **Je řadič příchozího přenosu dat dostatečný pro mé potřeby?** - Někdy mají schopnosti, jako je a / b testování nebo rozdělení provozu na příchozí přenos je dostatečná pro podporu požadovaného scénáře. Nepřidávejte složitost do vašeho prostředí bez proinflačního vývoje.

- **Může moje úlohy a prostředí tolerovat další režijní náklady?** - Všechny další součásti potřebné pro podporu sítě služeb vyžadují další prostředky, jako je procesor a paměť. Kromě toho všechny proxy servery a jejich přidružené kontroly zásad přidat latenci vašeho provozu. Pokud máte úlohy, které jsou velmi citlivé na latenci nebo nemohou poskytnout další prostředky k pokrytí součástí sítě služby, pak znovu zvažte.

- **Je to přidání další složitost zbytečně?** - Pokud je důvodem pro instalaci sítě služeb získat schopnost, která nemusí být nutně důležitá pro obchodní nebo provozní týmy, zvažte, zda další složitost instalace, údržby a konfigurace stojí za to.

- **Lze to přijmout postupným přístupem?** - Některé ze služeb ok, které poskytují mnoho možností mohou být přijata v inkrementální přístup. Nainstalujte pouze komponenty, které potřebujete k zajištění svého úspěchu. Jakmile jste si jistější a další funkce jsou požadovány, pak prozkoumat ty. Odolat nutkání instalovat *vše* od začátku.

Pokud se po pečlivém zvážení rozhodnete, že potřebujete síť služeb, abyste poskytli požadované funkce, pak je dalším *rozhodnutím, která síť služby?*

Zvažte následující oblasti a které z nich jsou nejvíce v souladu s vašimi požadavky. To vás provede k co nejlepší pro vaše prostředí a úlohy. V části [Další kroky](#next-steps) získáte další podrobné informace o konkrétních sítích služeb a o tom, jak se mapují do těchto oblastí.

- **Technické** - řízení dopravy, politika, bezpečnost, pozorovatelnost

- **Obchod** - komerční podpora, nadace (CNCF), licence OSS, správa a řízení

- **Provozní** – instalace/upgrady, požadavky na prostředky, požadavky na výkon, integrace (metriky, telemetrie, řídicí panely, nástroje, SMI), smíšené úlohy (fondy uzlů Linuxu a Windows), výpočetní (Kubernetes, virtuální počítače), multicluster

- **Zabezpečení** - ověření, identita, správa a rotace certifikátů, připojitelný externí certifikační autorita


## <a name="next-steps"></a>Další kroky

Následující dokumentace obsahuje další informace o sítích služeb, které si můžete vyzkoušet ve službě Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Další informace o Istio ...][istio-about]

> [!div class="nextstepaction"]
> [Další informace o Linkerd ...][linkerd-about]

> [!div class="nextstepaction"]
> [Další informace o konzulovi ...][consul-about]

Můžete také prozkoumat rozhraní Service Mesh Interface (SMI), standardní rozhraní pro servisní sítě na Kubernetes:

- [Rozhraní síťového propojení služby (SMI)][smi]


<!-- LINKS - external -->
[smi]: https://smi-spec.io/

<!-- LINKS - internal -->
[istio-about]: ./servicemesh-istio-about.md
[linkerd-about]: ./servicemesh-linkerd-about.md
[consul-about]: ./servicemesh-consul-about.md