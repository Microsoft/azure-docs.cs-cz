---
title: Přehled Istio
description: Získání přehledu o Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "77593896"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Přehled

[Istio][istio] je plně vybavená síť, přizpůsobitelné a rozšiřitelná síť pro služby.

## <a name="architecture"></a>Architektura

Istio poskytuje rovinu dat, která se skládá z sajdkáry založeného na [zástupné][envoy-proxy]. Tyto inteligentní proxy servery řídí veškerý síťový provoz v a z vašich aplikací a úloh.

Řídicí rovina spravuje konfiguraci, zásady a telemetrii prostřednictvím následujících [součástí][what-is-istio]:

- **Mixer** – vynutilo řízení přístupu a zásady použití. Shromažďuje telemetrii z proxy serverů, které jsou vloženy do [Prometheus][prometheus].

- **Pilotní nasazení** – poskytuje zásady pro zjišťování služeb a správu provozu a konfiguraci pro proxy servery.

- **Souborech Citadel** – poskytuje možnosti identity a zabezpečení, které umožňují mTLS mezi službami.

- **Sloupce** – abstrakce a poskytuje konfiguraci pro součásti.

Následující diagram architektury znázorňuje, jak různé komponenty v rovině dat a řídící rovině pracují.


![Přehled komponent a architektury Istio](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Kritéria výběru

Při vyhodnocování Istio pro vaše úlohy je důležité pochopit a vzít v úvahu následující oblasti:

- [Cíle návrhu](#design-goals)
- [Možnosti](#capabilities)
- [Scénáře](#scenarios)


### <a name="design-goals"></a>Cíle návrhu

Následující cíle návrhu [provedou][design-goals] projekt Istio:

- **Maximalizuje transparentnost** – umožní přijetí s minimálním množstvím práce, aby se získala skutečná hodnota ze systému.

- **Rozšiřitelnost** – musí být schopné rozrůstat a přizpůsobovat měnícím se potřebám.

- **Přenositelnost** – spouštějte snadno v různých druzích prostředí – cloudové, místní.

- **Jednotnost zásad** – konzistence v definici zásad napříč různými prostředky.


### <a name="capabilities"></a>Možnosti

Istio poskytuje následující sadu funkcí:

- **Síť** – brány (vícenásobné clustery), virtuální počítače (rozšíření sítě)

- **Správa provozu** – směrování, rozdělování, časové limity, přerušení okruhů, opakované pokusy, příchozí přenos dat

- **Zásady** – řízení přístupu, omezení četnosti, kvóta, vlastní adaptéry zásad

- **Zabezpečení** – ověřování (Jwt), autorizace, šifrování (mTLS), externí certifikační autorita (HashiCorp trezor)

- **Pozorování** – zlatá metrika, zrcadlení, trasování, vlastní adaptéry, Prometheus, grafana

### <a name="scenarios"></a>Scénáře

Istio je vhodný pro a navržený z následujících scénářů:

- Vyžadovat rozšiřitelnost a bohatou sadu funkcí

- Rozšíření sítě pro zahrnutí úloh založených na virtuálních počítačích

- Síť s více clustery

## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete nainstalovat Istio do služby Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalace Istio ve službě Azure Kubernetes (AKS)][istio-install]

Můžete také dále prozkoumat koncepty Istio a další modely nasazení:

- [Koncepty Istio][what-is-istio]
- [Modely nasazení Istio][deployment-models]

<!-- LINKS - external -->
[istio]: https://istio.io
[what-is-istio]: https://istio.io/docs/concepts/what-is-istio/
[design-goals]: https://istio.io/docs/concepts/what-is-istio/#design-goals
[deployment-models]: https://istio.io/docs/concepts/deployment-models/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[istio-install]: ./servicemesh-istio-install.md
