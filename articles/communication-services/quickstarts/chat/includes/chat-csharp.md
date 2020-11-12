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
ms.openlocfilehash: 5eb9867b17151987b165021f42a075e9355a315d
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523975"
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
dotnet add package Azure.Communication.Chat --version 1.0.0-beta.2
``` 

## <a name="object-model"></a>Objektový model

Následující třídy zpracovávají některé z hlavních funkcí služby Azure Communications chat Client Library pro C#.

| Název                                  | Popis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, posílání oznámení a čtení. |

## <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Chcete-li vytvořit chatovacího klienta, použijte koncový bod komunikačních služeb a přístupový token, který byl vygenerován jako součást požadovaných kroků. Chcete-li `CommunicationIdentityClient` `Administration` vytvořit uživatele a vystavit token, který bude předat klientovi chat, je nutné použít třídu z klientské knihovny. Přečtěte si další informace o [tokenech přístupu uživatele](../../access-tokens.md).

```csharp
using Azure.Communication.Identity;
using Azure.Communication.Chat;

// Your unique Azure Communication service endpoint
Uri endpoint = new Uri("https://<RESOURCE_NAME>.communication.azure.com");

CommunicationUserCredential communicationUserCredential = new CommunicationUserCredential(<Access_Token>);
ChatClient chatClient = new ChatClient(endpoint, communicationUserCredential);
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `createChatThread` metodu k vytvoření vlákna chatu.
- Použijte `topic` k poskytnutí tématu tomuto chatu; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `UpdateThread` funkce.
- Pomocí `members` vlastnosti můžete předat seznam `ChatThreadMember` objektů, které mají být přidány do konverzačního vlákna. `ChatThreadMember`Objekt je inicializován s `CommunicationUser` objektem. Chcete-li získat `CommunicationUser` objekt, budete muset předat ID přístupu, které jste vytvořili pomocí pokynů pro [Vytvoření uživatele](../../access-tokens.md#create-an-identity) .

Odpověď `chatThreadClient` se používá k provádění operací na vytvořeném konverzačním vlákně: Přidání členů do konverzačního vlákna, odeslání zprávy, odstranění zprávy atd. Obsahuje atribut, `Id` který je jedinečným identifikátorem konverzačního vlákna. 

```csharp
var chatThreadMember = new ChatThreadMember(new CommunicationUser("<Access_ID>"))
{
    DisplayName = "UserDisplayName"
};
ChatThreadClient chatThreadClient = await chatClient.CreateChatThreadAsync(topic: "Chat Thread topic C# sdk", members: new[] { chatThreadMember });
string threadId = chatThreadClient.Id;
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu
`GetChatThreadClient`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: přidat členy, poslat zprávu atd. IDvlákna je jedinečné ID existujícího konverzačního vlákna.

```csharp
string threadId = "<THREAD_ID>";
ChatThreadClient chatThreadClient = chatClient.GetChatThreadClient(threadId);
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `SendMessage` metodu k odeslání zprávy do vlákna identifikovaného pomocí IDvlákna.

- Použijte `content` k zadání obsahu zprávy chatu, která je povinná.
- Použijte `priority` k zadání úrovně priority zprávy, jako je ' Normal ' nebo ' High ', pokud není zadaná, použije se ' Normal '.
- Slouží `senderDisplayName` k zadání zobrazovaného názvu odesílatele, pokud není zadaný, použije se prázdný název.

`SendChatMessageResult` je odpověď vrácená z odeslání zprávy, obsahuje ID, což je jedinečné ID zprávy.

```csharp
var content = "hello world";
var priority = ChatMessagePriority.Normal;
var senderDisplayName = "sender name";

SendChatMessageResult sendChatMessageResult = await chatThreadClient.SendMessageAsync(content, priority, senderDisplayName);
string messageId = sendChatMessageResult.Id;
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete načíst zprávy chatu pomocí cyklického dotazování `GetMessages` metody v klientovi vlákna konverzace v zadaných intervalech.

```csharp
AsyncPageable<ChatMessage> allMessages = chatThreadClient.GetMessagesAsync();
await foreach (ChatMessage message in allMessages)
{
    Console.WriteLine($"{message.Id}:{message.Sender.Id}:{message.Content}");
}
```

`GetMessages` převezme volitelný `DateTimeOffset` parametr. Pokud je zadaný posun, dostanete po něm zprávy, které byly přijaty, aktualizovány nebo odstraněny. Všimněte si, že zprávy přijaté před časem posunutí, ale upravené nebo odebrané, budou vráceny také.

`GetMessages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí `UpdateMessage` a `DeleteMessage` . Pro odstraněné zprávy `chatMessage.DeletedOn` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `chatMessage.EditedOn` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze `chatMessage.CreatedOn` využít pomocí a lze jej použít k řazení zpráv.

`GetMessages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `chatMessage.Type` . Tyto typy:

- `Text`: Běžná zpráva chatu odeslaná členem vlákna.

- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma bylo aktualizováno.

- `ThreadActivity/AddMember`: Systémová zpráva, která indikuje, že jeden nebo více členů bylo přidáno do konverzačního vlákna.

- `ThreadActivity/DeleteMember`: Systémová zpráva, která indikuje, že člen byl odebrán z konverzačního vlákna.

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

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Přidat uživatele jako člena do vlákna chatu

Po vytvoření vlákna můžete z něj přidat a odebrat uživatele. Přidáním uživatelů udělíte jim přístup, aby bylo možné odesílat zprávy do vlákna a přidávat nebo odebírat další členy. Před voláním `AddMembers` se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Použijte `AddMembers` metodu pro přidání členů vlákna do vlákna identifikovaného pomocí IDvlákna.

 - Slouží `members` k vypsání členů, kteří mají být přidáni do konverzačního vlákna;
 - `User`je vyžadována identita, kterou získáte pro tohoto nového uživatele.
 - `DisplayName`volitelné, je zobrazované jméno člena vlákna.
 - `ShareHistoryTime`, volitelné, čas, ze kterého je historie chatu sdílena s členem. Chcete-li sdílet historii od začátku konverzačního vlákna, nastavte ji na DateTime. MinValue. Chcete-li sdílet žádnou historii, předchozí až po přidání člena, nastavte jej na aktuální čas. Chcete-li sdílet částečnou historii, nastavte ji na bod v čase mezi vytvořením vlákna a aktuálním časem.

```csharp
ChatThreadMember member = new ChatThreadMember(communicationUser);
member.DisplayName = "display name member 1";
member.ShareHistoryTime = DateTime.MinValue; // share all history
await chatThreadClient.AddMembersAsync(members: new[] {member});
```
## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání uživatele do vlákna můžete odebrat uživatele z konverzačního vlákna. K tomu je potřeba sledovat identitu (CommunicationUser) členů, které jste přidali.

```csharp
await chatThreadClient.RemoveMemberAsync(communicationUser);
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```
