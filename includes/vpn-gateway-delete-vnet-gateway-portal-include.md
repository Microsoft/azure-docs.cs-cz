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
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66157423"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: Přejděte k bráně virtuální sítě

1. V [webu Azure portal](https://portal.azure.com), přejděte na **všechny prostředky**. 
2. Otevřete stránku brány virtuální sítě, přejděte k bráně virtuální sítě, kterou chcete odstranit a klikněte na něj.

### <a name="step-2-delete-connections"></a>Krok 2: Odstranění připojení

1. Na stránce pro vaši bránu virtuální sítě, klikněte na tlačítko **připojení** zobrazíte všechna připojení k bráně.
2. Klikněte na tlačítko **'...'** na řádku název připojení, vyberte **odstranit** z rozevíracího seznamu.
3. Klikněte na tlačítko **Ano** potvrďte, že chcete připojení odstranit. Pokud máte více připojení, odstraňte každé připojení.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: Odstranit bránu virtuální sítě

Mějte na paměti, že pokud máte konfigurace P2S k této virtuální síti kromě konfiguraci S2S, odstraňuje se Brána virtuální sítě automaticky odpojí všechny klienty P2S bez předchozího upozornění.

1. Na stránce brány virtuální sítě, klikněte na tlačítko **přehled**.
2. Na **přehled** klikněte na **odstranit** odstranění brány.
