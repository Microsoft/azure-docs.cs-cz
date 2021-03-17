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
ms.openlocfilehash: 2d1c3d3be412f6f11f9d2e300b3a97cf5634f5e4
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2021
ms.locfileid: "103495386"
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
# Add required client library components from quickstart here

try:
    print('Azure Communication Services - Chat Quickstart')
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-client-library"></a>Nainstalovat klientskou knihovnu

```console

pip install azure-communication-chat

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce v knihovně klienta služby Azure Communications chat pro Python.

| Název                                  | Popis                                                  |
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
from azure.communication.chat import ChatClient
from azure.communication.identity._shared.user_credential import CommunicationTokenCredential
from azure.communication.chat._shared.user_token_refresh_options import CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `create_chat_thread` metodu k vytvoření vlákna chatu.

- Použijte `topic` k poskytnutí tématu vlákna; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `update_thread` funkce.
- Použijte `thread_participants` k vypsání seznamu, který se má `ChatThreadParticipant` Přidat do konverzačního vlákna, `ChatThreadParticipant` `CommunicationUserIdentifier` typ `user` , který je možné po vytvoření [vytvořit uživatelem](../../access-tokens.md#create-an-identity) .
- Použijte `repeatability_request_id` k přímému zacílení žádosti o zopakování. Klient může požadavek vytvořit víckrát se stejnou opakovatelností – požadavek-ID a vrátí odpovídající odpověď bez serveru, který požadavek vykoná několikrát.

`CreateChatThreadResult` je výsledek vrácen z vytvoření vlákna, můžete jej použít k načtení `id` vlákna chatu, který byl vytvořen. `id`Pak lze použít k načtení `ChatThreadClient` objektu pomocí `get_chat_thread_client` metody. `ChatThreadClient` dá se použít k provádění dalších operací chatu do tohoto konverzačního vlákna.

#### <a name="without-repeatability-request-id"></a>Bez opakovatelnosti – požadavek-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

create_chat_thread_result = chat_client.create_chat_thread(topic)
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)
```

#### <a name="with-repeatability-request-id"></a>S možností opakování – ID žádosti
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

# from azure.communication.identity import CommunicationIdentityClient
# 
# # create an user
# identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
# user = identity_client.create_user()
# 
# ## OR pass existing user
# # from azure.communication.identity import CommunicationUserIdentifier
# # user_id = 'some_user_id'
# # user = CommunicationUserIdentifier(user_id)

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, 
                                                    thread_participants=participants, 
                                                    repeatability_request_id=repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu
`get_chat_thread_client`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: Přidat účastníky, poslat zprávu atd. thread_id je jedinečné ID existujícího konverzačního vlákna.

`ChatThreadClient` dá se použít k provádění dalších operací chatu do tohoto konverzačního vlákna.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="get-a-chat-thread"></a>Získání konverzačního vlákna

Použijte `get_chat_thread` metodu načtení `ChatThread` ze služby; `thread_id` je jedinečné ID vlákna.
- Pokud `thread_id` chcete zadat jedinečné ID vlákna, použijte, povinné. 
```python
chat_thread = chat_client.get_chat_thread(thread_id=thread_id)
```

## <a name="list-all-chat-threads"></a>Zobrazit seznam všech vláken chatu
`list_chat_threads`Metoda vrátí iterátor typu `ChatThreadInfo` . Dá se použít k výpisu všech vláken chatu.

- Slouží `start_time` k zadání nejstaršího bodu v čase, který bude mít za následek vytvoření vlákna konverzace.
- Slouží `results_per_page` k určení maximálního počtu vláken konverzace vrácených na stránku.

Iterátorem `[ChatThreadInfo]` je odpověď vrácená ze seznamu vláken.

```python
from datetime import datetime, timedelta

start_time = datetime.utcnow() - timedelta(days=2)

chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)
for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        print(chat_thread_info)
```

## <a name="delete-a-chat-thread"></a>Odstraní vlákno chatu.
`delete_chat_thread`Slouží k odstranění konverzačního vlákna.

- Slouží `thread_id` k určení thread_id existujícího konverzačního vlákna, které je třeba odstranit.

