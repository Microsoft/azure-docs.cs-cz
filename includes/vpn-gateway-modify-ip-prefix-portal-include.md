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
ms.sourcegitcommit: fad3aaac5af8c1b3f2ec26f75a8f06e8692c94ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/27/2020
ms.locfileid: "67174843"
---
### <a name="to-modify-local-network-gateway-ip-address-prefixes---no-gateway-connection"></a><a name="noconnection"></a>Úprava předpon IP adres místní síťové brány – žádné připojení brány

#### <a name="to-add-additional-address-prefixes"></a>Přidání dalších předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Přidejte adresní prostor IP adres do pole *Přidat další rozsah adres* .
3. Uložte nastavení kliknutím na **Uložit** .

#### <a name="to-remove-address-prefixes"></a>Odebrání předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Klikněte na **'... '** na řádku obsahujícím předponu, kterou chcete odebrat.
3. Klikněte na **Odebrat**.
4. Uložte nastavení kliknutím na **Uložit** .

### <a name="to-modify-local-network-gateway-ip-address-prefixes---existing-gateway-connection"></a><a name="withconnection"></a>Úprava předpon IP adres místní síťové brány – existující připojení brány

Pokud máte připojení k bráně a chcete přidat nebo odebrat předpony IP adres obsažené v bráně místní sítě, musíte v uvedeném pořadí provést následující kroky. Způsobí to určitý výpadek připojení VPN. Při upravování předpon IP adres není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.

#### <a name="1-remove-the-connection"></a>1. odeberte připojení.

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **připojení**.
2. Klikněte na **...** na řádku pro každé připojení a pak klikněte na **Odstranit**.
3. Uložte nastavení kliknutím na **Uložit** .

#### <a name="2-modify-the-address-prefixes"></a>2. Upravte předpony adres.

Přidání dalších předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Přidejte adresní prostor IP adres.
3. Uložte nastavení kliknutím na **Uložit** .

Odebrání předpon adres:

1. V prostředku brány místní sítě v části **Nastavení** klikněte na **Konfigurace**.
2. Klikněte na tlačítko **...** na řádku obsahujícím předponu, kterou chcete odebrat.
3. Klikněte na **Odebrat**.
4. Uložte nastavení kliknutím na **Uložit** .

#### <a name="3-recreate-the-connection"></a>3. znovu vytvořte připojení.

1. Přejděte do brány Virtual Network pro vaši virtuální síť. (Nejedná se o bránu místní sítě.)
2. V bráně Virtual Network vyberte v části **Nastavení** možnost **připojení**.
3. Kliknutím na **+ Přidat** otevřete okno **Přidat připojení** .
4. Znovu vytvořte připojení.
5. Kliknutím na tlačítko **OK** vytvořte připojení.