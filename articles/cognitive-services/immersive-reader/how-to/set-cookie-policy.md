---
title: Nastavit zásady souborů cookie pro immersive čtečku
titleSuffix: Azure Cognitive Services
description: Tento článek vám ukáže, jak nastavit zásady souborů cookie pro immersive Reader.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946108"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Jak nastavit zásady souborů cookie pro immersive Reader

Immersive Reader ve výchozím nastavení zakáže používání souborů cookie. Pokud povolíte používání souborů cookie, může immersive Reader používat soubory cookie k zachování uživatelských preferencí a sledování používání funkcí. Pokud v aplikaci Immersive Reader povolíte používání souborů cookie, zvažte prosím požadavky zásad eu pro dodržování souborů cookie. Je odpovědností hostitelské aplikace získat potřebný souhlas uživatele v souladu se Zásadami dodržování souborů cookie EU.

Zásady souborů cookie lze nastavit prostřednictvím [možností](../reference.md#options)immersive reader . Další informace naleznete [v tématu Výčt cookiePolicy.](../reference.md#cookiepolicy-enum)

## <a name="enable-cookie-usage"></a>Povolit využití souborů cookie

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

* Zobrazení [rychlého startu node.js](../quickstart-nodejs.md) zobrazíte další, co dalšího můžete dělat se sadou Immersive Reader SDK pomocí souboru Node.js
* Podívejte se na [kurz v Pythonu](../tutorial-python.md) a zjistěte, co dalšího můžete dělat s sadou Immersive Reader SDK pomocí Pythonu
* Podívejte se na [výukový program Swift,](../tutorial-ios-picture-immersive-reader.md) abyste zjistili, co dalšího můžete dělat s sadou Immersive Reader SDK pomocí swiftu
* Seznamte se s [sadou Immersive Reader SDK](https://github.com/microsoft/immersive-reader-sdk) a [referenční sadou Immersive Reader SDK](../reference.md)