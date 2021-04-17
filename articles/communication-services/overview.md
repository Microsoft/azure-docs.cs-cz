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
ms.openlocfilehash: 8c2559315e3bfffc41c138be6826adae95dd7b07
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2021
ms.locfileid: "107588101"
---
# <a name="what-is-azure-communication-services"></a>Co je služba Azure Communication Services?

Komunikační služby Azure umožňují snadno přidat do vašich aplikací hlasovou komunikaci, video a telefonickou komunikaci v reálném čase. Sady SDK komunikačních služeb také umožňují přidávat do komunikačních řešení funkce SMS. Komunikační služby Azure jsou identity nezávislá a máte plnou kontrolu nad tím, jak se identifikují a ověřují koncoví uživatelé. Lidi můžete připojit k rovině komunikačních dat nebo službám (roboty).

Mezi aplikace patří:

- **B2C (Business to Consumer).** Zaměstnanci a služby firmy můžou s uživateli komunikovat pomocí hlasu, videa a multimediálního chatu ve vlastním prohlížeči nebo v mobilní aplikaci. Organizace může odesílat a přijímat zprávy SMS nebo pracovat s interaktivním systémem IRV (Voice Response System) pomocí telefonního čísla, které získáte prostřednictvím Azure. [Integrace s Microsoft Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md) umožňuje spotřebitelům připojit se k týmům, které hostuje zaměstnanci. ideální pro scénáře vzdáleného zdravotního zdravotnictví, bankovnictví a podpory produktů, kde zaměstnanci už můžou být obeznámeni s týmy.
- **Příjemce.** Vytvářejte poutavé společenské prostory pro interakci spotřebitelů a zákazníků pomocí hlasu, videa a multimediálního chatu s textem. Všechny typy uživatelského rozhraní se dají vytvořit na sadách Azure Communication Services SDK, ale k dispozici jsou kompletní ukázky aplikací a prostředky uživatelského rozhraní, které vám pomůžou rychle začít.

