---
title: Řízení souběžnosti | Azure Marketplace
description: Strategie řízení souběžnosti pro portál partnerů cloudu publikování rozhraní API.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: 6e2f8922d42e40d14338f06be983d3913b20859d
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819752"
---
# <a name="concurrency-control"></a>Řízení souběžnosti

Každé volání portál partnerů cloudu rozhraní API pro publikování musí explicitně určit, kterou strategii řízení souběžnosti použít. **Pokud záhlaví If-Match** neposkytnete, výsledkem bude chybná odpověď HTTP 400. Nabízíme dvě strategie řízení souběžnosti.

-   **Optimistická** – klient, který provádí aktualizaci, ověří, jestli se data od posledního načtení dat změnila.
-   **Poslední služba WINS** – klient přímo aktualizuje data, bez ohledu na to, jestli ji od posledního čtení změnila jiná aplikace.

<a name="optimistic-concurrency-workflow"></a>Optimistická pracovní postup souběžnosti
-------------------------------

Doporučujeme použít strategii optimistického souběžnosti s následujícím pracovním postupem, abyste zajistili, že se neprovádí žádné neočekávané úpravy vašich prostředků.

1.  Načtěte entitu pomocí rozhraní API. Odpověď obsahuje hodnotu ETag, která identifikuje aktuálně uloženou verzi entity (v době odezvy).
2.  V okamžiku aktualizace zahrňte stejnou hodnotu ETag do hlavičky žádosti o povinnou hodnotu **If-Match** .
3.  Rozhraní API porovnává hodnotu ETag přijatou v požadavku s aktuální hodnotou ETag entity v atomické transakci.
    *   Pokud se hodnoty ETag liší, vrátí rozhraní API odpověď `412 Precondition Failed` HTTP. Tato chyba znamená, že se entita aktualizovala jiným procesem, protože ji klient naposledy načetl nebo že hodnota ETag zadaná v požadavku není správná.
    *  Pokud jsou hodnoty ETag stejné nebo hlavička **If-Match** obsahuje zástupný znak hvězdičky (`*`), rozhraní API provede požadovanou operaci. Operace rozhraní API také aktualizuje uloženou hodnotu ETag entity.


> [!NOTE]
> Zadání zástupných znaků (*) v hlavičce **If-Match** vede k rozhraní API pomocí strategie souběžnosti poslední služby WINS. V tomto případě se neobjeví porovnávání značek ETag a prostředek se aktualizuje bez jakýchkoli kontrol. 
