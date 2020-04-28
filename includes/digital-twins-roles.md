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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "76044930"
---
Následující tabulka popisuje role, které jsou k dispozici v rámci digitálních vláken Azure:

| **Role** | **Popis** | **Identifikátor** |
| --- | --- | --- |
| Správce místa | Oprávnění *vytvořit*, *číst*, *aktualizovat*a *Odstranit* pro zadaný prostor a všechny uzly pod. Globální oprávnění | 98e44ad7-28d4-4007-853b-b9968ad132d1 |
| Správce uživatelů| Oprávnění k *vytváření*, *čtení*, *aktualizaci*a *odstraňování* uživatelů a objektů souvisejících s uživateli. Oprávnění *číst* pro mezery | dfaac54c-f583-4dd2-b45d-8d4bbc0aa1ac |
| Správce zařízení | Oprávnění k *vytváření*, *čtení*, *aktualizaci*a *odstraňování* pro zařízení a objekty související se zařízením. Oprávnění *číst* pro mezery | 3cdfde07-bc16-40d9-bed3-66d49a8f52ae |
| Správce klíčů | Oprávnění k *vytváření*, *čtení*, *aktualizaci*a *odstraňování* přístupových klíčů. Oprávnění *číst* pro mezery | 5a0b1afc-e118-4068-969f-b50efb8e5da6 |
| Správce tokenů |  Oprávnění ke *čtení* a *aktualizaci* přístupových klíčů. Oprávnění *číst* pro mezery | 38a3bb21-5424-43b4-b0bf-78ee228840c3 |
| Uživatel |  Oprávnění *číst* pro prostory, senzory a uživatele, které obsahují odpovídající související objekty. | b1ffdb77-c635-4e7e-ad25-948237d85b30 |
| Specialista na podporu |  Oprávnění *ke čtení* pro vše kromě přístupových klíčů. | 6e46958b-dc62-4e7c-990c-c3da2e030969 |
| Instalační program zařízení | Oprávnění *číst* a *aktualizovat* pro zařízení a senzory, které zahrnují odpovídající související objekty. Oprávnění *číst* pro mezery | b16dd9fe-4efe-467b-8c8c-720e2ff8817c |
| Zařízení brány | *Vytvořit* oprávnění pro senzory. Oprávnění *číst* pro zařízení a senzory, která zahrnují odpovídající související objekty. | d4c69766-e9bd-4e61-bfc1-d8b6e686c7a8 |