---
title: Řízení souběžnosti | Azure Marketplace
description: Strategie řízení souběžnosti pro portál partnerů cloudu publikování rozhraní API.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81256395"
---
# <a name="concurrency-control"></a>Řízení souběžnosti

> [!NOTE]
> Rozhraní API pro portál partnerů cloudu jsou integrovaná do partnerského centra a budou fungovat i po migraci nabídek do partnerského centra. Integrace přináší malé změny. Projděte si změny uvedené v části [portál partnerů cloudu rozhraní API](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) , abyste zajistili, že váš kód bude i nadále fungovat po migraci do partnerského centra.

Každé volání portál partnerů cloudu rozhraní API pro publikování musí explicitně určit, kterou strategii řízení souběžnosti použít. **Pokud záhlaví If-Match** neposkytnete, výsledkem bude chybná odpověď HTTP 400. Nabízíme dvě strategie řízení souběžnosti.

-   **Optimistická** – klient, který provádí aktualizaci, ověří, jestli se data od posledního načtení dat změnila.
-   **Poslední služba WINS** – klient přímo aktualizuje data, bez ohledu na to, jestli ji od posledního čtení změnila jiná aplikace.

<a name="optimistic-concurrency-workflow"></a>Optimistická pracovní postup souběžnosti
-------------------------------

Doporučujeme použít strategii optimistického souběžnosti s následujícím pracovním postupem, abyste zajistili, že se neprovádí žádné neočekávané úpravy vašich prostředků.

1.  Načtěte entitu pomocí rozhraní API. Odpověď obsahuje hodnotu ETag, která identifikuje aktuálně uloženou verzi entity (v době odezvy).
2.  V okamžiku aktualizace zahrňte stejnou hodnotu ETag do hlavičky žádosti o povinnou hodnotu **If-Match** .
3.  Rozhraní API porovnává hodnotu ETag přijatou v požadavku s aktuální hodnotou ETag entity v atomické transakci.
    *   Pokud se hodnoty ETag liší, rozhraní API vrátí odpověď `412 Precondition Failed` http. Tato chyba znamená, že se entita aktualizovala jiným procesem, protože ji klient naposledy načetl nebo že hodnota ETag zadaná v požadavku není správná.
    *  Pokud jsou hodnoty ETag stejné nebo hlavička **If-Match** obsahuje zástupný znak hvězdičky (`*`), rozhraní API provede požadovanou operaci. Operace rozhraní API také aktualizuje uloženou hodnotu ETag entity.


> [!NOTE]
> Zadání zástupných znaků (*) v hlavičce **If-Match** vede k rozhraní API pomocí strategie souběžnosti poslední služby WINS. V tomto případě se neobjeví porovnávání značek ETag a prostředek se aktualizuje bez jakýchkoli kontrol. 
