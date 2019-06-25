---
title: Řízení souběžnosti | Azure Marketplace
description: Strategie řízení souběžnosti pro portál partnerů cloudu publikování rozhraní API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 8cdcfd84a2f3bd4f920b97392255237db173cbf9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64935599"
---
# <a name="concurrency-control"></a>Řízení souběžnosti

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
