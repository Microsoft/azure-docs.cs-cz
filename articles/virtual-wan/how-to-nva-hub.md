---
title: 'Azure Virtual WAN: Vytvoření síťového virtuálního zařízení (síťové virtuální zařízení) v centru'
description: Přečtěte si, jak nasadit síťové virtuální zařízení ve virtuálním centru sítě WAN.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: cherylmc
Customer intent: As someone with a networking background, I want to create a Network Virtual Appliance (NVA) in my Virtual WAN hub.
ms.openlocfilehash: f02edf8e192f4d32f8bd2583d46bbb17c86d2049
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454800"
---
# <a name="how-to-create-a-network-virtual-appliance-in-an-azure-virtual-wan-hub-preview"></a>Postup vytvoření síťového virtuálního zařízení ve službě Azure Virtual WAN hub (Preview)

V tomto článku se dozvíte, jak pomocí virtuální sítě WAN se připojit k prostředkům v Azure prostřednictvím **síťového virtuálního zařízení** (síťové virtuální zařízení) v Azure. Tento typ připojení vyžaduje místní zařízení VPN, které má přiřazenou veřejnou IP adresu. Další informace o virtuální síti WAN najdete v tématu [co je virtuální síť WAN?](virtual-wan-about.md).

Kroky v tomto článku vám pomůžou vytvořit virtuální síťové zařízení **Barracuda CLOUDGEN WAN** ve virtuálním centru sítě WAN. K dokončení tohoto cvičení musíte mít Barracuda cloudové místní zařízení (CPE) a licenci pro zařízení Barracuda CloudGen WAN, které nasadíte do centra před tím, než začnete.

