---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: lakshmans
manager: ankita
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: lakshmans
ms.openlocfilehash: e8424f6b5b7617b00de6dedbece3325f3c5513c8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103622334"
---
Začněte s komunikačními službami Azure pomocí klientské knihovny služby Communications Pythonu SMS k posílání zpráv SMS.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](#todo-sdk-repo) | [Package (PiPy)](#todo-nuget) | [Samples](#todo-samples)--> 

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- [Python](https://www.python.org/downloads/) 2,7, 3,5 nebo novější.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo s povoleným SMS. [Získejte telefonní číslo](../get-phone-number.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V okně terminálu nebo příkazu spusťte `python --version` příkaz a ověřte, zda je Python nainstalován.
- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-python-application"></a>Vytvoření nové aplikace v Pythonu

Otevřete okno terminálu nebo příkaz, pro svou aplikaci vytvořte nový adresář a přejděte do něj.

```console
mkdir sms-quickstart && cd sms-quickstart
```

Pomocí textového editoru vytvořte soubor s názvem **Send-SMS.py** v kořenovém adresáři projektu a přidejte strukturu pro program, včetně základního zpracování výjimek. Do tohoto souboru přidáte veškerý zdrojový kód pro tento rychlý Start v následujících oddílech.

```python
import os
from azure.communication.sms import SmsClient

try:
    # Quickstart code goes here
except Exception as ex:
    print('Exception:')
    print(ex)
```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte knihovnu klienta služby Azure Communications Services pro balíček python pomocí `pip install` příkazu.

```console
pip install azure-communication-sms --pre
```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny SMS služby Azure Communications Services pro Python.

| Název                                  | Description                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| SmsClient | Tato třída je potřebná pro všechny funkce SMS. Vytvoří se jeho instance s informacemi o předplatném a použije se k posílání zpráv SMS.                                                                                                                 |
| SmsSendResult               | Tato třída obsahuje výsledek ze služby SMS.                                          |

## <a name="authenticate-the-client"></a>Ověření klienta

Vytvořte instanci **SmsClient** s připojovacím řetězcem. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).

```python
# This code demonstrates how to fetch your connection string
# from an environment variable.
connection_string = os.getenv('COMMUNICATION_SERVICES_CONNECTION_STRING')

# Create the SmsClient object which will be used to send SMS messages
sms_client = SmsClient.from_connection_string(connection_string)
```

## <a name="send-a-11-sms-message"></a>Poslat zprávu SMS 1:1

Chcete-li odeslat zprávu SMS jednomu příjemci, zavolejte ```send``` metodu z **SmsClient** s jedním příjemcem telefonního čísla. K určení, zda má být povolena zpráva o doručení a nastavena vlastní značky, můžete také předat volitelné parametry. Přidejte tento kód na konec `try` bloku v **Send-SMS.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to="<to-phone-number>,
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Měli byste nahradit `<from-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým ke komunikační službě a `<to-phone-number>` telefonním číslem, na které chcete poslat zprávu. 

## <a name="send-a-1n-sms-message"></a>Odeslat zprávu o 1: N SMS

Chcete-li odeslat zprávu SMS seznamu příjemců, zavolejte ```send``` metodu ze **SmsClient** se seznamem telefonních čísel příjemců. K určení, zda má být povolena zpráva o doručení a nastavena vlastní značky, můžete také předat volitelné parametry. Přidejte tento kód na konec `try` bloku v **Send-SMS.py**:

```python

# calling send() with sms values
sms_responses = sms_client.send(
    from_="<from-phone-number>",
    to=["<to-phone-number-1>", "<to-phone-number-2>"],
    message="Hello World via SMS",
    enable_delivery_report=True, # optional property
    tag="custom-tag") # optional property

```

Měli byste nahradit `<from-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým ke komunikační službě a `<to-phone-number-1>` a `<to-phone-number-2>` telefonními čísly, na které chcete poslat zprávu. 

## <a name="optional-parameters"></a>Volitelné parametry

`enable_delivery_report`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .

`tag`Parametr je volitelný parametr, který lze použít ke konfiguraci vlastního označování.

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `python` příkazu.

```console
python send-sms.py
```
