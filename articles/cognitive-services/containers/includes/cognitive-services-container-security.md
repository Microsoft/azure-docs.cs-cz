---
title: Zabezpečení kontejneru
titleSuffix: Azure Cognitive Services
description: Naučte se zabezpečit svůj kontejner.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/11/2020
ms.author: aahi
ms.openlocfilehash: bb9b0da609169288521d21ee6d5e412a786c7549
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94573863"
---
## <a name="azure-cognitive-services-container-security"></a>Zabezpečení kontejneru Azure Cognitive Services

Zabezpečení by mělo být primárním fokusem při vývoji aplikací. Důležitosti zabezpečení je metrika pro úspěch. Při navrhování softwarového řešení, které zahrnuje Cognitive Services kontejnery, je důležité pochopit omezení a možnosti, které máte k dispozici. Další informace o zabezpečení sítě najdete v tématu [Konfigurace virtuálních sítí Azure Cognitive Services][az-security].

> [!IMPORTANT]
> Ve výchozím nastavení neexistuje *žádné zabezpečení* na Cognitive Services rozhraní API kontejneru. Důvodem je, že nejčastěji se kontejner spouští jako součást pod, která je chráněná proti síťovému mostu zvenčí. Je ale možné povolit ověřování, které funguje stejně jako ověřování používané při přístupu k [cloudovým Cognitive Services][request-authentication].

Následující diagram znázorňuje výchozí a **nezabezpečený** přístup:

![Zabezpečení kontejneru](../media/container-security.svg)

Jako alternativní a *bezpečný* přístup by spotřebitelé Cognitive Services kontejnerů mohli rozšířit kontejner s front-orientované komponentou a zachovat tak koncový bod kontejneru privátní. Pojďme se považovat za scénář, ve kterém používáme [Istio][istio] jako bránu příchozího přenosu dat. Istio podporuje protokol HTTPS/TLS a ověřování klientů a certifikátů. V tomto scénáři zveřejňuje Istio front-endu přístup k kontejneru a prezentuje klientský certifikát, který je schválen předem pomocí Istio.

[Nginx][nginx] je další oblíbená volba ve stejné kategorii. Istio i Nginx fungují jako síť služby a nabízejí další funkce, jako například vyrovnávání zatížení, směrování a řízení sazeb.

### <a name="container-networking"></a>Sítě kontejnerů

K odeslání informací o měření pro účely fakturace se vyžadují kontejnery Cognitive Services. Nepovedlo se zakázat seznam různých síťových kanálů, na kterých se spoléhá Cognitive Services kontejnery, aby se zabránilo tomu, že kontejner nebude fungovat.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Seznam povolených Cognitive Services domén a portů

Hostitel by měl povolený seznam **port 443** a následující domény:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Zakázat hloubkovou kontrolu paketů

> [Hloubková kontrola paketů](https://en.wikipedia.org/wiki/Deep_packet_inspection) (dpi) je typ zpracování dat, který podrobněji kontroluje data odesílaná přes počítačovou síť a obvykle provádí akci blokováním, opětovného směrování nebo jeho přihlašování.

Zakáže DPI u zabezpečených kanálů, které kontejnery Cognitive Services vytvoří na servery Microsoftu. V takovém případě se zabrání správnému fungování kontejneru.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
