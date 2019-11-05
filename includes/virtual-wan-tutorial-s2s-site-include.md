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
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488948"
---
1. Na stránce portálu pro virtuální síť WAN vyberte v části **připojení** možnost **sítě VPN** a otevřete stránku sítě VPN.
2. Na stránce **Lokality VPN** klikněte na **+Vytvořit lokalitu**.

   ![Základy](./media/virtual-wan-tutorial-site-include/basics.png "Základy")
3. Na stránce **vytvořit lokalitu sítě VPN** na kartě **základy** vyplňte následující pole:

    * **Oblast** – dříve odkazovala na umístění. Toto je umístění, ve kterém chcete vytvořit tento prostředek lokality.
    * **Název** – název, podle kterého chcete odkazovat na místní lokalitu.
    * **Dodavatel zařízení** – název dodavatele zařízení VPN (například: Citrix, Cisco, Barracuda). Díky tomu může tým Azure lépe pochopit vaše prostředí, aby v budoucnu přidal další možnosti optimalizace, nebo vám pomůže při odstraňování potíží.
    * **Border Gateway Protocol** -Enable znamená, že všechna připojení z webu budou povolena protokol BGP. Nakonec můžete nastavit informace protokolu BGP pro každý odkaz z webu VPN v části odkazy. Konfigurace protokolu BGP ve virtuální síti WAN je rovnocenná konfiguraci protokolu BGP v síti VPN brány virtuální sítě Azure. Vaše místní adresa partnerského uzlu BGP nesmí být stejná jako veřejná IP adresa vaší sítě VPN k zařízení nebo adresní prostor virtuální sítě sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Nemůže to ale být adresa APIPa (169.254. x. x). Zadejte tuto adresu do odpovídající sítě VPN, která představuje umístění. Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Po povolení nastavení protokolu BGP sítě VPN můžete vždy upravit připojení k síti VPN a aktualizovat jeho parametry protokolu BGP (IP adresy partnerského vztahu na odkaz a jako #).
    * **Soukromý adresní prostor** – adresní prostor IP adres umístěný ve vaší místní lokalitě. Provoz určený do tohoto adresního prostoru se přesměruje do místní lokality. Tato možnost je vyžadována, pokud není pro daný web povolen protokol BGP.
    * **Centra** – rozbočovač, ke kterému se má vaše lokalita připojit Lokalita může být připojena pouze k rozbočovačům, které mají VPN Gateway. Pokud se nezobrazuje rozbočovač, vytvořte nejdřív v tomto centru bránu VPN.
4. Vyberte **odkazy** a přidejte informace o fyzických odkazech ve větvi. Pokud máte zařízení s virtuálním partnerem WAN, podívejte se na ně a zjistěte, jestli se tyto informace vyměňují s Azure jako součást nahrávání informací o větvích nastavených ze svých systémů.

   ![odkazy](./media/virtual-wan-tutorial-site-include/links.png "Odkazy")

    * **Název odkazu** – název, který chcete zadat pro fyzické propojení na webu VPN. Příklad: mylink1.
    * **Název poskytovatele** – název fyzického odkazu na webu VPN. Příklad: ATT, Verizon.
    * **Rychlost** – jedná se o rychlost zařízení VPN v umístění pobočky. Příklad: 50, což znamená, že 50 MB/s je rychlost zařízení VPN na pobočkové síti.
    * **IP adresa** – veřejná IP adresa zařízení Prem pomocí tohoto odkazu. Volitelně můžete zadat privátní IP adresu vašeho místního zařízení VPN, které je za ExpressRoute.
5. K odstranění nebo přidání dalších odkazů můžete použít zaškrtávací políčko. Podporují se čtyři odkazy na lokalitu VPN. Pokud máte například čtyři poskytovatele internetových služeb (Internet Service Provider) na umístění pobočky, můžete vytvořit čtyři odkazy. jednu na jednotlivé poskytovatele internetových služeb a poskytněte informace pro každý odkaz.
6. Až dokončíte vyplňování polí, vyberte **zkontrolovat + vytvořit** a ověřte a vytvořte lokalitu.
7. Zobrazte stav na stránce weby sítě VPN. Lokalita přejde k **požadovanému připojení** , protože lokalita ještě není připojená k rozbočovači.