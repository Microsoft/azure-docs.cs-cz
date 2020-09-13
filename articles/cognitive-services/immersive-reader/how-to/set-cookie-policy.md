---
title: Nastavení zásad pro moderní čtečku souborů cookie
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak nastavit zásady souborů cookie pro moderní čtečku.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-javascript
ms.openlocfilehash: 44de79afd6e2fb1f1d4fff1323484364a17da8d0
ms.sourcegitcommit: 59ea8436d7f23bee75e04a84ee6ec24702fb2e61
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2020
ms.locfileid: "89505297"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Jak nastavit zásady souborů cookie pro moderní čtečku

Moderní čtečka standardně zakáže použití souborů cookie. Pokud povolíte použití souborů cookie, moderní čtečka může používat soubory cookie k údržbě uživatelských předvoleb a sledování využití funkcí. Pokud povolíte použití souborů cookie v moderní čtečce, zvažte prosím požadavky zásad dodržování předpisů v souborech cookie EU. Hostitelská aplikace je odpovědná za získání veškerého souhlasu uživatele v souladu se zásadami dodržování předpisů v souborech cookie EU.

Zásady souborů cookie je možné nastavit pomocí [možností](../reference.md#options)moderního čtecího zařízení.

## <a name="enable-cookie-usage"></a>Povolit používání souborů cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Zakázat používání souborů cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Další kroky

* Podívejte se na úvodní úvodní [Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) , abyste viděli, co dalšího můžete dělat se sadou pro moderní čtečku pomocí Node.js
* Podívejte se na [kurz pro Android](../tutorial-android.md) , kde najdete další informace, které můžete dělat v sadě moderní čtečky pomocí Java nebo Kotlin pro Android.
* Podívejte se na [kurz pro iOS](../tutorial-ios.md) , kde najdete další informace, které můžete dělat v sadě pro moderní čtečku pomocí SWIFT pro iOS.
* Podívejte se na [kurz Pythonu](../tutorial-python.md) , abyste viděli, co dalšího můžete dělat se sadou moderní čtečky pomocí Pythonu.
* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](../reference.md)