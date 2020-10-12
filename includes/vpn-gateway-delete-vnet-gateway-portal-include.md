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
ms.openlocfilehash: d36d2be59010c47348a8e196b28d87e5b967868e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "67174878"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: přechod na bránu virtuální sítě

1. V [Azure Portal](https://portal.azure.com)přejděte na **všechny prostředky**. 
2. Chcete-li otevřít stránku brány virtuální sítě, přejděte k bráně virtuální sítě, kterou chcete odstranit, a klikněte na ni.

### <a name="step-2-delete-connections"></a>Krok 2: odstranění připojení

1. Na stránce pro bránu virtuální sítě klikněte na **připojení** . zobrazí se všechna připojení k bráně.
2. Klikněte na **'... '** na řádku názvu připojení a pak vyberte **Odstranit** z rozevíracího seznamu.
3. Kliknutím na **Ano** potvrďte, že chcete připojení odstranit. Pokud máte více připojení, odstraňte jednotlivá připojení.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: odstranění brány virtuální sítě

Uvědomte si, že pokud máte kromě konfigurace S2S P2S konfiguraci do této virtuální sítě, při odstranění brány virtuální sítě se automaticky odpojí všichni klienti P2S bez upozornění.

1. Na stránce Brána virtuální sítě klikněte na **Přehled**.
2. Na stránce **Přehled** klikněte na **Odstranit** , aby se brána odstranila.
