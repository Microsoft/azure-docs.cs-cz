---
title: Zprávy a připojení v Azure SignalR
description: Přehled klíčové pojmy v oblasti zpráv a připojení ve službě Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: 5a0430e9ad124319147342c49fc51e11472ac8ff
ms.sourcegitcommit: 9f87a992c77bf8e3927486f8d7d1ca46aa13e849
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/28/2018
ms.locfileid: "53811837"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Zpráva a připojení ve službě Azure SignalR

Službě Azure SignalR je model fakturace na základě počtu připojení a číslo zprávy. Jak jsou definovány a pro účely účtování počítat zprávy a připojení je popsán níže.

## <a name="message-formats-supported"></a>Podporované formáty zpráv

Službě Azure SignalR podporuje stejné formáty, které podporuje funkce SignalR technologie ASP.NET Core: [JSON](https://www.json.org/) a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Velikost zpráv

Službě Azure SignalR nemá žádné omezení velikosti zpráv.

V praxi, velkých zpráv je rozdělený do menších zprávy více než 2 KB / a přenáší jako samostatné zprávy. Zpráva rozdělení a sestavení jsou zpracovávány sady SDK. Nejsou potřeba žádné úsilí pro vývojáře.

Ale velkých zpráv má negativní dopad na výkon, zasílání zpráv. Použijte menší velikost zprávy, kdykoli je to možné a testu zvolte velikost optimální zprávy pro jednotlivé scénáře použití.

## <a name="how-to-count-messages-for-billing-purpose"></a>Jak počet zpráv pro fakturační účely?

Jsme pouze počet odchozích zpráv ze služby SignalR a ignoruje zprávy ping mezi klienty a servery.

Zprávy je větší než 2 KB se počítá jako více zpráv 2 KB. Graf počtu zpráv na webu Azure portal, aktualizuje každých 100 zpráv za rozbočovač.

Uživatel má například 3 klienty a serverem aplikace: 1. Jeden klient odešle jednu zprávu 4 KB nechat server vysílat pro všechny klienty. Počet zpráv bude 8: 1 zpráv ze služby aplikační server, 3 zprávy ze služby pro klienty a každá zpráva se počítá jako 2 zprávy 2 KB.

Počet zpráv, které jsou uvedené na webu Azure portal je stále 0, dokud se nahromadí být více než 100.

## <a name="how-to-count-connections"></a>Jak počet připojení?

Nejsou k dispozici připojení k serveru a připojení klientů. Ve výchozím nastavení má každý server aplikace 5 připojení na rozbočovači SignalR službou a každý klient má 1 připojení klienta pomocí služby SignalR.

Počet připojení webu Azure Portal obsahuje serverová připojení a připojení klientů.

Například uživatel má dva servery aplikace a definuje kódy 5 rozbočovače. Počet připojení serverů webu Azure Portal bude 2 servery aplikace * 5 hubs * 5 připojení/rozbočovač = 50 připojení k serveru.

## <a name="related-resources"></a>Související prostředky

- [Konfigurace jádra SignalR technologie ASP.NET](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)