```python
thread_id = create_chat_thread_result.chat_thread.id
chat_client.delete_chat_thread(thread_id=thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `send_message` metodu k odeslání zprávy do vlákna chatu, které jste právě vytvořili, identifikovaného thread_id.

- Slouží `content` k zadání obsahu zprávy chatu;
- Slouží `chat_message_type` k určení typu obsahu zprávy. Možné hodnoty jsou "text" a "HTML"; Pokud není zadána výchozí hodnota ' text ', je přiřazena.
- Slouží `sender_display_name` k zadání zobrazovaného jména odesílatele.

Odpověď je "ID" typu `str` , což je jedinečné ID této zprávy.

#### <a name="message-type-not-specified"></a>Není zadaný typ zprávy.
```python
topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)

content='hello world'

send_message_result_id = chat_thread_client.send_message(content)
```

#### <a name="message-type-specified"></a>Zadaný typ zprávy
```python
from azure.communication.chat import ChatMessageType

topic = "test topic"
create_chat_thread_result = chat_client.create_chat_thread(topic)
thread_id = create_chat_thread_result.chat_thread.id
chat_thread_client = chat_client.get_chat_thread_client(create_chat_thread_result.chat_thread.id)


content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)
print("Message sent: id: ", send_message_result_id_w_enum)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
print("Message sent: id: ", send_message_result_id_w_str)
```

## <a name="get-a-specific-chat-message-from-a-chat-thread"></a>Získání konkrétní zprávy chatu z konverzačního vlákna
`get_message`Funkci lze použít k načtení konkrétní zprávy identifikované message_id

- Slouží `message_id` k zadání ID zprávy.

Odpověď typu `ChatMessage` obsahuje všechny informace týkající se jedné zprávy.

```python
message_id = send_message_result_id
chat_message = chat_thread_client.get_message(message_id)
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

`list_messages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `ChatMessage.type` . Tyto typy:

- `ChatMessageType.TEXT`: Běžná zpráva chatu odeslaná účastníkem vlákna.

- `ChatMessageType.HTML`: Zpráva chatu HTML odeslaná účastníkem vlákna.

- `ChatMessageType.TOPIC_UPDATED`: Systémová zpráva, která indikuje, že téma bylo aktualizováno.

- `ChatMessageType.PARTICIPANT_ADDED`: Systémová zpráva, která indikuje, že jeden nebo více účastníků bylo přidáno do konverzačního vlákna.

- `ChatMessageType.PARTICIPANT_REMOVED`: Systémová zpráva, která indikuje, že účastník byl odebrán z konverzačního vlákna.

Další podrobnosti najdete v tématu [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="update-topic-of-a-chat-thread"></a>Aktualizace tématu konverzačního vlákna
Můžete aktualizovat téma konverzačního vlákna pomocí `update_topic` metody.

```python
topic = "updated thread topic"
chat_thread_client.update_topic(topic=topic)

chat_thread = chat_client.get_chat_thread(chat_thread_client.thread_id)
updated_topic = chat_thread.topic

print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Aktualizace zprávy
Obsah existující zprávy můžete aktualizovat pomocí `update_message` metody, kterou identifikuje message_id

- Použití `message_id` , povinné, je jedinečné ID zprávy.
- Použití `content` , volitelné, je obsah zprávy, který se má aktualizovat. Pokud není zadaný, bude přiřazený jako prázdný.

```python
content = 'Hello world!'
send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)

content = 'Hello! I am updated content'
chat_thread_client.update_message(message_id=send_message_result_id, content=content)

chat_message = chat_thread_client.get_message(send_message_result_id)
print('Updated message content: ', chat_message.content.message)
```

## <a name="send-read-receipt-for-a-message"></a>Odeslat oznámení pro čtení zprávy
`send_read_receipt`Metoda může být použita k odeslání události přijetí pro čtení do vlákna jménem uživatele.

- Slouží `message_id` k zadání ID poslední přečtené zprávy aktuálním uživatelem.

```python
message_id=send_message_result_id
chat_thread_client.send_read_receipt(message_id=message_id)
```

## <a name="list-read-receipts-for-a-chat-thread"></a>Výpis oznámení o čtení pro vlákno chatu
`list_read_receipts`Metodu lze použít k získání oznámení o čtení pro vlákno.

