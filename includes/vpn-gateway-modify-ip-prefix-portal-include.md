---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/21/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 1199819d274590cc81d0234680f8765f9cc36c0a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174843"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Úprava předpon IP adres místní síťové brány – žádné připojení brány

#### <a name="to-add-additional-address-prefixes"></a>Přidání dalších předpon adres:

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Konfigurace**.
2. Přidejte adresní prostor IP do pole *Přidat další rozsah adres.*
3. Kliknutím na **Uložit** nastavení uložte.

#### <a name="to-remove-address-prefixes"></a>Odebrání předpon adres:

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Konfigurace**.
2. Klikněte na **'...'** na řádku obsahujícím předponu, kterou chcete odebrat.
3. Klikněte na **Odebrat**.
4. Kliknutím na **Uložit** nastavení uložte.

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Úprava předpon IP adres místní síťové brány – existující připojení brány

Pokud máte připojení k bráně a chcete přidat nebo odebrat předpony IP adres obsažené v bráně místní sítě, musíte v uvedeném pořadí provést následující kroky. Způsobí to určitý výpadek připojení VPN. Při upravování předpon IP adres není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.

#### <a name="1-remove-the-connection"></a>1. Odstraňte připojení.

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Připojení**.
2. Klepněte na tlačítko **...** na řádku pro každé připojení a potom klepněte na tlačítko **Odstranit**.
3. Kliknutím na **Uložit** nastavení uložte.

#### <a name="2-modify-the-address-prefixes"></a>2. Upravte předpony adres.

Přidání dalších předpon adres:

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Konfigurace**.
2. Přidejte adresní prostor IP.
3. Kliknutím na **Uložit** nastavení uložte.

Odebrání předpon adres:

1. V prostředku brány místní sítě klikněte v části **Nastavení** na **položku Konfigurace**.
2. Klikněte na **...** na řádku obsahujícím předponu, kterou chcete odebrat.
3. Klikněte na **Odebrat**.
4. Kliknutím na **Uložit** nastavení uložte.

#### <a name="3-recreate-the-connection"></a>3. Znovu vytvořte připojení.

1. Přejděte do brány virtuální sítě pro vaši virtuální síť. (Není brána místní sítě.)
2. V bráně virtuální sítě klikněte v části **Nastavení** na **položku Připojení**.
3. Kliknutím na **tlačítko + Přidat** otevřete okno Přidat **připojení.**
4. Znovu vytvořte připojení.
5. Chcete-li vytvořit připojení, klepněte na tlačítko **OK.**