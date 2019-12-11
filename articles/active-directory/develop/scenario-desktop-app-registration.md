---
title: Registrace desktopové aplikace, která volá webová rozhraní API – Microsoft Identity Platform | Azure
description: Informace o tom, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (registrace aplikace)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94f7f2dfdbf404a092773857a0f7727618cd429a
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74965530"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API – registrace aplikace

Tento článek popisuje specifickou registraci aplikací pro desktopovou aplikaci.

## <a name="supported-accounts-types"></a>Podporované typy účtů

Typy účtů podporované v desktopové aplikaci závisí na prostředí, které chcete vysvětlit. Z důvodu tohoto vztahu jsou podporované typy účtů závislé na tokůch, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro získání interaktivního tokenu

Pokud vaše aplikace klasické pracovní plochy používá interaktivní ověřování, můžete se přihlásit z libovolného [typu účtu](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Cílová skupina pro tiché toky aplikace klasické pracovní plochy

- Pokud chcete použít integrované ověřování systému Windows nebo uživatelské jméno/heslo, vaše aplikace musí přihlašovat uživatele ve vašem vlastním tenantovi (LOB Developer) nebo v organizacích Azure Active Directory (ISV). Tyto toky ověřování nejsou podporované pro osobní účty Microsoft.
- Pokud chcete tok kódu zařízení použít, nemůžete zatím přihlašovat uživatele pomocí osobních účtů Microsoftu.
- Pokud se přihlašujete uživatelům pomocí sociálních identit, které procházejí autoritou B2C a zásadou, můžete použít jenom interaktivní ověřování pomocí uživatelského jména a hesla.

## <a name="redirect-uris"></a>Identifikátory URI pro přesměrování

Identifikátory URI pro přesměrování používané v desktopové aplikaci budou záviset na toku, který chcete použít.

- Pokud používáte **interaktivní ověřování** nebo **tok kódu zařízení**, budete chtít použít `https://login.microsoftonline.com/common/oauth2/nativeclient`. Tuto konfiguraci dosáhnete kliknutím na odpovídající adresu URL v části **ověřování** pro vaši aplikaci.
  
  > [!IMPORTANT]
  > V dnešní době MSAL.NET používá jiný identifikátor URI přesměrování ve výchozím nastavení v desktopových aplikacích běžících ve Windows (`urn:ietf:wg:oauth:2.0:oob`). V budoucnu bude vhodné změnit toto výchozí nastavení, proto doporučujeme, abyste používali `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Pokud vytváříte nativní cíl-C nebo aplikaci SWIFT pro macOS, budete chtít zaregistrovat redirectUri na základě identifikátoru sady prostředků vaší aplikace v následujícím formátu: **msauth. < Your. app... id >://auth** (nahraďte < vaší. app. revision. ID > identifikátorem sady prostředků vaší aplikace).
- Pokud je vaše aplikace jenom pomocí integrovaného ověřování systému Windows nebo uživatelského jména a hesla, nemusíte pro svoji aplikaci registrovat identifikátor URI přesměrování. Tyto toky se týkají odezvy koncového bodu Microsoft Identity Platform v 2.0 a vaše aplikace se nebude volat zpátky na žádný konkrétní identifikátor URI.
- Chcete-li odlišit tok kódu zařízení, integrované ověřování systému Windows a uživatelské jméno/heslo z toku důvěrné klientské aplikace, které nemají identifikátory URI pro přesměrování (tok přihlašovacích údajů klienta použitý v aplikacích démon), musíte vyjádřit, že vaše aplikace je veřejná klientská aplikace. Pokud chcete dosáhnout této konfigurace, přečtěte si část **ověřování** pro vaši aplikaci. Pak v podčásti **Upřesnit nastavení** v části **výchozí typ klienta** zvolte možnost **Ano** pro dotaz **zpracovat aplikaci jako veřejný klient**.

  ![Povolení veřejného klienta](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Aplikace klasické pracovní plochy volají rozhraní API pro přihlášeného uživatele. Potřebují požádat o delegovaná oprávnění. Nemůžou ale žádat o oprávnění aplikace, která se zpracují jenom v [aplikacích démona](scenario-daemon-overview.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Desktopová aplikace – konfigurace aplikace](scenario-desktop-app-configuration.md)
