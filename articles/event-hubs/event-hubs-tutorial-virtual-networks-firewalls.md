---
title: Azure Event Hubs – povolení integrace virtuálních sítí a bran firewall
description: V tomto kurzu se naučíte, jak integrovat Event Hubs s virtuálními sítěmi a branami firewall, aby se povolil zabezpečený přístup.
services: event-hubs
author: axisc
manager: darosa
ms.author: aschhab
ms.date: 12/20/2019
ms.topic: tutorial
ms.service: event-hubs
ms.custom: mvc
ms.openlocfilehash: f911a1513c6f89180ea51cc0de96dc8a475c7fc8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75437108"
---
# <a name="tutorial-enable-virtual-networks-integration-and-firewalls-on-event-hubs-namespace"></a>Kurz: povolení integrace virtuálních sítí a bran firewall v oboru názvů Event Hubs

[Koncové body služby Virtual Network (VNET)](../virtual-network/virtual-network-service-endpoints-overview.md) zvyšují privátní adresní prostor vaší virtuální sítě a identitu vaší virtuální sítě do služeb Azure přes přímé připojení. Koncové body umožňují svázat vaše důležité prostředky služeb Azure pouze s vašimi virtuálními sítěmi. Provoz z vaší virtuální sítě do služby Azure vždy zůstává v páteřní síti Microsoft Azure.

Brány firewall umožňují omezit přístup k oboru názvů Event Hubs z konkrétních IP adres (nebo rozsahů IP adres).

V tomto kurzu se dozvíte, jak integrovat koncové body služby virtuální sítě a jak pomocí portálu nastavit brány firewall (filtrování IP adres) se stávajícím oborem názvů Azure Event Hubs.

V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Postup integrace koncových bodů služby virtuální sítě s oborem názvů Event Hubs.
> * Jak nastavit bránu firewall (filtrování IP) s oborem názvů Event Hubs.

>[!WARNING]
> Implementace integrace virtuálních sítí může ostatním službám Azure zabránit v interakci s Event Hubs.
>
> Integrace se službou First stran není podporována, pokud jsou povoleny virtuální sítě.
> Běžné scénáře Azure, které nefungují s virtuálními sítěmi –
> * Azure Diagnostics a protokolování
> * Azure Stream Analytics
> * Integrace Event Grid
> * Web Apps & funkce musí být ve virtuální síti.
> * IoT Hub Routes
> * Device Explorer IoT


> [!IMPORTANT]
> Virtuální sítě jsou podporovány v **standardní** a **vyhrazené** úrovně služby Event Hubs. Není podporována v úrovni basic.

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet] [] před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

Využijeme stávající obor názvů Event Hubs. Ujistěte se prosím, že máte k dispozici Event Hubs obor názvů. Pokud to neuděláte, přečtěte si prosím [Tento kurz](./event-hubs-create.md) .

## <a name="sign-in-to-the-azure-portal"></a>Přihlášení k webu Azure Portal

Nejprve přejdete na [Azure Portal][Azure portal] a přihlaste se pomocí svého předplatného Azure.

## <a name="select-event-hubs-namespace"></a>Vybrat Event Hubs obor názvů

Pro účely tohoto kurzu jsme vytvořili obor názvů Event Hubs a přejdou na to.

## <a name="navigate-to-firewalls-and-virtual-networks-experience"></a>Přechod na možnosti brány firewall a virtuální sítě

Pomocí navigační nabídky v levém podokně na portálu vyberte možnost **brány firewall a virtuální sítě** .

  ![Přejít k nabídce](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-landing-page.png)

  Při první návštěvě této stránky je nutné vybrat přepínač **všechny sítě** . To znamená, že obor názvů Event Hubs umožňuje všechna příchozí připojení.

## <a name="add-virtual-network-service-endpoint"></a>Přidat Virtual Network koncový bod služby

Chcete-li omezit přístup, je nutné do tohoto oboru názvů Event Hubs integrovat koncový bod služby Virtual Network.

