---
title: Přehled služby Azure Relay a důvodů pro její používání | Microsoft Docs
description: Přehled služby Azure Relay
services: service-bus-relay
documentationcenter: .net
author: sethmanheim
manager: timlt
editor: ''
ms.assetid: 1e3e971d-2a24-4f96-a88a-ce3ea2b1a1cd
ms.service: service-bus-relay
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: get-started-article
ms.date: 05/02/2018
ms.author: sethm
ms.openlocfilehash: 2b179f8f5de9a0020ea6457c11bb6f48f3a51320
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="what-is-azure-relay"></a>Co je Azure Relay?

Služba Azure Relay hybridním aplikacím usnadňuje práci tím, že vám umožní bezpečně vystavit veřejnému cloudu služby, které se nacházejí v podnikové síti, a to bez nutnosti otevřít připojení brány firewall nebo provést výraznější změny v infrastruktuře podnikové sítě. Služba Relay podporuje různé přenosové protokoly a standardy webových služeb.

Služba Relay podporuje tradiční jednosměrný provoz, provoz typu požadavek/odpověď a provoz peer-to-peer. Taky podporuje distribuci událostí na úrovni internetu, která umožňuje scénáře typu publikování+odběr a obousměrnou soketovou komunikací pro zvýšenou účinnost mezi body.

V přenosu dat s předáváním se lokální služba připojí k službě Relay přes port pro odchozí spojení a vytvoří obousměrný soket pro komunikaci vázanou na konkrétní potkávací adresu. Klient pak může komunikovat s místní službou odesíláním provozu do služby Relay s tím, že cílem je potkávací adresa. Služba Relay pak data „předá“ místní službě přes obousměrný soket vyhrazený pro jednotlivé klienty. Klient nepotřebuje přímé spojení s lokální službou, nemusí ani vědět, kde se služba nachází, a lokální služba nepotřebuje mít ve firewallu otevřené žádné příchozí porty.

Mezi klíčové schopnosti, které služba Relay nabízí, patří obousměrná komunikace bez vyrovnávací paměti přes hranice sítí s omezováním toku dat (podobně jako u protokolu TCP), zjišťování koncových bodů, stav připojení a překrývající se zabezpečení koncových bodů.

Schopnosti služby Relay se liší od integračních technologií na úrovni sítě, jako je například síť VPN, tím, že službu Relay lze omezit na jediný koncový bod aplikace na jednom počítači, zatímco technologie sítě VPN se spoléhá na upravování síťového prostředí a mnohem víc do něj zasahuje.

Azure Relay má dvě funkce:

1. [Hybridní připojení](#hybrid-connections) – Pomocí otevřených webových soketů umožňuje scénáře s podporou více platforem.
2. [Přenosy WCF](#wcf-relays) – Pomocí technologie Windows Communication Foundation (WCF) umožňuje vzdálená volání procedur. WCF Relay je starší verze nabídky přenosu, kterou již mnozí uživatelé používají ve svých programovacích modelech WCF.

Hybridní připojení i přenosy WCF umožňují zabezpečené připojení k prostředkům existujícím v rámci podnikové sítě. Použití jedné nebo druhé funkce závisí na konkrétních požadavcích, jak je popsáno v následující tabulce:

|  | WCF Relay | Hybridní připojení |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/NodeJS** | |x |
| **Otevřený protokol založený na standardech** | |x |
| **Několik programovacích modelů protokolu RPC** | |x |

## <a name="hybrid-connections"></a>Hybridní připojení

Schopnost zabezpečených hybridních připojení služby Azure Relay s podporou otevřených protokolů je důsledkem vývoje stávajících funkcí služby Relay a dá se nasadit na jakékoli platformě a v libovolném jazyce. Hybridní připojení můžou přenášet protokoly WebSocket i požadavky a odpovědi HTTP(S). Tyto schopnosti jsou kompatibilní s rozhraním API WebSocket v běžných webových prohlížečích. Hybridní připojení jsou založená na protokolech HTTP a WebSocket.

Protokol je plně zdokumentovaný v [průvodci protokolem hybridních připojení](relay-hybrid-connections-protocol.md) a umožňuje používání služby Relay hybridních připojení s prakticky jakoukoli knihovnou WebSocketů pro všechny moduly runtime a jazyky.

### <a name="service-history"></a>Historie služby

Hybridní připojení nahrazuje starší funkci služby BizTalk Services s podobným názvem, která byla postavená na službě Azure Service Bus WCF Relay. Nová schopnost Hybrid Connections doplňuje stávající funkci WCF Relay a tyto dvě schopnosti existují ve službě Azure Relay vedle sebe. Sdílejí sice společnou bránu, jinak se ale jedná o rozdílné implementace.

## <a name="wcf-relay"></a>WCF Relay

Přenos WCF funguje v celém rozhraní .NET Framework (NETFX) a funguje i s technologií WCF. Propojení místní služby se službou Relay vytvoříte pomocí skupiny „předávacích“ vazeb WCF. Na pozadí se děje to, že předávací vazby mapují do nových elementů přenosové vazby určené k vytvoření komponentů kanálu WCF, které se integrují se službou Service Bus v cloudu. Další informace najdete v článku [Začínáme s přenosy WCF](relay-wcf-dotnet-get-started.md).

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architektura: Zpracování příchozích požadavků na předání

Když klient odešle požadavek do služby [Azure Relay](/azure/service-bus-relay/), nástroj pro vyrovnávání zatížení Azure ho přesměruje do některého z uzlů brány. Pokud se jedná o požadavek na poslech, uzel brány vytvoří nové propojení. Pokud se jedná o požadavek na připojení ke konkrétnímu propojení, uzel brány předá požadavek na spojení uzlu brány, který vlastní požadované propojení. Uzel brány, který vlastní požadované propojení, pošle čekajícímu klientovi požadavek na setkání a pokyn, aby klient vytvořil dočasný kanál pro uzel brány, který obdržel požadavek na připojení.

Když se vytvoří předávací spojení, klienti si můžou vyměňovat zprávy přes uzel brány, který se používá pro setkání.

![Zpracování příchozích událostí požadavků na předání WCF](./media/relay-what-is-it/ic690645.png)

## <a name="next-steps"></a>Další kroky

* [Přenos – nejčastější dotazy](relay-faq.md)
* [Vytvoření oboru názvů](relay-create-namespace-portal.md)
* [Začínáme s WebSockety v .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s požadavky HTTP v .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Začínáme s WebSockety v Node](relay-hybrid-connections-node-get-started.md)
* [Začínáme s požadavky HTTP v Node](relay-hybrid-connections-http-requests-node-get-started.md)

