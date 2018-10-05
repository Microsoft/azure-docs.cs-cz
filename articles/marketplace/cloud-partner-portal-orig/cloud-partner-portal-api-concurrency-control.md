---
title: Řízení souběžnosti | Dokumentace Microsoftu
description: Strategie řízení souběžnosti pro portál partnerů cloudu publikování rozhraní API.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: ecf0bb6ac7fc77e804c9fc8d62aba52810de5640
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809396"
---
<a name="concurrency-control"></a>Řízení souběžnosti
===================

Všechna volání na portál Cloud Partner publikování rozhraní API musíte explicitně zadat strategii, kterou řízení souběžnosti používat. Nepodařilo se zadat **If-Match** záhlaví způsobí chybová odpověď HTTP 400. Nabízíme dvě strategie pro kontrolu souběžnosti.

-   **Optimistická** – klient provedete aktualizaci ověří, pokud se data změnila od posledního čtení data.
-   **Naposledy jeden wins** -klienta přímo aktualizuje data, bez ohledu na to, zda jiné aplikace jej změnila od posledního čtení čas.

<a name="optimistic-concurrency-workflow"></a>Pracovní postup optimistického řízení souběžnosti
-------------------------------

Doporučujeme používat strategii optimistické souběžnosti s následující pracovní postup, chcete-li zaručit, že žádné neočekávané úpravy se provedly vaše prostředky.

1.  Načtení entity pomocí rozhraní API. Odpověď obsahuje hodnotou ETag, která identifikuje aktuálně uložené verze entity (při odpovědi).
2.  Během aktualizace, zahrnují tato stejná hodnota ETag v povinných **If-Match** hlavičky žádosti.
3.  Rozhraní API porovná hodnotu značky ETag přijatý v požadavku s aktuální hodnotou ETag entity v jednu atomickou transakci.
    *   Pokud hodnoty ETag se liší, rozhraní API vrátí `412 Precondition Failed` odpověď HTTP. Tato chyba označuje, že buď jiný proces aktualizoval entitu od klienta posledního obnovení nebo je nesprávná hodnota ETag zadaná v žádosti.
    *  Pokud jsou značky ETag hodnoty stejné, nebo **If-Match** záhlaví obsahuje zástupný znak hvězdička (`*`), provede požadovanou operaci rozhraní API. Operace rozhraní API také aktualizuje uložená hodnota ETag entity.


> [!NOTE]
> Určení zástupný znak (*) v **If-Match** hlavičky výsledků v rozhraní API pro použití služby wins poslední jeden strategie souběžnosti. V tomto případě nedojde k porovnání ETag a aktualizaci prostředku bez žádné kontroly. 
