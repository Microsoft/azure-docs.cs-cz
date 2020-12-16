---
title: Rychlý Start – připojení týmu ke schůzce
author: askaur
ms.author: askaur
ms.date: 12/08/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 0c41771af81989ff965098a762338216db54fd27
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97578116"
---
## <a name="join-the-meeting-chat"></a>Zapojit se do chatu o schůzkách 

Po povolení interoperability týmů může uživatel komunikačních služeb připojit se k volání Teams jako uživatel typu Host pomocí klientské knihovny volání. Spojení s voláním je přidá také jako účastník konverzace o schůzce, kde mohou odesílat a přijímat zprávy s ostatními uživateli v volání. Uživatel nebude mít přístup ke zprávám chatu, které byly odeslány před připojením k volání. 

## <a name="get-a-teams-meeting-chat-thread-for-a-communication-services-user"></a>Získání týmů pro uživatele komunikačních služeb v rámci konverzačního vlákna

Nejprve vytvořte instanci `ChatThreadClient` pro vlákno konverzace pro schůzku. Pokud chcete získat ID vlákna, analyzujte odkaz na schůzku nebo použijte rozhraní API grafu s ID schůzky. 

- Odkaz na schůzku týmu vypadá takto: `https://teams.microsoft.com/l/meetup-join/meeting_chat_thread_id/1606337455313?context=some_context_here` . ID vlákna bude `meeting_chat_thread_id` na tomto odkazu. 
- Pokud máte ID schůzky, můžete k získání ID vlákna použít [Graph API](https://docs.microsoft.com/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta) . Odpověď [rozhraní Get API](https://docs.microsoft.com/graph/api/onlinemeeting-get?view=graph-rest-beta&tabs=http%22%20%5C) bude mít `chatInfo` objekt, který obsahuje `threadID` . 

Jakmile budete mít ID vlákna chatu, můžete získat klienta konverzačního vlákna pomocí klientské knihovny JavaScriptu chat: 

```javascript
let chatThreadClient = await chatClient.getChatThreadClient(threadId); 

console.log(`Chat Thread client for threadId:${chatThreadClient.threadId}`); 
```
  
`chatThreadClient`Lze ji použít k vypsání členů v konverzačním vlákně, k získání historie chatu a odesílání zpráv.  

## <a name="send-and-receive-messages"></a>Odesílání a příjem zpráv  

K `SendMessage` odeslání zprávy na schůzku chatu použijte. Pro příjem příchozích zpráv se možnost přihlásit k odběru událostí, jako `chatMessageReceived` je Nepodporovaná, jako signalizace v reálném čase zatím není pro tento scénář povolená. Pokud chcete získat nejnovější zprávy, můžete se dotázat na `ListMessages` rozhraní API. V případě scénáře interoperability `ListMessages` teď rozhraní API podporuje vracení tří nových typů zpráv:
- `RichText/HTML`
- `ThreadActivity/MemberJoined`
- `ThreadActivity/MemberLeft` </br>

Další informace o typech zpráv najdete [tady](../../../concepts/chat/concepts.md). 

**Poznámka:** pro scénáře interoperability s týmy jsou aktuálně podporovány pouze zprávy, které jsou posílány a přijímány. Další funkce, jako je například psaní ukazatelů a komunikačních služeb uživatele, kteří přidávají nebo odebírají jiné uživatele z jednání týmů, ještě nejsou podporovány.  

 
