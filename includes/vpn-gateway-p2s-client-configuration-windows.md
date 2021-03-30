---
title: zahrnout soubor
description: zahrnout soubor
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 10/28/2020
ms.author: cherylmc
ms.openlocfilehash: f37876a0b6b7e8b520b5b6428381ebaf7b5b5c24
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "93042578"
---
Stejný konfigurační balíček klienta VPN můžete použít na každém klientském počítači s Windows, pokud verze odpovídá architektuře pro klienta. Seznam podporovaných klientských operačních systémů najdete v části [Nejčastější dotazy k VPN Gateway](../articles/vpn-gateway/vpn-gateway-vpn-faq.md#P2S)v části Point-to-site.

>[!NOTE]
>Musíte mít oprávnění správce na klientském počítači s Windows, ze kterého se chcete připojit.
>

Pomocí následujících kroků nakonfigurujte nativního klienta VPN systému Windows pro ověřování certifikátů:

1. Vyberte konfigurační soubory klienta VPN, které odpovídají architektuře počítače s Windows. V případě 64bitové architektury procesoru zvolte instalační balíček VpnClientSetupAmd64. V případě 32bitové architektury procesoru zvolte instalační balíček VpnClientSetupX86. 
1. Dvakrát klikněte na balíček a nainstalujte ho. Pokud se zobrazí automaticky otevírané okno filtru SmartScreen, klikněte na **Další informace** a potom na **přesto spustit**.
1. Na klientském počítači přejděte do části **Nastavení sítě** a klikněte na možnost **Síť VPN**. Připojení k síti VPN zobrazuje název virtuální sítě, ke které se připojuje.
1. Než se pokusíte o připojení, ověřte, že jste na klientském počítači nainstalovali klientský certifikát. Klientský certifikát se vyžaduje k ověřování při použití typu nativního ověřování certifikátů Azure.