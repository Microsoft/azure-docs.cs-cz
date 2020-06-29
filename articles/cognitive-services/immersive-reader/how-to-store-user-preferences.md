---
title: Ukládat předvolby uživatele
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak ukládat předvolby uživatele.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486899"
---
# <a name="how-to-store-user-preferences"></a>Jak ukládat předvolby uživatele

Tento článek ukazuje, jak uložit předvolby uživatele. Tato funkce se označuje jako alternativní způsob ukládání předvoleb uživatele v případě, že používání souborů cookie není žádoucí nebo proveditelné.

## <a name="how-to-enable-storing-user-preferences"></a>Jak povolit ukládání uživatelských předvoleb

`options`Parametr obsahuje `onPreferencesChanged` zpětné volání. Tato funkce bude volána, kdykoli uživatel změní Předvolby (například změní velikost textu, barvu motivu, písmo atd.). `value`Parametr obsahuje řetězec, který představuje aktuální předvolby uživatele. Tento řetězec může být uložený pomocí libovolného mechanismu, kterému dáváte přednost.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Jak načíst předvolby uživatele do moderního čtecího zařízení

Předejte do moderního čtecího zařízení předvolby uživatele pomocí `preferences` parametru. Mezi triviální příklad pro ukládání a načítání předvoleb uživatele je následující:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)