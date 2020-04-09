---
title: Zabezpečení kontejneru
titleSuffix: Azure Cognitive Services
description: Přečtěte si, jak zabezpečit kontejner
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: fd2a6cdad01302501e30ec60a4d3ccf6efd9c266
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80876812"
---
## <a name="azure-cognitive-services-container-security"></a>Zabezpečení kontejnerů azure cognitive services

Zabezpečení by mělo být primární zaměření při každém vývoji aplikací. Důležitost zabezpečení je metrika pro úspěch. Při navrhování softwarového řešení, které obsahuje kontejnery služeb Cognitive Services, je důležité porozumět omezením a možnostem, které máte k dispozici. Další informace o zabezpečení sítě najdete [v tématu Konfigurace virtuálních sítí Azure Cognitive Services][az-security].

> [!IMPORTANT]
> Ve výchozím nastavení neexistuje *žádné zabezpečení* rozhraní API kontejneru Cognitive Services. Důvodem je, že nejčastěji kontejner poběží jako součást podu, který je chráněn zvenčí síťovým mostem. Je však možné povolit ověřování, které funguje stejně jako ověřování používané při přístupu ke [cloudovým službám Cognitive Services][request-authentication].

Následující diagram znázorňuje výchozí a **nezabezpečený** přístup:

![Zabezpečení kontejneru](../media/container-security.svg)

Jako alternativní a *bezpečný* přístup mohou spotřebitelé kontejnerů služby Cognitive Services rozšířit kontejner s frontovou součástí a zachovat koncový bod kontejneru soukromý. Podívejme se na scénář, kde používáme [Istio][istio] jako vstupní bránu. Istio podporuje ověřování HTTPS/TLS a klientského certifikátu. V tomto scénáři front-end Istio zpřístupňuje přístup ke kontejneru, představující klientský certifikát, který je na seznamu povolených předem s Istio.

[Nginx][nginx] je další populární volbou ve stejné kategorii. Istio i Nginx fungují jako síť služeb a nabízejí další funkce, jako je vyrovnávání zatížení, směrování a řízení sazeb.

### <a name="container-networking"></a>Sítě kontejnerů

Kontejnery služeb Cognitive Services jsou nutné k odeslání informací o měření pro účely fakturace. Jedinou výjimkou jsou *offline kontejnery,* protože se řídí jinou metodikou fakturace. Nepovolení seznamu různých síťových kanálů, na které kontejnery služeb Cognitive Services spoléhají, zabrání teze kontejneru v práci.

#### <a name="allow-list-cognitive-services-domains-and-ports"></a>Povolit seznam domén a portů služeb Cognitive Services

Hostitel by měl povolit **seznam portů 443** a následujících domén:

* `*.cognitive.microsoft.com`
* `*.cognitiveservices.azure.com`

#### <a name="disable-deep-packet-inspection"></a>Zakázat hloubkovou kontrolu paketů

> [Hloubková kontrola paketů](https://en.wikipedia.org/wiki/Deep_packet_inspection) (DPI) je typ zpracování dat, který podrobně kontroluje data odesílaná v počítačové síti a obvykle provádí akci blokováním, přesměrováním nebo odpovídajícím protokolováním.

Zakažte DPI na zabezpečených kanálech, které kontejnery služeb Cognitive Services vytvářejí na serverech společnosti Microsoft. Pokud tak neučiníte, zabráníte správnému fungování kontejneru.

[istio]: https://istio.io/
[nginx]: https://www.nginx.com
[request-authentication]: ../../authentication.md
[az-security]: ../../cognitive-services-virtual-networks.md
