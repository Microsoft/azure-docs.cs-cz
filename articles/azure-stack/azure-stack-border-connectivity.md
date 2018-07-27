---
title: Ohraničení, která se připojení k síti integrace důležité informace pro integrované systémy Azure Stack | Dokumentace Microsoftu
description: Zjistěte, co vám pomůžou plánovat pro připojení k síti ohraničení datového centra pomocí služby Azure Stack víc uzlů.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 260c58ad9099a4532c8a6558cfcf5c13f0fc8d52
ms.sourcegitcommit: 068fc623c1bb7fb767919c4882280cad8bc33e3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/27/2018
ms.locfileid: "39282004"
---
# <a name="border-connectivity"></a>Připojení k ohraničení 
Plánování integrace sítě je důležitá povinná součást pro úspěšné nasazení Azure Stack integrované systémy, provoz a správu. Plánování připojení ohraničení začíná výběru, jestli se mají použít s dynamickým směrováním pomocí protokolu border gateway protocol (BGP). To vyžaduje přiřazení 16bitové číslo autonomního systému protokolu BGP (veřejné nebo soukromé) nebo pomocí statické směrování, kde výchozí statické trasy je přiřazená zařízení ohraničení.

> [!IMPORTANT]
> Horní části přepínače (TOR rack) vyžadují odchozí připojení vrstvy 3 s IP adresami Point-to-Point (/ 30 sítě) nakonfigurované na fyzických rozhraní. Není možné použít odchozí připojení vrstvy 2 pomocí přepínače TOR podporují operace služby Azure Stack. 

## <a name="bgp-routing"></a>Směrování protokolu BGP
Pomocí o dynamický směrovací protokol, jako je protokol BGP zaručuje, že váš systém je vždy vědom změn v síti a usnadňuje správu. 

Jak je znázorněno v následujícím diagramu, reklamní privátní IP adresy místo v přepínači TOR je omezen pomocí seznam předpon. Předpona seznamy zakazuje privátní podsítě IP a jeho použití jako mapu trasy pro připojení mezi TOR a ohraničení.

Nástroje pro vyrovnávání zatížení softwaru (SLB), spuštěné v Azure stacku řešení partnerský vztah TOR zařízení, takže ho můžete dynamicky inzerovat virtuální IP adresy.

Pokud chcete zajistit, aby uživatelský provoz okamžitě a transparentně provádí zotavení z chyby, VPC nebo MLAG nakonfigurované mezi zařízeními, TOR umožňuje použití více skříní odkaz síť agregaci na hostitelích a HSRP nebo VRRP, která poskytuje redundance pro sítě IP.

![Směrování protokolu BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>Statické směrování
Statické směrování vyžaduje další konfiguraci pro zařízení ohraničení. Vyžaduje další ruční zásah a řízení, jakož i důkladné analýzy před všechny změny a potíže způsobené službou chyby v konfiguraci může trvat delší dobu vrácení zpět v závislosti na změny. Není doporučenou metodu směrování, ale je podporované.

K integraci Azure Stack do vašeho síťového prostředí pomocí statické směrování, musí být připojené všechny čtyři fyzické propojení mezi ohraničením a zařízení sítě TOR a vysoké dostupnosti nelze zaručit kvůli funguje jak statické směrování.

Hraniční zařízení musí mít nakonfigurovanou statické trasy ukazující na zařízení TOR P2P pro provoz směřující na externí síti nebo veřejné virtuální IP adresy a síťové infrastruktury. Statické trasy do sítě BMC vyžaduje pro nasazení. Zákazníci můžou rozhodnout pro statické trasy ponechte ohraničení pro přístup k některé prostředky, které se nacházejí v síti BMC.  Přidání statické trasy do *přepínač infrastruktury* a *přepnout správu* sítí je volitelné.

Zařízení TOR součástí nakonfigurované statické výchozí trasa odesílá veškerý provoz do zařízení ohraničení. Jedinou výjimkou provoz do výchozí pravidlo je privátní prostoru, který se zablokoval použít seznam řízení přístupu na TOR u připojení ohraničení.

Statické směrování platí jenom pro odchozí připojení mezi přepínače TOR a ohraničení. Dynamické směrování protokolu BGP se používá v racku, protože je to důležitý nástroj SLB a další součásti a nemůže být zakázána nebo odebrat.

![Statické směrování](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transparentní proxy server
Pokud vaše datové centrum vyžaduje veškerý provoz směrem k použití proxy serveru, musíte nakonfigurovat *transparentní proxy server* zpracovat veškerý provoz z rack, aby to zvládnul souladu se zásadami, oddělení provozu mezi zónami ve vaší síti.

> [!IMPORTANT]
> Řešení Azure Stack nepodporuje běžné webové proxy servery.  

Transparentní proxy server (označované také jako zachycení, vložených nebo vynucené proxy) zachycuje normální komunikace na síťové vrstvě bez nutnosti jakékoli konfigurace speciální klienta. Klienti nemusí vědět o existenci proxy serveru.

![Transparentní proxy server](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Další postup
[Integrace DNS](azure-stack-integrate-dns.md)