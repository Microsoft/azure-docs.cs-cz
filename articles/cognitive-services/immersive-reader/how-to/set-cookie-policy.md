---
title: Nastavení zásad pro moderní čtečku souborů cookie
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak nastavit zásady souborů cookie pro moderní čtečku.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 876379a211038933cf665bd1a4a4daae9c9b2787
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267065"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Jak nastavit zásady souborů cookie pro moderní čtečku

Moderní čtečka standardně zakáže použití souborů cookie. Pokud povolíte použití souborů cookie, moderní čtečka může používat soubory cookie k údržbě uživatelských předvoleb a sledování využití funkcí. Pokud povolíte použití souborů cookie v moderní čtečce, zvažte prosím požadavky zásad dodržování předpisů v souborech cookie EU. Hostitelská aplikace je odpovědná za získání veškerého souhlasu uživatele v souladu se zásadami dodržování předpisů v souborech cookie EU.

Zásady souborů cookie je možné nastavit pomocí [možností](../reference.md#options)moderního čtecího zařízení. Další informace najdete v tématu [CookiePolicy enum](../reference.md#cookiepolicy-enum) .

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

* Podívejte se na úvodní seznam pro [Node. js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) , kde vidíte, co dalšího můžete dělat se sadou moderního čtecího zařízení pomocí Node. js.
* Podívejte se na [kurz Pythonu](../tutorial-python.md) , abyste viděli, co dalšího můžete dělat se sadou moderní čtečky pomocí Pythonu.
* Podívejte se na [kurz SWIFT](../tutorial-ios-picture-immersive-reader.md) , který vám umožní zobrazit další informace, které můžete dělat v sadě moderní čtečky pomocí SWIFT.
* Prozkoumejte [sadu moderních čtenářů](https://github.com/microsoft/immersive-reader-sdk) a [referenční materiály k sadě pro moderní čtečku](../reference.md)