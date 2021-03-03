---
title: Koncepce chatu v komunikačních službách Azure
titleSuffix: An Azure Communication Services concept document
description: Přečtěte si o konceptech chatu komunikačních služeb.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 2360cc8d202ed29051551231d14bef69c0e66ce4
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657804"
---
# <a name="chat-concepts"></a>Koncepce chatu

Konverzace klientských knihoven služby Azure Communication Services slouží k přidávání textových konverzací do vašich aplikací v reálném čase. Tato stránka shrnuje klíčové pojmy a možnosti chatu.

Další informace o konkrétních jazycích a funkcích klientské knihovny najdete v tématu [Přehled klientské knihovny služby Communications](./sdk-features.md) .

## <a name="chat-overview"></a>Přehled chatu

Chatové konverzace se vyskytují v rámci konverzačních vláken. Vlákno konverzace může obsahovat mnoho zpráv a mnoho uživatelů. Každá zpráva patří do jednoho vlákna a uživatel může být součástí jednoho nebo více vláken.

Každý uživatel ve vlákně chat se nazývá člen. Ve vlákně chatu můžete mít až 250 členů. Pouze členové vlákna mohou odesílat a přijímat zprávy nebo přidávat nebo odebírat členy ve vlákně chatu. Maximální povolená velikost zprávy je přibližně 28KB. Pomocí operace můžete načíst všechny zprávy ve vlákně chatu `List/Get Messages` . Komunikační služby ukládají historii chatů, dokud neprovedete operaci odstranění ve vlákně nebo zprávě chatu nebo dokud žádné členy nezůstanou ve vlákně chatu, kdy je v tom případě nezůstane osamocená a zpracovaná pro odstranění.

Pro vlákna chatu s více než 20 členy je přečtení potvrzení a zadávání funkcí indikátoru zakázané.

## <a name="chat-architecture"></a>Architektura chatu

K dispozici jsou dvě základní součásti pro konverzaci: 1) klientská aplikace pro důvěryhodné služby a 2).

:::image type="content" source="../../media/chat-architecture.png" alt-text="Diagram znázorňující architekturu chatu komunikačních služeb":::

 - **Důvěryhodná služba:** Aby bylo možné správně spravovat relaci konverzace, potřebujete službu, která vám pomůže se připojit ke komunikačním službám pomocí připojovacího řetězce prostředků. Tato služba zodpovídá za vytváření chatovacích vláken, správu členství v vláknech a poskytování přístupových tokenů uživatelům. Další informace o přístupových tokenech najdete v našem rychlém startu [přístupových tokenů](../../quickstarts/access-tokens.md) .

 - **Klientská aplikace:**  Klientská aplikace se připojí k vaší důvěryhodné službě a získá přístupové tokeny, které se používají pro připojení přímo ke komunikačním službám. Po navázání tohoto připojení může klientská aplikace odesílat a přijímat zprávy.

K vygenerování přístupových tokenů doporučujeme použít úroveň důvěryhodné služby. V tomto scénáři by strana na straně serveru byla zodpovědná za vytváření a správu uživatelů a vydávání tokenů.

## <a name="message-types"></a>Typy zpráv

Služba Communications Services chat sdílí uživatelem generované zprávy i zprávy generované systémem, které se nazývají **aktivity vláken**. Aktivity vlákna jsou generovány při aktualizaci vlákna chatu. Při volání `List Messages` nebo `Get Messages` v konverzačním vlákně bude výsledek obsahovat textové zprávy generované uživatelem a také systémové zprávy v chronologickém pořadí. To vám pomůže určit, kdy se člen přidal nebo odebral nebo když se aktualizovalo téma konverzačního vlákna. Podporované typy zpráv:

 - `Text`: Zpráva s prostým textem složená a odeslaná uživatelem jako součást konverzace chatu.
 - `RichText/HTML`: Naformátovaná textová zpráva. Všimněte si, že uživatelé komunikačních služeb aktuálně nemůžou odesílat zprávy RTF. Tento typ zprávy je podporován zprávami odesílanými od týmů uživatelů ke komunikačním službám ve scénářích spolupráce týmů.

 - `ThreadActivity/ParticipantAdded`: Systémová zpráva, která indikuje, že jeden nebo více účastníků bylo přidáno do konverzačního vlákna. Například:

```
{
            "id": "1613589626560",
            "type": "participantAdded",
            "sequenceId": "7",
            "version": "1613589626560",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:26Z"
        }
```

