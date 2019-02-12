---
title: Zprávy a připojení ve službě Azure SignalR
description: Přehled klíčových konceptů o zprávách a připojení ve službě Azure SignalR.
author: sffamily
ms.service: signalr
ms.topic: overview
ms.date: 09/13/2018
ms.author: zhshang
ms.openlocfilehash: ce1542278303910837a69d3184c1de86a9237f8e
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "55996232"
---
# <a name="messages-and-connections-in-azure-signalr-service"></a>Zprávy a připojení ve službě Azure SignalR

Fakturační model pro služby Azure SignalR vychází z počtu připojení a číslo zprávy. Tento článek vysvětluje, jak jsou definovány a počítá fakturace zprávy a připojení.


## <a name="message-formats"></a>Formáty zpráv 

Službě Azure SignalR podporuje stejný formát jako funkce SignalR technologie ASP.NET Core: [JSON](https://www.json.org/) a [MessagePack](/aspnet/core/signalr/messagepackhubprotocol).

## <a name="message-size"></a>Velikost zpráv

Službě Azure SignalR nemá žádné omezení velikosti zpráv.

Velké zprávy jsou rozděleny do menších zpráv, které jsou více než 2 KB a přenesených samostatně. Sady SDK se zpracovala zpráva rozdělení a propojením. Nejsou potřeba žádné úsilí pro vývojáře.

Velké zprávy negativně ovlivnit výkon služby zasílání zpráv. Použijte menší zprávy, kdykoli je to možné a testování k určení velikosti optimální zprávy pro jednotlivé scénáře použití.

## <a name="how-messages-are-counted-for-billing"></a>Jak se počítají zprávy pro fakturaci

Při fakturaci se počítají jenom odchozí zprávy ze služby Azure SignalR. Příkaz ping zpráv mezi klienty a servery jsou ignorovány.

Větší než 2 KB zprávy se počítají jako více zpráv 2 KB. Graf počtu zpráv na webu Azure Portal se aktualizuje každých 100 zpráv za rozbočovač.

Představte si například, že máte tři klienty a serverem jednu aplikaci. Jeden klient odešle zprávu 4 KB nechat server vysílat pro všechny klienty. Počet zpráv je 8: jednu zprávu ze služby aplikační server a tři zprávy ze služby klientům. Každá zpráva se počítá jako dvě zprávy 2 KB.

Počet zpráv, které jsou uvedené na webu Azure Portal, 0 zůstane, dokud se shromažďuje více než 100.

## <a name="how-connections-are-counted"></a>Jak se počítají připojení

Nejsou k dispozici připojení k serveru a připojení klientů. Ve výchozím nastavení každý aplikační server má pět připojení za centra pomocí služby Azure SignalR a každý klient má jedno připojení klienta pomocí služby Azure SignalR.

Počet připojení webu Azure Portal obsahuje serverová připojení a připojení klientů.

Předpokládejme například, že máte dva servery aplikace a definování pěti hubs v kódu. Počet připojení serverů bude 50: 2 servery aplikace * 5 hubs * 5 připojení pro každé centrum.

Připojení k serveru funkce SignalR technologie ASP.NET vypočítá jiným způsobem. Obsahuje jeden výchozí centra kromě rozbočovačů, které definujete. Ve výchozím nastavení musí každý server aplikace pět další připojení serverů. Počet připojení pro rozbočovač výchozí zůstane konzistentní s s ostatními centry.

## <a name="how-inboundoutbound-traffic-is-counted"></a>Jak se počítá odchozího/příchozího provozu

Rozdíl mezi příchozích a odchozích přenosů vychází z perspektivy služby Azure SignalR. Provoz se počítá v bajtech. Jako počet zpráv přenosu má také vzorkovací frekvenci. Příchozí/odchozí grafu na webu Azure Portal se aktualizuje každých 100 KB na rozbočovači.

## <a name="related-resources"></a>Související prostředky

- [Typ agregace ve službě Azure Monitor](/azure/azure-monitor/platform/metrics-supported#microsoftsignalrservicesignalr )
- [Konfigurace jádra SignalR technologie ASP.NET](/aspnet/core/signalr/configuration)
- [JSON](https://www.json.org/)
- [MessagePack](/aspnet/core/signalr/messagepackhubprotocol)