1. Klikněte na přepínač **vybrané sítě** v horní části stránky a povolte tak zbývající část stránky s možnostmi nabídky.
  ![vybrané sítě](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. V části Virtual Network stránky vyberte možnost ***+ Přidat existující virtuální síť***. Tím přejdete do podokna, které vám umožní vybrat už vytvořenou virtuální síť.
  ![přidat existující virtuální síť](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane.png)
3. V seznamu vyberte Virtual Network a vyberte podsíť.
   ![vybrat podsíť](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-with-subnet-query.png)
4. Před přidáním Virtual Network do seznamu musíte povolit koncový bod služby. Pokud koncový bod služby není povolený, portál vás vyzve, abyste ho povolili.
  ![vyberte podsíť a povolte](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-enabling.png) koncového bodu.
    > [!NOTE]
    > Pokud nemůžete povolit koncový bod služby, můžete chybějící koncový bod služby virtuální sítě ignorovat pomocí šablony ARM. Tato funkce není na portálu k dispozici.

5. Po povolení koncového bodu služby ve vybrané podsíti můžete pokračovat v přidávání do seznamu povolených virtuálních sítí.
  ![přidání podsítě po povolení](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-vnet-from-portal-slide-in-pane-after-adding.png) koncového bodu

6. Pokračujte stisknutím tlačítka **Uložit** na horním pásu karet a uložte konfiguraci virtuální sítě ve službě. Počkejte prosím několik minut, než se potvrzení zobrazí v oznámeních na portálu.

## <a name="add-firewall-for-specified-ip"></a>Přidat bránu firewall pro zadanou IP adresu

Přístup k oboru názvů Event Hubs můžete omezit na omezený rozsah IP adres nebo na konkrétní IP adresu pomocí pravidel brány firewall.

1. Klikněte na přepínač **vybrané sítě** v horní části stránky a povolte tak zbývající část stránky s možnostmi nabídky.
  ![vybrané sítě](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-selecting-selected-networks.png)
2. V části **Brána firewall** v rámci mřížky ***Rozsah adres*** můžete přidat jednu nebo mnoho konkrétní IP adresy nebo rozsahy IP adres.
  ![přidat IP adresy](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall.png)
3. Po přidání více IP adres (nebo rozsahů IP adres) stiskněte tlačítko **Uložit** na horním pásu karet a ujistěte se, že je konfigurace uložena na straně služby. Počkejte prosím několik minut, než se potvrzení zobrazí v oznámeních na portálu.
  ![přidat IP adresy a stisknout Uložit](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-firewall-hitting-save.png)

## <a name="adding-your-current-ip-address-to-the-firewall-rules"></a>Přidání aktuální IP adresy k pravidlům brány firewall

1. Svou aktuální IP adresu můžete rychle přidat také tak, že zkontrolujete zaškrtávací políčko ***Přidat IP adresu klienta (vaši aktuální IP adresu)*** hned nad mřížku ***rozsahu adres*** .
  ![přidávání aktuální IP adresy](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save.png)
2. Po přidání aktuální IP adresy k pravidlům brány firewall stiskněte tlačítko **Uložit** na horním pásu karet a ujistěte se, že je konfigurace uložena na straně služby. Počkejte prosím několik minut, než se potvrzení zobrazí v oznámeních na portálu.
  ![přidat aktuální IP adresu a stisknout Uložit](./media/event-hubs-tutorial-vnet-and-firewalls/vnet-firewall-adding-current-ip-hitting-save-after-saving.png)

## <a name="conclusion"></a>Závěr

V tomto kurzu budete mít integrované Virtual Network koncové body a pravidla brány firewall s existujícím oborem názvů Event Hubs. Naučíte se:
> [!div class="checklist"]
> * Postup integrace koncových bodů služby virtuální sítě s oborem názvů Event Hubs.
> * Jak nastavit bránu firewall (filtrování IP) s oborem názvů Event Hubs.


[Azure portal]: https://portal.azure.com/
