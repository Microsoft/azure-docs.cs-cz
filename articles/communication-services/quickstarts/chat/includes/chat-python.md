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
ms.openlocfilehash: 2b7d00335253772683b867acf0765b77fc493e79
ms.sourcegitcommit: 4bee52a3601b226cfc4e6eac71c1cb3b4b0eafe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94523857"
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

```console
pip install azure-communication-administration
```

```python
from azure.communication.chat import ChatClient, CommunicationUserCredential

endpoint = "https://<RESOURCE_NAME>.communication.azure.com"
chat_client = ChatClient(endpoint, CommunicationUserCredential(<Access Token>))
```

## <a name="start-a-chat-thread"></a>Spustit chatovací vlákno

Použijte `create_chat_thread` metodu k vytvoření vlákna chatu.

- Použijte `topic` k poskytnutí tématu vlákna; Téma lze aktualizovat poté, co je vlákno konverzace vytvořeno pomocí `update_thread` funkce.
- Použijte `members` k vypsání seznamu, který se má `ChatThreadMember` Přidat do konverzačního vlákna, `ChatThreadMember` `CommunicationUser` typ `user` , který je možné po vytvoření [vytvořit uživatelem](../../access-tokens.md#create-an-identity) .

Odpověď `chat_thread_client` se používá k provádění operací v nově vytvořeném konverzačním vlákně, jako je přidání členů do konverzačního vlákna, odeslání zprávy, odstranění zprávy atd. Obsahuje vlastnost, `thread_id` která je jedinečné ID vlákna chatu.

```python
from datetime import datetime
from azure.communication.chat import ChatThreadMember

topic="test topic"
thread_members=[ChatThreadMember(
    user=user,
    display_name='name',
    share_history_time=datetime.utcnow()
)]
chat_thread_client = chat_client.create_chat_thread(topic, thread_members)
```

## <a name="get-a-chat-thread-client"></a>Získat klienta vlákna chatu
Metoda get_chat_thread_client vrací klienta vlákna pro vlákno, které již existuje. Dá se použít k provádění operací na vytvořeném vlákně: přidat členy, poslat zprávu atd. thread_id je jedinečné ID existujícího konverzačního vlákna.

```python
thread_id = 'id'
chat_thread_client = chat_client.get_chat_thread_client(thread_id)
```

## <a name="send-a-message-to-a-chat-thread"></a>Odeslání zprávy do konverzačního vlákna

Použijte `send_message` metodu k odeslání zprávy do vlákna chatu, které jste právě vytvořili a identifikovali pomocí IDvlákna.

- Slouží `content` k zadání obsahu zprávy chatu;
- Slouží `priority` k zadání úrovně priority zprávy, například Normal nebo high. Tato vlastnost se dá použít k tomu, aby uživatel příjemce ve vaší aplikaci upozornil na zprávu nebo spustil vlastní obchodní logiku.
- Slouží `senderDisplayName` k zadání zobrazovaného jména odesílatele.

Odpověď `SendChatMessageResult` obsahuje "ID", což je jedinečné ID této zprávy.

```python
from azure.communication.chat import ChatMessagePriority

content='hello world'
priority=ChatMessagePriority.NORMAL
sender_display_name='sender name'

send_message_result = chat_thread_client.send_message(content, priority=priority, sender_display_name=sender_display_name)
```

## <a name="receive-chat-messages-from-a-chat-thread"></a>Příjem zpráv chatu z konverzačního vlákna

Můžete načíst zprávy chatu pomocí cyklického dotazování `list_messages` metody v určených intervalech.

```python
chat_messages = chat_thread_client.list_messages()
```
`list_messages` Vrátí nejnovější verzi zprávy, včetně všech úprav nebo odstranění, ke kterým došlo u zprávy pomocí `update_message` a `delete_message` . Pro odstraněné zprávy `ChatMessage.deleted_on` vrátí hodnotu DateTime, která indikuje, kdy se tato zpráva odstranila. U upravených zpráv `ChatMessage.edited_on` vrátí hodnotu DateTime, která indikuje, kdy byla zpráva upravena. Původní čas vytvoření zprávy lze použít pomocí `ChatMessage.created_on` , který lze použít k řazení zpráv.

`list_messages` vrátí různé typy zpráv, které mohou být identifikovány pomocí `ChatMessage.type` . Tyto typy:

- `Text`: Běžná zpráva chatu odeslaná členem vlákna.

- `ThreadActivity/TopicUpdate`: Systémová zpráva, která indikuje, že téma bylo aktualizováno.

- `ThreadActivity/AddMember`: Systémová zpráva, která indikuje, že jeden nebo více členů bylo přidáno do konverzačního vlákna.

- `ThreadActivity/DeleteMember`: Systémová zpráva, která indikuje, že člen byl odebrán z konverzačního vlákna.

Další podrobnosti najdete v tématu [typy zpráv](../../../concepts/chat/concepts.md#message-types).

## <a name="add-a-user-as-member-to-the-chat-thread"></a>Přidat uživatele jako člena do vlákna chatu

Po vytvoření vlákna chatu můžete z něj přidat uživatele nebo je z něj odebrat. Přidáním uživatelů udělíte jim přístup, aby bylo možné odesílat zprávy do konverzačního vlákna a přidávat nebo odebírat další členy. Před voláním `add_members` metody se ujistěte, že jste pro tohoto uživatele získali nový přístupový token a identitu. Uživatel bude potřebovat přístupový token, aby mohl inicializovat svého chatového klienta.

Použijte `add_members` metodu pro přidání členů vlákna do vlákna identifikovaného pomocí IDvlákna.

- Slouží `members` k vypsání členů, kteří mají být přidáni do konverzačního vlákna;
- `user`je povinné, `CommunicationUser` když jste vytvořili v části `CommunicationIdentityClient` [Vytvoření uživatele](../../access-tokens.md#create-an-identity) .
- `display_name`volitelné, je zobrazované jméno člena vlákna.
- `share_history_time`volitelné, je čas, od kterého je historie chatu sdílena s členem. Chcete-li sdílet historii od vytvoření vlákna chatu, nastavte tuto vlastnost na jakékoli datum, které je rovno nebo menší než čas vytvoření vlákna. Chcete-li sdílet žádnou historii předchozí po přidání člena, nastavte jej na aktuální datum. Chcete-li sdílet částečnou historii, nastavte ji na datum zprostředkovatele.

```python
new_user = identity_client.create_user()

from azure.communication.chat import ChatThreadMember
from datetime import datetime
member = ChatThreadMember(
    user=new_user,
    display_name='name',
    share_history_time=datetime.utcnow())
thread_members = [member]
chat_thread_client.add_members(thread_members)
```

## <a name="remove-user-from-a-chat-thread"></a>Odebrání uživatele z konverzačního vlákna

Podobně jako při přidávání člena můžete také odebrat členy z vlákna. Aby bylo možné odebrat, budete muset sledovat ID členů, které jste přidali.

Použijte `remove_member` metodu pro odebrání člena vlákna z vlákna identifikovaného pomocí IDvlákna.
- `user` je CommunicationUser, který má být odebrán z vlákna.

```python
chat_thread_client.remove_member(user)
```

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `python` příkazu.

```console
python start-chat.py
```
