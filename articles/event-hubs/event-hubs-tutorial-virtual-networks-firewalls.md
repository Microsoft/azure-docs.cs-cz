---
title: Kurz – povolení virtuální sítě, integrace a brány firewall na služby Event Hubs | Dokumentace Microsoftu
description: V tomto kurzu se dozvíte, jak integrovat službu Event Hubs s virtuálními sítěmi a bránami firewall povolit zabezpečený přístup.
services: event-hubs-messaging
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 11/28/2018
ms.topic: tutorial
ms.service: event-hubs-messaging
ms.custom: mvc
ms.openlocfilehash: bd76d8a7f3f41a8aa6b2e614d37f361a98ac4efd
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53282756"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Kurz: Povolení integrace virtuální sítě a brány firewall na obor názvů Event Hubs

[Koncové body služeb virtuální sítě (VNet)](../virtual-network/virtual-network-service-endpoints-overview.md) rozšiřují privátní adresní prostor vaší virtuální sítě a její identitu vaší virtuální sítě do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

Brány firewall umožňují omezit přístup k oboru názvů Event Hubs z konkrétní IP adresy (nebo rozsahy IP adres)

Tento kurz ukazuje, jak integrovat koncové body služby virtuální sítě a nastavení brány firewall (filtrování protokolu IP) s existující názvů Azure Event Hubs pomocí portálu.

V tomto kurzu se dozvíte, jak:
> [!div class="checklist"]
> * Postup při integraci koncové body služby virtuální sítě s váš obor názvů služby Event Hubs.
> * Jak nastavit bránu Firewall (filtrování protokolu IP) s váš obor názvů služby Event Hubs.

>[!WARNING]
> Implementace integrace virtuální sítě můžete zabránit komunikaci se Service Bus dalšími službami Azure.
>
> První strany integrace nejsou podporovány při virtuální sítě jsou povolené a budou brzy dostupné.
> Běžné scénáře služby Azure, které nefungují s virtuálními sítěmi-
> * Protokolování a Diagnostika Azure
> * Azure Stream Analytics
> * Integrace Event gridu
> * Webové aplikace a funkce musí být ve virtuální síti.
> * IoT Hub trasy
> * IoT Device Explorer


> [!IMPORTANT]
> Virtuální sítě jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic.

Pokud nemáte předplatné Azure, vytvořte před zahájením [] [bezplatného účtu].

## <a name="prerequisites"></a>Požadavky

Můžeme využít existujícího oboru názvů služby Event Hubs, takže zkontrolujte, zda máte k dispozici oboru názvů Event Hubs. Pokud ne, najdete na [v tomto kurzu](./event-hubs-create.md)

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Nejprve přejděte na web [Azure Portal][Azure portal] a přihlaste se pomocí svého předplatného Azure.

## <a name="select-event-hubs-namespace"></a>Vyberte obor názvů služby Event Hubs

Pro účely tohoto kurzu jsme vytvořili obor názvů služby Event Hubs a přejdete na, který.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Přejděte na možnosti brány firewall a virtuální sítě

Použijte navigační nabídce a vyberte v levém podokně na portálu **"Brány firewall a virtuální sítě"** možnost.

  ![Přejděte do nabídky](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  První návštěvě této stránky **všechny sítě** přepínač musí být vybrán. Z toho vyplývá, že obor názvů Event Hubs umožňuje všechna příchozí připojení.

## <a name="add-virtual-network-service-endpoint"></a>Přidat koncový bod služby virtuální sítě

Pokud chcete omezit přístup, budete muset integraci koncového bodu služby virtuální sítě pro tento obor názvů služby Event Hubs.

1. Klikněte na tlačítko **vybrané sítě** přepínací tlačítka v horní části stránky umožňuje zbytku stránky s možností v nabídce.
  ![vybrané sítě](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. V části virtuální sítě na stránce vyberte možnost ***+ přidat existující virtuální síť***. To bude snímek v podokně, které vám umožní vybrat již vytvořená virtuální síť.
  ![Přidat existující virtuální síť](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. Vybrat virtuální síť v seznamu a vyberte podsíť.
   ![Vyberte podsíť.](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Je nutné povolit koncový bod služby před přidáním virtuální sítě do seznamu. Pokud koncový bod služby není povolen, na portálu zobrazí výzvu, aby je.
  ![Vyberte podsíť a povolení koncového bodu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png)
    > [!NOTE]
    > Pokud nemůžete povolit koncový bod služby, můžete ignorovat chybějící virtuální sítě koncového bodu služby pomocí šablony ARM. Tato funkce není k dispozici na portálu.

5. Po povolení koncového bodu služby v vybrané podsítě, můžete přejít k přidání do seznamu povolených virtuálních sítí.
  ![přidává se podsíť po povolení koncového bodu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png)

6. Přejít k dosažení **Uložit** tlačítko na horním pásu karet se uložit konfiguraci virtuální sítě ve službě. Počkejte prosím několik minut, než potvrzení se zobrazí na portálu oznámení.

## <a name="add-firewall-for-specified-ip"></a>Přidání brány Firewall pro zadané IP adresy

Pomocí pravidel brány Firewall jsme můžete omezit přístup k oboru názvů služby Event Hubs pro omezený rozsah IP adres nebo konkrétní IP adresu.

1. Klikněte na tlačítko **vybrané sítě** přepínací tlačítka v horní části stránky umožňuje zbytku stránky s možností v nabídce.
  ![vybrané sítě](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. V **brány Firewall** pod ***rozsah adres*** mřížky, můžete přidat jeden nebo mnoho konkrétní IP adresy nebo rozsahy IP adres.
  ![Přidejte ip adresy](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Po přidání několika IP adresami (nebo rozsahy IP adres), stiskněte **Uložit** na horním pásu karet Ujistěte se, že v konfiguraci uloží na straně služby. Počkejte prosím několik minut, než potvrzení se zobrazí na portálu oznámení.
  ![Přidejte ip adresy a stiskněte tlačítko Uložit](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Přidání vaši aktuální IP adresu do pravidla brány Firewall

1. Můžete také přidat vaši aktuální IP adresu rychle kontrolou ***přidat IP adresu vašeho klienta (VAŠI aktuální IP adresu)*** zaškrtávací políčko přímo nad ***rozsah adres*** mřížky.
  ![Přidat aktuální IP adresu](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Po přidání vaši aktuální IP adresu pro pravidla brány firewall, volání **Uložit** na horním pásu karet Ujistěte se, že v konfiguraci uloží na straně služby. Počkejte prosím několik minut, než potvrzení se zobrazí na portálu oznámení.
  ![Přidat aktuální IP adresu a klikněte na Uložit](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Závěr

V tomto kurzu se integrované koncové body virtuální sítě a pravidla brány Firewall pomocí existujícího oboru názvů služby Event Hubs. Můžete se dozvědět, jak do:
> [!div class="checklist"]
> * Postup při integraci koncové body služby virtuální sítě s váš obor názvů služby Event Hubs.
> * Jak nastavit bránu Firewall (filtrování protokolu IP) s váš obor názvů služby Event Hubs.


[Azure portal]: https://portal.azure.com/