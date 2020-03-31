---
title: Přehled konzula
description: Získat přehled o konzul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: c518985b360fa3264bd5ac1e3fe76d61b2810b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77594205"
---
# <a name="consul"></a>Konzul

## <a name="overview"></a>Přehled

[Consul][consul] je síťové řešení pro více datových center pro připojení a zabezpečení služeb napříč platformami runtime. [Připojit][consul-features] je součást, která poskytuje možnosti sítě služby.

## <a name="architecture"></a>Architektura

Konzul poskytuje datovou rovinu, která se ve výchozím nastavení skládá z postranních [vozů][consul-sidecar] založených na [vyslanci.][envoy-proxy] Konzul má připojitelnou proxy architekturu. Tyto inteligentní proxy servery řídí veškerý síťový provoz ve vašich aplikacích a úlohách.

Řídicí rovina spravuje konfiguraci a zásady prostřednictvím [následujících součástí][consul-architecture]:

- **Server** - Agent konzula spuštěný v režimu serveru, který udržuje stav clusteru konzula.

- **Klient** - Consul Agent běží v režimu zjednodušeného klienta. Každý výpočetní uzel musí mít spuštěného agenta klienta. Tento klient zprostředkovává konfiguraci a zásady mezi úlohami a konfigurací konzula. 

Následující diagram architektury ukazuje, jak různé součásti v rovině dat a rovině ovládacího prvku interagují.

![Přehled consulských komponent a architektury.](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Kritéria výběru

Při hodnocení konzula pro vaše úlohy je důležité porozumět následujícím oblastem a zvážit je:

- [Principy konzula](#consul-principles)
- [Možnosti](#capabilities)
- [Scénáře](#scenarios)


### <a name="consul-principles"></a>Principy konzula

Projekt konzula [se řídí][consul-principles] následujícími zásadami:

- **API-Driven** - Kodifikujte všechny konfigurace a zásady.

- **Spouštět a připojovat odkudkoli** – propojovat úlohy napříč platformami runtime (Kubernetes, VMs, Serverless).

- **Rozšířit a integrovat** – bezpečně propojovat úlohy napříč infrastrukturou.


### <a name="capabilities"></a>Možnosti

Konzul poskytuje následující sadu funkcí:

- **Mesh** – brána (multi datová centra), virtuální počítače (mimo uzly clusteru), synchronizace služeb, integrovaná možnost ladění

- **Proxy servery** – vyslanec, vestavěný proxy server, připojitelný, l4 proxy k dispozici pro úlohy systému Windows

- **Řízení provozu** – směrování, rozdělení, řešení

- **Politika** – záměry, ACL

- **Zabezpečení** – autorizace, ověřování, šifrování, identity založené na SPIFFE, externí CA (Vault), správa certifikátů a rotace

- **Pozorovatelnost** – metriky, ui přístrojová deska, prometheus, grafana


### <a name="scenarios"></a>Scénáře

Konzul je vhodný a navrhl pro následující scénáře:

- Rozšíření stávajících propojených úloh konzula

- Požadavky na dodržování předpisů týkající se správy certifikátů

- Síť více clusterových služeb

- Úlohy založené na virtuálních mkcích, které mají být zahrnuty do sítě služeb



## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete nainstalovat konzula do služby Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalace konzula ve službě Azure Kubernetes Service (AKS)][consul-install]

Můžete také dále prozkoumat funkce a architekturu konzula:

- [Funkce konzula][consul-features]
- [Konzularchitektura][consul-architecture]
- [Konzul - Jak connect funguje][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
