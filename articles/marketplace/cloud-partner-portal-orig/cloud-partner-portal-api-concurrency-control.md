---
title: Kontrola souběžnosti | Azure Marketplace
description: Strategie řízení souběžnosti pro portál cloudových partnerů publikování api.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: dsindona
ms.openlocfilehash: 302ba8d550f5e91efe12c620d766550958d3bf68
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/13/2020
ms.locfileid: "81256395"
---
# <a name="concurrency-control"></a>Řízení souběžnosti

> [!NOTE]
> Api portálu pro partnery cloudu jsou integrovaná s Partnerským centrem a budou fungovat i po migraci nabídek do Centra partnerů. Integrace přináší malé změny. Zkontrolujte změny uvedené v [referenčním rozhraní API portálu cloudových partnerů a](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) ujistěte se, že váš kód bude fungovat i po migraci do Centra partnerů.

Každé volání na portál cloudových partnerů publikování api musí explicitně určit, které strategie řízení souběžnosti použít. Neposkytnutí hlavičky **If-Match** bude mít za následek odpověď na chybu HTTP 400. Nabízíme dvě strategie pro řízení souběžnosti.

-   **Optimistický** - Klient provádějící aktualizaci ověří, zda se data změnila od posledního čtení dat.
-   **Poslední vyhrává** - Klient přímo aktualizuje data, bez ohledu na to, zda ji od posledního čtení upravila jiná aplikace.

<a name="optimistic-concurrency-workflow"></a>Optimistický pracovní postup souběžnosti
-------------------------------

Doporučujeme použít optimistickou strategii souběžnosti s následujícím pracovním postupem, která zaručí, že nebudou provedeny žádné neočekávané úpravy vašich prostředků.

1.  Načíst entitu pomocí api. Odpověď obsahuje hodnotu ETag, která identifikuje aktuálně uloženou verzi entity (v době odpovědi).
2.  V době aktualizace zahrňte stejnou hodnotu ETag do povinné hlavičky požadavku **If-Match.**
3.  Rozhraní API porovná hodnotu ETag přijatou v požadavku s aktuální hodnotou ETag entity v atomické transakci.
    *   Pokud eTag hodnoty se liší, `412 Precondition Failed` rozhraní API vrátí odpověď HTTP. Tato chyba označuje, že buď jiný proces aktualizoval entitu od posledního načtení klienta, nebo že hodnota ETag zadaná v požadavku je nesprávná.
    *  Pokud jsou hodnoty ETag stejné nebo hlavička **If-Match** obsahuje zástupný`*`znak hvězdičky ( ), rozhraní API provede požadovanou operaci. Operace rozhraní API také aktualizuje uloženou hodnotu ETag entity.


> [!NOTE]
> Zadání zástupný znak (*) v **záhlaví If-Match** výsledky v rozhraní API pomocí Last-one wins souběžnosti strategie. V tomto případě nedochází k porovnání eTag a prostředek je aktualizován bez kontrol. 
