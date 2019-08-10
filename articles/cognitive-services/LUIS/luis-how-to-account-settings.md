---
title: Správa účtu a klíčů – LUIS
titleSuffix: Azure Cognitive Services
description: Dvě důležité informace pro účet LUIS jsou uživatelský účet a pro tvorbu klíč. Vaše přihlašovací údaje se spravují na adrese account.microsoft.com. Váš klíč pro vytváření obsahu se spravuje na stránce nastavení portálu LUIS.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/29/2019
ms.author: diberry
ms.openlocfilehash: 11a2040e674a0ee38fbce8b21097f058fe603da2
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881934"
---
# <a name="manage-account-and-authoring-key"></a>Správa účtu a klíč pro tvorbu

Dvě důležité informace pro účet LUIS jsou uživatelský účet a pro tvorbu klíč. Přihlašovací údaje se spravuje na [account.microsoft.com](https://account.microsoft.com). Váš klíč pro vytváření obsahu se spravuje na stránce **Nastavení** portálu [Luis](luis-reference-regions.md) .

## <a name="authoring-key"></a>Klíč pro tvorbu

Tento jediný klíč pro vytváření obsahu specifický pro oblast na stránce **Nastavení** vám umožní vytvářet všechny vaše aplikace z portálu [Luis](luis-reference-regions.md) a také s [rozhraními API pro vytváření obsahu](https://go.microsoft.com/fwlink/?linkid=2092087). Pro zjednodušení vytváření klíč má povoleno provést [omezené](luis-boundaries.md) počet koncových bodů dotazuje každý měsíc.

[![Stránka nastavení služby LUIS](./media/luis-how-to-account-settings/account-settings.png)](./media/luis-how-to-account-settings/account-settings.png#lightbox)

Vytváření klíč se používá pro všechny aplikace, které vlastníte, jakož i všechny aplikace, které jsou uvedené jako spolupracovníka.

## <a name="authoring-key-regions"></a>Vytváření klíčových oblastech

Klíč pro vytváření obsahu je specifická pro [pro vytváření oblasti](luis-reference-regions.md#publishing-regions). Klíč nebude fungovat v různých oblastech.

## <a name="reset-authoring-key"></a>Obnovit klíč pro tvorbu

Pokud pro tvorbu klíče je ohrožena obnovte klíč. Klíč se resetuje na všech vašich aplikacích na portálu [Luis](luis-reference-regions.md) . Pokud vytváříte aplikace pomocí rozhraní API pro vytváření, budete muset změnit hodnotu `Ocp-Apim-Subscription-Key` do nového klíče.

## <a name="delete-account"></a>Odstranit účet

Zobrazit [úložiště dat a odebrání](luis-concept-data-storage.md#accounts) informace o tom, jaká data se odstraní při odstranění účtu.

## <a name="next-steps"></a>Další postup

Další informace o vaší [vytváření klíč](luis-concept-keys.md#authoring-key).

