---
title: Jak funguje nastavení clusteru pro Azure Dev Spaces
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje, jak funguje nastavení clusteru služby Azure Kubernetes pro Azure Dev Spaces.
keywords: Azure Dev Spaces, Dev Spaces, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 00f8262f3008ce9ba82726960f78d18395458a2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241722"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Jak funguje nastavení clusteru pro Azure Dev Spaces

Azure Dev Spaces vám nabízí několik způsobů, jak rychle itetovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Služby Azure Kubernetes (AKS). Jedním ze způsobů je povolit Azure Dev Spaces ve vašem clusteru AKS, abyste mohli [spouštět služby přímo ve vašem clusteru][how-it-works-up] a používat [další možnosti sítě a směrování][how-it-works-routing]. Tento článek popisuje, co se stane při přípravě clusteru a povolení Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Příprava clusteru AKS

Chcete-li připravit cluster AKS pro Dev Spaces, ověřte, že váš cluster AKS je v oblasti [podporované Azure Dev Spaces][supported-regions] a používáte Kubernetes 1.10.3 nebo novější. Pokud chcete v clusteru povolit Azure Dev Spaces z portálu Azure, přejděte do svého clusteru, klikněte na *Dev Spaces*, změňte *použít dev spaces* na *Ano*a klikněte na *Uložit*. Azure Dev Spaces můžete taky povolit z `az aks use-dev-spaces`azure cli spuštěním .

Příklad nastavení clusteru AKS pro funkce Dev Spaces naleznete v [rychlém startu vývoje týmu][quickstart-team].

Když je azure dev spaces v clusteru AKS povolen, nainstaluje řadič pro váš cluster. Řadič je umístěn mimo cluster AKS. Řídí chování a komunikaci mezi nástroji na straně klienta a clusterem AKS. Jakmile je povolena, můžete pracovat s řadičem pomocí nástrojů na straně klienta.

Řadič provádí následující akce:

* Spravuje vytváření a výběr dev prostoru.
* Nainstaluje helmchart vaší aplikace a vytvoří objekty Kubernetes.
* Vytvoří image kontejneru vaší aplikace.
* Nasazuje vaši aplikaci do AKS.
* Má přírůstkové sestavení a restartuje při změně zdrojového kódu.
* Spravuje protokoly a trasování HTTP.
* Přeposílá stdout a stderr na straně klienta nástroje.
* Konfiguruje směrování pro aplikace v rámci prostoru, stejně jako napříč nadřazené a podřízené prostory.

Řadič je samostatný prostředek Azure mimo váš cluster a provádí následující prostředky v clusteru:

* Vytvoří nebo označí obor názvů Kubernetes, který se použije jako dev prostor.
* Odebere všechny obory názvů Kubernetes s názvem *azds*, pokud existuje, a vytvoří nový.
* Nasazuje konfiguraci webhooku Kubernetes.
* Nasazuje webhookový přístupový server.

Používá stejný instanční objekt, který váš cluster AKS používá k volání služby do jiných součástí Azure Dev Spaces.

![Azure Dev Spaces připravit cluster](media/how-dev-spaces-works/prepare-cluster.svg)

Aby bylo možné používat Azure Dev Spaces, musí existovat alespoň jeden dev prostor. Azure Dev Spaces používá obory názvů Kubernetes v rámci clusteru AKS pro dev prostory. Při instalaci řadiče vás vyzve k vytvoření nového oboru názvů Kubernetes nebo k výběru existujícího oboru názvů, který chcete použít jako první dev prostor. Ve výchozím nastavení nabízí řadič upgrade existujícího *výchozího* oboru názvů Kubernetes na první dev prostor.

Pokud je obor názvů určen jako dev prostor, řadič přidá *popisek azds.io/space=true* do tohoto oboru názvů k jeho identifikaci jako dev prostoru. Počáteční dev prostor, který vytvoříte nebo určíte, je vybrán ve výchozím nastavení po přípravě clusteru. Když je vybrána mezera, používá Azure Dev Spaces pro vytváření nových úloh.

Nástroje na straně klienta můžete použít k vytvoření nových dev mezer a odebrání existujících dev mezer. Z důvodu omezení v Kubernetes nelze odebrat *výchozí* dev prostor. Řadič také odebere všechny existující obory názvů Kubernetes s názvem `azds` *azds,* aby se zabránilo konfliktům s příkazem používaným nástroji na straně klienta.