Dokumentaci k nasazení **Cisco SD-WAN** v Azure Virtual WAN najdete v článku [Cisco Cloud OnRamp pro multi-Cloud](https://www.cisco.com/c/en/us/td/docs/routers/sdwan/configuration/cloudonramp/ios-xe-17/cloud-onramp-book-xe/cloud-onramp-multi-cloud.html#Cisco_Concept.dita_c61e0e7a-fff8-4080-afee-47b81e8df701). 

Dokumentaci k nasazení **VMware SD-WAN** v rámci Azure Virtual WAN najdete v [Průvodci nasazením pro VMware SD-WAN ve virtuálním centru sítě WAN](https://kb.vmware.com/s/article/82746) .

## <a name="prerequisites"></a>Požadavky

Před zahájením konfigurace ověřte, že splňujete následující kritéria:

* Získejte licenci pro bránu Barracuda CloudGen WAN. Další informace o tom, jak to provést, najdete v [dokumentaci k Barracuda CLOUDGEN WAN](https://www.barracuda.com/products/cloudgenwan) .

* Máte virtuální síť, ke které se chcete připojit. Ověřte, že se žádná z podsítí místních sítí nepřekrývá s virtuálními sítěmi, ke kterým se chcete připojit. Pokud chcete vytvořit virtuální síť v Azure Portal, přečtěte si [rychlý Start](../virtual-network/quick-create-portal.md).

* Vaše virtuální síť nemá žádné brány virtuální sítě. Pokud má vaše virtuální síť bránu (buď VPN, nebo ExpressRoute), musíte odebrat všechny brány. Tato konfigurace vyžaduje, aby se virtuální sítě místo toho připojovaly k virtuální bráně WAN hub.

* Zařiďte rozsah IP adres pro oblast vašeho rozbočovače. Centrum je virtuální síť, kterou vytváří a používá virtuální síť WAN. Rozsah adres, který zadáte pro centrum, se nemůže překrývat s žádnou ze stávajících virtuálních sítí, ke kterým se připojujete. Taky se nesmí překrývat s rozsahy adres, ke kterým se připojujete v místním prostředí. Pokud neznáte rozsahy IP adres nacházející se v konfiguraci vaší místní sítě, zajistěte koordinaci s někým, kdo vám poskytne tyto podrobnosti.

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="create-a-virtual-wan"></a><a name="openvwan"></a>Vytvoření virtuální sítě WAN

[!INCLUDE [Create virtual WAN](../../includes/virtual-wan-create-vwan-include.md)]

## <a name="create-a-hub"></a><a name="hub"></a>Vytvoření rozbočovače

Centrum je virtuální síť, která může obsahovat brány pro funkce typu Site-to-site, ExpressRoute, Point-to-site nebo síťové virtuální zařízení. Po vytvoření centra se vám bude centrum účtovat i v případě, že nepřipojíte žádné servery. Pokud se rozhodnete vytvořit bránu sítě VPN typu Site-to-site, bude vytvoření brány VPN typu Site-to-site ve virtuálním rozbočovači trvat 30 minut. Na rozdíl od typu Site-to-site, ExpressRoute nebo Point-to-site je nutné nejprve vytvořit rozbočovač, aby bylo možné nasadit síťové virtuální zařízení do virtuální sítě rozbočovače.

1. Vyhledejte virtuální síť WAN, kterou jste vytvořili. Na stránce **virtuální síť WAN** v části **připojení** vyberte **rozbočovače**.
1. Na stránce **centra** vyberte + nové centrum a otevřete stránku **vytvořit virtuální rozbočovač** .

   :::image type="content" source="./media/how-to-nva-hub/vwan-hub.png" alt-text="Základy":::
1. Na kartě **základy** stránky **vytvořit virtuální rozbočovač** vyplňte následující pole:

   **Podrobnosti o projektu**

   * Oblast (dříve označovaná jako umístění)
   * Name
   * Privátní adresní prostor centra Minimální adresní prostor je/24 pro vytvoření centra, což znamená, že při vytváření dojde k chybě z rozsahu od/25 do/32. Azure Virtual WAN, což je spravovaná služba Microsoftu, vytvoří ve virtuálním centru příslušné podsítě pro různé brány nebo služby. (Například: Síťová virtuální zařízení, brány VPN, brány ExpressRoute, uživatelské VPN/brány, brány firewall, směrování atd.). Není potřeba, aby uživatel explicitně naplánoval adresní prostor podsítě pro služby ve virtuálním centru, protože Microsoft to dělá jako součást služby.
1. Vyberte **zkontrolovat + vytvořit** k ověření.
1. Vyberte **vytvořit** a vytvořte tak centrum.

## <a name="create-the-network-virtual-appliance-in-the-hub"></a>Vytvoření síťového virtuálního zařízení v centru

V tomto kroku vytvoříte síťové virtuální zařízení v centru. Postup pro jednotlivé síťové virtuální zařízení se bude lišit pro jednotlivé produkty partnerů síťové virtuální zařízení. V tomto příkladu vytváříme bránu Barracuda CloudGen WAN.

1. Vyhledejte virtuální síť WAN, kterou jste vytvořili v předchozím kroku, a otevřete ji.

   :::image type="content" source="./media/how-to-nva-hub/nva-hub.png" alt-text="Virtuální rozbočovač":::
1. Vyhledejte dlaždici síťová virtuální zařízení a vyberte odkaz **vytvořit** .
1. V okně **virtuální síťové zařízení** vyberte **Barracuda CloudGen WAN** a pak klikněte na tlačítko **vytvořit** .

   :::image type="content" source="./media/how-to-nva-hub/select-nva.png" alt-text="Vybrat síťové virtuální zařízení":::
1. Tím přejdete na nabídku Azure Marketplace pro bránu Barracuda CloudGen WAN. Přečtěte si tyto informace a po dokončení klikněte na tlačítko **vytvořit** .

   :::image type="content" source="./media/how-to-nva-hub/barracuda-create-basics.png" alt-text="Základy síťové virtuální zařízení pro Barracuda":::
1. Na stránce **základy** budete potřebovat zadat následující informace:

   * **Předplatné** – vyberte předplatné, které jste použili k nasazení virtuální sítě WAN a centra.
   * **Skupina prostředků** – vyberte stejnou skupinu prostředků, kterou jste použili k nasazení virtuální sítě WAN a centra.
   * **Oblast** – vyberte stejnou oblast, ve které se nachází prostředek virtuálního rozbočovače.
   * **Název aplikace** – Barracuda NextGen WAN je spravovaná aplikace. Vyberte název, který usnadňuje identifikaci tohoto prostředku, protože se to bude volat při zobrazení ve vašem předplatném.
   * **Spravovaná skupina prostředků** – jedná se o název spravované skupiny prostředků, ve které bude Barracuda nasazovat prostředky, které jsou jimi spravované. Název by měl být předem vyplněný.
1. Vyberte tlačítko **Další: CLOUDGEN WAN Gateway** .

   :::image type="content" source="./media/how-to-nva-hub/barracuda-cloudgen-wan.png" alt-text="CloudGen brána WAN":::
1. Sem zadejte následující informace:

   * **Virtuální síť WAN** – virtuální centrum WAN, do kterého chcete nasadit tuto síťové virtuální zařízení.
   * **Jednotky infrastruktury síťové virtuální zařízení** – určete počet jednotek infrastruktury síťové virtuální zařízení, s kterými chcete nasadit tuto síťové virtuální zařízení. Vyberte velikost agregované kapacity šířky pásma, kterou chcete poskytnout napříč všemi pobočkami, které se budou připojovat k tomuto centru prostřednictvím tohoto síťové virtuální zařízení.
   * **Token** -Barracuda vyžaduje, abyste v tomto případě zadali ověřovací token, který se identifikuje jako registrovaný uživatel tohoto produktu. Budete ho muset získat z Barracuda.
1. Pokračujte výběrem tlačítka **zkontrolovat a vytvořit** .
1. Na této stránce se zobrazí výzva, abyste přijali podmínky smlouvy o přístupu k Co-Admin. To je standard u spravovaných aplikací, kde bude mít Vydavatel přístup k některým prostředkům v tomto nasazení. Zaškrtněte políčko **Souhlasím s podmínkami a ujednáními uvedenými nahoře** a pak vyberte **vytvořit**.

## <a name="connect-the-vnet-to-the-hub"></a><a name="vnet"></a>Připojení virtuální sítě k centru

[!INCLUDE [Connect](../../includes/virtual-wan-connect-vnet-hub-include.md)]

## <a name="next-steps"></a>Další kroky

* Další informace o virtuální síti WAN najdete na stránce [co je Virtual WAN?](virtual-wan-about.md) .
* Další informace o síťová virtuální zařízení ve virtuálním rozbočovači WAN najdete v tématu [informace o síťovém virtuálním zařízení ve virtuální síti WAN (Preview)](about-nva-hub.md).
