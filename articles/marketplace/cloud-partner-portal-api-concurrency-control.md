---
title: Řízení souběžnosti – Azure Marketplace
description: Strategie řízení souběžnosti pro portál partnerů cloudu publikování rozhraní API.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e468898daffe8fc42250575d3efa42f99279c410
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "88031644"
---
# <a name="concurrency-control"></a>Řízení souběžnosti

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná s a budou pokračovat v práci v partnerském centru. Přechod přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](./cloud-partner-portal-api-overview.md) , abyste zajistili, že kód pokračuje v práci po přechodu do partnerského centra. Rozhraní API CPP by se mělo používat jenom pro existující produkty, které už jsou integrované před přechodem do partnerského centra; nové produkty by měly používat rozhraní API pro odesílání v partnerském centru.

Každé volání portál partnerů cloudu rozhraní API pro publikování musí explicitně určit, kterou strategii řízení souběžnosti použít. **Pokud záhlaví If-Match** neposkytnete, výsledkem bude chybná odpověď HTTP 400. Nabízíme dvě strategie řízení souběžnosti.

-   **Optimistická** – klient, který provádí aktualizaci, ověří, jestli se data od posledního načtení dat změnila.
-   **Poslední služba WINS** – klient přímo aktualizuje data, bez ohledu na to, jestli ji od posledního čtení změnila jiná aplikace.

<a name="optimistic-concurrency-workflow"></a>Optimistická pracovní postup souběžnosti
-------------------------------

Doporučujeme použít strategii optimistického souběžnosti s následujícím pracovním postupem, abyste zajistili, že se neprovádí žádné neočekávané úpravy vašich prostředků.

1.  Načtěte entitu pomocí rozhraní API. Odpověď obsahuje hodnotu ETag, která identifikuje aktuálně uloženou verzi entity (v době odezvy).
2.  V okamžiku aktualizace zahrňte stejnou hodnotu ETag do hlavičky žádosti o povinnou hodnotu **If-Match** .
3.  Rozhraní API porovnává hodnotu ETag přijatou v požadavku s aktuální hodnotou ETag entity v atomické transakci.
    *   Pokud se hodnoty ETag liší, rozhraní API vrátí `412 Precondition Failed` odpověď HTTP. Tato chyba znamená, že se entita aktualizovala jiným procesem, protože ji klient naposledy načetl nebo že hodnota ETag zadaná v požadavku není správná.
    *  Pokud jsou hodnoty ETag stejné nebo hlavička **If-Match** obsahuje zástupný znak hvězdičky ( `*` ), rozhraní API provede požadovanou operaci. Operace rozhraní API také aktualizuje uloženou hodnotu ETag entity.


> [!NOTE]
> Zadání zástupných znaků (*) v hlavičce **If-Match** vede k rozhraní API pomocí strategie souběžnosti poslední služby WINS. V tomto případě se neobjeví porovnávání značek ETag a prostředek se aktualizuje bez jakýchkoli kontrol. 
