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
ms.openlocfilehash: 5b2aa7fedbc203c50796a0e0c8d9cdb3895ae6c1
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/23/2018
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: Přejděte na bránu virtuální sítě

1. V [portál Azure](https://portal.azure.com), přejděte na **všechny prostředky**. 
2. Chcete-li otevřít stránku brány virtuální sítě, přejděte na bránu virtuální sítě, který chcete odstranit a klikněte na něj.

### <a name="step-2-delete-connections"></a>Krok 2: Odstranění připojení

1. Na stránce pro bránu virtuální sítě, klikněte na **připojení** zobrazíte všechna připojení k bráně.
2. Klikněte na tlačítko **"..."** na řádku název připojení, potom vyberte **odstranit** z rozevíracího seznamu.
3. Klikněte na tlačítko **Ano** potvrďte, že chcete odstranit připojení. Pokud máte více připojení, odstranění každé připojení.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: Odstranit bránu virtuální sítě

Upozorňujeme, že pokud máte kromě konfiguraci S2S P2S konfigurace do této virtuální sítě, odstraňuje se Brána virtuální sítě automaticky odpojí všichni klienti P2S bez upozornění.

1. Na stránce brány virtuální sítě klikněte na **přehled**.
2. Na **přehled** klikněte na tlačítko **odstranit** odstranit bránu.
