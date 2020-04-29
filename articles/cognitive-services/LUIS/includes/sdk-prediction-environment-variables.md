---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 34a6fa5fc66b1cfdfbf8178ef5cdb151c97c721c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76772435"
---
### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče za běhu a koncového bodu modulu runtime vytvořte proměnné prostředí pro ověřování a přístup:

* `LUIS_RUNTIME_KEY`– Klíč prostředku modulu runtime pro ověřování vašich požadavků.
* `LUIS_RUNTIME_ENDPOINT`– Koncový bod modulu runtime přidružený k vašemu klíči.
* `LUIS_APP_ID`– Veřejné ID aplikace IoT LUIS `df67dcdb-c37d-46af-88e1-8b97951ca1c2`.
* `LUIS_APP_SLOT_NAME` - `production`ani`staging`

Pokud máte v úmyslu používat tento rychlý Start k přístupu k vlastní aplikaci, musíte provést další kroky:
* Vytvoření aplikace a získání ID aplikace
* Přiřazení klíče za běhu k aplikaci na portálu LUIS
* Otestujte adresu URL v prohlížeči, abyste mohli získat přístup k aplikaci.

Použijte pokyny pro váš operační systém.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx LUIS_RUNTIME_KEY <replace-with-your-resource-key>
setx LUIS_RUNTIME_ENDPOINT <replace-with-your-resource-endpoint>
setx LUIS_APP_ID <replace-with-your-app-id>
setx LUIS_APP_SLOT_NAME <replace-with-production-or-staging>
```

Po přidání proměnných prostředí restartujte okno konzoly.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po přidání proměnných prostředí spusťte `source ~/.bashrc` z okna konzoly, aby se změny projevily.

#### <a name="macos"></a>[macOS](#tab/unix)

`.bash_profile`Upravte a přidejte proměnnou prostředí:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po přidání proměnných prostředí spusťte `source .bash_profile` z okna konzoly, aby se změny projevily.

***
