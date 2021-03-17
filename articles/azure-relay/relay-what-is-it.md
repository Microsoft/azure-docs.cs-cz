---
title: Co je Azure Relay? | Dokumentace Microsoftu
description: Tento článek obsahuje přehled služby Azure Relay, která umožňuje vyvíjet cloudové aplikace využívající místní služby spuštěné v podnikové síti, a to bez nutnosti otevírat připojení brány firewall nebo provádět výraznější změny síťové infrastruktury.
ms.topic: conceptual
ms.date: 06/23/2020
ms.openlocfilehash: fbf1b2134a4c2dce7a3e6a62668d0852dc08c18a
ms.sourcegitcommit: 19ffdad48bc4caca8f93c3b067d1cf29234fef47
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/06/2021
ms.locfileid: "97955378"
---
# <a name="what-is-azure-relay"></a>Co je Azure Relay?
Služba Azure Relay umožňuje bezpečně zpřístupnit služby spuštěné v podnikové síti pro veřejný cloud. Můžete to udělat bez otevření portu v bráně firewall nebo provedení rušivých změn v infrastruktuře podnikové sítě. 

Služba Relay podporuje následující scénáře mezi místními službami a aplikacemi spuštěnými v cloudu nebo v jiném místním prostředí. 

- Tradiční jednosměrná komunikace, komunikace typu požadavek/odpověď a komunikace peer-to-peer 
- Distribuce událostí na úrovni internetu, která umožňuje scénáře typu publikování/odběr 
- Obousměrná a nebufferovaná komunikace soketů přes hranice sítě

Azure Relay se liší od integračních technologií na úrovni sítě, jako je například síť VPN. Službu Azure Relay je možné omezit na jediný koncový bod aplikace na jednom počítači. Technologie sítě VPN se spoléhá na upravování síťového prostředí a mnohem víc do něj zasahuje. 

## <a name="basic-flow"></a>Základní tok
Přenos dat s předáváním zahrnuje následující základní kroky:

1. Místní služba se připojí k službě Relay přes odchozí port. 
2. Vytvoří obousměrný soket pro komunikaci vázanou na konkrétní adresu. 
3. Klient pak může komunikovat s místní službou odesíláním provozu do služby Relay s tím, že cílem je tato adresa. 
4. Služba Relay pak data *předá* místní službě přes obousměrný soket vyhrazený pro daného klienty. Klient nepotřebuje přímé připojení k místní službě. Nemusí ani vědět, kde se služba nachází. A místní služba nepotřebuje mít v bráně firewall otevřené žádné příchozí porty.


## <a name="features"></a>Funkce 
Azure Relay má dvě funkce:

- [Hybridní připojení](#hybrid-connections) – Pomocí otevřených webových soketů umožňuje scénáře s podporou více platforem.
- Přenosy WCF – Pomocí technologie Windows Communication Foundation (WCF) umožňuje vzdálená volání procedur. WCF Relay je starší verze nabídky přenosu, kterou již mnozí uživatelé používají ve svých programovacích modelech WCF.

## <a name="hybrid-connections"></a>Hybridní připojení

Zabezpečená funkce Hybrid Connections s podporou otevřených protokolů ve službě Azure Relay je důsledkem vývoje funkcí služby Relay, které existovaly dříve. Můžete ji používat na jakékoli platformě a v libovolném jazyce. Funkce Hybrid Connections ve službě Azure Relay je založená na protokolech HTTP a WebSockets. Umožňuje odesílat požadavky a přijímat odpovědi přes webové sokety nebo protokol HTTP nebo HTTPS. Tato funkce je kompatibilní s rozhraním API WebSocket v běžných webových prohlížečích. 

Podrobnosti o protokolu pro hybridní připojení najdete v [příručce k protokolům Hybrid Connections](relay-hybrid-connections-protocol.md). Hybrid Connections můžete používat s jakoukoli knihovnou webových soketů pro všechny moduly runtime a jazyky.

> [!NOTE]
> Funkce Hybrid Connections služby Azure Relay nahrazuje starší funkci hybridních připojení služby BizTalk Services. Funkce Hybrid Connections ve službě BizTalk Services využívala službu Azure Service Bus WCF Relay. Schopnost Hybrid Connections ve službě Azure Relay doplňuje již dříve existující funkci WCF Relay. Tyto dvě schopnosti služeb (WCF Relay a Hybrid Connections) existují ve službě Azure Relay vedle sebe. Sdílejí sice společnou bránu, jinak se ale jedná o rozdílné implementace.

## <a name="wcf-relay"></a>WCF Relay
WCF Relay funguje v celém rozhraní .NET Framework a funguje i s technologií WCF. Propojení místní služby se službou Relay vytvoříte pomocí skupiny „předávacích“ vazeb WCF. Vazby předávání se mapují na nové prvky vazby přenosu navržené pro vytváření komponent kanálu WCF, které se integrují s Service Bus v cloudu. Další informace najdete v článku [Začínáme s přenosy WCF](service-bus-relay-tutorial.md).

## <a name="hybrid-connections-vs-wcf-relay"></a>Hybrid Connections vs. WCF Relay
Hybrid Connections i WCF Relay umožňují zabezpečené připojení k prostředkům existujícím v rámci podnikové sítě. Použití jedné nebo druhé funkce závisí na konkrétních požadavcích, jak je popsáno v následující tabulce:

|  | WCF Relay | Hybridní připojení |
| --- |:---:|:---:|
| **WCF** |x | |
| **.NET Core** | |x |
| **.NET Framework** |x |x |
| **JavaScript/Node.js** | |x |
| **Otevřený protokol založený na standardech** | |x |
| **Programovací modely RPC** | |x |

## <a name="architecture-processing-of-incoming-relay-requests"></a>Architektura: Zpracování příchozích požadavků na předání
Následující diagram ukazuje, jak služba Azure Relay zpracovává příchozí požadavky na předání:

![Zpracování příchozích událostí požadavků na předání WCF](./media/relay-what-is-it/ic690645.png)

1. Čekající klient odešle do služby Azure Relay požadavek na poslech. Nástroj pro vyrovnávání zatížení Azure požadavek přesměruje do jednoho z uzlů brány. 
2. Služba Azure Relay vytvoří předání v úložišti brány. 
3. Odesílající klient odešle požadavek na připojení k čekající službě. 
4. Brána, která tento požadavek přijme, vyhledá předání v úložišti brány. 
5. Brána přesměruje požadavek na připojení do správné brány uvedené v úložišti brány. 
6. Brána odešle do čekajícího klienta požadavek na vytvoření dočasného kanálu k uzlu brány, která je nejblíže odesílajícímu klientovi. 
7. Čekající klient vytvoří dočasný kanál k bráně, která je nejblíže odesílajícímu klientovi. Když je teď navázané připojení mezi klienty prostřednictvím brány firewall, můžou si klienti mezi sebou vyměňovat zprávy. 
8. Brána přepošle všechny zprávy z naslouchajícího klienta do odesílajícího klienta. 
9. Brána přesměruje všechny zprávy z odesílajícího klienta do čekajícího klienta.  

## <a name="next-steps"></a>Další kroky
* [Začínáme s objekty WebSockets .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Začínáme s požadavky HTTP v .NET](relay-hybrid-connections-http-requests-dotnet-get-started.md)
* [Začínáme s objekty WebSockets uzlu](relay-hybrid-connections-node-get-started.md)
* [Začínáme s požadavky HTTP v Node](relay-hybrid-connections-http-requests-node-get-started.md)
* [Přenos – nejčastější dotazy](relay-faq.md)

