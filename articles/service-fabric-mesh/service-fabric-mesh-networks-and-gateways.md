---
title: Úvod do služby Azure Service Fabric sítě | Dokumentace Microsoftu
description: Další informace o sítích, bran a směrování provozu inteligentní v Service Fabric mřížky.
services: service-fabric-mesh
documentationcenter: .net
author: rwike77
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/26/2018
ms.author: ryanwi
ms.custom: mvc, devcenter
ms.openlocfilehash: 8d9f5c71d05477094d733fd6b798831b3ff02ce1
ms.sourcegitcommit: 2bb46e5b3bcadc0a21f39072b981a3d357559191
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2018
ms.locfileid: "52892243"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Úvod do sítě aplikace Service Fabric mřížky
Tento článek popisuje různé typy nástrojů pro vyrovnávání zatížení, jak brány připojit k síti s vašimi aplikacemi k jiným sítím a směrování provozu mezi službami ve svých aplikacích.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Vrstvy 4 Vyrovnávání zatížení vrstvy 7 vs
Vyrovnávání zatížení může běžet na různých úrovních v [modelu OSI](https://en.wikipedia.org/wiki/OSI_model) pro sítě, často v vrstvy 4 (L4) a vrstvy 7 (L7).  Obvykle existují dva typy nástrojů pro vyrovnávání zatížení:

- Služby load balancer L4 funguje na přenosové vrstvě sítě, která se zabývá doručování paketů, bez ohledu na obsah pakety. Pouze hlavičky paketů jsou kontrolovány nástroje pro vyrovnávání zatížení, proto vyrovnávání kritéria je omezena na IP adresy a porty. Například klient zadává připojení TCP pro nástroj pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení ukončí připojení (Reagováním přímo na SYN), vybere back-endu. a vytvoří nové připojení TCP do back-endu (odešle nové SYN). Služby load balancer L4 obvykle funguje pouze na úrovni připojení L4 TCP/UDP nebo relace. Proto nástroj pro vyrovnávání zatížení přesměruje bajtů kolem a zajišťuje, že bajtů ze stejné relace dostal na stejný back-end. Nástroj pro vyrovnávání zatížení L4 nebude vědět o žádné podrobnosti o aplikaci bajtů, které se přesouvá. Počet bajtů, může být libovolného protokolu pro aplikaci.

- Nástroje pro vyrovnávání zatížení L7 funguje na aplikační vrstvu, která se zabývá obsah části jednotlivých paketů. Vzhledem k tomu, že to rozumí protokoly, například HTTP, HTTPS nebo protokoly Websocket prohlédne obsah paketů. To poskytuje možnost provádět pokročilé směrování nástroje pro vyrovnávání zatížení. Například klient zadává jediné připojení HTTP/2 TCP nástroji pro vyrovnávání zatížení. Nástroje pro vyrovnávání zatížení se pak pokračuje provést dvě připojení k back-endu. Když klient odešle dva datové proudy HTTP/2 do nástroje pro vyrovnávání zatížení, datového proudu, jeden je odeslána do back-endu, jeden a datový proud dvě posílá na back-end dvě. Díky tomu se ještě multiplexing klienti, kteří mají značně rozdílné žádost o načtení se vyvážená efektivně back-EndY. 

## <a name="networks-and-gateways"></a>Sítí a bran
V [modelu prostředků služby Service Fabric](service-fabric-mesh-service-fabric-resources.md), síťový prostředek je prostředek samostatně nasaditelných, nezávisle na prostředek aplikace nebo služby, který může označujeme jako jejich závislosti. Používá se k vytvoření sítě pro vaše aplikace, který je přístupný z Internetu. Více služeb z různých aplikací může být součástí stejné sítě. Tato privátní sítě se vytvoří a spravuje Service Fabric a není virtuální sítě Azure (VNET). Aplikace může dynamicky přidávat a odebírat z prostředku sítě můžete povolit nebo zakázat připojení k virtuální síti. 

Brány se používá pro přemostění dvě sítě. Prostředek brána nasadí [Envoy proxy](https://www.envoyproxy.io/) poskytující L4 směrování pro libovolný protokol a L7 směrování pro pokročilé směrování aplikace HTTP (S). Brány směruje provoz do vaší sítě z externí sítě a určuje, které pro směrování provozu do služby.  Externí síť může být otevřené sítě (v podstatě veřejného Internetu) nebo virtuální síť Azure, umožňuje propojení s vaší aplikací Azure a prostředky. 

![Sítě a brány][Image1]

Vytvoření síťových prostředků pomocí `ingressConfig`, veřejná IP adresa je přiřazen k síťovému prostředku. Veřejná IP adresa bude vázán na životnost síťový prostředek.

Po vytvoření aplikace OK ji by měl odkazovala na existující prostředek sítě. Je možné přidat nové veřejné porty nebo existující portů lze odebrat z příchozího přenosu dat konfigurace. Odstranění síťového prostředku se nezdaří, pokud odkazuje na prostředek aplikace. Když je aplikace odstraněna, síťový prostředek se odebere.

## <a name="next-steps"></a>Další postup 
Další informace o Service Fabric sítě, naleznete v přehledu:
- [Přehled Service Fabric mřížky](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png