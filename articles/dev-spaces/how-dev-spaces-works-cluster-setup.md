---
title: Jak nastavovat cluster pro Azure Dev Spaces funguje
services: azure-dev-spaces
ms.date: 03/24/2020
ms.topic: conceptual
description: Popisuje, jak nastavovat cluster služby Azure Kubernetes pro Azure Dev Spaces funguje.
keywords: Azure Dev Spaces, vývojářské prostory, Docker, Kubernetes, Azure, AKS, Azure Kubernetes Service, kontejnery
ms.openlocfilehash: 841e67b96e95aa251fa5bf1ef469b68de30f54d9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "91972675"
---
# <a name="how-setting-up-a-cluster-for-azure-dev-spaces-works"></a>Jak nastavovat cluster pro Azure Dev Spaces funguje

[!INCLUDE [Azure Dev Spaces deprecation](../../includes/dev-spaces-deprecation.md)]

Azure Dev Spaces poskytuje několik způsobů, jak rychle iterovat a ladit aplikace Kubernetes a spolupracovat s týmem v clusteru Azure Kubernetes Service (AKS). Jedním ze způsobů je povolit Azure Dev Spaces v clusteru AKS, abyste mohli [spouštět služby přímo v clusteru][how-it-works-up] a využívat [Další možnosti sítě a směrování][how-it-works-routing]. Tento článek popisuje, co se stane, když připravíte cluster a povolíte Azure Dev Spaces.

## <a name="prepare-your-aks-cluster"></a>Příprava clusteru AKS

Pokud chcete připravit cluster AKS pro vývojové prostory, ověřte, jestli je cluster AKS v oblasti [podporované Azure dev Spaces][supported-regions] a používáte Kubernetes 1.10.3 nebo novější. Spuštěním nástroje můžete povolit Azure Dev Spaces z rozhraní příkazového řádku Azure `az aks use-dev-spaces` .

Když je v clusteru AKS povolená možnost Azure Dev Spaces, nainstaluje se kontrolér pro váš cluster. Kontroler se nachází mimo váš cluster AKS. Řídí chování a komunikaci mezi nástroji na straně klienta a clusterem AKS. Jakmile je tato možnost povolená, můžete s řadičem pracovat pomocí nástrojů na straně klienta.

Kontroler provádí následující akce:

* Spravuje vytváření a výběr vývojového prostoru.
* Nainstaluje graf Helm vaší aplikace a vytvoří objekty Kubernetes.
* Vytvoří Image kontejneru aplikace.
* Nasadí vaši aplikaci do AKS.
* Provede přírůstkové sestavení a restart při změně zdrojového kódu.
* Spravuje protokoly a trasování HTTP.
* Přepošle stdout a stderr do nástrojů na straně klienta.
* Nakonfiguruje směrování pro aplikace v rámci prostoru i mezi nadřazenými a podřízenými mezerami.

Kontroler je samostatný prostředek Azure mimo váš cluster a u prostředků v clusteru provádí následující akce:

* Vytvoří nebo určí obor názvů Kubernetes, který se má použít jako místo pro vývoj.
* Odebere libovolný obor názvů Kubernetes s názvem *azds*, pokud existuje, a vytvoří nový.
* Nasadí konfiguraci Webhooku Kubernetes.
* Nasadí Server pro přístup Webhooku.

Používá stejný instanční objekt, který používá váš cluster AKS k tomu, aby vyvolaly služby na jiné součásti Azure Dev Spaces.

![Azure Dev Spaces Příprava clusteru](media/how-dev-spaces-works/prepare-cluster.svg)

Aby bylo možné použít Azure Dev Spaces, musí existovat alespoň jeden vývojového prostoru. Azure Dev Spaces používá obory názvů Kubernetes v rámci clusteru AKS pro vývojové prostory. Při instalaci kontroleru se zobrazí výzva k vytvoření nového oboru názvů Kubernetes nebo výběru existujícího oboru názvů, který se použije jako první místo pro vývoj. Ve výchozím nastavení kontroler nabízí upgrade existujícího *výchozího* oboru názvů Kubernetes na první místo pro vývoj.

Pokud je obor názvů určen jako místo pro vývoj, kontroler přidá k tomuto oboru názvů jmenovku *azds.IO/Space=true* , aby jej identifikoval jako místo pro vývoj. Počáteční prostor pro vývoj, který vytvoříte nebo označíte, se po přípravě clusteru vybere ve výchozím nastavení. Když je vybraná mezera, používá se Azure Dev Spaces k vytváření nových úloh.

Pomocí nástrojů na straně klienta můžete vytvořit nové vývojové prostory a odebrat existující vývojové prostory. V důsledku omezení v Kubernetes nejde *výchozí* místo pro vývoj odebrat. Kontroler taky odebere všechny existující obory názvů Kubernetes s názvem *azds* , aby nedocházelo ke konfliktům s `azds` příkazem používaným nástroji na straně klienta.

