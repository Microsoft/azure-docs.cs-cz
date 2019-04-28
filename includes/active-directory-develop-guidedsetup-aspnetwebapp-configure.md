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
ms.date: 04/11/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 2cdc6ea01e6c3555740102f319d0f4e8e4fc1c22
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60298101"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

Zaregistrujte si vaši aplikaci a přidejte informace o registraci aplikace do svého řešení, máte dvě možnosti:

### <a name="option-1-express-mode"></a>Option 1: Expresní režim

Můžete rychle registrace vaší aplikace následujícím způsobem:

1. Přejděte k novému [portál Azure – registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_RegisteredApps/applicationsListBlade/quickStartType/AspNetWebAppQuickstartPage/sourceType/docs) podokně.
1. Zadejte název vaší aplikace a klikněte na **Zaregistrovat**.
1. Postupujte podle pokynů ke stažení a automatické konfiguraci nové aplikace jedním kliknutím.

### <a name="option-2-advanced-mode"></a>Option 2: Rozšířený režim

Pokud chcete zaregistrovat aplikaci a ručně přidat informace o registraci aplikace ke svému řešení, postupujte následovně:

1. Přejděte do sady Visual Studio a:
   1. v Průzkumníku řešení vyberte projekt a podívejte se na okno Vlastnosti (Pokud se nezobrazí okno Vlastnosti, stisknutím klávesy F4).
   1. Změnit protokol SSL povolený pro `True`.
   1. Klikněte pravým tlačítkem na projekt v sadě Visual Studio a pak zvolte **vlastnosti**a **webové** kartu. V *servery* části změnit *adresa Url projektu* bude adresa URL protokolu SSL.
   1. Zkopírujte adresu URL protokolu SSL. Do seznamu adresy URL pro přesměrování v portálu pro registraci seznam adres URL pro přesměrování v dalším kroku přidáte tuto adresu URL:<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí pracovního nebo školního účtu nebo osobního účtu Microsoft.
1. Pokud váš účet umožňuje přístup k více tenantům, vyberte svůj účet v pravém horním rohu a nastavte relaci portálu na požadovaného tenanta Azure AD.
1. Přejděte na Microsoft identity platform pro vývojáře [registrace aplikací](https://go.microsoft.com/fwlink/?linkid=2083908) stránky.
1. Vyberte **registrace nové**.
1. Když se zobrazí stránka **Registrace aplikace**, zadejte registrační informace vaší aplikace:
   1. V části **Název** zadejte smysluplný název aplikace, který se zobrazí uživatelům aplikace, například `ASPNET-Tutorial`.
   1. Přidejte adresu URL protokolu SSL, měli jste zkopírovali ze sady Visual Studio v kroku 1 (například `https://localhost:44368/`) v **adresy URL odpovědi**a klikněte na tlačítko **zaregistrovat**.
1. Vyberte nabídku **Ověřování**, v části **Implicitní udělení oprávnění** nastavte **Tokeny ID** a pak vyberte **Uložit**.
1. Přidejte následující kód do `web.config` nachází v kořenové složce v části `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

1. Nahraďte `ClientId` s ID aplikace, který jste právě zaregistrovali.
1. Nahraďte `redirectUri` s adresou URL protokolu SSL vašeho projektu.
