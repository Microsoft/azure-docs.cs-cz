---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: include
ms.date: 02/11/2021
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 708baa83ca919adcc374be36c229ce3ff30da384
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100362922"
---
1. Na stránce portálu pro virtuální síť WAN vyberte v části **připojení** možnost **sítě VPN** a otevřete stránku sítě VPN.
1. Na stránce **Lokality VPN** klikněte na **+Vytvořit lokalitu**.
1. Na stránce **vytvořit lokalitu sítě VPN** na kartě **základy** vyplňte následující pole:

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-basics.png" alt-text="Karta základy" lightbox="./media/virtual-wan-tutorial-site-include/site-basics.png":::

    * **Oblast** – dříve odkazovala na umístění. Toto je umístění, ve kterém chcete vytvořit tento prostředek lokality.
    * **Název** – název, podle kterého chcete odkazovat na místní lokalitu.
    * **Dodavatel zařízení** – název dodavatele zařízení VPN (například: Citrix, Cisco, Barracuda). Přidání dodavatele zařízení může pomoct týmu Azure lépe pochopit vaše prostředí, aby bylo možné v budoucnu přidat další možnosti optimalizace, nebo vám pomůže při odstraňování potíží.
    * **Soukromý adresní prostor** – adresní prostor IP adres umístěný ve vaší místní lokalitě. Provoz určený do tohoto adresního prostoru se přesměruje do místní lokality. Tato možnost je vyžadována, pokud není pro daný web povolen protokol BGP.
    
      >[!NOTE]
      >Pokud po vytvoření lokality upravíte adresní prostor (například přidáte další adresní prostor), může trvat 8-10 minut, než se aktualizují efektivní trasy, zatímco se komponenty znovu vytvoří.
      >
1. Vyberte **odkazy** a přidejte informace o fyzických odkazech ve větvi. Pokud máte zařízení s virtuálním partnerem WAN, podívejte se na ně a zjistěte, jestli se tyto informace vyměňují s Azure jako součást nahrávání informací o větvích nastavených ze svých systémů.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/site-links.png" alt-text="Karta odkazy" lightbox="./media/virtual-wan-tutorial-site-include/site-links.png":::

   * **Název odkazu** – název, který chcete zadat pro fyzické propojení na webu VPN. Příklad: mylink1.
   * **Rychlost připojení** – jedná se o rychlost zařízení VPN v umístění pobočky. Příklad: 50, což znamená, že 50 MB/s je rychlost zařízení VPN na pobočkové síti.
   * **Název poskytovatele odkazů** – název fyzického odkazu na webu VPN. Příklad: ATT, Verizon.
   * **Propojte IP adresu nebo plně kvalifikovaný název domény** – veřejnou IP adresu místního zařízení pomocí tohoto odkazu. Volitelně můžete zadat privátní IP adresu vašeho místního zařízení VPN, které je za ExpressRoute. Můžete také zadat plně kvalifikovaný název domény. Například *Something.contoso.com*. Plně kvalifikovaný název domény by se měl přeložit z brány VPN. To je možné v případě, že je server DNS hostující tento plně kvalifikovaný název domény dosažitelný přes Internet. Pokud je zadaná jak IP adresa, tak plně kvalifikovaný název domény, má IP adresa přednost.

     >[!NOTE]
     >
     >* Podporuje jednu adresu IPv4 na plně kvalifikovaný název domény. Pokud se plně kvalifikovaný název domény přeloží na více IP adres, pak Brána VPN vybere první IP4 adresu ze seznamu. Adresy IPv6 se v tuto chvíli nepodporují.
     >
     >* Brána VPN Gateway udržuje mezipaměť DNS, která se aktualizuje každých 5 minut. Brána se pokusí přeložit plně kvalifikované názvy domén pouze pro odpojené tunely. Překlad plně kvalifikovaného názvu domény může také aktivovat resetování brány nebo změna konfigurace.
     >
   * **Link Border Gateway Protocol** – konfigurace protokolu BGP ve virtuální síti WAN je ekvivalentní konfiguraci protokolu BGP u sítě VPN brány virtuální sítě Azure. Vaše místní adresa partnerského uzlu BGP nesmí být stejná jako veřejná IP adresa vaší sítě VPN k zařízení nebo adresní prostor virtuální sítě sítě VPN. Pro IP adresu partnerského uzlu BGP použijte jinou IP adresu na zařízení VPN. Může se jednat o adresu přiřazenou rozhraní zpětné smyčky v zařízení. Zadejte tuto adresu do odpovídající sítě VPN, která představuje umístění.  Požadavky protokolu BGP najdete v tématu [informace o protokolu BGP s Azure VPN Gateway](../articles/vpn-gateway/vpn-gateway-bgp-overview.md). Kdykoli můžete upravit připojení k síti VPN a aktualizovat parametry protokolu BGP (partnerské vztahy IP adresy na odkazu a jako #).
1. Můžete přidat nebo odstranit další odkazy. Podporují se čtyři odkazy na lokalitu VPN. Pokud máte například čtyři poskytovatele internetových služeb (Internet Service Provider) na umístění pobočky, můžete vytvořit čtyři odkazy, jednu pro každého poskytovatele internetových služeb a poskytnout informace pro každý odkaz.
1. Až dokončíte vyplňování polí, vyberte **zkontrolovat + vytvořit** a ověřte a vytvořte lokalitu.
1. Přejděte k požadovanému virtuálnímu rozbočovači a zrušte výběr **přidružení centra** pro připojení vašeho serveru VPN k centru.

   :::image type="content" source="./media/virtual-wan-tutorial-site-include/connect.png" alt-text="Připojit k tomuto centru" lightbox="./media/virtual-wan-tutorial-site-include/connect.png":::
