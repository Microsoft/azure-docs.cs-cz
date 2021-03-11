---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 9/1/2020
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: f0e1208adb0ce937c7b72d88564e346a30608291
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102603232"
---
## <a name="prerequisites"></a>Požadavky
Než začnete, nezapomeňte:
- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nainstalovat [Visual Studio](https://visualstudio.microsoft.com/downloads/)
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset zaznamenat **koncový bod** prostředku.
- [Přístupový token uživatele](../../access-tokens.md). Ujistěte se, že jste nastavili obor na "chat" a poznamenali jste řetězec tokenu a také řetězec userId.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `ChatQuickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program.cs**.

```console
dotnet new console -o ChatQuickstart
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd ChatQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalace balíčku

Instalace klientské knihovny služby Azure Communications chat pro .NET

```PowerShell
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.5
```

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé z hlavních funkcí služby Azure Communications chat Client Library pro C#.

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání účastníků, odesílání oznámení o přečtení a čtení. |

## <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Chcete-li vytvořit chatovacího klienta, použijte koncový bod komunikačních služeb a přístupový token, který byl vygenerován jako součást požadovaných kroků. Chcete-li `CommunicationIdentityClient` vytvořit uživatele a vystavit token, který bude předat klientovi chat, je nutné použít třídu z klientské knihovny identity.

Přečtěte si další informace o [tokenech přístupu uživatele](../../access-tokens.md).

V tomto rychlém startu se nezabývá vytvořením vrstvy služby pro správu tokenů pro aplikaci Chat, i když se doporučuje. Další informace o [architektuře chatu](../../../concepts/chat/concepts.md)

Zkopírujte následující fragmenty kódu a vložte je do zdrojového souboru: **program.cs**
```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;
using Azure;
using Azure.Communication;

namespace ChatQuickstart
{
    class Program
    {
        static async System.Threading.Tasks.Task Main(string[] args)
        {
            // Your unique Azure Communication service endpoint
            Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

            CommunicationTokenCredential communicationTokenCredential = new CommunicationTokenCredential(<Access_Token>);
            ChatClient chatClient = new ChatClient(endpoint, communicationTokenCredential);
        }
    }
}
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Pomocí `createChatThread` metody v chatClient vytvořte vlákno chatu.
- Použijte `topic` k poskytnutí tématu tomuto chatu; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `UpdateTopic` funkce.
- Pomocí `participants` vlastnosti můžete předat seznam `ChatParticipant` objektů, které mají být přidány do konverzačního vlákna. `ChatParticipant`Objekt je inicializován s `CommunicationIdentifier` objektem. `CommunicationIdentifier` může být typu `CommunicationUserIdentifier` `MicrosoftTeamsUserIdentifier` nebo `PhoneNumberIdentifier` . Například pro získání `CommunicationIdentifier` objektu budete muset předat ID přístupu, které jste vytvořili pomocí pokynů pro [Vytvoření uživatele](../../access-tokens.md#create-an-identity) .

Objekt Response z `createChatThread` metody obsahuje `chatThread` Podrobnosti. Pro interakci s operacemi vlákna chatu, jako je přidání účastníků, odeslání zprávy, odstranění zprávy atd. `chatThreadClient` instance klienta musí být vytvořena pomocí `GetChatThreadClient` metody v `ChatClient` klientovi.

```csharp
var chatParticipant = new ChatParticipant(communicationIdentifier: new CommunicationUserIdentifier(id: "<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
CreateChatThreadResult createChatThreadResult = await chatClient.CreateChatThreadAsync(topic: "Hello world!", participants: new[] { chatParticipant });
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(createChatThreadResult.ChatThread.Id);
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu
`GetChatThreadClient`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: přidat členy, poslat zprávu atd. IDvlákna je jedinečné ID existujícího konverzačního vlákna.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Slouží `SendMessage` k odeslání zprávy do vlákna.

- Slouží `content` k zadání obsahu zprávy, která je povinná.
- Používá `type` se pro typ obsahu zprávy, například text nebo HTML. Není-li tento parametr zadán, bude nastavena hodnota "text".
- Slouží `senderDisplayName` k zadání zobrazovaného jména odesílatele. Pokud není zadán, bude nastaven prázdný řetězec.

```csharp
var messageId = await chatThreadClient.SendMessageAsync(content:"hello world", type: ChatMessageType.Text);
```
## <a name="get-a-message"></a>Získat zprávu

Slouží `GetMessage` k načtení zprávy ze služby.
`messageId` je jedinečné ID zprávy.

`ChatMessage` je odpověď vrácená z získání zprávy, obsahuje ID, což je jedinečný identifikátor zprávy, mimo jiné pole. Přečtěte si prosím Azure. Communication. chat. ChatMessage.

```csharp
ChatMessage chatMessage = await chatThreadClient.GetMessageAsync(messageId);
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete načíst zprávy chatu pomocí cyklického dotazování `GetMessages` metody v klientovi vlákna konverzace v zadaných intervalech.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Id}:{message.Content}");
}
```

`GetMessages` převezme volitelný `DateTimeOffset` parametr. Pokud je zadaný posun, dostanete po něm zprávy, které byly přijaty, aktualizovány nebo odstraněny. Všimněte si, že zprávy přijaté před časem posunutí, ale upravené nebo odebrané, budou vráceny také.

`GetMessages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí `UpdateMessage` a `DeleteMessage` . Pro odstraněné zprávy `chatMessage.DeletedOn` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `chatMessage.EditedOn` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze `chatMessage.CreatedOn` využít pomocí a lze jej použít k řazení zpráv.

`GetMessages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `chatMessage.Type` . Tyto typy:

- `Text`: Běžná zpráva chatu odeslaná členem vlákna.

- `Html`: Naformátovaná textová zpráva. Všimněte si, že uživatelé komunikačních služeb aktuálně nemůžou odesílat zprávy RTF. Tento typ zprávy je podporován zprávami odesílanými od týmů uživatelů ke komunikačním službám ve scénářích spolupráce týmů.

- `TopicUpdated`: Systémová zpráva, která indikuje, že téma bylo aktualizováno. ReadOnly

- `ParticipantAdded`: Systémová zpráva, která indikuje, že jeden nebo více účastníků bylo přidáno do konverzačního vlákna. ReadOnly

- `ParticipantRemoved`: Systémová zpráva, která indikuje, že účastník byl odebrán z konverzačního vlákna.

Další podrobnosti najdete v tématu [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="update-a-message"></a>Aktualizace zprávy

Můžete aktualizovat zprávu, která již byla odeslána voláním metody `UpdateMessage` on `ChatThreadClient` .

```csharp
string id = "id-of-message-to-edit";
string content = "updated content";
await chatThreadClient.UpdateMessageAsync(id, content);
```

## <a name="deleting-a-message"></a>Odstranění zprávy

Zprávu můžete odstranit vyvoláním `DeleteMessage` na `ChatThreadClient` .

```csharp
string id = "id-of-message-to-delete";
await chatThreadClient.DeleteMessageAsync(id);
```

## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Po vytvoření vlákna můžete z něj přidat a odebrat uživatele. Přidáním uživatelů udělíte jim přístup, aby bylo možné odesílat zprávy do vlákna a přidat nebo odebrat jiného účastníka. Před voláním `AddParticipants` se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Slouží `AddParticipants` k přidání jednoho nebo více účastníků do konverzačního vlákna. Následující jsou podporované atributy pro každého účastníka vlákna:
- `communicationUser`, požadováno, je identita účastníka vlákna.
- `displayName`volitelné, je zobrazované jméno účastníka vlákna.
- `shareHistoryTime`, volitelné, čas, ze kterého je historie chatu sdílena s účastníkem.

```csharp
var josh = new CommunicationUserIdentifier(id: "<Access_ID_For_Josh>");
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
var amy = new CommunicationUserIdentifier(id: "<Access_ID_For_Amy>");

var participants = new[]
{
    new ChatParticipant(josh) { DisplayName = "Josh" },
    new ChatParticipant(gloria) { DisplayName = "Gloria" },
    new ChatParticipant(amy) { DisplayName = "Amy" }
};

await chatThreadClient.AddParticipantsAsync(participants);
```
## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání uživatele do vlákna můžete odebrat uživatele z konverzačního vlákna. K tomu je potřeba sledovat identitu `CommunicationUser` účastníka, kterého jste přidali.

```csharp
var gloria = new CommunicationUserIdentifier(id: "<Access_ID_For_Gloria>");
await chatThreadClient.RemoveParticipantAsync(gloria);
```

## <a name="get-thread-participants"></a>Získat účastníky vlákna

Slouží `GetParticipants` k načtení účastníků konverzačního vlákna.

```csharp
AsyncPageable<ChatParticipant> allParticipants = chatThreadClient.GetParticipantsAsync();
await foreach (ChatParticipant participant in allParticipants)
{
    Console.WriteLine($"{((CommunicationUserIdentifier)participant.User).Id}:{participant.DisplayName}:{participant.ShareHistoryTime}");
}
```

## <a name="send-typing-notification"></a>Odeslat oznámení o zápisu

Slouží `SendTypingNotification` k označení toho, že uživatel zadává odpověď do vlákna.

```csharp
await chatThreadClient.SendTypingNotificationAsync();
```

## <a name="send-read-receipt"></a>Odeslat oznámení pro čtení

Slouží `SendReadReceipt` k oznámení ostatním účastníkům, že uživatel přečte zprávu.

```csharp
await chatThreadClient.SendReadReceiptAsync(messageId);
```

## <a name="get-read-receipts"></a>Získat účtenky pro čtení

Slouží `GetReadReceipts` ke kontrole stavu zpráv, abyste viděli, které jsou čteny ostatními účastníky konverzačního vlákna.

```csharp
AsyncPageable<ChatMessageReadReceipt> allReadReceipts = chatThreadClient.GetReadReceiptsAsync();
await foreach (ChatMessageReadReceipt readReceipt in allReadReceipts)
{
    Console.WriteLine($"{readReceipt.ChatMessageId}:{((CommunicationUserIdentifier)readReceipt.Sender).Id}:{readReceipt.ReadOn}");
}
```
## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```
