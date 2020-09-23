---
title: Vyžádané čtení Docker pro kontejner stavu
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro Analýza textu pro kontejner stavu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90905999"
---
Vyplňte a odešlete [formulář žádosti o Cognitive Services kontejnery](https://aka.ms/cognitivegate) , který bude požadovat přístup ke kontejneru.
Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete kontejner používat. Po odeslání formuláře ho tým Azure Cognitive Services zkontroluje a ověří, jestli splňujete kritéria pro přístup k privátnímu registru kontejneru.

> [!IMPORTANT]
> * Ve formuláři je nutné použít e-mailovou adresu přidruženou k ID předplatného Azure.
> * Prostředek Azure, který používáte ke spuštění kontejneru, se musí vytvořit s schváleným ID předplatného Azure. 
> * Pokud chcete aktualizovat stav vaší aplikace od Microsoftu, Projděte si e-mail (Doručená pošta a složky nevyžádané pošty).

Pomocí příkazu Docker Login s přihlašovacími údaji, které jste zadali v e-mailu pro registraci, se připojte k našemu soukromému registru kontejnerů pro kontejnery Cognitive Services.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte tuto image kontejneru z našeho soukromého registru kontejnerů.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
