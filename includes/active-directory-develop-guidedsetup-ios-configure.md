---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: brandwe
manager: mtillman
ms.openlocfilehash: ca7a47fbe2c5ee2a4eb10abf3b9b50a2d28c252e
ms.sourcegitcommit: 06724c499837ba342c81f4d349ec0ce4f2dfd6d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46466151"
---
## <a name="register-your-application"></a>Registrace vaší aplikace
Můžete zaregistrovat aplikaci v některém ze dvou způsobů, jak je popsáno v následujících dvou částech.

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim
Teď musíte zaregistrovat svoji aplikaci *portál pro registraci aplikací Microsoft*:
1. Registrace vaší aplikace prostřednictvím [portál pro registraci aplikací Microsoftu](https://apps.dev.microsoft.com/portal/register-app?appType=mobileAndDesktopApp&appTech=ios&step=configure)
2.  Zadejte název vaší aplikace a e-mailu
3.  Ujistěte se, že je zaškrtnutá možnost pro instalační program s asistencí
4.  Postupujte podle pokynů k získání ID aplikace a vložte ho do svého kódu

### <a name="option-2-advanced-mode"></a>Možnost 2: Rozšířeném režimu

1.  Přejděte na [portál pro registraci aplikací Microsoftu](https://apps.dev.microsoft.com/portal/register-app)
2.  Zadejte název pro vaši aplikaci
3.  Ujistěte se, že není zaškrtnuta možnost pro instalační program s asistencí
4.  Klikněte na tlačítko `Add Platform`a pak vyberte `Native Application` a klikněte na tlačítko `Save`
5.  Přejděte zpět na Xcode. V `ViewController.swift`, nahraďte řádek začínající "`let kClientID`" s ID aplikace, který jste právě zaregistrovali:

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
