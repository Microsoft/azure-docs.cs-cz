---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: c4dc3d422e18d9ee41bf16ac3e6f22c3d7e466d7
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121621"
---
## <a name="register-your-application"></a>Zaregistrujte svoji aplikaci.

Registrace vaší aplikace v jednom ze dvou způsobů.

### <a name="option-1-express-mode"></a>Option 1: Expresní režim

Můžete rychle registrace vaší aplikace následujícím způsobem:
1. Přejděte [portál Azure – registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/WinDesktopQuickstartPage/sourceType/docs).
1. Zadejte název vaší aplikace a Vyberte **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.

### <a name="option-2-advanced-mode"></a>Option 2: Rozšířený režim

Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Vyberte **registrace nové**.
   - V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `Win-App-calling-MsGraph`.
   - V části **Podporované typy účtu** vyberte **Účty v libovolném organizačním adresáři a osobní účty Microsoft (například Skype, Xbox, Outlook.com)**.
   - Výběrem možnosti **Registrovat** aplikaci vytvořte.
1. V seznamu stránek pro aplikaci vyberte **Ověřování**.
   1. V **identifikátory URI přesměrování** části, v seznamu identifikátorů URI pro přesměrování:
   1. V **typ** vybrat sloupec **veřejným klientem (mobilních a desktopových)**.
   1. Zadejte `urn:ietf:wg:oauth:2.0:oob` v **identifikátor URI pro PŘESMĚROVÁNÍ** sloupce.
1. Vyberte **Uložit**.
1. Přejděte do sady Visual Studio, otevřete *App.xaml.cs* souboru a potom nahraďte `Enter_the_Application_Id_here` s ID aplikace, které jste právě zaregistrovali a zkopírovat.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
