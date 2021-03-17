---
title: Ukládání předvoleb uživatelů
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak ukládat předvolby uživatele.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/10/2021
ms.locfileid: "102617688"
---
# <a name="how-to-store-user-preferences"></a>Jak ukládat předvolby uživatele

Tento článek ukazuje, jak uložit nastavení uživatelského rozhraní uživatele, označované jako **předvolby uživatele**, prostřednictvím možností sady [OnPreferencesChanged](./reference.md#options) [pro moderní čtečku a sady](./reference.md#options) SDK.

Pokud je možnost [CookiePolicy](./reference.md#cookiepolicy-options) SDK nastavená na *povolenou*, aplikace pro moderní čtečku ukládá do souborů cookie **předvolby uživatele** (velikost textu, barva motivu, písmo atd.), které jsou místní pro konkrétní prohlížeč a zařízení. Pokaždé, když uživatel spustí moderní čtečku na stejném prohlížeči a v zařízení, otevře se s preferencemi uživatele z poslední relace v tomto zařízení. Pokud však uživatel otevře moderní čtečku na jiném prohlížeči nebo zařízení, nastavení se zpočátku nakonfiguruje s výchozím nastavením moderního čtečky a uživatel bude muset nastavit předvolby znovu a tak dále pro každé zařízení, které používají. `-preferences`Možnosti a `-onPreferencesChanged` moderní čtecí sada SDK poskytují způsob, jakým aplikace přenáší předvolby uživatele v různých prohlížečích a zařízeních, aby měl uživatel konzistentní prostředí bez ohledu na to, kde používají aplikaci.

Napřed poskytnutím `-onPreferencesChanged` možnosti zpětného volání sady SDK při spuštění aplikace s moderní čtečkou pak moderní čtečka pošle `-preferences` řetězec zpátky do hostitelské aplikace pokaždé, když uživatel změní předvolby během relace ponořeného čtenáře. Hostitelská aplikace pak zodpovídá za ukládání uživatelských předvoleb ve vlastním systému. Poté, když tento stejný uživatel spustí moderní čtečku znovu, může hostitelská aplikace načíst předvolby tohoto uživatele z úložiště a zadat je jako `-preferences` možnost sady String SDK při spuštění aplikace s moderní čtečkou, aby byly obnoveny předvolby uživatele.

Tato funkce se dá použít jako alternativní způsob ukládání **uživatelských předvoleb** v případě, že používání souborů cookie není žádoucí nebo proveditelné.

> [!CAUTION]
> **Důležité** informace Nepokoušejte se programově změnit hodnoty `-preferences` řetězce odesílaného do a z aplikace moderního čtecího zařízení, protože to může způsobit neočekávané chování, které by vašemu zákazníkům vedlo ke zhoršení uživatelského prostředí. Hostitelské aplikace by nikdy neměly přiřazovat vlastní hodnotu nebo manipulovat s `-preferences` řetězcem. Při použití `-preferences` Možnosti řetězec použijte pouze přesnou hodnotu, která byla vrácena z `-onPreferencesChanged` možnosti zpětného volání.

## <a name="how-to-enable-storing-user-preferences"></a>Jak povolit ukládání uživatelských předvoleb

parametr [LaunchAsync](./reference.md#launchasync) pro moderní čtecí sadu SDK `options` obsahuje `-onPreferencesChanged` zpětné volání. Tato funkce bude volána, kdykoli uživatel změní předvolby. `value`Parametr obsahuje řetězec, který představuje aktuální předvolby uživatele. Tento řetězec je pak uložen pro daného uživatele hostitelskou aplikací.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Jak načíst předvolby uživatele do moderního čtecího zařízení

Předejte do moderního čtecího zařízení preference uživatele pomocí `-preferences` Možnosti. Mezi triviální příklad pro ukládání a načítání předvoleb uživatele je následující:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Další kroky

* Prozkoumejte [referenční materiály k sadě pro moderní čtečku](./reference.md)