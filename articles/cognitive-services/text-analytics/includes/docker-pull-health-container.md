---
title: Vyžádané čtení Docker pro kontejner stavu
titleSuffix: Azure Cognitive Services
description: Příkaz Docker Pull pro Analýza textu pro kontejner stavu
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779307"
---
Vyplňte a odešlete [formulář žádosti o Cognitive Services kontejnery](https://aka.ms/csgate) , který bude požadovat přístup ke kontejneru.
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
