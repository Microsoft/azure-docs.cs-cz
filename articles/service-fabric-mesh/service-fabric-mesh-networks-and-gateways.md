---
title: Úvod do sítě Azure Service Fabric
description: Seznamte se se sítěmi, branami a inteligentním směrováním provozu v síti Service Fabric Mesh.
author: dkkapur
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: dc793e2991783cc9b7b46d92fcc8e0267feb529b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75459132"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Úvod do sítě v aplikacích Service Fabric Mesh
Tento článek popisuje různé typy nástrojů pro vyrovnávání zatížení, jak brány spojují síť s aplikacemi s jinými sítěmi a jak je směrován provoz mezi službami ve vašich aplikacích.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Vrstva 4 vs vrstva 7 vykladače zatížení
Vyrovnávání zatížení lze provádět v různých vrstvách [modelu OSI](https://en.wikipedia.org/wiki/OSI_model) pro sítě, často ve vrstvě 4 (L4) a vrstvě 7 (L7).  Obvykle existují dva typy nástrojů pro vyrovnávání zatížení:

- Vyrovnávání zatížení L4 pracuje na síťové transportní vrstvě, která se zabývá doručováním paketů bez ohledu na obsah paketů. Pouze hlavičky paketů jsou kontrolovány v analyzátoru zatížení, takže kritéria vyrovnávání je omezena na IP adresy a porty. Klient například vytvoří připojení TCP k systému vyrovnávání zatížení. Vyrovnávání zatížení ukončí připojení (přímým reakcí na SYN), vybere back-end a vytvoří nové připojení TCP k back-endu (odešle nový SYN). Vykladač zatížení L4 obvykle pracuje pouze na úrovni připojení nebo relace L4 TCP/UDP. Proto vyrovnávání zatížení přesměruje bajty kolem a zajišťuje, že bajty ze stejné relace skončí ve stejném back-endu. L4 vyrovnávání zatížení neví o žádné podrobnosti aplikace bajtů, které se pohybuje. Bajty může být libovolný protokol aplikace.

- Vyrovnávání zatížení L7 pracuje na aplikační vrstvě, která se zabývá obsahem každého paketu. Kontroluje obsah paketů, protože rozumí protokolům, jako je HTTP, HTTPS nebo WebSockets. To dává balancer schopnost provádět pokročilé směrování. Klient například vytvoří jedno připojení TCP HTTP/2 k nástrojovi pro vyrovnávání zatížení. Vyrovnávání zatížení pak pokračuje, aby se dvě back-endová připojení. Když klient odešle dva datové proudy HTTP/2 do nástrojpro vyrovnávání zatížení, datový proud jeden je odeslán do back-endu jeden a datový proud dva je odeslán do back-endu dva. Proto i multiplexní klienti, kteří mají diametrálně odlišné zatížení požadavků, budou efektivně vyváženi napříč back-endy. 

## <a name="networks-and-gateways"></a>Sítě a brány
V [modelu prostředků Service Fabric](service-fabric-mesh-service-fabric-resources.md)je síťový prostředek individuálně nasaditelný prostředek, nezávislý na prostředku aplikace nebo služby, který se na něj může odkazovat jako na svou závislost. Používá se k vytvoření sítě pro vaše aplikace, která je otevřena pro internet. Součástí stejné sítě může být více služeb z různých aplikací. Tato privátní síť je vytvořená a spravovaná service fabric a není virtuální síť Azure (VNET). Aplikace lze dynamicky přidávat a odebírat ze síťového prostředku, aby bylo možné povolit a zakázat připojení virtuální sítě. 

Brána se používá k přemostění dvou sítí. Prostředek brány nasazuje [proxy server Envoy,](https://www.envoyproxy.io/) který poskytuje směrování L4 pro libovolný protokol a směrování L7 pro pokročilé směrování aplikací HTTP(S). Brána směruje provoz do vaší sítě z externí sítě a určuje, ke které službě má být provoz směrován.  Externí síť může být otevřená síť (v podstatě veřejný internet) nebo virtuální síť Azure, což vám umožní připojit se k ostatním aplikacím a prostředkům Azure. 

![Síť a brána][Image1]

Při vytváření síťového `ingressConfig`prostředku pomocí je k síťovému prostředku přiřazena veřejná adresa IP. Veřejná ip adresa bude vázána na životnost síťového prostředku.

Při vytvoření aplikace sítě by měla odkazovat na existující síťový prostředek. Nové veřejné porty mohou být přidány nebo existující porty mohou být odebrány z konfigurace příchozího přenosu dat. Odstranění síťového prostředku se nezdaří, pokud na něj odkazuje prostředek aplikace. Po odstranění aplikace je síťový prostředek odebrán.

## <a name="next-steps"></a>Další kroky 
Další informace o síti Service Fabric Mesh najdete v přehledu:
- [Service Fabric Mesh – přehled](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png