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
ms.openlocfilehash: a0b2c9548f9c1289ae0abd61a72d7146a3bbca29
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965139"
---
Vyplňte a odešlete [formulář žádosti o Cognitive Services](https://aka.ms/csgate) , abyste požádali o přístup k analýza textu ve verzi Public Preview.  Tato aplikace se vztahuje jak na kontejner, tak na hostované rozhraní Web API Public Preview.
Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete kontejner používat. Po odeslání formuláře ho tým Azure Cognitive Services zkontroluje a ověří, jestli splňujete kritéria pro přístup k privátnímu registru kontejneru.

> [!IMPORTANT]
> * Ve formuláři je nutné použít e-mailovou adresu přidruženou k ID předplatného Azure.
> * Prostředek Azure, který používáte ke spuštění kontejneru, se musí vytvořit s schváleným ID předplatného Azure. 
> * Pokud chcete aktualizovat stav vaší aplikace od Microsoftu, Projděte si e-mail (Doručená pošta a složky nevyžádané pošty).

Po schválení se vám pošle e-mail s přihlašovacími údaji pro přístup k soukromému registru kontejneru.  Pomocí příkazu Docker Login s přihlašovacími údaji, které jste zadali v e-mailu pro registraci, se připojte k našemu soukromému registru kontejnerů pro kontejnery Cognitive Services.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu si stáhněte tuto image kontejneru z našeho soukromého registru kontejnerů.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
