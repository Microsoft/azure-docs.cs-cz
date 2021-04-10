---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: mikben
manager: mikben
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/10/2021
ms.topic: include
ms.custom: include file
ms.author: mikben
ms.openlocfilehash: 9bb4b5c27a114463b807ce9bba7f4d1887a40fb1
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105726644"
---
## <a name="prerequisites"></a>Požadavky
Než začnete, nezapomeňte:

- Vytvořte si účet Azure s aktivním předplatným. Podrobnosti najdete v článku o [Vytvoření účtu zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Instalace [Pythonu](https://www.python.org/downloads/)
- Vytvořte prostředek služby Azure Communication Services. Podrobnosti najdete v tématu [vytvoření prostředku komunikace Azure](../../create-communication-resource.md). Pro tento rychlý Start budete muset nahrát **koncový bod** prostředku.
- [Přístupový token uživatele](../../access-tokens.md). Ujistěte se, že jste nastavili obor na "chat" a poznamenali jste řetězec tokenu a také řetězec userId.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir chat-quickstart && cd chat-quickstart
```

Pomocí textového editoru vytvořte soubor s názvem **Start-chat.py** v kořenovém adresáři projektu a přidejte strukturu pro program, včetně základního zpracování výjimek. Do tohoto souboru přidáte veškerý zdrojový kód pro tento rychlý Start v následujících oddílech.

```python
import os
# Add required SDK components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-sdk"></a>Instalace sady SDK

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Chat SDK pro Python.

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ChatClient | Tato třída je potřebná pro funkci chatu. Vytvoří se jeho instance s informacemi o předplatném a použije se k vytváření, získávání a odstraňování vláken. |
| ChatThreadClient | Tato třída je potřebná pro funkci konverzačního vlákna. Získáte instanci prostřednictvím ChatClient a použijete ji k posílání, přijímání, aktualizaci a odstraňování zpráv, přidávání, odebírání a získávání uživatelů, posílání oznámení a čtení. |

## <a name="create-a-chat-client"></a>Vytvoření chatového klienta

Chcete-li vytvořit chatovacího klienta, použijte koncový bod komunikační služby a `Access Token` vygenerovaný v rámci požadavků nutných k provedení. Přečtěte si další informace o [tokenech přístupu uživatele](../../access-tokens.md).

V tomto rychlém startu se nezabývá vytvořením vrstvy služby pro správu tokenů pro aplikaci Chat, i když se doporučuje. Další informace o [architektuře chatu](../../../concepts/chat/concepts.md) najdete v následující dokumentaci.

```console
pip install azure-communication-identity
```

```python
from azure.communication.chat import ChatClient, CommunicationTokenCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationTokenCredential("<Access Token>"))
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `create_chat_thread` metodu k vytvoření vlákna chatu.

- Použijte `topic` k poskytnutí tématu vlákna; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `update_thread` funkce.
- Použijte `thread_participants` k vypsání seznamu, který se má `ChatThreadParticipant` Přidat do konverzačního vlákna, `ChatThreadParticipant` `CommunicationUserIdentifier` typ `user` , který je možné po vytvoření [vytvořit uživatelem](../../access-tokens.md#create-an-identity) .

`CreateChatThreadResult` je výsledek vrácen z vytvoření vlákna, můžete jej použít k načtení `id` vlákna chatu, který byl vytvořen. `id`Pak lze použít k načtení `ChatThreadClient` objektu pomocí `get_chat_thread_client` metody. `ChatThreadClient` dá se použít k provádění dalších operací chatu do tohoto konverzačního vlákna.

```python
from azure.communication.chat import ChatThreadParticipant

topic="test topic"

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu
`get_chat_thread_client`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: Přidat účastníky, poslat zprávu atd. thread_id je jedinečné ID existujícího konverzačního vlákna.

`ChatThreadClient` dá se použít k provádění dalších operací chatu do tohoto konverzačního vlákna.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```


## <a name="list-all-chat-threads"></a>Zobrazit seznam všech vláken chatu
`list_chat_threads`Metoda vrátí iterátor typu `ChatThreadItem` . Dá se použít k výpisu všech vláken chatu.

- Slouží `start_time` k zadání nejstaršího bodu v čase, který bude mít za následek vytvoření vlákna konverzace.
- Slouží `results_per_page` k určení maximálního počtu vláken konverzace vrácených na stránku.

Iterátorem `[ChatThreadItem]` je odpověď vrácená ze seznamu vláken.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_threads = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_item_page in chat_threads.by_page():
    for chat_thread_item in chat_thread_item_page:
        print(chat_thread_item)
        print('Chat Thread Id: ', chat_thread_item.id)
```


## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `send_message` metodu k odeslání zprávy do vlákna chatu, které jste právě vytvořili, identifikovaného thread_id.

- Slouží `content` k zadání obsahu zprávy chatu;
- Slouží `chat_message_type` k určení typu obsahu zprávy. Možné hodnoty jsou "text" a "HTML"; Pokud není zadána výchozí hodnota ' text ', je přiřazena.
- Slouží `sender_display_name` k zadání zobrazovaného jména odesílatele.

`SendChatMessageResult` je odpověď vrácená z odeslání zprávy, obsahuje ID, což je jedinečné ID zprávy.

```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_w_enum.id)
```


## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete načíst zprávy chatu pomocí cyklického dotazování `list_messages` metody v určených intervalech.

- Slouží `results_per_page` k určení maximálního počtu zpráv, které se mají vrátit na stránku.
- Slouží `start_time` k zadání nejstaršího bodu v čase, ve kterém se budou zprávy zobrazovat.

Iterátorem `[ChatMessage]` je odpověď vrácená z výpisu zpráv.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=1)

chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print("ChatMessage: Id=", chat_message.id, "; Content=", chat_message.content.message)
```

`list_messages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí `update_message` a `delete_message` . Pro odstraněné zprávy `ChatMessage.deleted_on` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `ChatMessage.edited_on` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze použít pomocí `ChatMessage.created_on` , který lze použít k řazení zpráv.

`list_messages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `ChatMessage.type` . 

Další informace o typech zpráv naleznete zde: [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="send-read-receipt"></a>Odeslat oznámení pro čtení
`send_read_receipt`Metoda může být použita k odeslání události přijetí pro čtení do vlákna jménem uživatele.

- Slouží `message_id` k zadání ID poslední přečtené zprávy aktuálním uživatelem.

```python
content='hello world'

send_message_result = chat_thread_client.send_message(content)
chat_thread_client.send_read_receipt(message_id=send_message_result.id)
```


## <a name="add-a-user-as-a-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup, aby bylo možné odesílat zprávy do konverzačního vlákna a přidávat nebo odebírat další účastníky. Před voláním `add_participants` metody se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Jeden nebo více uživatelů lze do vlákna chatu přidat pomocí `add_participants` metody, pokud je k dispozici nový přístupový token a identifikace je k dispozici pro všechny uživatele.

`list(tuple(ChatThreadParticipant, CommunicationError))`Je vrácen. Po úspěšném přidání účastníka se očekává prázdný seznam. V případě, že došlo k chybě při přidávání účastníka, seznam se naplní neúspěšnými účastníky spolu s chybou, ke které došlo.

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create 2 users
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_users = [identity_client.create_user() for i in range(2)]

# # conversely, you can also add an existing user to a chat thread; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
#
# user_id = 'some user id'
# user_display_name = "Wilma Flinstone"
# new_user = CommunicationUserIdentifier(user_id)
# participant = ChatThreadParticipant(
#     user=new_user,
#     display_name=user_display_name,
#     share_history_time=datetime.utcnow())

participants = []
for _user in new_users:
  chat_thread_participant = ChatThreadParticipant(
    user=_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow()
  ) 
  participants.append(chat_thread_participant) 

response = chat_thread_client.add_participants(participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if retry:
    chat_thread_client.add_participants(retry)
```


## <a name="list-thread-participants-in-a-chat-thread"></a>Výpis účastníků vlákna ve vlákně chatu

Podobně jako při přidávání účastníka můžete také vypisovat účastníky z vlákna.

Použijte `list_participants` k načtení účastníků vlákna.
- Použijte `results_per_page` , volitelné, maximální počet účastníků, který se má vrátit na stránku.
- Použijte `skip` volitelnou pro přeskočení účastníků až do zadané pozice v reakci.

Iterátorem `[ChatThreadParticipant]` je odpověď vrácená z výpisu účastníků.

```python
chat_thread_participants = chat_thread_client.list_participants()
for chat_thread_participant_page in chat_thread_participants.by_page():
    for chat_thread_participant in chat_thread_participant_page:
        print("ChatThreadParticipant: ", chat_thread_participant)
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `python` příkazu.

```console
python start-chat.py
```
