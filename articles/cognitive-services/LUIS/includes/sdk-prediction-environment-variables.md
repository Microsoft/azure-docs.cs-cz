---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.date: 01/23/2020
ms.openlocfilehash: a7f4e0b47b3aaf9924f8c82eb6a9fa37decc0b2a
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76772435"
---
### <a name="create-an-environment-variable"></a>Vytvoření proměnné prostředí

Pomocí klíče runtime a koncového bodu runtime vytvořte proměnné prostředí pro ověřování a přístup:

* `LUIS_RUNTIME_KEY`- Klíč zdroje za běhu pro ověřování vašich požadavků.
* `LUIS_RUNTIME_ENDPOINT`- Koncový bod runtime přidružený k vašemu klíči.
* `LUIS_APP_ID`- Veřejné ID aplikace LUIS `df67dcdb-c37d-46af-88e1-8b97951ca1c2`IoT je .
* `LUIS_APP_SLOT_NAME` - `production`Nebo`staging`

Pokud máte v úmyslu použít tento rychlý start pro přístup k vlastní aplikaci, musíte provést další kroky:
* Vytvoření aplikace a získání ID aplikace
* Přiřazení klíče runtime k aplikaci na portálu LUIS
* Otestujte url s prohlížečem, že můžete přistupovat k aplikaci

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

Po přidání proměnných prostředí `source ~/.bashrc` spusťte z okna konzoly, aby změny byly účinné.

#### <a name="macos"></a>[Macos](#tab/unix)

Upravte `.bash_profile`aplikaci a přidejte proměnnou prostředí:

```bash
export LUIS_RUNTIME_KEY= <replace-with-your-resource-key>
export LUIS_RUNTIME_ENDPOINT= <replace-with-your-resource-endpoint>
export LUIS_APP_ID= <replace-with-your-app-id>
export LUIS_APP_SLOT_NAME= <replace-with-production-or-staging>
```

Po přidání proměnných prostředí `source .bash_profile` spusťte z okna konzoly, aby změny byly účinné.

***
