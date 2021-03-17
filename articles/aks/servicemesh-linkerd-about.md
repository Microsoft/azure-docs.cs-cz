---
title: Přehled linkeru
description: Získání přehledu linkeru
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "77593763"
---
# <a name="linkerd"></a>Linker

## <a name="overview"></a>Přehled

[Linker][linkerd] je snadno použitelný a odlehčená síť služby.

## <a name="architecture"></a>Architektura

Linkerd poskytuje rovinu dat, která se skládá z Ultralight [linkeru][linkerd-proxy] specializovaného proxy sajdkáry. Tyto inteligentní proxy servery řídí veškerý síťový provoz v a z vašich aplikací a úloh. Proxy servery také zpřístupňují metriky prostřednictvím koncových bodů metriky [Prometheus][prometheus] .

Rovina ovládacího prvku spravuje konfiguraci a agregovanou telemetrii prostřednictvím následujících [komponent][linkerd-architecture]:

- **Controller** – poskytuje rozhraní API, které řídí linkerem rozhraní příkazového řádku a řídicího panelu. Poskytuje konfiguraci pro proxy.

- **Klepněte** na požadavky a odpovědi navažte v reálném čase.

- **Identita** – poskytuje možnosti identity a zabezpečení, které umožňují mTLS mezi službami.

- **Web** – poskytuje linkerový řídicí panel.


Následující diagram architektury znázorňuje, jak různé komponenty v rovině dat a řídící rovině pracují.


![Přehled Linkerových komponent a architektury.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Kritéria výběru

Při hodnocení linkeru pro vaše úlohy je důležité pochopit a zvážit následující oblasti:

- [Principy návrhu](#design-principles)
- [Vestavěn](#capabilities)
- [Scénáře](#scenarios)


### <a name="design-principles"></a>Principy návrhu

Následující zásady návrhu [provedou][design-principles] linkerový projekt:

- **Snadné použití** a pochopení.

- **Minimalizace požadavků na prostředky** – omezení minimálního výkonu a nákladů na prostředky.

- **Pouze práce** – Nerušit stávající aplikace a nevyžadují složitou konfiguraci.


### <a name="capabilities"></a>Možnosti

Linkerd poskytuje následující sadu funkcí:

- **Síť** – možnost integrovaného ladění

- **Správa provozu** – rozdělení, vypršení časových limitů, opakování, příchozí přenos dat

- **Zabezpečení** – šifrování (mTLS), certifikáty se pro automatické střídání každých 24 hodin

- **Pozorování** – zlatá metrika, klepnutí, trasování, profily služeb a metriky jednotlivých tras, webový řídicí panel s grafy topologie, Prometheus, grafana


### <a name="scenarios"></a>Scénáře

Linker je vhodný pro a navržený z následujících scénářů:

- Jednoduché pro použití jenom se základní sadou požadavků na funkce

- Nízká latence, nízká režie, se zaměřením na dodržování a jednoduché řízení provozu


## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete nainstalovat linker ve službě Azure Kubernetes (AKS):

> [!div class="nextstepaction"]
> [Instalace linkeru ve službě Azure Kubernetes Service (AKS)][linkerd-install]

Můžete také dále prozkoumat Linkerované funkce a architekturu:

- [Linkerované funkce][linkerd-features]
- [Linkerovaná architektura][linkerd-architecture]

<!-- LINKS - external -->
[linkerd]: https://linkerd.io/2/overview/
[linkerd-architecture]: https://linkerd.io/2/reference/architecture/
[linkerd-features]: https://linkerd.io/2/features/
[design-principles]: https://linkerd.io/2/design-principles/
[linkerd-proxy]: https://github.com/linkerd/linkerd2-proxy

[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[linkerd-install]: ./servicemesh-linkerd-install.md