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
ms.openlocfilehash: 561855704d157f9ad826b5db83600a79d9437fc6
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107500681"
---
# <a name="chat-sdk-overview"></a>Přehled sady Chat SDK 

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include-chat.md)]

Sady SDK pro konverzační služby Azure Communication Services slouží k přidávání bohatých konverzací v reálném čase do vašich aplikací.
    
## <a name="chat-sdk-capabilities"></a>Chat – možnosti sady SDK    

Následující seznam obsahuje sadu funkcí, které jsou aktuálně k dispozici v sadách SDK služby Communications chat.  

| Skupina funkcí | Schopnost | JavaScript  | Java | .NET | Python | iOS | Android |
|-----------------|-------------------|---|-----|----|-----|----|----|
| Základní funkce | Vytvořit chatovací vlákno mezi 2 nebo více uživateli                                                     | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |    
|                   | Aktualizace tématu konverzačního vlákna                                                                              | ✔️   | ✔️ | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Přidání nebo odebrání účastníků z konverzačního vlákna                                                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Vyberte, jestli se má sdílet historie zpráv chatu se přidaným účastníkem.                                   | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Získat seznam účastníků ve vlákně chatu                                                                          | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   | 
|                   | Odstraní vlákno chatu.                                                                                              | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|                   | Uživatel, který má zadaného uživatele komunikace, získá seznam vláken konverzace, ke kterým je uživatel součástí.                                           | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Získat informace o konkrétním vlákně chatu                                                                              | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |   
|                   | Posílání a přijímání zpráv ve vlákně chatu                                                                            | ✔️   | ✔️   | ✔️    | ✔️  |  ✔️    | ✔️   |   
|                   | Aktualizace obsahu odeslané zprávy                                                                               | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Odstraní zprávu, kterou jste odeslali dřív.                                                                                                      | ✔️   | ✔️  | ✔️ | ✔️ |  ✔️    | ✔️   |    
|                   | Čtení oznámení pro zprávy, které byly přečteny jinými účastníky v chatu                                        | ✔️   | ✔️  | ✔️    | ✔️   |  ✔️    | ✔️   |   
|                   | Dostávat oznámení, když účastníci aktivně zadávají zprávu ve vlákně chatu                                         | ✔️   | ✔️   | ✔️    | ✔️    |  ✔️    | ✔️   | 
|                   | Získá všechny zprávy ve vlákně chatu.                                                                        | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   | 
|                   | Odeslat Unicode Emoji jako součást obsahu zprávy                                                                            | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
|Oznámení v reálném čase (s povoleným proprietárním balíčkem pro signalizaci * * *)|  Klienti chatu se můžou přihlásit k odběru a získávat aktualizace příchozích zpráv v reálném čase a další operace, ke kterým dochází ve vlákně chatu. Seznam podporovaných aktualizací pro oznámení v reálném čase najdete v tématu [Koncepty chatu](concepts.md#real-time-notifications) .                                     | ✔️   | ❌    | ❌  | ❌  | ✔️  | ✔️  |   
| Integrace s Azure Event Grid             | Použijte události chatu dostupné v Azure Event Grid k připojení vlastních služeb oznámení nebo k odeslání této události Webhooku za účelem provedení obchodní logiky, jako je aktualizace záznamů CRM po dokončení chatu.   | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |    
| Vytváření sestav </br>(Tato informace je k dispozici na kartě monitorování pro prostředek komunikačních služeb na Azure Portal)      | Pochopení provozu rozhraní API z aplikace chatu sledováním publikovaných metrik v Azure Průzkumník metrik a nastavením výstrah pro detekci anomálií     | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |  
|                   | Monitorování a ladění řešení komunikačních služeb povolením diagnostického protokolování pro váš prostředek    | ✔️   | ✔️  | ✔️    | ✔️  |  ✔️    | ✔️   |   


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
- Vysvětlení způsobu práce s [cenami](../pricing.md#chat) pro chat
