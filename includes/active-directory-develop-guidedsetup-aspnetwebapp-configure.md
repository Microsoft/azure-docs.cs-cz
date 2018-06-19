---
title: zahrnout soubor
description: zahrnout soubor
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: mtillman
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/04/2018
ms.author: andret
ms.custom: include file
ms.openlocfilehash: f1dc23729f32a7a9535b887acf638cf5464c24bd
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2018
ms.locfileid: "36206111"
---
## <a name="register-your-application"></a>Registrace vaší aplikace

Registrace vaší aplikace a přidat informace o registraci aplikace do vašeho řešení, máte dvě možnosti:

### <a name="option-1-express-mode"></a>Možnost 1: Expresní režim

Pomocí následujícího postupu můžete rychle registrace vaší aplikace:

1. Registrace vaší aplikace pomocí [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/portal/register-app?appType=serverSideWebApp&appTech=aspNetWebAppOwin&step=configure)
2.  Zadejte název vaší aplikace a e-mailu
3.  Ujistěte se, že je zaškrtnuté políčko pro instalaci na základě
4.  Postupujte podle pokynů k přidání do aplikace adresy URL pro přesměrování

### <a name="option-2-advanced-mode"></a>Možnost 2: Pokročilého režimu

Registrace vaší aplikace a přidat informace o registraci aplikace do vašeho řešení, postupujte takto:

1. Přejděte na [portálu pro registraci aplikace Microsoft](https://apps.dev.microsoft.com/portal/register-app) zaregistrovat aplikaci
2. Zadejte název vaší aplikace a e-mailu 
3. Ujistěte se, že není zaškrtnuto políčko pro instalaci na základě
4. Klikněte na tlačítko `Add Platform`, zvolte položku `Web`
5. Přejděte zpět do Visual Studio a v Průzkumníku řešení vyberte projekt a podívejte se na okno vlastností (Pokud se nezobrazí okno Vlastnosti stisknutím klávesy F4)
6. Změna SSL povoleno `True`
7. Klikněte pravým tlačítkem na projekt v sadě Visual Studio, a potom vyberte **vlastnosti**a **webové** kartě. V *servery* části změnit *adresa Url projektu* jako adresu URL protokolu SSL
8. Zkopírujte adresu URL protokolu SSL a přidejte tuto adresu URL do seznamu adres URL pro přesměrování v portálu pro registraci seznam adres URL pro přesměrování:<br/><br/>![Vlastnosti projektu](media/active-directory-develop-guidedsetup-aspnetwebapp-configure/vsprojectproperties.png)<br />
9. Přidejte následující v `web.config` umístěné v kořenové složce části `configuration\appSettings`:

    ```xml
    <add key="ClientId" value="Enter_the_Application_Id_here" />
    <add key="redirectUri" value="Enter_the_Redirect_URL_here" />
    <add key="Tenant" value="common" />
    <add key="Authority" value="https://login.microsoftonline.com/{0}/v2.0" />
    ```

10. Nahraďte `ClientId` s ID aplikace, který jste právě zaregistrovali
11. Nahraďte `redirectUri` pomocí adresy URL protokolu SSL vašeho projektu