Kubernetes webhook přijímací server se používá k vkládání pods tři kontejnery během nasazení pro instrumentaci: devspaces proxy kontejner, devspaces proxy-init kontejner u devspaces a devspaces sestavení kontejneru. **Všechny tři tyto kontejnery běží s root přístup v clusteru AKS.** Používají také stejný instanční objekt, který váš cluster AKS používá k volání služby do jiných součástí Azure Dev Spaces.

![Server pro přijetí webhooku Azure Dev Spaces Kubernetes](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Kontejner proxy devspaces je kontejner sajdkáru, který zpracovává veškerý přenos protokolu TCP do a z kontejneru aplikace a pomáhá provádět směrování. Kontejner devspaces-proxy přesměruje zprávy HTTP, pokud jsou používány určité mezery. Může například pomoci směrovat zprávy HTTP mezi aplikacemi v nadřazených a podřízených prostorech. Všechny non-HTTP přenosy prochází devspaces proxy nezměněno. Kontejner devspaces-proxy také protokoluje všechny příchozí a odchozí zprávy HTTP a odešle je do nástrojů na straně klienta jako trasování. Tyto trasování pak mohou být zobrazeny vývojářem ke kontrole chování aplikace.

Kontejner devspaces-proxy-init je [kontejner init,](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) který přidává další pravidla směrování založená na hierarchii prostoru do kontejneru vaší aplikace. Přidá pravidla směrování aktualizací souboru */etc/resolv.conf* kontejneru aplikace a konfigurace iptables před jeho spuštěním. Aktualizace */etc/resolv.conf* umožňují rozlišení služby DNS v nadřazených prostorech. Aktualizace konfigurace iptables zajišťují, že veškerý provoz Protokolu TCP do a z kontejneru aplikace jsou směrovány prostřednictvím devspaces-proxy. Všechny aktualizace z devspaces-proxy-init dojít kromě pravidel, která Kubernetes přidává.

Kontejner sestavení devspaces je kontejner init a má zdrojový kód projektu a soket Dockeru připojený. Zdrojový kód projektu a přístup k Dockeru umožňuje kontejneru aplikace sestavit přímo pod.

> [!NOTE]
> Azure Dev Spaces používá stejný uzel k sestavení kontejneru vaší aplikace a jeho spuštění. V důsledku toho Azure Dev Spaces nepotřebuje externí registru kontejnerů pro vytváření a spouštění aplikace.

Webhookový přístupový server Kubernetes naslouchá každému novému podu, který je vytvořen v clusteru AKS. Pokud tento pod je nasazen do libovolného oboru názvů s *popiskem azds.io/space=true,* vloží tento pod s další kontejnery. Kontejner sestavení devspaces je vložen pouze v případě, že kontejner aplikace je spuštěn pomocí nástrojů na straně klienta.

Po přípravě clusteru AKS můžete pomocí nástrojů na straně klienta připravit a spustit kód v prostoru pro spuštění.

## <a name="client-side-tooling"></a>Nástroje na straně klienta

Nástroje na straně klienta umožňují uživateli:
* Vygenerujte konfigurační soubor Dockerfile, Helm a konfigurační soubor Azure Dev Spaces pro aplikaci.
* Vytvořte nadřazené a podřízené dev prostory.
* Řekněte kontroleru, aby vytvořil a spustil aplikaci.

Když je aplikace spuštěna, nástroje na straně klienta také:
* Přijímá a zobrazuje stdout a stderr z vaší aplikace spuštěné v AKS.
* Používá [port-forward](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) povolit webový přístup k\/vaší aplikaci pomocí http: /localhost.
* Připojí ladicí program ke spuštěné aplikaci v AKS.
* Synchronizuje zdrojový kód do dev prostoru, když je zjištěna změna pro přírůstková sestavení, což umožňuje rychlou iteraci.
* Umožňuje připojit vývojářský počítač přímo k clusteru AKS.

Jako součást `azds` příkazu můžete použít nástroje na straně klienta z příkazového řádku. Nástroje na straně klienta můžete také použít s:

* Visual Studio Kód pomocí [rozšíření Azure Dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio s [nástroji Visual Studio pro Kubernetes](https://aka.ms/get-vsk8stools).

## <a name="next-steps"></a>Další kroky

Další informace o použití nástrojů na straně klienta k přípravě a spuštění kódu v prostoru pro konkrétní prostředí najdete v [tématu Jak funguje příprava projektu pro Azure Dev Spaces][how-it-works-prep].

Pokud chcete začít používat Azure Dev Spaces pro vývoj týmu, podívejte se na vývoj týmu v azure [dev spaces][quickstart-team] quickstart.

[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service
[quickstart-team]: quickstart-team-development.md