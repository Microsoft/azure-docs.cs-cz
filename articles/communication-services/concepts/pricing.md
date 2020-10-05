---
title: Cenové scénáře pro volání (hlasové a video) a chat
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o cenovém modelu komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/29/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 0b82d7dc8f6aaaa28a5293966440f058fbf42dc1
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91460930"
---
# <a name="pricing-scenarios"></a>Cenové scénáře

[!INCLUDE [Public Preview Notice](../includes/public-preview-include.md)]

<!--
> [!WARNING]
> This document is under construction and needs the following items to be addressed:
> - Looks like other resources point to a /pricing page that is managed by Commerce or Marketing? https://azure.microsoft.com/pricing/details/functions/ Should we? FOLLOWING UP WITH KRISTIN TO FIND THE RIGHT ACS PAGE
-->

Ceny za služby Azure Communication Services jsou založené na modelu průběžných plateb bez jakýchkoli poplatků předem. Budeme vám účtovat jenom vaši spotřebu a používání služeb.

## <a name="voicevideo-calling-and-screen-sharing"></a>Volání hlasu a videa a sdílení obrazovky

Komunikační služby Azure umožňují přidat do vašich aplikací volání hlasu a videa a sdílení obrazovky. Můžete vložit prostředí do svých aplikací pomocí jazyka JavaScript, objektivu: C (Apple), Java (Android) nebo klientských knihoven .NET. Podívejte se na náš [úplný seznam dostupných klientských knihoven](./sdk-options.md).

### <a name="pricing"></a>Ceny

Služby pro volání a sdílení obrazovky se účtují za minutu za účastníka na úrovni $0,004 za účastníka za minutu pro volání skupin. Chcete-li pochopit různé toky volání, které jsou možné, přečtěte si [tuto stránku](./call-flows.md).

Každý účastník volání se bude počítat za každou minutu, ke které jsou připojeny ke volání. To má hodnotu true bez ohledu na to, zda uživatel používá audiovizuální volání, volání hlasu nebo sdílení obrazovky.

### <a name="pricing-example-group-audiovideo-call-using-js-and-ios-client-libraries"></a>Příklad ceny: rozdělit zvuk/video volání pomocí klientských knihoven JS a iOS

Alice provedla volání skupiny s kolegy, Bobem a Charlie. Alice a Bob použily klientské knihovny JS, Charlie klientské knihovny iOS.

- Volání trvá celkem 60 minut.
- Alice a Bob se účastnily celého volání. Alice zapnula své video po dobu pěti minut a Nasdílela obrazovku po dobu 23 minut. U Boba bylo video zapnuté pro celé volání (60 minut) a sdílená obrazovka po dobu 12 minut.
- Charlie opustí volání po 43 minutách. Charlie využití zvuku a videa po dobu, po kterou se účastnila (43 minut).

**Výpočty nákladů**

- 2 účastníci × 60 minut × $0,004 za účastníka za minutu = $0,48 [video i zvuk se účtují stejnou sazbou.]
- 1 účastník x 43 minut × $0,004 za účastníka za minutu = $0,172 [video i zvuk se účtují stejnou sazbou.]

**Celkové náklady na volání skupiny**: $0,48 + $0,172 = $0,652

## <a name="chat"></a>Chat

Pomocí komunikačních služeb můžete rozšířit aplikaci o možnost odesílat a přijímat zprávy chatu mezi 2 nebo více uživateli. Chat pro klientské knihovny jsou k dispozici pro jazyky JavaScript, .NET, Python a Java. [Další informace o knihovnách klienta najdete na této stránce](./sdk-options.md) .

### <a name="price"></a>Cena

- Za každou odeslanou zprávu chatu se vám bude účtovat $0,0008.

### <a name="pricing-example-chat-between-two-users"></a>Příklad ceny: chat mezi dvěma uživateli 

Geeta spustí chatovací vlákno s Emily ke sdílení aktualizace a pošle 5 zpráv. Chat trvá 10 minut na Geeta a Emily odeslání dalších 15 zpráv.

**Výpočty nákladů** 
- Počet odeslaných zpráv (5 + 15 + 15) × $0,0008 = $0,028

### <a name="pricing-example-group-chat-with-multiple-users"></a>Příklad ceny: seskupení chatu s více uživateli 

Charlie spustí chatovací vlákno se svými přáteli Casey & jednotek Jasmine k naplánování dovolené. Při současném chatování Charlie, Casey & jednotek Jasmine odesílají 20, 30 a 18 zpráv. Uvědomují si, že jejich přítel růže může být zajímat se i na cestách, aby se připojili ke konverzačnímu vláknu a aby s ní sdílel veškerou historii zpráv. 

Tato zpráva se zobrazí a spustí se konverzace. V mezitím Casey získá hovor a rozhodne o tom, aby se v konverzaci později mohl zachytit. Charlie, jednotek Jasmine & růže na data o cestování a pošle další 30, 25 a 35 zpráv.

**Výpočty nákladů** 

- Počet odeslaných zpráv (20 + 30 + 18 + 30 + 25 + 35) x $0,0008 = $0,1264