- Slouží `results_per_page` k určení maximálního počtu zpráv konverzace ke čtení, které se mají vrátit na stránku.
- Slouží `skip` k zadání oznámení o přeskočení čtení zprávy chatu až do zadané pozice v odpovědi.

Iterátorem `[ChatMessageReadReceipt]` je odpověď vrácená z výpisu oznámení o čtení.

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=5, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
        print('Sender', read_receipt.sender)
        print('Message Id', read_receipt.chat_message_id)
        print('Read On Timestamp', read_receipt.read_on)
```

## <a name="send-typing-notification"></a>Odeslat oznámení o zápisu
`send_typing_notification`Metoda může být použita k odeslání události zápisu do vlákna jménem uživatele.

```python
chat_thread_client.send_typing_notification()
```

## <a name="delete-message"></a>Odstranit zprávu
`delete_message`Metodu lze použít k odstranění zprávy identifikované message_id

- Použijte `message_id` k určení message_id

```python
message_id=send_message_result_id
chat_thread_client.delete_message(message_id=message_id)
```

## <a name="add-a-user-as-participant-to-the-chat-thread"></a>Přidat uživatele jako účastníka do konverzačního vlákna

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup, aby bylo možné odesílat zprávy do konverzačního vlákna a přidávat nebo odebírat další účastníky. Před voláním `add_participant` metody se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Použijte `add_participant` metodu pro přidání účastníků vlákna do vlákna identifikovaného thread_id.

- Použijte `thread_participant` k určení účastníka, který se má přidat do konverzačního vlákna;
- `user`je povinné, `CommunicationUserIdentifier` když jste vytvořili v části `CommunicationIdentityClient` [Vytvoření uživatele](../../access-tokens.md#create-an-identity) .
- `display_name`volitelné, je zobrazované jméno účastníka vlákna.
- `share_history_time`volitelné, je čas, od kterého je historie chatu sdílena s účastníkem. Chcete-li sdílet historii od vytvoření vlákna chatu, nastavte tuto vlastnost na jakékoli datum, které je rovno nebo menší než čas vytvoření vlákna. Pokud chcete sdílet žádnou historii předchozí až po přidání účastníka, nastavte ho na aktuální datum. Chcete-li sdílet částečnou historii, nastavte ji na datum zprostředkovatele.

Po úspěšném přidání účastníka není vyvolána žádná chyba. V případě, že došlo k chybě při přidávání účastníka, `RuntimeError` je vyvolána výjimka

```python
from azure.communication.identity import CommunicationIdentityClient
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

# create an user
identity_client = CommunicationIdentityClient.from_connection_string('<connection_string>')
new_user = identity_client.create_user()

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

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

participant = ChatThreadParticipant(
    user=new_user,
    display_name='Fred Flinstone',
    share_history_time=datetime.utcnow())

try:
    chat_thread_client.add_participant(thread_participant=participant)
except RuntimeError as e:
    if e is not None and decide_to_retry(error=e):
        chat_thread_client.add_participant(thread_participant=participant)
```

Do konverzačního vlákna lze přidat také více uživatelů pomocí `add_participants` metody, pokud je k dispozici nový přístupový token a identifikace je k dispozici pro všechny uživatele.

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

response = chat_thread_client.add_participants(thread_participants=participants)

def decide_to_retry(error, **kwargs):
    """
    Insert some custom logic to decide if retry is applicable based on error
    """
    return True

# verify if all users has been successfully added or not
# in case of partial failures, you can retry to add all the failed participants 
retry = [p for p, e in response if decide_to_retry(e)]
if len(retry) > 0:
    chat_thread_client.add_participants(retry)
```


## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání účastníka můžete také odebrat účastníky z vlákna. Aby bylo možné odebrat, budete muset sledovat ID přidaných účastníků.

Použijte `remove_participant` metodu k odebrání účastníka vlákna z vlákna identifikovaného IDvlákna.
- `user` má `CommunicationUserIdentifier` být odebrán z vlákna.

```python
chat_thread_client.remove_participant(user=new_user)

# # converesely you can also do the following; provided the user_id is known
# from azure.communication.identity import CommunicationUserIdentifier
# 
# user_id = 'some user id'
# chat_thread_client.remove_participant(user=CommunincationUserIdentfier(new_user))
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `python` příkazu.

```console
python start-chat.py
```
