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
ms.openlocfilehash: 5eaee4f932c4e42f6fed3d839314346b3a93f360
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2019
ms.locfileid: "59799530"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

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
1. V části **Identifikátory URI pro přesměrování** vyhledejte část **Navrhované identifikátory URI pro přesměrování pro veřejné klienty (mobilní, desktopové)** a vyberte **urn:ietf:wg:oauth:2.0:oob**.
1. Vyberte **Uložit**.
1. Přejděte do sady Visual Studio, otevřete *App.xaml.cs* souboru a potom nahraďte `Enter_the_Application_Id_here` s ID aplikace, které jste právě zaregistrovali a zkopírovat.

    ```csharp
    private static string ClientId = "Enter_the_Application_Id_here";
    ```
