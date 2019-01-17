---
title: Zprávy a připojení v Azure SignalR
description: Přehled klíčové pojmy v oblasti zpráv a připojení ve službě Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: c2348df7a1a55584807a03216e294486ddadfc52
ms.sourcegitcommit: a1cf88246e230c1888b197fdb4514aec6f1a8de2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54352593"
---
# <a name="message-and-connection-in-azure-signalr-service"></a>Zpráva a připojení ve službě Azure SignalR

Službě Azure SignalR je model fakturace na základě počtu připojení a číslo zprávy. Jak jsou definovány a pro účely účtování počítat zprávy a připojení je popsán níže.

## <a name="message-formats-supported"></a>Podporované formáty zpráv

Službě Azure SignalR podporuje stejné formáty, které podporuje funkce SignalR technologie ASP.NET Core: [JSON](https://www.json.org/) a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)

## <a name="message-size"></a>Velikost zpráv

Službě Azure SignalR nemá žádné omezení velikosti zpráv.

V praxi, velkých zpráv je rozdělený do menších zprávy více než 2 KB / a přenáší jako samostatné zprávy. Sady SDK se zpracovala zpráva rozdělení a propojením. Nejsou potřeba žádné úsilí pro vývojáře.

Ale velkých zpráv má negativní dopad na výkon, zasílání zpráv. Použijte menší velikost zprávy, kdykoli je to možné a testu zvolte velikost optimální zprávy pro jednotlivé scénáře použití.

## <a name="how-to-count-messages-for-billing-purpose"></a>Jak počet zpráv pro fakturační účely?

Jsme pouze počet odchozích zpráv ze služby SignalR a ignoruje zprávy ping mezi klienty a servery.

Zprávy je větší než 2 KB se počítá jako více zpráv 2 KB. Graf počtu zpráv na webu Azure portal se aktualizuje každých 100 zpráv za rozbočovač.

Například máte tři klienty a serverem jednu aplikaci. Jeden klient odešle jednu zprávu 4 KB nechat server vysílat pro všechny klienty. Počet zpráv je 8: Zpráv ze služby aplikační server, tři zprávy ze služby pro klienty a každá zpráva se počítá jako dvě zprávy 2 KB.

Počet zpráv, které jsou uvedené na webu Azure portal je stále 0, dokud se nahromadí být více než 100.

## <a name="how-to-count-connections"></a>Jak počet připojení?

Nejsou k dispozici připojení k serveru a připojení klientů. Ve výchozím nastavení má každý aplikační server pěti připojení na rozbočovači SignalR službou a každý klient má jedno připojení klienta pomocí služby SignalR.

Počet připojení webu Azure Portal obsahuje serverová připojení a připojení klientů.

Například můžete mít dva servery aplikace a definovat pět rozbočovače v kódu. Počet připojení serverů je 50: 2 servery aplikace * 5 hubs * 5 připojení/rozbočovač.

Funkce SignalR technologie ASP.NET se liší ve výpočtu připojení k serveru. Obsahuje jeden výchozí centra kromě definovaných zákazníkem rozbočovače. Každý server aplikace potřebuje 5 další připojení serveru ve výchozím nastavení. Počet připojení pro rozbočovač výchozí zajišťuje konzistentní s ostatními centry.

## <a name="how-to-count-inbound-traffic--outbound-traffic"></a>Jak vypočítat příchozího provozu / odchozí provoz

Příchozí / odchozí je z hlediska služby SignalR. Provoz se počítá v bajtech. Jako počet zpráv přenosu má také jeho vzorkovací frekvenci. Příchozí nebo odchozí grafu na webu Azure portal, aktualizuje každých 100 KB na rozbočovači.

## <a name="related-resources"></a>Související prostředky

- [Typ agregace ve službě Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Konfigurace jádra SignalR technologie ASP.NET](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)