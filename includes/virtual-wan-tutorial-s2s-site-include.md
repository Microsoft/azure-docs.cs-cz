---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/04/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: dbc4d73ab5faee89895f5bd06349dfe6bc8bfd5d
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627691"
---
1. Na stránce portálu pro virtuální síť WAN vyberte v části **připojení** možnost **sítě VPN** a otevřete stránku sítě VPN.
2. Na stránce **Lokality VPN** klikněte na **+Vytvořit lokalitu**.

   ![Snímek obrazovky s otevřeným panelem vytvořit lokalitu VPN zobrazuje okno site to Site (lokalita).](./media/virtual-wan-tutorial-site-include/basics.png "Základy")
3. Na stránce **vytvořit lokalitu sítě VPN** na kartě **základy** vyplňte následující pole:

    * **Oblast** – dříve odkazovala na umístění. Toto je umístění, ve kterém chcete vytvořit tento prostředek lokality.
    * **Název** – název, podle kterého chcete odkazovat na místní lokalitu.
    * **Dodavatel zařízení** – název dodavatele zařízení VPN (například: Citrix, Cisco, Barracuda). Přidání dodavatele zařízení může pomoct týmu Azure lépe pochopit vaše prostředí, aby bylo možné v budoucnu přidat další možnosti optimalizace, nebo vám pomůže při odstraňování potíží.
    * **Border Gateway Protocol** – nastavení **Povolit** znamená, že všechna připojení z webu budou povolena protokol BGP. Nakonec můžete nastavit informace protokolu BGP pro každý odkaz z webu VPN v části odkazy. Konfigurace protokolu BGP ve virtuální síti WAN je rovnocenná konfiguraci protokolu BGP v síti VPN brány virtuální sítě Azure. Vaše místní adresa partnerského uzlu BGP nesmí být stejná jako veřejná IP adresa vaší sítě VPN k zařízení nebo adresní prostor virtuální sítě sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Zadejte tuto adresu do odpovídající sítě VPN, která představuje umístění. Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Po povolení nastavení protokolu BGP sítě VPN můžete vždy upravit připojení k síti VPN a aktualizovat jeho parametry protokolu BGP (IP adresy partnerského vztahu na odkaz a jako #).
    * **Soukromý adresní prostor** – adresní prostor IP adres umístěný ve vaší místní lokalitě. Provoz určený do tohoto adresního prostoru se přesměruje do místní lokality. Tato možnost je vyžadována, pokud není pro daný web povolen protokol BGP.
    
      >[!NOTE]
      >Pokud po vytvoření lokality upravíte adresní prostor (například přidáte další adresní prostor), může trvat 8-10 minut, než se aktualizují efektivní trasy, zatímco se komponenty znovu vytvoří.
      >
    * **Centra** – rozbočovač, ke kterému se má vaše lokalita připojit Lokalita může být připojena pouze k rozbočovačům, které mají VPN Gateway. Pokud nevidíte centrum, vytvořte nejdřív v tomto centru bránu VPN.
4. Vyberte **odkazy** a přidejte informace o fyzických odkazech ve větvi. Pokud máte zařízení s virtuálním partnerem WAN, podívejte se na ně a zjistěte, jestli se tyto informace vyměňují s Azure jako součást nahrávání informací o větvích nastavených ze svých systémů.

   ![Snímek obrazovky se zobrazí podokno vytvořit lokalitu VPN s vybranou kartou odkazy.](./media/virtual-wan-tutorial-site-include/links.png "Odkazy")

    * **Název odkazu** – název, který chcete zadat pro fyzické propojení na webu VPN. Příklad: mylink1.
    * **Název poskytovatele** – název fyzického odkazu na webu VPN. Příklad: ATT, Verizon.
    * **Rychlost** – jedná se o rychlost zařízení VPN v umístění pobočky. Příklad: 50, což znamená, že 50 MB/s je rychlost zařízení VPN na pobočkové síti.
    * **IP adresa nebo plně kvalifikovaný název domény** – veřejná IP adresa místního zařízení pomocí tohoto odkazu. Volitelně můžete zadat privátní IP adresu vašeho místního zařízení VPN, které je za ExpressRoute. Můžete také zadat plně kvalifikovaný název domény. Například *Something.contoso.com*. Plně kvalifikovaný název domény by se měl přeložit z brány VPN. To je možné v případě, že je server DNS hostující tento plně kvalifikovaný název domény dosažitelný přes Internet. Pokud je zadaná jak IP adresa, tak plně kvalifikovaný název domény, má IP adresa přednost.

      >[!NOTE]
      >
      >* Podporuje jednu adresu IPv4 na plně kvalifikovaný název domény. Pokud se plně kvalifikovaný název domény přeloží na více IP adres, pak Brána VPN vybere první IP4 adresu ze seznamu. Adresy IPv6 se v tuto chvíli nepodporují.
      >
      >* Brána VPN Gateway udržuje mezipaměť DNS, která se aktualizuje každých 5 minut. Brána se pokusí přeložit plně kvalifikované názvy domén pouze pro odpojené tunely. Překlad plně kvalifikovaného názvu domény může také aktivovat resetování brány nebo změna konfigurace.
      >
5. Pomocí zaškrtávacího políčka můžete odstranit nebo přidat další odkazy. Podporují se čtyři odkazy na lokalitu VPN. Pokud máte například čtyři poskytovatele internetových služeb (Internet Service Provider) na umístění pobočky, můžete vytvořit čtyři odkazy, jednu pro každého poskytovatele internetových služeb a poskytnout informace pro každý odkaz.
6. Až dokončíte vyplňování polí, vyberte **zkontrolovat + vytvořit** a ověřte a vytvořte lokalitu.
7. Zobrazte stav na stránce weby sítě VPN. Lokalita přejde k **požadovanému připojení** , protože lokalita ještě není připojená k rozbočovači.
