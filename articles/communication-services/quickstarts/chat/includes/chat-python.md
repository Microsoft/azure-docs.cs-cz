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
ms.openlocfilehash: 9e83203e937d794451dfb91fe0403117df72c8c0
ms.sourcegitcommit: 8d1b97c3777684bd98f2cfbc9d440b1299a02e8f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2021
ms.locfileid: "102489689"
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
from azure.communication.chat import ChatClient, CommunicationTokenCredential, CommunicationTokenRefreshOptions

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
refresh_options = CommunicationTokenRefreshOptions(<Access Token>)
chat_client = ChatClient(endpoint, CommunicationTokenCredential(refresh_options))
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `create_chat_thread` metodu k vytvoření vlákna chatu.

- Použijte `topic` k poskytnutí tématu vlákna; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `update_thread` funkce.
- Použijte `thread_participants` k vypsání seznamu, který se má `ChatThreadParticipant` Přidat do konverzačního vlákna, `ChatThreadParticipant` `CommunicationUserIdentifier` typ `user` , který je možné po vytvoření [vytvořit uživatelem](../../access-tokens.md#create-an-identity) .
- Použijte `repeatability_request_id` k přímému zacílení žádosti o zopakování. Klient může požadavek vytvořit víckrát se stejnou opakovatelností – požadavek-ID a vrátí odpovídající odpověď bez serveru, který požadavek vykoná několikrát.

Odpověď `chat_thread_client` se používá k provádění operací v nově vytvořeném konverzačním vlákně, jako je přidání účastníků do konverzačního vlákna, odeslání zprávy, odstranění zprávy atd. Obsahuje vlastnost, `thread_id` která je jedinečné ID vlákna chatu.

#### <a name="without-repeatability-request-id"></a>Bez opakovatelnosti – požadavek-ID
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

chat_thread_client = chat_client.create_chat_thread(topic, participants)
```

#### <a name="with-repeatability-request-id"></a>S možností opakování – ID žádosti
```python
from datetime import datetime
from azure.communication.chat import ChatThreadParticipant

topic="test topic"
participants = [ChatThreadParticipant(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]

repeatability_request_id = 'b66d6031-fdcc-41df-8306-e524c9f226b8' # some unique identifier
chat_thread_client = chat_client.create_chat_thread(topic, participants, repeatability_request_id)
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu
`get_chat_thread_client`Metoda vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: Přidat účastníky, poslat zprávu atd. thread_id je jedinečné ID existujícího konverzačního vlákna.

```python
thread_id = chat_thread_client.thread_id
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="list-all-chat-threads"></a>Zobrazit seznam všech vláken chatu
`list_chat_threads`Metoda vrátí iterátor typu `ChatThreadInfo` . Dá se použít k výpisu všech vláken chatu.

- Slouží `start_time` k zadání nejstaršího bodu v čase, který bude mít za následek vytvoření vlákna konverzace.
- Slouží `results_per_page` k určení maximálního počtu vláken konverzace vrácených na stránku.

```python
from datetime import datetime, timedelta
import pytz

start_time = datetime.utcnow() - timedelta(days=2)
start_time = start_time.replace(tzinfo=pytz.utc)
chat_thread_infos = chat_client.list_chat_threads(results_per_page=5, start_time=start_time)

for chat_thread_info_page in chat_thread_infos.by_page():
    for chat_thread_info in chat_thread_info_page:
        # Iterate over all chat threads
        print("thread id:", chat_thread_info.id)
```

## <a name="delete-a-chat-thread"></a>Odstraní vlákno chatu.
`delete_chat_thread`Slouží k odstranění konverzačního vlákna.

- Slouží `thread_id` k určení thread_id existujícího konverzačního vlákna, které je třeba odstranit.

```python
thread_id = chat_thread_client.thread_id
chat_client.delete_chat_thread(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `send_message` metodu k odeslání zprávy do vlákna chatu, které jste právě vytvořili, identifikovaného thread_id.

- Slouží `content` k zadání obsahu zprávy chatu;
- Slouží `chat_message_type` k určení typu obsahu zprávy. Možné hodnoty jsou "text" a "HTML"; Pokud není zadána výchozí hodnota ' text ', je přiřazena.
- Slouží `sender_display_name` k zadání zobrazovaného jména odesílatele.

Odpověď je "ID" typu `str` , což je jedinečné ID této zprávy.

#### <a name="message-type-not-specified"></a>Není zadaný typ zprávy.
```python
chat_thread_client = chat_client.create_chat_thread(topic, participants)

content='hello world'
sender_display_name='sender name'

send_message_result_id = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name)
```

#### <a name="message-type-specified"></a>Zadaný typ zprávy
```python
from azure.communication.chat import ChatMessageType

content='hello world'
sender_display_name='sender name'

# specify chat message type with pre-built enumerations
send_message_result_id_w_enum = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type=ChatMessageType.TEXT)

# specify chat message type as string
send_message_result_id_w_str = chat_thread_client.send_message(content=content, sender_display_name=sender_display_name, chat_message_type='text')
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

```python
chat_messages = chat_thread_client.list_messages(results_per_page=1, start_time=start_time)
for chat_message_page in chat_messages.by_page():
    for chat_message in chat_message_page:
        print('ChatMessage: ', chat_message)
        print('ChatMessage: ', chat_message.content.message)
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
updated_topic = chat_client.get_chat_thread(chat_thread_client.thread_id).topic
print('Updated topic: ', updated_topic)
```

## <a name="update-a-message"></a>Aktualizace zprávy
Obsah existující zprávy můžete aktualizovat pomocí `update_message` metody, kterou identifikuje message_id

- Použijte `message_id` k určení message_id
- Slouží `content` k nastavení nového obsahu zprávy.

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

```python
read_receipts = chat_thread_client.list_read_receipts(results_per_page=2, skip=0)

for read_receipt_page in read_receipts.by_page():
    for read_receipt in read_receipt_page:
        print('ChatMessageReadReceipt: ', read_receipt)
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

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())

chat_thread_client.add_participant(new_chat_thread_participant)
```

Do konverzačního vlákna lze přidat také více uživatelů pomocí `add_participants` metody, pokud je k dispozici nový přístupový token a identifikace je k dispozici pro všechny uživatele.

```python
from azure.communication.chat import ChatThreadParticipant
from datetime import datetime

new_chat_thread_participant = ChatThreadParticipant(
        user=self.new_user,
        display_name='name',
        share_history_time=datetime.utcnow())
thread_participants = [new_chat_thread_participant] # instead of passing a single participant, you can pass a list of participants
chat_thread_client.add_participants(thread_participants)
```


## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání účastníka můžete také odebrat účastníky z vlákna. Aby bylo možné odebrat, budete muset sledovat ID přidaných účastníků.

Použijte `remove_participant` metodu k odebrání účastníka vlákna z vlákna identifikovaného IDvlákna.
- `user` má `CommunicationUserIdentifier` být odebrán z vlákna.

```python
chat_thread_client.remove_participant(new_user)
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `python` příkazu.

```console
python start-chat.py
```
