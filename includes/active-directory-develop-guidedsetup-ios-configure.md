---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 09/19/2018
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: a4b0c2e2d7732c6d2a4f57411731f7145cf3e73e
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967670"
---
## <a name="register-your-application"></a>Zaregistrujte svoji aplikaci.

Můžete zaregistrovat aplikaci v některém ze dvou způsobů, jak je popsáno v následujících dvou částech.

### <a name="option-1-express-mode"></a>Option 1: Expresní režim

Teď musíte zaregistrovat svoji aplikaci *portál pro registraci aplikací Microsoft*:

1. Registrace vaší aplikace prostřednictvím [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure).
2. Zadejte název vaší aplikace a e-mailu.
3. Ujistěte se, že je zaškrtnuté políčko pro instalační program s asistencí.
4. Postupujte podle pokynů k získání ID aplikace a vložte ho do svého kódu.

### <a name="option-2-advanced-mode"></a>Option 2: Rozšířený režim

1. Přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Zadejte název své aplikace.
3. Ujistěte se, že není zaškrtnuta možnost pro instalační program s asistencí.
4. Vyberte `Add Platform` a pak vyberte `Native Application`.
5. Vyberte `Save`.
6. Přejděte zpět na Xcode. V `ViewController.swift`, nahraďte řádek začínající "`let kClientID`" s ID aplikace, který jste právě zaregistrovali:

```swift
let kClientID = "Your_Application_Id_Here"
```

<!-- Workaround for Docs conversion bug -->
<ol start="6">
<li>
CTRL + klikněte na tlačítko <code>Info.plist</code> vyvolali místní nabídku, a potom klikněte na: <code>Open As</code> > <code>Source Code</code>
</li>
<li>
V části <code>dict</code> kořenový uzel, přidejte následující:
</li>
</ol>

```xml
<key>CFBundleURLTypes</key>
<array>
    <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        <key>CFBundleURLSchemes</key>
        <array>
            <string>msal[Your_Application_Id_Here]</string>
        </array>
    </dict>
</array>
```
<ol start="8">
<li>
Nahraďte <i> <code>[Your_Application_Id_Here]</code> </i> s Id aplikace, který jste právě zaregistrovali
</li>
</ol>