Přidaný server Webhooku Kubernetes se používá k vkládání lusků se třemi kontejnery během nasazování do instrumentace: kontejner devspaces-proxy, kontejner devspaces-proxy-init a kontejner devspaces-buildu. **Všechny tři tyto kontejnery se spouštějí s kořenovým přístupem v clusteru AKS.** Používají také stejný instanční objekt, který cluster AKS používá k tomu, aby vyvolaly služby na jiné součásti Azure Dev Spaces.

![Kubernetes Server pro přístup Webhooku Webhooku Azure Dev Spaces](media/how-dev-spaces-works/kubernetes-webhook-admission-server.svg)

Kontejner devspaces-proxy je kontejner postranového vozíku, který zpracovává veškerý provoz TCP do kontejneru aplikace a z něj a pomáhá provádět směrování. Kontejner devspaces-proxy přesměruje zprávy HTTP, pokud se používají určité mezery. Může například přispět k směrování zpráv HTTP mezi aplikacemi v nadřazených a podřízených prostorech. Veškerý provoz jiného typu než HTTP projde prostřednictvím devspaces-proxy beze změny. Kontejner devspaces-proxy také zaznamenává všechny příchozí a odchozí zprávy HTTP a odesílá je do nástrojů na straně klienta jako trasování. Tato trasování pak mohou vývojáři zobrazit a zkontrolovat chování aplikace.

Kontejner devspaces-proxy-init je [inicializační kontejner](https://kubernetes.io/docs/concepts/workloads/pods/init-containers/) , který přidává další pravidla směrování založená na hierarchii prostoru do kontejneru vaší aplikace. Přidává pravidla směrování tím, že aktualizuje soubor */etc/resolv.conf* kontejneru aplikace a konfiguraci softwaru iptables před tím, než se spustí. Aktualizace */etc/resolv.conf* umožňují překlad DNS služeb v nadřazených prostorech. Aktualizace konfigurace softwaru iptables zajistí směrování všech přenosů dat TCP do kontejneru aplikace a z něj, i když devspaces-proxy. Všechny aktualizace z devspaces-proxy-init nastávají kromě pravidel, která Kubernetes přidává.

Kontejner devspaces-Build je inicializační kontejner a má připojený zdrojový kód projektu a Docker Socket. Zdrojový kód projektu a přístup k Docker umožňuje, aby byl kontejner aplikace sestaven přímo pod ním.

> [!NOTE]
> Azure Dev Spaces používá stejný uzel k sestavení kontejneru aplikace a jeho spuštění. V důsledku toho Azure Dev Spaces pro sestavování a spouštění vaší aplikace potřebovat externí registr kontejnerů.

Server pro příjem Webhooku Kubernetes naslouchá každému novému z nich, který je vytvořený v clusteru AKS. Pokud je tento příkaz nasazený do libovolného oboru názvů s popiskem *azds.IO/Space=true* , vloží ho pod další kontejnery. Kontejner devspaces-Build je vložen pouze v případě, že je kontejner aplikace spuštěn pomocí nástrojů na straně klienta.

Po přípravě clusteru AKS můžete pomocí nástrojů na straně klienta připravit a spustit kód ve vývojovém prostoru.

## <a name="client-side-tooling"></a>Nástroje na straně klienta

Nástroje na straně klienta umožňují uživateli:
* Vygenerujte souboru Dockerfile, Helm graf a Azure Dev Spaces konfigurační soubor pro aplikaci.
* Vytvoření nadřazených a podřízených vývojových prostorů.
* Řekněte řadiči, aby sestavil a spustil vaši aplikaci.

I když je vaše aplikace spuštěná, nástroje na straně klienta:
* Přijímá a zobrazuje stdout a stderr z vaší aplikace spuštěné v AKS.
* Pomocí [předávaného portu](https://kubernetes.io/docs/tasks/access-application-cluster/port-forward-access-application-cluster/) umožní webovému přístupu k vaší aplikaci používat http: \/ /localhost..
* Připojí ladicí program ke svojí spuštěné aplikaci v AKS.
* Synchronizuje zdrojový kód s vývojovým místem, když je zjištěna změna přírůstkových sestavení, což umožňuje rychlou iteraci.
* Umožňuje připojit počítač pro vývojáře přímo ke clusteru AKS.

V rámci příkazu můžete použít nástroje na straně klienta z příkazového řádku `azds` . Můžete také použít nástroje na straně klienta s nástrojem:

* Visual Studio Code pomocí [rozšíření Azure dev Spaces](https://marketplace.visualstudio.com/items?itemName=azuredevspaces.azds).
* Visual Studio s úlohou vývoje Azure.

## <a name="next-steps"></a>Další kroky

Další informace o tom, jak pomocí nástrojů na straně klienta připravit a spustit váš kód ve vývojovém prostoru, najdete v tématu [jak připravovat projekt pro Azure dev Spaces funguje][how-it-works-prep].


[how-it-works-prep]: how-dev-spaces-works-prep.md
[how-it-works-routing]: how-dev-spaces-works-routing.md
[how-it-works-up]: how-dev-spaces-works-up.md
[supported-regions]: https://azure.microsoft.com/global-infrastructure/services/?products=kubernetes-service