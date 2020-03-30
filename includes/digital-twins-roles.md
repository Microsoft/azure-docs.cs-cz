---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 01/15/2020
ms.custom: include file
ms.openlocfilehash: 864d693f2919922b5035f963103b2ce98600a51c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76044930"
---
Následující tabulka popisuje role, které jsou dostupné v Azure Digital Twins:

| **Role** | **Popis** | **Identifikátor** |
| --- | --- | --- |
| Správce prostoru | *Vytvořit,* *číst,* *aktualizovat*a *odstranit* oprávnění pro zadaný prostor a všechny uzly pod ním. Globální povolení. | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Správce uživatelů| *Oprávnění vytvořit,* *číst,* *aktualizovat*a *odstranit* pro uživatele a objekty související s uživateli. *Oprávnění pro čtení* pro mezery. | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Správce zařízení | *Oprávnění vytvořit,* *číst,* *aktualizovat*a *odstranit* oprávnění pro zařízení a objekty související se zařízením. *Oprávnění pro čtení* pro mezery. | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Správce klíčů | *Vytvořit,* *číst,* *aktualizovat*a *odstranit* oprávnění pro přístupové klíče. *Oprávnění pro čtení* pro mezery. | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Správce tokenu |  *Oprávnění pro čtení* a *aktualizaci* pro přístupové klíče. *Oprávnění pro čtení* pro mezery. | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Uživatel |  *Oprávnění pro čtení* pro mezery, senzory a uživatele, které zahrnuje odpovídající související objekty. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specialista podpory |  *Oprávnění ke čtení* pro všechny kromě přístupových klíčů. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalační program zařízení | *Oprávnění ke čtení* a *aktualizaci* pro zařízení a senzory, které zahrnují odpovídající související objekty. *Oprávnění pro čtení* pro mezery. | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Zařízení brány | *VYTVOŘTE* oprávnění pro senzory. *Oprávnění ke čtení* pro zařízení a senzory, které zahrnují odpovídající související objekty. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |