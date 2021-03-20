---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
author: sdgilley
ms.service: machine-learning
ms.author: sgilley
manager: cgronlund
ms.custom: include file
ms.topic: include
ms.date: 08/24/2020
ms.openlocfilehash: aa8aea66c5481454f0d7d4d118934f5fbf34a911
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89147403"
---
**Výchozí spravovaná identita** je spravovaná identita přiřazená systémem nebo první spravovaná identita přiřazená uživatelem.

Během spuštění jsou k dispozici dvě aplikace identity:

1. Systém používá identitu k nastavení připojení úložiště uživatele, registru kontejnerů a úložišť dat.

    * V takovém případě systém použije výchozí identitu spravovanou.

1. Uživatel použije identitu pro přístup k prostředkům v rámci kódu pro odeslaný běh.

    * V takovém případě zadejte *client_id* odpovídající spravované identitě, kterou chcete použít k načtení přihlašovacích údajů.
    * Případně můžete získat ID klienta identity přiřazené uživatelem pomocí proměnné prostředí *DEFAULT_IDENTITY_CLIENT_ID* .

    Pokud například chcete načíst token pro úložiště dat s výchozí identitou spravovanou:

    ```python
    client_id = os.environ.get('DEFAULT_IDENTITY_CLIENT_ID')
    credential = ManagedIdentityCredential(client_id=client_id)
    token = credential.get_token('https://storage.azure.com/')
    ```