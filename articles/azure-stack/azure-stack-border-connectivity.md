---
title: Připojení k síti integrace důležité informace týkající se Azure zásobníku integrované systémy ohraničení | Microsoft Docs
description: Zjistěte, jak chcete pro připojení k síti datového centra ohraničení s několika uzly Azure zásobníku.
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
ms.date: 01/31/2018
ms.author: jeffgilb
ms.reviewer: wamota
ms.openlocfilehash: 93dd609df90adac2c84ba8c62cf0d18f55a317bb
ms.sourcegitcommit: 9d317dabf4a5cca13308c50a10349af0e72e1b7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2018
ms.locfileid: "28919430"
---
# <a name="border-connectivity"></a>Připojení ohraničení 
Plánování integrace sítě je důležitá povinná součást pro úspěšné nasazení Azure zásobníku integrované systémy, operace a správy. Plánování ohraničení připojení začne tak, že zvolíte, zda se má používat dynamické směrování s protokolu border gateway protocol (BGP). To vyžaduje přiřazení 16bitové číslo autonomního systému protokolu BGP (veřejných nebo soukromých) nebo pomocí statické směrování, kde výchozí statické trasy je přiřazená zařízení ohraničení.

> [!IMPORTANT]
> Horní části přepínače (TOR rack) vyžadují odchozí připojení vrstvy 3 s Point-to-Point IP adresy (/ 30 sítě) nakonfigurované na fyzických rozhraní. Není možné pomocí přepínače TOR podpora Azure zásobníku operations odchozí připojení vrstvy 2. 

## <a name="bgp-routing"></a>Směrování protokolu BGP
Pomocí o dynamický směrovací protokol, jako je protokol BGP zaručuje, že váš systém je vždy vědět změn v síti a usnadňuje správu. 

Jak je znázorněno v následujícím diagramu, inzerování privátní adresy IP místa v přepínači TOR je omezen pomocí předpony seznamu. Předpona seznamy odmítne privátní podsítě IP a jeho použití jako mapování trasy k připojení mezi TOR a ohraničení.

Nástroje pro vyrovnávání zatížení softwaru (SLB), běžících v rámci řešení zásobník Azure partnerský vztah do zařízení TOR, takže ho můžete dynamicky inzerovat virtuálních IP adres.

Pokud chcete, aby uživatel provozu okamžitě a transparentně obnoví selhání, VPC nebo MLAG nakonfigurované mezi zařízeními, TOR umožňuje použití více skříň odkaz agregace k hostitelům a HSRP nebo VRRP, která poskytuje síťové redundance pro sítě IP.

![Směrování protokolu BGP](media/azure-stack-border-connectivity/bgp-routing.png)

## <a name="static-routing"></a>statické směrování
Používání statických tras přidá více pevné konfigurace ohraničení a TOR zařízení. To vyžaduje důkladné analýzy před všechny změny. Problémy se nezdařila z důvodu chyby v konfiguraci může trvat delší dobu vrátit zpět v závislosti na změny provedené. Není mezi doporučené metody směrování, ale je podporovaná.

K integraci Azure zásobníku do vašeho síťového prostředí pomocí této metody, ohraničení zařízení musí být nakonfigurované statické trasy ukazující na zařízení TOR pro přenosy určené do externí sítě, veřejná virtuální IP adresy.

TOR zařízení musí být nakonfigurované výchozí statické trasy odesílání veškerý provoz do zařízení ohraničení. Jedinou výjimkou provoz toto pravidlo je pro privátní prostor, který se zablokuje, použít seznam řízení přístupu na TOR použita ohraničení připojení.

Všem ostatním musí být stejná jako první metodu. Protokol BGP dynamické směrování se stále použije v racku vzhledem k tomu, že je důležitý nástroj SLB a ostatními komponentami a nemůže být zakázán nebo odebrat.

![statické směrování](media/azure-stack-border-connectivity/static-routing.png)

## <a name="transparent-proxy"></a>Transparentní server proxy
Pokud vaše datové centrum vyžaduje, aby veškerý provoz do používat proxy server, musíte nakonfigurovat *transparentní proxy* zpracovat veškerý provoz z racku pro zpracování podle zásad oddělení provozu mezi zóny ve vaší síti.

> [!IMPORTANT]
> Řešení Azure zásobník nepodporuje normální webové proxy servery.  

Transparentní proxy (také označované jako zachycení, vložené nebo vynucené proxy) zachycuje normální komunikace síťové vrstvy bez nutnosti konfigurace žádné speciální klienta. Klienti nemusí být vědomi existenci proxy serveru.

![Transparentní server proxy](media/azure-stack-border-connectivity/transparent-proxy.png)

## <a name="next-steps"></a>Další postup
[Integrace služby DNS](azure-stack-integrate-dns.md)