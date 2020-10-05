---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: matthewrobertson
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 08/20/2020
ms.topic: include
ms.custom: include file
ms.author: marobert
ms.openlocfilehash: 4be8821a949527fefcc9005b1de7f4f7c438c568
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "90946963"
---
## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Python](https://www.python.org/downloads/) 2,7, 3,5 nebo novější.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../create-communication-resource.md).

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

1. Otevřete okno terminálu nebo příkazového řádku pro svoji aplikaci vytvořte nový adresář a přejděte do něj.

   ```console
   mkdir user-tokens-quickstart && cd user-tokens-quickstart
   ```

1. Pomocí textového editoru vytvořte soubor s názvem **Issue-Tokens.py** v kořenovém adresáři projektu a přidejte strukturu pro program, včetně základního zpracování výjimek. Do tohoto souboru přidáte veškerý zdrojový kód pro tento rychlý Start v následujících oddílech.

   ```python
   import os
   from azure.communication.administration import CommunicationIdentityClient

   try:
      print('Azure Communication Services - User Access Tokens Quickstart')
      # Quickstart code goes here
   except Exception as ex:
      print('Exception:')
      print(ex)
   ```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte klientskou knihovnu pro správu služby Azure Communication Services pro balíček python pomocí `pip install` příkazu.

```console
pip install azure-communication-administration
```

[!INCLUDE [User Access Tokens Object Model](user-access-tokens-object-model.md)]

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci `CommunicationIdentityClient` s připojovacím řetězcem. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../create-communication-resource.md#store-your-connection-string).

Přidejte tento kód do `try` bloku:

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.environ['COMMUNICATION_SERVICES_CONNECTION_STRING']

# Instantiate the identity client
client = CommunicationIdentityClient.from_connection_string(connection_string)
```

## <a name="create-a-user"></a>Vytvoření uživatele

Komunikační služby Azure udržují zjednodušený adresář identit. Použijte `create_user` metodu k vytvoření nové položky v adresáři s jedinečným objektem `Id` . Měli byste udržovat mapování mezi uživateli vaší aplikace a komunikačními službami, které vygenerovaly identity (například jejich uložením do databáze aplikačního serveru).

```python
user = client.create_user()
print("\nCreated a user with ID: " + user.identifier + ":")
```

## <a name="issue-user-access-tokens"></a>Vystavení přístupových tokenů uživatele

Použijte `issue_token` metodu pro vydání přístupového tokenu pro uživatele komunikačních služeb. Pokud nezadáte volitelný parametr, vytvoří se `user` Nový uživatel, který se vrátí s tokenem.

```python
# Issue an access token with the "voip" scope for a new user
token_result = client.issue_token(user, ["voip"])
expires_on = token_result.expires_on.strftime('%d/%m/%y %I:%M %S %p')
print("\nIssued a token with 'voip' scope that expires at " + expires_on + ":")
print(token_result.token)
```

Tokeny přístupu uživatele jsou krátkodobé přihlašovací údaje, které je potřeba znovu vystavit, aby nedocházelo k přerušení služeb uživatelů. `expires_on`Vlastnost Response označuje dobu života tokenu.

## <a name="revoke-user-access-tokens"></a>Odvolat tokeny přístupu uživatele

V některých případech může být nutné explicitně odvolat tokeny přístupu uživatele, například když uživatel změní heslo, které používá k ověření vaší služby. Tato funkce je k dispozici prostřednictvím klientské knihovny pro správu služby Azure Communication Services.

```python  
client.revoke_tokens(user)
print("\nSuccessfully revoked all tokens for user with ID: " + user.identifier)
```

## <a name="delete-a-user"></a>Odstranění uživatele

Odstranění identity odvolá všechny aktivní tokeny a zabrání vystavení následných tokenů pro identity. Zároveň se tím odebere veškerý trvalý obsah přidružený k uživateli.

```python
client.delete_user(user)
print("\nDeleted the user with ID: " + user.identifier)
```

## <a name="run-the-code"></a>Spuštění kódu

V příkazovém řádku konzoly přejděte do adresáře obsahujícího soubor *Issue-token.py* a `python` Spusťte aplikaci spuštěním následujícího příkazu.

```console
python ./issue-token.py
```
