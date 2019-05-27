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
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121003"
---
### <a name="noconnection"></a>Úprava předpon IP adres místní síťové brány – žádné připojení brány

#### <a name="to-add-additional-address-prefixes"></a>Přidání dalších předpon adres:

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **konfigurace**.
2. Přidat adresní prostor IP adres v *přidat další rozsah adres* pole.
3. Klikněte na tlačítko **Uložit** uložte nastavení.

#### <a name="to-remove-address-prefixes"></a>Odebrání předpon adres:

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **konfigurace**.
2. Klikněte na tlačítko **'...'** na řádek obsahující předponu, kterou chcete odebrat.
3. Klikněte na tlačítko **odebrat**.
4. Klikněte na tlačítko **Uložit** uložte nastavení.

### <a name="withconnection"></a>Úprava předpon IP adres místní síťové brány – existující připojení brány

Pokud máte připojení k bráně a chcete přidat nebo odebrat předpony IP adres obsažené v bráně místní sítě, musíte v uvedeném pořadí provést následující kroky. Způsobí to určitý výpadek připojení VPN. Při upravování předpon IP adres není potřeba odstraňovat bránu VPN. Stačí jenom odebrat připojení.

#### <a name="1-remove-the-connection"></a>1. Odeberte připojení.

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **připojení**.
2. Klikněte na tlačítko **...**  na řádku pro každé připojení a pak klikněte na tlačítko **odstranit**.
3. Klikněte na tlačítko **Uložit** uložte nastavení.

#### <a name="2-modify-the-address-prefixes"></a>2. Úprava předpon adres.

Přidání dalších předpon adres:

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **konfigurace**.
2. Přidáte adresní prostor IP adres.
3. Klikněte na tlačítko **Uložit** uložte nastavení.

Odebrání předpon adres:

1. V prostředku brány místní sítě v **nastavení** klikněte na tlačítko **konfigurace**.
2. Klikněte na tlačítko **...**  na řádek obsahující předponu, kterou chcete odebrat.
3. Klikněte na tlačítko **odebrat**.
4. Klikněte na tlačítko **Uložit** uložte nastavení.

#### <a name="3-recreate-the-connection"></a>3. Znovu vytvořte připojení.

1. Přejděte k bráně virtuální sítě pro vaši virtuální síť. (Nikoli brány místní sítě.)
2. Na Brána virtuální sítě v **nastavení** klikněte na tlačítko **připojení**.
3. Klikněte na tlačítko **+ přidat** otevřít **přidat připojení** okno.
4. Znovu vytvořte připojení.
5. Klikněte na tlačítko **OK** k vytvoření připojení.