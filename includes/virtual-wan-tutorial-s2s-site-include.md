---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 06/23/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: ba69b94514a0c1b59001011a64a4fb28e5e4a827
ms.sourcegitcommit: 6e1124fc25c3ddb3053b482b0ed33900f46464b3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/15/2020
ms.locfileid: "90606783"
---
1. Na stránce portálu pro virtuální síť WAN vyberte v části **připojení** možnost **sítě VPN** a otevřete stránku sítě VPN.
2. Na stránce **Lokality VPN** klikněte na **+Vytvořit lokalitu**.

   ![Snímek obrazovky se zobrazí v okně lokalita V nasazení sítě VPN s otevřeným podoknem vytvořit síť VPN.](./media/virtual-wan-tutorial-site-include/basics.png "Základy")
3. Na stránce **vytvořit lokalitu sítě VPN** na kartě **základy** vyplňte následující pole:

    * **Oblast** – dříve odkazovala na umístění. Toto je umístění, ve kterém chcete vytvořit tento prostředek lokality.
    * **Název** – název, podle kterého chcete odkazovat na místní lokalitu.
    * **Dodavatel zařízení** – název dodavatele zařízení VPN (například: Citrix, Cisco, Barracuda). Díky tomu může tým Azure lépe pochopit vaše prostředí, aby v budoucnu přidal další možnosti optimalizace, nebo vám pomůže při odstraňování potíží.
    * **Border Gateway Protocol** -Enable znamená, že všechna připojení z webu budou povolena protokol BGP. Nakonec můžete nastavit informace protokolu BGP pro každý odkaz z webu VPN v části odkazy. Konfigurace protokolu BGP ve virtuální síti WAN je rovnocenná konfiguraci protokolu BGP v síti VPN brány virtuální sítě Azure. Vaše místní adresa partnerského uzlu BGP nesmí být stejná jako veřejná IP adresa vaší sítě VPN k zařízení nebo adresní prostor virtuální sítě sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může to být adresa přiřazená k rozhraní zpětné smyčky v zařízení. Zadejte tuto adresu do odpovídající sítě VPN, která představuje umístění. Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Po povolení nastavení protokolu BGP sítě VPN můžete vždy upravit připojení k síti VPN a aktualizovat jeho parametry protokolu BGP (IP adresy partnerského vztahu na odkaz a jako #).
    * **Soukromý adresní prostor** – adresní prostor IP adres umístěný ve vaší místní lokalitě. Provoz určený do tohoto adresního prostoru se přesměruje do místní lokality. Tato možnost je vyžadována, pokud není pro daný web povolen protokol BGP.
    * **Centra** – rozbočovač, ke kterému se má vaše lokalita připojit Lokalita může být připojena pouze k rozbočovačům, které mají VPN Gateway. Pokud nevidíte centrum, vytvořte nejdřív v tomto centru bránu VPN.
4. Vyberte **odkazy** a přidejte informace o fyzických odkazech ve větvi. Pokud máte zařízení s virtuálním partnerem WAN, podívejte se na ně a zjistěte, jestli se tyto informace vyměňují s Azure jako součást nahrávání informací o větvích nastavených ze svých systémů.

   ![Snímek obrazovky se zobrazí v podokně webu vytvořit V P N s vybranou kartou odkazy.](./media/virtual-wan-tutorial-site-include/links.png "Odkazy")

    * **Název odkazu** – název, který chcete zadat pro fyzické propojení na webu VPN. Příklad: mylink1.
    * **Název poskytovatele** – název fyzického odkazu na webu VPN. Příklad: ATT, Verizon.
    * **Rychlost** – jedná se o rychlost zařízení VPN v umístění pobočky. Příklad: 50, což znamená, že 50 MB/s je rychlost zařízení VPN na pobočkové síti.
    * **IP adresa nebo plně kvalifikovaný název domény** – veřejná IP adresa místního zařízení pomocí tohoto odkazu. Volitelně můžete zadat privátní IP adresu vašeho místního zařízení VPN, které je za ExpressRoute. Můžete také zadat plně kvalifikovaný název domény. Například *Something.contoso.com*. Plně kvalifikovaný název domény by se měl přeložit z brány VPN. To je možné v případě, že je server DNS hostující tento plně kvalifikovaný název domény dosažitelný přes Internet. Pokud je zadaná jak IP adresa, tak plně kvalifikovaný název domény, má IP adresa přednost.

      >[!NOTE]
      >* Podporuje jednu adresu IPv4 na plně kvalifikovaný název domény. Pokud se plně kvalifikovaný název domény přeloží na více IP adres, pak Brána VPN vybere první IP4 adresu ze seznamu. Adresy IPv6 se v tuto chvíli nepodporují.
      >* Brána VPN Gateway udržuje mezipaměť DNS, která se aktualizuje každých 5 minut. Brána se pokusí přeložit plně kvalifikované názvy domén pouze pro odpojené tunely. Překlad plně kvalifikovaného názvu domény může také aktivovat resetování brány nebo změna konfigurace.
      >
5. K odstranění nebo přidání dalších odkazů můžete použít zaškrtávací políčko. Podporují se čtyři odkazy na lokalitu VPN. Pokud máte například čtyři poskytovatele internetových služeb (Internet Service Provider) na umístění pobočky, můžete vytvořit čtyři odkazy. jednu na jednotlivé poskytovatele internetových služeb a poskytněte informace pro každý odkaz.
6. Až dokončíte vyplňování polí, vyberte **zkontrolovat + vytvořit** a ověřte a vytvořte lokalitu.
7. Zobrazte stav na stránce weby sítě VPN. Lokalita přejde k **požadovanému připojení** , protože lokalita ještě není připojená k rozbočovači.
