---
title: Přehled sady Chat SDK pro komunikační služby Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o sadě Azure Communication Services Chat SDK.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: b275c3af2e92dc5af677120b5082751d19676b2e
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110810"
---
# <a name="chat-sdk-overview"></a>Přehled sady Chat SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]    

Sady SDK pro konverzační služby Azure Communication Services slouží k přidávání bohatých konverzací v reálném čase do vašich aplikací.
    
## <a name="chat-sdk-capabilities"></a>Chat – možnosti sady SDK    

Následující seznam obsahuje sadu funkcí, které jsou aktuálně k dispozici v sadách SDK služby Communications chat.  

| Skupina funkcí | Schopnost | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Základní funkce | Vytvoření konverzačního vlákna mezi 2 nebo více uživateli (až 250 uživatelů)                                                       | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aktualizace tématu konverzačního vlákna                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Přidání nebo odebrání účastníků z konverzačního vlákna                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Vyberte, jestli se má sdílet historie zpráv chatu se přidaným účastníkem.                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Získat seznam účastníků ve vlákně chatu                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Odstraní vlákno chatu.                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Uživatel, který má zadaného uživatele komunikace, získá seznam vláken konverzace, ke kterým je uživatel součástí.                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Získat informace o konkrétním vlákně chatu                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Posílání a přijímání zpráv ve vlákně chatu                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Úprava obsahu odeslané zprávy                                                                                | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Odstranění zprávy                                                                                                       | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Čtení oznámení pro zprávy, které byly přečteny jinými účastníky v chatu <br/> *Není k dispozici, pokud je v konverzačním vlákně více než 20 účastníků.*    | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Dostávat oznámení, když účastníci aktivně zadávají zprávu ve vlákně chatu <br/> *Není k dispozici, pokud je v konverzačním vlákně více než 20 členů.*      | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Získá všechny zprávy ve vlákně chatu. <br/>                                                                         | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Odeslat Unicode Emoji jako součást obsahu zprávy                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Signalizace v reálném čase (povolený pomocí vlastního signalizačního balíčku * *)|  Přihlaste se k odběru a Získejte aktualizace příchozích zpráv a dalších operací v aplikaci Chat v reálném čase. Seznam podporovaných aktualizací pro signalizaci v reálném čase najdete v tématu [Koncepty chatu](concepts.md#real-time-signaling) .                                     | ✔️   | ❌    | ❌  | ❌  | ❌  | ❌  |    
| Podpora Event Grid             | Využijte integraci s Azure Event Grid a nakonfigurujte komunikační službu pro provádění obchodní logiky na základě aktivity chatu nebo připojení vlastní služby nabízených oznámení.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
| Monitorování        | Pomocí metrik požadavků rozhraní API, které se vygenerovaly v Azure Portal, můžete vytvářet řídicí panely, monitorovat stav aplikace chatu a nastavovat výstrahy pro detekci anomálií.      | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Konfigurace prostředku služby Communications pro příjem provozních protokolů pro účely monitorování a diagnostiky          | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  


* * Proprietární balíček signalizace se implementuje pomocí webových soketů. V případě, že nejsou podporovány webové sokety, dojde k přechodu na dlouhé cyklické dotazování.  

## <a name="javascript-chat-sdk-support-by-os-and-browser"></a>Podpora sady JavaScript Chat SDK podle operačního systému a prohlížeče    

Následující tabulka představuje sadu podporovaných prohlížečů a verzí, které jsou aktuálně k dispozici.
    
|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | operační systém iPad|
|--------------------------------|----------------|--------------|-------|------|------|------|-------|
| **Chat SDK** | Firefox *, Chrome*, nový okraj | Firefox *, Chrome*, Safari * | Chrome  | Chrome | Chrome | Prohlížeče | Prohlížeče |

* Všimněte si, že nejnovější verze je podporovaná Kromě předchozích dvou verzí.<br/>   

## <a name="next-steps"></a>Další kroky   

> [!div class="nextstepaction"] 
> [Začínáme s chatem](../../quickstarts/chat/get-started.md)    

Následující dokumenty můžou být zajímavé:  
- Seznamte se s [Koncepty chatu](../chat/concepts.md)