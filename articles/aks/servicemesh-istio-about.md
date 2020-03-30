---
title: Přehled Istio
description: Získat přehled o Istio
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 8518e30a54c2486abf84cd9ac026cc4dccb3fa84
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77593896"
---
# <a name="istio"></a>Istio

## <a name="overview"></a>Přehled

[Istio][istio] je plně vybavený, přizpůsobitelný a rozšiřitelný servisní síť.

## <a name="architecture"></a>Architektura

Istio poskytuje datovou rovinu, která se skládá z postranních vozů založených na [vyslanci.][envoy-proxy] Tyto inteligentní proxy servery řídí veškerý síťový provoz ve vašich aplikacích a úlohách.

Řídicí rovina spravuje konfiguraci, zásadu a telemetrii pomocí [následujících součástí][what-is-istio]:

- **Mixer** - Vynucuje zásady řízení přístupu a použití. Shromažďuje telemetrii z proxy, která je zasunuta do [Prometheus][prometheus].

- **Pilot** - Poskytuje zásady/konfiguraci zjišťování a řízení provozu pro servery proxy.

- **Citadela** – poskytuje funkce identity a zabezpečení, které umožňují mTLS mezi službami.

- **Galley** - Abstrakty a poskytuje konfiguraci komponent.

Následující diagram architektury ukazuje, jak různé součásti v rovině dat a rovině ovládacího prvku interagují.


![Přehled komponent a architektury Istio.](media/servicemesh/istio/about-architecture.png)


## <a name="selection-criteria"></a>Kritéria výběru

Při hodnocení istio pro vaše úlohy je důležité porozumět následujícím oblastem a zvážit je následující oblasti:

- [Cíle návrhu](#design-goals)
- [Možnosti](#capabilities)
- [Scénáře](#scenarios)


### <a name="design-goals"></a>Cíle návrhu

Projekt Istio [se řídí][design-goals] následujícími cíli návrhu:

- **Maximalizovat transparentnost** - Povolit přijetí s minimálním množstvím práce získat skutečnou hodnotu ze systému.

- **Rozšiřitelnost** - Musí být schopen růst a přizpůsobit se měnícím se potřebám.

- **Přenositelnost** – spouštění se snadno v různých typech prostředí – cloud, místně.

- **Jednotnost zásad** – konzistence v definici zásad napříč různými prostředky.


### <a name="capabilities"></a>Možnosti

Istio poskytuje následující sadu funkcí:

- **Mesh** – brány (multicluster), virtuální počítače (rozšíření sítě)

- **Řízení provozu** – směrování, rozdělení, časové výtky, jističe, opakování, vstup, výstup

- **Zásady** – řízení přístupu, omezení rychlosti, kvóta, adaptéry vlastních zásad

- **Bezpečnost** – autentizace (jwt), autorizace, šifrování (mTLS), externí CA (HashiCorp Vault)

- **Pozorovatelnost** – zlaté metriky, zrcadlo, trasování, vlastní adaptéry, prometheus, grafana

### <a name="scenarios"></a>Scénáře

Istio je vhodný pro následující scénáře a je navržen takto:

- Vyžadovat rozšiřitelnost a bohatou sadu funkcí

- Rozšíření sítě tak, aby zahrnovalo úlohy založené na virtuálních virtuálních mích

- Síť víceclusterových služeb

## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete nainstalovat Istio na Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalace Istia ve službě Azure Kubernetes Service (AKS)][istio-install]

Můžete také dále prozkoumat koncepty Istio a další modely nasazení:

- [Istio Koncepty][what-is-istio]
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