Další informace najdete v našem [videu Microsoftu](https://www.youtube.com/watch?v=apBX7ASurgM) nebo níže odkazovaných zdrojích.

## <a name="common-scenarios"></a>Obvyklé scénáře

<br>

| Prostředek                               |Popis                           |
|---                                    |---                                   |
|**[Vytvoření prostředku Communication Services](./quickstarts/create-communication-resource.md)**|Začněte používat komunikační služby Azure pomocí Azure Portal nebo sady Communications Services SDK ke zřízení prvního prostředku komunikačních služeb. Jakmile budete mít připojovací řetězec prostředků služby Communication Services, můžete zřídit své první tokeny přístupu uživatele.|
|**[Získat telefonní číslo](./quickstarts/telephony-sms/get-phone-number.md)**|K zřizování a vydávání telefonních čísel můžete použít komunikační služby Azure. Tato telefonní čísla se dají použít k zahájení nebo přijetí telefonních hovorů a k sestavování řešení SMS.|
|**[Poslat SMS z vaší aplikace](./quickstarts/telephony-sms/send.md)**|Služba Azure Communications Services SMS SDK slouží k posílání a přijímání zpráv SMS od aplikací služby.|

Po vytvoření prostředku komunikačních služeb můžete začít vytvářet scénáře klienta, například volání hlasu a videa nebo textový chat:

| Prostředek                               |Popis                           |
|---                                    |---                                   |
|**[Vytvoření prvního přístupového tokenu uživatele](./quickstarts/access-tokens.md)**|Tokeny přístupu uživatele se používají k ověřování klientů proti vašemu prostředku služby Azure Communication Services. Tyto tokeny se zřídí a znovu vydávají pomocí služby Communications SDK.|
|**[Začínáme s voláním hlasu a videu](./quickstarts/voice-video-calling/getting-started-with-calling.md)**| Komunikační služby Azure umožňují přidat volání hlasu a videa do prohlížeče nebo nativních aplikací pomocí volání sady SDK. |
|**[Připojení volající aplikace ke schůzce v Teams](./quickstarts/voice-video-calling/get-started-teams-interop.md)**|Komunikační služby Azure je možné využít k vytváření vlastních prostředí pro schůzky, která spolupracují s Microsoft Teams. Uživatelé vašich řešení komunikačních služeb můžou s týmy komunikovat prostřednictvím hlasu, videa, chatu a sdílení obrazovky.|
|**[Začínáme s chatem](./quickstarts/chat/get-started.md)**|Sada Chat SDK pro službu Azure Communications Services slouží k přidání formátovaného textu v reálném čase do vašich aplikací.|

## <a name="samples"></a>ukázky

Následující ukázky ukazují kompletní využití komunikačních služeb Azure. Tyto ukázky použijte k zavedení vlastních řešení komunikačních služeb.
<br>

| Název vzorku                               | Description                           |
|---                                    |---                                   |
|**[Ukázka volání Hero skupiny](./samples/calling-hero-sample.md)**| Stáhněte si ukázku navržené aplikace pro volání skupin pro prohlížeče, zařízení s iOS a Androidem. |
|**[Ukázka Hero ve skupině chat](./samples/chat-hero-sample.md)**| Stáhněte si ukázku navržené aplikace pro text seskupit chat pro prohlížeče. |


## <a name="platforms-and-sdk-libraries"></a>Platformy a knihovny SDK

Přečtěte si další informace o sadách Azure Communication Services SDK pomocí níže uvedených prostředků. Rozhraní REST API jsou k dispozici pro většinu funkcí, pokud chcete sestavovat vlastní klienty nebo jinak přistupovat ke službě přes Internet.

| Prostředek                               | Popis                           |
|---                                    |---                                   |
|**[Knihovny SDK a rozhraní REST API](./concepts/sdk-options.md)**|Funkce komunikačních služeb Azure jsou koncepčně rozdělené do šesti oblastí, z nichž každý představuje sada SDK. Můžete se rozhodnout, které knihovny SDK se mají použít na základě vašich potřeb komunikace v reálném čase.|
|**[Přehled volání sady SDK](./concepts/voice-video-calling/calling-sdk-features.md)**|Projděte si přehled služby Communications Call SDK.|
|**[Přehled sady Chat SDK](./concepts/chat/sdk-features.md)**|Projděte si přehled služby Communications Chat SDK.|
|**[Přehled sady SMS SDK](./concepts/telephony-sms/sdk-features.md)**|Seznamte se s přehledem služby Communications Services SMS SDK.|

## <a name="other-microsoft-communication-services"></a>Další služby Microsoft Communication Services

Existují dva další komunikační produkty Microsoftu, které byste měli zvážit, protože nebudete moci přímo spolupracovat s komunikačními službami v tuto chvíli:

 - [Rozhraní API pro komunikaci Microsoft Graph cloudu](/graph/cloud-communications-concept-overview) umožňují organizacím vytvářet komunikační prostředí vázané na Azure Active Directory uživatele s Microsoft 365mi licencemi. To je ideální pro aplikace, které jsou vázané na Azure Active Directory nebo které chcete v Microsoft Teams využít k širšímu využití produktivity. Existují také rozhraní API pro sestavování aplikací a přizpůsobení v rámci [prostředí týmů.](/microsoftteams/platform/?preserve-view=true&view=msteams-client-js-latest)

 - [PlayFab strana](/gaming/playfab/features/multiplayer/networking/) zjednodušuje přidávání chatu s nízkou latencí a datové komunikace do her. I když můžete pomocí komunikačních služeb vysílat herní systémy pro konverzace a sítě, PlayFab je upravená možnost a v konzole Xbox zdarma.


## <a name="next-steps"></a>Další kroky

 - [Vytvoření prostředku Communication Services](./quickstarts/create-communication-resource.md)
