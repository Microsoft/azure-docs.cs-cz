---
title: Seznámení se službou Azure Service Fabric Networking
description: Přečtěte si o sítích, branách a inteligentním směrování provozu v Service Fabric sítě.
author: georgewallace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: gwallace
ms.custom: mvc, devcenter
ms.openlocfilehash: ef8b40be366afe24b960f7419d84cc2a9976b592
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99625697"
---
# <a name="introduction-to-networking-in-service-fabric-mesh-applications"></a>Úvod do sítě v aplikacích Service Fabric sítě

> [!IMPORTANT]
> Náhled sítě Azure Service Fabric je vyřazený. Nová nasazení již nebudou povolena prostřednictvím rozhraní API pro Service Fabric sítě. Podpora stávajících nasazení bude pokračovat do 28. dubna 2021.
> 
> Podrobnosti najdete v tématu [vyřazení náhledu do sítě Azure Service Fabric](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/).

Tento článek popisuje různé typy nástrojů pro vyrovnávání zatížení, jak brány spojují síť s vašimi aplikacemi s ostatními sítěmi a jak se směruje provoz mezi službami ve vašich aplikacích.

## <a name="layer-4-vs-layer-7-load-balancers"></a>Nástroje pro vyrovnávání zatížení vrstvy 4 vs. 7
Vyrovnávání zatížení je možné provádět v různých vrstvách [modelu OSI](https://en.wikipedia.org/wiki/OSI_model) pro sítě, často ve vrstvě 4 (L4) a ve vrstvě 7 (L7).  Obvykle existují dva typy nástrojů pro vyrovnávání zatížení:

- Nástroj pro vyrovnávání zatížení L4 funguje v síťové přenosové vrstvě, která se zabývá doručováním paketů bez ohledu na obsah paketů. Nástroj pro vyrovnávání zatížení kontroluje pouze hlavičky paketů, proto jsou kritéria vyrovnávání omezena na IP adresy a porty. Klient například provede připojení TCP k nástroji pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení ukončí připojení (přímým reakcí na SYN), vybere back-end a vytvoří nové připojení TCP k back-endu (odešle novou hodnotu SYN). Nástroj pro vyrovnávání zatížení L4 typicky funguje jenom na úrovni připojení nebo relace ke L4 protokolu TCP/UDP. Proto vyrovnávání zatížení přesměruje bajty kolem a zajistí, že bajty ze stejné relace se budou převíjet do stejného back-endu. Nástroj pro vyrovnávání zatížení L4 neví o všech podrobnostech aplikace v bajtech, které přesouvá. Bajty můžou být libovolný aplikační protokol.

- Nástroj pro vyrovnávání zatížení L7 funguje na vrstvě aplikace, která se zabývá obsahem každého paketu. Kontroluje obsah paketů, protože rozumí protokolům, jako jsou HTTP, HTTPS nebo WebSockets. Díky tomu může nástroj pro vyrovnávání zatížení provádět pokročilé směrování. Například klient provede jedno připojení HTTP/2 TCP k nástroji pro vyrovnávání zatížení. Nástroj pro vyrovnávání zatížení pak provede dvě back-endové připojení. Když klient pošle do nástroje pro vyrovnávání zatížení dva proudy HTTP/2, pošle se datový proud do back-endu One a datový proud 2 se pošle do back-endu. Takže i multiplexní klienti, kteří mají rozsáhlé různé zátěže požadavků, budou efektivně vyvážené napříč back-endy. 

## <a name="networks-and-gateways"></a>Sítě a brány
V [modelu Service Fabric prostředků](service-fabric-mesh-service-fabric-resources.md)je síťový prostředek samostatně nasaditelné prostředky nezávisle na prostředku aplikace nebo služby, který se na něj může odkazovat jako na jejich závislosti. Slouží k vytvoření sítě pro vaše aplikace, které jsou otevřeny v Internetu. Několik služeb z různých aplikací může být součástí stejné sítě. Tato privátní síť se vytváří a spravuje pomocí Service Fabric a nejedná se o virtuální síť Azure (VNET). Aplikace se dají dynamicky přidávat a odebírat ze síťového prostředku, aby bylo možné povolit a zakázat připojení k virtuální síti. 

Brána se používá pro mostování dvou sítí. Prostředek brány nasadí [proxy server zástupné](https://www.envoyproxy.io/) , který poskytuje směrování L4 pro jakýkoliv protokol a směrování L7 pro pokročilou směrování aplikace HTTP (S). Brána směruje provoz do vaší sítě z externí sítě a určuje, do které služby se má směrovat provoz.  Externí síť může být otevřená síť (v podstatě veřejný Internet) nebo virtuální síť Azure, která vám umožní připojit se k ostatním aplikacím a prostředkům Azure. 

![Síť a brána][Image1]

Při vytvoření síťového prostředku pomocí se `ingressConfig` k síťovému prostředku přiřadí veřejná IP adresa. Veřejná IP adresa bude vázaná na životnost síťového prostředku.

Při vytvoření mřížky aplikace by se měla odkazovat na stávající síťový prostředek. Můžete přidat nové veřejné porty nebo odebrat existující porty z konfigurace příchozího přenosu dat. Odstranění pro síťový prostředek se nezdaří, pokud na něj odkazuje prostředek aplikace. Po odstranění aplikace dojde k odebrání síťového prostředku.

## <a name="next-steps"></a>Další kroky 
Další informace o Service Fabric sítě najdete v článku Přehled:
- [Přehled Service Fabric sítě](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-networks-and-gateways/NetworkAndGateway.png