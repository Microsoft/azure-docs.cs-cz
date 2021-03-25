---
title: Co je služba Azure Communication Services?
description: Přečtěte si, jak vám komunikační služby Azure pomáhají vyvíjet bohatou práci s uživateli v reálném čase.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: a993a4ad4c709a3736f5d5f577741c82c95af83f
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105107257"
---
# <a name="what-is-azure-communication-services"></a>Co je služba Azure Communication Services?

[!INCLUDE [Public Preview Notice](./includes/public-preview-include.md)]

> [!IMPORTANT]
> Aplikace, které sestavíte pomocí komunikačních služeb Azure, můžou komunikovat s Microsoft Teams. Další informace najdete v dokumentaci pro [spolupráci týmů](./quickstarts/voice-video-calling/get-started-teams-interop.md) .


Komunikační služby Azure umožňují snadno přidávat do vašich aplikací funkce pro komunikaci multimédií, videa a telefonování přes protokol IP v reálném čase. Sady SDK komunikačních služeb také umožňují přidat do komunikačních řešení funkce Chat a SMS.

<br>

> [!VIDEO https://www.youtube.com/embed/apBX7ASurgM]

<br>
<br>

Komunikační služby můžete použít pro komunikaci hlasu, videa, textu a dat v nejrůznějších scénářích:

- Komunikace z prohlížeče do prohlížeče, z prohlížeče do aplikace a z aplikace do aplikace
- Uživatelé, kteří pracují s roboty nebo jinými službami
- Uživatelé a roboty, kteří pracují přes síť s veřejným přepínáním

Podporují se smíšené scénáře. Například aplikace komunikačních služeb může uživatelům mluvit z prohlížečů a tradičních zařízení telefonního subsystému. Komunikační služby můžou být také kombinovány s Azure Bot Service pro vytváření interaktivních systémů hlasových odpovědí (IRV) založených na robotu.

## <a name="common-scenarios"></a>Obvyklé scénáře

Následující zdroje jsou skvělým místem, kde se můžete začít, pokud začínáte s komunikačními službami Azure:
<br>

| Prostředek                               |Popis                           |
|---                                    |---                                   |
|**[Připojení volající aplikace ke schůzce v Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Komunikační služby Azure je možné využít k vytváření vlastních prostředí pro schůzky, která spolupracují s Microsoft Teams. Uživatelé vašich řešení komunikačních služeb můžou s týmy komunikovat prostřednictvím hlasu, videa, chatu a sdílení obrazovky.|
|**[Vytvoření prostředku Communication Services](./quickstarts/create-communication-resource.md)**|Můžete začít používat komunikační služby Azure pomocí Azure Portal nebo sady Communications Services SDK ke zřízení prvního prostředku komunikačních služeb. Jakmile budete mít připojovací řetězec prostředků služby Communication Services, můžete zřídit své první tokeny přístupu uživatele.|
|**[Vytvoření prvního přístupového tokenu uživatele](./quickstarts/access-tokens.md)**|Tokeny přístupu uživatele slouží k ověřování vašich služeb proti vašemu prostředku služby Azure Communication Services. Tyto tokeny se zřídí a znovu vydávají pomocí služby Communications SDK.|
|**[Získat telefonní číslo](./quickstarts/telephony-sms/get-phone-number.md)**|K zřizování a vydávání telefonních čísel můžete použít komunikační služby Azure. Tato telefonní čísla lze použít k zahájení odchozích volání a k sestavování řešení komunikace SMS.|
|**[Poslat SMS z vaší aplikace](./quickstarts/telephony-sms/send.md)**|Služba Azure Communications Services SMS SDK umožňuje odesílat a přijímat zprávy SMS z aplikací .NET a JavaScript.|
|**[Začínáme s voláním hlasu a videu](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Komunikační služby Azure umožňují přidat Hlasová a vizuální volání do vašich aplikací pomocí volání sady SDK. Tato knihovna je založená na WebRTC a umožňuje vytvořit v aplikacích komunikaci peer-to-peer a komunikovat v reálném čase.|
|**[Začínáme s chatem](./quickstarts/chat/get-started.md)**|Pomocí chatovací SDK služby Azure Communications Services je možné do svých aplikací integrovat chat v reálném čase.|


## <a name="samples"></a>ukázky

Následující ukázky demonstrují ucelené využití sad Azure Communications Services. Využijte tyto ukázky k zavedení vlastních řešení komunikačních služeb.
<br>

| Název vzorku                               | Description                           |
|---                                    |---                                   |
|**[Ukázka volání Hero skupiny](./samples/calling-hero-sample.md)**|Podívejte se, jak lze sady SDK komunikačních služeb použít k vytvoření skupinového volání.|
|**[Ukázka Hero ve skupině chat](./samples/chat-hero-sample.md)**|Podívejte se, jak se dají sady SDK komunikačních služeb použít k sestavení prostředí skupinového chatu.|


## <a name="platforms-and-sdks"></a>Platformy a sady SDK

Následující materiály vám pomůžou získat informace o sadách Azure Communication Services SDK:

| Prostředek                               | Popis                           |
|---                                    |---                                   |
|**[Klientské knihovny a rozhraní REST API](./concepts/sdk-options.md)**|Funkce komunikačních služeb Azure jsou koncepčně rozdělené do šesti oblastí, z nichž každý představuje sada SDK. Můžete se rozhodnout, které sady SDK použít na základě vašich požadavků na komunikaci v reálném čase.|
|**[Přehled volání sady SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Projděte si přehled služby Communications Call SDK.|
|**[Přehled sady Chat SDK](./concepts/chat/sdk-features.md)**|Projděte si přehled služby Communications Chat SDK.|
|**[Přehled sady SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Seznamte se s přehledem služby Communications Services SMS SDK.|

## <a name="compare-azure-communication-services"></a>Porovnání komunikačních služeb Azure

Existují dva další komunikační produkty Microsoftu, které byste měli zvážit, že nebudete moci přímo spolupracovat s komunikačními službami v tuto chvíli:

 - [Rozhraní API pro komunikaci Microsoft Graph cloudu](/graph/cloud-communications-concept-overview) umožňují organizacím vytvářet komunikační prostředí vázané na Azure Active Directory uživatele s M365 licencemi. To je ideální pro aplikace, které jsou vázané na Azure Active Directory nebo které chcete v Microsoft Teams využít k širšímu využití produktivity. Existují také rozhraní API pro sestavování aplikací a přizpůsobení v rámci [prostředí týmů.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [PlayFab strana](/gaming/playfab/features/multiplayer/networking/) zjednodušuje přidávání chatu s nízkou latencí a datové komunikace do her. I když můžete pomocí komunikačních služeb vysílat herní systémy pro konverzace a sítě, PlayFab je upravená možnost a v konzole Xbox zdarma.


## <a name="next-steps"></a>Další kroky

 - [Vytvoření prostředku Communication Services](./quickstarts/create-communication-resource.md)
