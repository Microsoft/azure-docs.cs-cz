---
title: Spravovat nastavení svého účtu v LUIS | Microsoft Docs
description: Použití LUIS webu ke správě nastavení svého účtu.
titleSuffix: Azure
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 11/26/2018
ms.author: diberry
ms.openlocfilehash: bb41331228e700c55da21c627d617d16faa2dcb9
ms.sourcegitcommit: 922f7a8b75e9e15a17e904cc941bdfb0f32dc153
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/27/2018
ms.locfileid: "52335396"
---
# <a name="manage-account-and-authoring-key"></a>Správa účtu a klíč pro tvorbu
Dvě důležité informace pro účet LUIS jsou uživatelský účet a pro tvorbu klíč. Přihlašovací údaje se spravuje na [account.microsoft.com](https://account.microsoft.com). Spravuje se z vašeho klíče pro vytváření obsahu [LUIS](luis-reference-regions.md) webu **nastavení** stránky. 

## <a name="authoring-key"></a>Klíč pro tvorbu

Tento jeden, oblast pro tvorbu klíče na **nastavení** stránce umožňuje vytvářet všechny aplikace [LUIS](luis-reference-regions.md) webu i na [rozhraní API pro vytváření](https://aka.ms/luis-authoring-api). Pro zjednodušení vytváření klíč má povoleno provést [omezené](luis-boundaries.md) počet koncových bodů dotazuje každý měsíc. 

[![Stránka nastavení služby LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Vytváření klíč se používá pro všechny aplikace, které vlastníte, jakož i všechny aplikace, které jsou uvedené jako spolupracovníka.

## <a name="authoring-key-regions"></a>Vytváření klíčových oblastech
Klíč pro vytváření obsahu je specifická pro [pro vytváření oblasti](luis-reference-regions.md#publishing-regions). Klíč nebude fungovat v různých oblastech. 

## <a name="reset-authoring-key"></a>Obnovit klíč pro tvorbu
Pokud pro tvorbu klíče je ohrožena obnovte klíč. Klíč se resetuje na všech aplikacích [LUIS](luis-reference-regions.md) webu. Pokud vytváříte aplikace pomocí rozhraní API pro vytváření, budete muset změnit hodnotu `Ocp-Apim-Subscription-Key` do nového klíče. 

## <a name="delete-account"></a>Odstranit účet
Zobrazit [úložiště dat a odebrání](luis-concept-data-storage.md#accounts) informace o tom, jaká data se odstraní při odstranění účtu. 

## <a name="next-steps"></a>Další postup

Další informace o vaší [vytváření klíč](luis-concept-keys.md#authoring-key). 

