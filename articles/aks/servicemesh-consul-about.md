---
title: Přehled Consul
description: Získání přehledu o Consul
author: paulbouwer
ms.topic: article
ms.date: 10/09/2019
ms.author: pabouwer
ms.openlocfilehash: 77cb6a693c5a73d0498b0acf9bc4ad8cc9f8f32f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "83774009"
---
# <a name="consul"></a>Consul

## <a name="overview"></a>Přehled

[Consul][consul] je řešení pro síťové služby podporující více datových center pro připojení a zabezpečení služeb napříč platformami za běhu. [Connect][consul-features] je součást poskytující možnosti sítě služby.

## <a name="architecture"></a>Architektura

Consul poskytuje rovinu dat, která se skládá z [sajdkáry][consul-sidecar] založeného na [zástupné][envoy-proxy]ve výchozím nastavení. Consul má připojenou architekturu proxy serveru. Tyto inteligentní proxy servery řídí veškerý síťový provoz v a z vašich aplikací a úloh.

Rovina ovládacího prvku spravuje konfiguraci a zásady prostřednictvím následujících [komponent][consul-architecture]:

- **Server** – agent Consul spuštěný v režimu serveru, který udržuje stav clusteru Consul.

- **Klient** -agent Consul spuštěný v režimu zjednodušeného klienta. Každý výpočetní uzel musí mít spuštěného klientského agenta. Tento klient brokere konfiguraci a zásadu mezi úlohami a konfigurací Consul. 

Následující diagram architektury znázorňuje, jak různé komponenty v rovině dat a řídící rovině pracují.

![Přehled komponent a architektury Consul](media/servicemesh/consul/about-architecture.png)


## <a name="selection-criteria"></a>Kritéria výběru

Při vyhodnocování Consul pro vaše úlohy je důležité pochopit a vzít v úvahu následující oblasti:

- [Principy Consul](#consul-principles)
- [Možnosti](#capabilities)
- [Scénáře](#scenarios)


### <a name="consul-principles"></a>Principy Consul

Následující principy [provedou][consul-principles] projekt Consul:

- **Rozhraní API – řízené** Codify všechny konfigurace a zásady.

- **Spouštění a připojování úloh odkudkoli a** připojení napříč platformami za běhu (Kubernetes, virtuální počítače a bez serveru).

- **Rozšíří a integruje** úlohy s bezpečným připojením napříč infrastrukturou.


### <a name="capabilities"></a>Možnosti

Consul poskytuje následující sadu funkcí:

- **Síť** – brána (více datových Center), virtuální počítače (z uzlů clusteru), synchronizace služby, integrovaná možnost ladění

- **Proxy servery** – zástupné, integrovaný proxy server, který je možné připojit k dispozici pro úlohy Windows

- **Správa provozu** – směrování, rozdělování, řešení

- **Zásady** – záměry, seznamy ACL

- **Zabezpečení** – autorizace, ověřování, šifrování, identity založené na SPIFFE, externí certifikační autorita (trezor), Správa certifikátů a rotace

- **Pozorování** – metriky, řídicí panel uživatelského rozhraní, Prometheus, grafana


### <a name="scenarios"></a>Scénáře

Consul je vhodný pro a navržený z následujících scénářů:

- Rozšíření stávajících úloh připojených k Consul

- Požadavky na dodržování předpisů kolem správy certifikátů

- Síť s více clustery

- Úlohy na bázi virtuálních počítačů, které se mají zahrnout do sítě



## <a name="next-steps"></a>Další kroky

Následující dokumentace popisuje, jak můžete nainstalovat Consul do služby Azure Kubernetes Service (AKS):

> [!div class="nextstepaction"]
> [Instalace Consul ve službě Azure Kubernetes (AKS)][consul-install]

Můžete také dále prozkoumat funkce a architekturu Consul:

- [Kurzy Začínáme Consul][consul-getting-started]
- [Funkce Consul][consul-features]
- [Architektura Consul][consul-architecture]
- [Consul – jak funguje připojení][consul-how-connect-works]

<!-- LINKS - external -->
[consul]: https://www.consul.io/mesh.html
[consul-features]: https://www.consul.io/docs/connect/index.html
[consul-architecture]: https://www.consul.io/docs/internals/architecture.html
[consul-sidecar]: https://www.consul.io/docs/connect/proxies.html
[consul-how-connect-works]: https://www.consul.io/docs/connect/connect-internals.html
[consul-principles]: https://www.consul.io/
[consul-getting-started]:https://learn.hashicorp.com/consul?track=gs-consul-service-mesh#gs-consul-service-mesh

[envoy-proxy]: https://www.envoyproxy.io/
[grafana]: https://grafana.com/
[prometheus]: https://prometheus.io/

<!-- LINKS - internal -->
[consul-install]: ./servicemesh-consul-install.md
