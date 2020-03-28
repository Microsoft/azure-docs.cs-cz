---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 11/04/2019
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 4bcee1097010bb8746b11185a470ca2584485c3f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "73488948"
---
1. Na stránce portálu pro virtuální wan vyberte v části **Připojení** **stránky VPN** a otevřete stránku lokalit VPN.
2. Na stránce **Lokality VPN** klikněte na **+Vytvořit lokalitu**.

   ![Základy](./media/virtual-wan-tutorial-site-include/basics.png "Základy")
3. Na stránce **Vytvořit web VPN** vyplňte na kartě **Základy** následující pole:

    * **Oblast** - Dříve označované jako umístění. Toto je umístění, ve kterém chcete vytvořit tento prostředek webu.
    * **Název** – název, pod kterým chcete odkazovat na místní web.
    * **Dodavatel zařízení** – název dodavatele zařízení VPN (například: Citrix, Cisco, Barracuda). To může pomoci Týmu Azure lépe pochopit vaše prostředí přidat další možnosti optimalizace v budoucnu, nebo vám pomůže řešit problémy.
    * **Protokol hraniční brány** – povolení znamená, že všechna připojení z lokality budou povolena protokolem BGP. Nakonec nastavíte informace protokolu BGP pro každý odkaz ze stránek VPN v části Odkazy. Konfigurace protokolu BGP ve virtuální síti WAN je ekvivalentní konfiguraci protokolu BGP v síti VPN virtuální síťové brány Azure. Vaše místní adresa partnerské sítě BGP se nesmí shodovat s veřejnou IP adresou vaší sítě VPN do zařízení nebo adresního prostoru virtuální sítě na webu VPN. Pro ip adresu partnera Protokolu BGP použijte na zařízení VPN jinou ADRESU IP. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Nemůže se však jedná o adresu APIPA (169.254.x.x). Zadejte tuto adresu v odpovídající síti VPN představující umístění. Požadavky protokolu BGP najdete [v tématu O protokolu BGP s bránou Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Vždy můžete upravit připojení VPN a aktualizovat jeho parametry Protokolu BGP (IP partnerského vztahu na odkazu a AS #) po povolení nastavení BGP lokality VPN.
    * **Privátní adresní prostor** – adresní prostor IP, který se nachází v místním webu. Provoz určený do tohoto adresního prostoru se přesměruje do místní lokality. To je vyžadováno v případě, že protokol BGP není pro web povolen.
    * **Rozbočovače** – rozbočovač, ke kterému se má web připojit. Web lze připojit pouze k rozbočovačům, které mají bránu VPN. Pokud nevidíte rozbočovač, nejprve vytvořte bránu VPN v tomto rozbočovači.
4. Výběrem **možnosti Odkazy** přidáte informace o fyzických odkazech na větvi. Pokud máte virtuální wan partner CPE zařízení, zkontrolujte s nimi, zda tyto informace se vyměňují s Azure jako součást pobočky informace nahrát nastavit z jejich systémů.

   ![Odkazy](./media/virtual-wan-tutorial-site-include/links.png "Odkazy")

    * **Název odkazu** – Název, který chcete poskytnout pro fyzický odkaz na webu VPN. Příklad: mylink1.
    * **Název zprostředkovatele** – název fyzického odkazu na webu VPN. Příklad: ATT, Verizon.
    * **Rychlost** - Jedná se o rychlost zařízení VPN v místě pobočky. Příklad: 50, což znamená, že 50 Mbps je rychlost zařízení VPN na pobočce.
    * **IP adresa** - Veřejná IP adresa on-prem zařízení pomocí tohoto odkazu. Volitelně můžete zadat privátní IP adresu místního zařízení VPN, které je za ExpressRoute.
5. Toto políčko můžete použít k odstranění nebo přidání dalších odkazů. Podporovány jsou čtyři odkazy na stránku VPN. Máte-li například v pobočkovém umístění čtyři poskytovatele služeb Internetu (poskytovatel služeb Internetu), můžete vytvořit čtyři odkazy. jeden na každého isp, a poskytnout informace pro každý odkaz.
6. Po dokončení vyplňování polí vyberte **Zkontrolovat + vytvořit,** abyste web ověřili a vytvořili.
7. Zobrazení stavu na stránce webů VPN. Web přejde na **potřebné připojení,** protože web ještě nebyl připojen k rozbočovači.