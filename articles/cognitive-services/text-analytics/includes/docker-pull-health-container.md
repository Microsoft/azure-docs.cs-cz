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
ms.date: 03/25/2021
ms.author: aahi
ms.openlocfilehash: 8595af7f46b63f9991d6a02279ccad76afb38311
ms.sourcegitcommit: 99fc6ced979d780f773d73ec01bf651d18e89b93
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/31/2021
ms.locfileid: "106089698"
---
Vyplňte a odešlete [formulář žádosti o Cognitive Services](https://aka.ms/csgate) , abyste požádali o přístup k analýza textu ve verzi Public Preview.  Tato aplikace se vztahuje jak na kontejner, tak na hostované rozhraní Web API Public Preview.
Formulář požaduje informace o vás, vaší společnosti a scénáři uživatele, pro které budete kontejner používat. Po odeslání formuláře ho tým Azure Cognitive Services zkontroluje a ověří, jestli splňujete kritéria pro přístup k privátnímu registru kontejneru.

> [!IMPORTANT]
> * Ve formuláři je nutné použít e-mailovou adresu přidruženou k ID předplatného Azure.
> * Prostředek Analýza textu (koncový bod fakturace a apikey), který použijete ke spuštění kontejneru, se musí vytvořit s schváleným ID předplatného Azure. 
> * Pokud chcete aktualizovat stav vaší aplikace od Microsoftu, Projděte si e-mail (Doručená pošta a složky nevyžádané pošty).
> * Tato adresa URL kontejneru se změnila, viz následující příklady. Kontejner nebude k dispozici ke stažení od `containerpreview.azurecr.io` dubna 26 2021.


Po schválení pomocí [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) příkazu Stáhněte tuto image kontejneru z registru veřejného kontejneru Microsoftu.

```
docker pull mcr.microsoft.com/azure-cognitive-services/textanalytics/healthcare:latest
```
