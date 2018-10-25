---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: 15db2192703971a8056df34343c427db11c8411a
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49988497"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

Zaregistrujte si vaši aplikaci a přidejte informace o registraci aplikace do svého řešení, máte dvě možnosti:

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Můžete rychle registrace vaší aplikace následujícím způsobem:

1. Registrace vaší aplikace prostřednictvím [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure).
2. Zadejte název vaší aplikace a e-mailu.
3. Ujistěte se, že je zaškrtnuté políčko pro instalační program s asistencí.
4. Postupujte podle pokynů pro přidání adresy URL přesměrování pro aplikaci.

### <a name="option-2-advanced-mode"></a>Možnost 2: Rozšířeném režimu

Postup při registraci aplikace a přidání informací o registraci aplikace k řešení:

1. Abyste mohli zaregistrovat aplikaci, přejděte na [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/portal/register-app).
2. Zadejte název vaší aplikace a e-mailu.
3. Ujistěte se, že není zaškrtnuta možnost pro instalační program s asistencí
4. Vyberte `Add Platform`a pak vyberte `Web`.
5. Přejděte zpět do sady Visual Studio a v Průzkumníku řešení vyberte projekt a podívejte se na okno Vlastnosti (Pokud se nezobrazí okno Vlastnosti, stisknutím klávesy F4)
6. Změnit protokol SSL povolený pro `True`.
7. Klikněte pravým tlačítkem na projekt v sadě Visual Studio a pak zvolte **vlastnosti**a **webové** kartu. V *servery* části změnit *adresa Url projektu* bude adresa URL protokolu SSL.
8. Zkopírujte adresu URL protokolu SSL a přidejte tuto adresu URL do seznamu adresy URL pro přesměrování v seznamu portál pro registraci adresy URL pro přesměrování:<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Přidejte následující kód do `web.config` nachází v kořenové složce v části `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Nahraďte `ClientId` s ID aplikace, který jste právě zaregistrovali.
11. Nahraďte `redirectUri` s adresou URL protokolu SSL vašeho projektu.
