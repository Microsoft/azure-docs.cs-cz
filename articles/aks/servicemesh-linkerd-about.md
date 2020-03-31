---
title: Přehled linkerdů
description: Získat přehled linkerd
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 3181be62a14ec1b3450bd181172b5323ca176427
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593763"
---
# <a name="linkerd"></a>Linkerd

## <a name="overview"></a>Přehled

[Linkerd][linkerd] je snadno použitelná a lehká servisní síť.

## <a name="architecture"></a>Architektura

Linkerd poskytuje datovou rovinu, která se skládá z ultralehkých [linkerdspecializovaných][linkerd-proxy] proxy sidecars. Tyto inteligentní proxy servery řídí veškerý síťový provoz ve vašich aplikacích a úlohách. Proxy servery také zveřejňují metriky prostřednictvím koncových bodů metrik [Prometheus.][prometheus]

Řídicí rovina spravuje konfiguraci a agregovotní telemetrii pomocí [následujících součástí][linkerd-architecture]:

- **Řadič** - Poskytuje rozhraní API, které řídí linkerd cli a řídicí hospo- Poskytuje konfiguraci pro proxy servery.

- **Klepněte** na - Vytvořte hodinky v reálném čase na požadavky a odpovědi.

- **Identita** – poskytuje funkce identity a zabezpečení, které umožňují mTLS mezi službami.

- **Web** - Poskytuje řídicí panel Linkerd.


Následující diagram architektury ukazuje, jak různé součásti v rovině dat a rovině ovládacího prvku interagují.


![Přehled komponent a architektury linkerdu.](media/servicemesh/linkerd/about-architecture.png)


## <a name="selection-criteria"></a>Kritéria výběru

Při vyhodnocování linkerdu pro vaše úlohy je důležité porozumět následujícím oblastem a zvážit je:

- [Principy návrhu](#design-principles)
- [Možnosti](#capabilities)
- [Scénáře](#scenarios)


### <a name="design-principles"></a>Principy návrhu

Projekt Linkerd [se řídí][design-principles] následujícími principy návrhu:

- **Keep it Simple** - Musí být snadné použití a pochopení.

- **Minimalizovat požadavky na prostředky** – zavést minimální výkon a náklady na prostředky.

- **Jen práce** – nepřerušuj stávající aplikace a nevyžadují složitou konfiguraci.


### <a name="capabilities"></a>Možnosti

Linkerd poskytuje následující sadu funkcí:

- **Mesh** – vestavěná možnost ladění

- **Řízení provozu** – rozdělení, časové osázy, opakování, příchozí přenos dat

- **Zabezpečení** – šifrování (mTLS), certifikáty autorotated každých 24 hodin

- **Pozorovatelnost** – zlatá metrika, klepnutí, trasování, profily služeb a metriky trasy, webový přístrojový panel s grafy topologie, prometheus, grafana


### <a name="scenarios"></a>Scénáře

Linkerd je vhodný a navrhl pro následující scénáře:

- Jednoduché použití pouze se základní sadou požadavků na schopnosti

- Nízká latence, nízká režie, se zaměřením na pozorovatelnost a jednoduché řízení provozu


## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete nainstalovat Linkerd na Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalace linkerdu ve službě Azure Kubernetes Service (AKS)][linkerd-install]

Můžete také dále prozkoumat funkce a architekturu linkerdu:

- [Propojovací funkce][linkerd-features]
- [Architektura linkerdů][linkerd-architecture]

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