- `ThreadActivity/ParticipantRemoved`: Systémová zpráva, která indikuje, že účastník byl odebrán z konverzačního vlákna. Například:

```
{
            "id": "1613589627603",
            "type": "participantRemoved",
            "sequenceId": "8",
            "version": "1613589627603",
            "content":
            {
                "participants":
                [
                    {
                        "id": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4df6-f40f-343a0d003226",
                        "displayName": "Jane",
                        "shareHistoryTime": "1970-01-01T00:00:00Z"
                    }
                ],
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:27Z"
        }
```

- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma vlákna bylo aktualizováno. Například:

```
{
            "id": "1613589623037",
            "type": "topicUpdated",
            "sequenceId": "2",
            "version": "1613589623037",
            "content":
            {
                "topic": "New topic",
                "initiator": "8:acs:d2a829bc-8523-4404-b727-022345e48ca6_00000008-511c-4ce0-f40f-343a0d003224"
            },
            "createdOn": "2021-02-17T19:20:23Z"
        }
```

## <a name="real-time-signaling"></a>Signalizace v reálném čase

Klientská knihovna JavaScriptu pro chat zahrnuje signalizaci v reálném čase. To umožňuje klientům naslouchat v reálném čase aktualizace a příchozí zprávy do konverzačního vlákna, aniž by museli dotazovat rozhraní API. K dispozici jsou tyto události:

 - `ChatMessageReceived` – Při odeslání nové zprávy do konverzačního vlákna, ve kterém je uživatel členem. Tato událost se neposílá pro automaticky generované systémové zprávy, které jsme probrali v předchozím tématu.
 - `ChatMessageEdited` – Když se ve vlákně chatu upraví zpráva, na kterou je uživatel členem.
 - `ChatMessageDeleted` – Když se ve vlákně chatu odstraní zpráva, na kterou je uživatel členem.
 - `TypingIndicatorReceived` – když jiný člen zadá zprávu ve vlákně chatu, na které je uživatel členem.
 - `ReadReceiptReceived` – když jiný člen přečte zprávu, kterou odeslal uživatel ve vlákně chatu.

## <a name="chat-events"></a>Události chatu

Signalizace v reálném čase umožňuje uživatelům chatovat v reálném čase. Vaše služby můžou použít Azure Event Grid k přihlášení k odběru událostí souvisejících s chatem. Další podrobnosti najdete v tématu [zpracování událostí – koncepční](../event-handling.md).

## <a name="using-cognitive-services-with-chat-client-library-to-enable-intelligent-features"></a>Povolení inteligentních funkcí pomocí Cognitive Services s využitím klientské knihovny chatu

[Rozhraní API pro rozpoznávání Azure](../../../cognitive-services/index.yml) můžete použít spolu s knihovnou klienta chat k přidání inteligentních funkcí do aplikací. Můžete například:

- Umožněte uživatelům, aby v různých jazycích navzájemly konverzaci.
- Pomoc agentovi podpory určení priorit lístků pomocí zjištění negativního míněníu příchozího problému od zákazníka.
- Analyzujte příchozí zprávy pro detekci klíčů a rozpoznávání entit a pro uživatele v aplikaci podle obsahu zprávy vyzvat relevantní informace.

Jedním ze způsobů, jak toho dosáhnout, je jednat o členství vaší důvěryhodné služby jako člena konverzačního vlákna. Řekněme, že chcete povolit převod jazyka. Tato služba bude zodpovědná za naslouchání zpráv vyměňovaných jinými členy [1], volání rozhraní API pro rozpoznávání obsahu pro překlad obsahu na požadovaný jazyk [2, 3] a odeslání přeloženého výsledku jako zprávy ve vlákně chatu [4].

Historie zpráv tak bude obsahovat původní i přeložené zprávy. V klientské aplikaci můžete přidat logiku pro zobrazení původní nebo přeložené zprávy. V [tomto rychlém](../../../cognitive-services/translator/quickstart-translator.md) startu se naučíte, jak používat rozpoznávání rozhraní API k překladu textu do různých jazyků.

:::image type="content" source="../media/chat/cognitive-services.png" alt-text="Diagram znázorňující Cognitive Services interakci s komunikačními službami.":::

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Začínáme s chatem](../../quickstarts/chat/get-started.md)

Následující dokumenty můžou být zajímavé:

- Seznámení s [knihovnou klienta chat](sdk-features.md)
