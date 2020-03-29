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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67174878"
---
### <a name="step-1-navigate-to-the-virtual-network-gateway"></a>Krok 1: Přechod na bránu virtuální sítě

1. Na [webu Azure Portal](https://portal.azure.com)přejděte na **Všechny prostředky**. 
2. Pokud chcete otevřít stránku brány virtuální sítě, přejděte na bránu virtuální sítě, kterou chcete odstranit, a klikněte na ni.

### <a name="step-2-delete-connections"></a>Krok 2: Odstranění připojení

1. Na stránce brány virtuální sítě klikněte na **Připojení** a zobrazte všechna připojení k bráně.
2. Klikněte na **'...'** na řádku názvu připojení a v rozevíracím souboru vyberte **Odstranit.**
3. Klepnutím na tlačítko **Ano** potvrďte, že chcete připojení odstranit. Pokud máte více připojení, odstraňte každé připojení.

### <a name="step-3-delete-the-virtual-network-gateway"></a>Krok 3: Odstranění brány virtuální sítě

Uvědomte si, že pokud máte konfiguraci P2S do této virtuální sítě kromě konfigurace S2S, odstranění brány virtuální sítě automaticky odpojí všechny klienty P2S bez upozornění.

1. Na stránce brány virtuální sítě klikněte na **Přehled**.
2. Na stránce **Přehled** klikněte na **Odstranit** a bránu odstraňte.
