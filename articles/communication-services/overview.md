---
title: Co je služba komunikace Azure?
description: Přečtěte si, jak vám komunikační služby Azure pomáhají vyvíjet bohatou práci s uživateli v reálném čase.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 07/20/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a006ece278e4ec750040bde5bd4b7a6144c9e720
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90947316"
---
# <a name="what-is-azure-communication-services"></a>Co je služba komunikace Azure?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

Komunikační služby Azure umožňují snadno přidávat do vašich aplikací funkce pro komunikaci multimédií, videa a telefonování přes protokol IP v reálném čase. Klientské knihovny komunikačních služeb také umožňují přidat do komunikačních řešení funkce Chat a SMS.

Komunikační služby můžete použít pro komunikaci hlasu, videa, textu a dat v nejrůznějších scénářích:

- Komunikace z prohlížeče do prohlížeče, z prohlížeče do aplikace a z aplikace do aplikace
- Uživatelé, kteří pracují s roboty nebo jinými službami
- Uživatelé a roboty, kteří pracují přes síť s veřejným přepínáním

Podporují se smíšené scénáře. Například aplikace komunikačních služeb může uživatelům mluvit z prohlížečů a tradičních zařízení telefonního subsystému. Komunikační služby můžou být také kombinovány s Azure Bot Service pro vytváření interaktivních systémů hlasových odpovědí (IRV) založených na robotu.

## <a name="common-scenarios"></a>Typické scénáře

Následující zdroje jsou skvělým místem, kde se můžete začít, pokud začínáte s komunikačními službami Azure:
<br>

| Prostředek                               |Popis                           |
|---                                    |---                                   |
|**[Vytvořit prostředek komunikačních služeb](./quickstarts/create-communication-resource.md)**|Ke zřízení prvního prostředku komunikačních služeb můžete začít používat komunikační služby Azure pomocí klientské knihovny pro správu Azure Portal nebo služby Communication Services. Jakmile budete mít připojovací řetězec prostředků služby Communication Services, můžete zřídit své první tokeny přístupu uživatele.|
|**[Vytvoření prvního přístupového tokenu uživatele](./quickstarts/access-tokens.md)**|Tokeny přístupu uživatele slouží k ověřování vašich služeb proti vašemu prostředku služby Azure Communication Services. Tyto tokeny se zřídí a znovu vydávají pomocí klientské knihovny pro správu komunikačních služeb.|
|**[Získat telefonní číslo](./quickstarts/telephony-sms/get-phone-number.md)**|K zřizování a vydávání telefonních čísel můžete použít komunikační služby Azure. Tato telefonní čísla lze použít k zahájení odchozích volání a k sestavování řešení komunikace SMS.|
|**[Poslat SMS z vaší aplikace](./quickstarts/telephony-sms/send.md)**|Klientská knihovna služby Azure Communications Services umožňuje odesílat a přijímat zprávy SMS z aplikací .NET a JavaScript.|
|**[Začínáme s voláním hlasu a videu](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Komunikační služby Azure umožňují přidat Hlasová a vizuální volání do vašich aplikací pomocí volání klientské knihovny. Tato knihovna je založená na WebRTC a umožňuje vytvořit v aplikacích komunikaci peer-to-peer a komunikovat v reálném čase.|
|**[Začínáme s chatem](./quickstarts/chat/get-started.md)**|Klientská knihovna pro konverzaci Azure Communications Services se dá použít k integraci konverzací v reálném čase do vašich aplikací.|


## <a name="samples"></a>Ukázky

Následující ukázky ukazují ucelené využití klientských knihoven služby Azure Communications. Využijte tyto ukázky k zavedení vlastních řešení komunikačních služeb.
<br>

| Název vzorku                               | Description                           |
|---                                    |---                                   |
|**[Ukázka volání Hero skupiny](./samples/calling-hero-sample.md)**|Podívejte se, jak lze pomocí klientských knihoven komunikačních služeb vytvořit skupinu volání.|
|**[Ukázka Hero ve skupině chat](./samples/chat-hero-sample.md)**|Podívejte se, jak lze pomocí klientských knihoven komunikačních služeb vytvořit prostředí pro konverzaci ve skupině.|


## <a name="platforms-and-client-libraries"></a>Platformy a klientské knihovny

Následující zdroje vám pomůžou získat informace o klientských knihovnách služby Azure Communication Services:

| Prostředek                               | Popis                           |
|---                                    |---                                   |
|**[Klientské knihovny a rozhraní REST API](./concepts/sdk-options.md)**|Funkce komunikačních služeb Azure jsou koncepčně rozdělené do šesti oblastí, z nichž každý představuje knihovna klienta. Můžete se rozhodnout, které klientské knihovny se budou používat v závislosti na vašich potřebách komunikace v reálném čase.|
|**[Přehled volání klientské knihovny](./concepts/voice-video-calling/calling-sdk-features.md)**|Projděte si přehled o klientské knihovně volání komunikačních služeb.|
|**[Přehled klientské knihovny pro chat](./concepts/chat/sdk-features.md)**|Projděte si přehled klientské knihovny služby Communications chat.|
|**[Přehled klientské knihovny SMS](./concepts/telephony-sms/sdk-features.md)**|Projděte si přehled klientské knihovny SMS služby Communications Services.|

## <a name="compare-azure-communication-services"></a>Porovnání komunikačních služeb Azure

Existují dva další komunikační produkty Microsoftu, které byste měli zvážit, že nebudete moci přímo spolupracovat s komunikačními službami v tuto chvíli:

 - [Rozhraní API pro komunikaci Microsoft Graph cloudu](https://docs.microsoft.com/graph/cloud-communications-concept-overview) umožňují organizacím vytvářet komunikační prostředí vázané na Azure Active Directory uživatele s M365 licencemi. To je ideální pro aplikace, které jsou vázané na Azure Active Directory nebo které chcete v Microsoft Teams využít k širšímu využití produktivity. Existují také rozhraní API pro sestavování aplikací a přizpůsobení v rámci [prostředí týmů.](https://docs.microsoft.com/microsoftteams/platform/?view=msteams-client-js-latest&preserve-view=true)

 - [PlayFab strana](https://docs.microsoft.com/gaming/playfab/features/multiplayer/networking/) zjednodušuje přidávání chatu s nízkou latencí a datové komunikace do her. I když můžete pomocí komunikačních služeb vysílat herní systémy pro konverzace a sítě, PlayFab je upravená možnost a v konzole Xbox zdarma.


## <a name="next-steps"></a>Další kroky

 - [Vytvořit prostředek komunikačních služeb](./quickstarts/create-communication-resource.md)
