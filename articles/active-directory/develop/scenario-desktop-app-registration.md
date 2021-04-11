---
title: Registrace aplikací klasické pracovní plochy, které volají webová rozhraní API | Azure
titleSuffix: Microsoft identity platform
description: Informace o tom, jak vytvořit desktopovou aplikaci, která volá webová rozhraní API (registrace aplikace)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/09/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 263397aa2cd09ba24fa750131b76047801869a65
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104798931"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API: registrace aplikace

Tento článek popisuje specifickou registraci aplikací pro desktopovou aplikaci.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů podporované v desktopové aplikaci závisí na zkušenostech, které chcete vysvětlit. Z důvodu tohoto vztahu jsou podporované typy účtů závislé na tokůch, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro získání interaktivního tokenu

Pokud vaše aplikace klasické pracovní plochy používá interaktivní ověřování, můžete se přihlásit z libovolného [typu účtu](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Cílová skupina pro tiché toky aplikace klasické pracovní plochy

- Pokud chcete použít integrované ověřování systému Windows nebo uživatelské jméno a heslo, vaše aplikace musí přihlašovat uživatele ve vašem vlastním tenantovi, například pokud jste vývojář obchodní aplikace (LOB). Nebo v Azure Active Directory organizacích musí vaše aplikace přihlašovat uživatele ve vašem vlastním tenantovi, pokud se jedná o scénář nezávislého výrobce softwaru. Tyto toky ověřování nejsou podporované pro osobní účty Microsoft.
- Pokud se přihlašujete uživatelům pomocí sociálních identit, které předají autoritu a zásadu pro B2C (Business-to-Commerce), můžete použít jenom interaktivní ověřování pomocí uživatelského jména a hesla.

## <a name="redirect-uris"></a>Identifikátory URI pro přesměrování

Identifikátor URI pro přesměrování, který se má použít v desktopové aplikaci, závisí na toku, který chcete použít.

Určete identifikátor URI pro přesměrování vaší aplikace [nakonfigurováním nastavení platformy](quickstart-register-app.md#add-a-redirect-uri) pro aplikaci v **Registrace aplikací** Azure Portal.

- Pro aplikace, které používají interaktivní ověřování:
  - Aplikace, které používají vložené prohlížeče: `https://login.microsoftonline.com/common/oauth2/nativeclient`
  - Aplikace, které používají systémové prohlížeče: `http://localhost`

  > [!IMPORTANT]
  > Jako osvědčený postup zabezpečení doporučujeme explicitně nastavit `https://login.microsoftonline.com/common/oauth2/nativeclient` nebo `http://localhost` jako identifikátor URI přesměrování. Některé knihovny ověřování, jako je MSAL.NET, používají výchozí hodnotu `urn:ietf:wg:oauth:2.0:oob` , pokud není zadán jiný identifikátor URI pro přesměrování, což se nedoporučuje. Tato výchozí hodnota se aktualizuje jako zásadní změna v další hlavní verzi.

- Pokud vytváříte nativní cíl-C nebo aplikaci SWIFT pro macOS, zaregistrujte identifikátor URI přesměrování na základě identifikátoru sady prostředků vaší aplikace v následujícím formátu: `msauth.<your.app.bundle.id>://auth` . Nahraďte `<your.app.bundle.id>` identifikátorem sady prostředků vaší aplikace.
- Pokud vytváříte Node.js elektronickou aplikaci, použijte k tomu vlastní protokol souborů místo identifikátoru URI pro přesměrování webu (https://), aby bylo možné zpracovat krok přesměrování v případě instance `msal://redirect` . Název vlastního protokolu souboru by neměl být vypsaný jako odhad a měl by postupovat podle návrhů ve [specifikaci OAuth 2.0 pro nativní aplikace](https://tools.ietf.org/html/rfc8252#section-7.1).
- Pokud vaše aplikace používá integrované ověřování systému Windows nebo uživatelské jméno a heslo, nemusíte pro svoji aplikaci registrovat identifikátor URI přesměrování. Tyto toky dovedou k přenosu koncového bodu Microsoft Identity Platform v 2.0. Vaše aplikace se nebude volat zpátky na žádný konkrétní identifikátor URI.
- Aby bylo možné rozlišovat [tok kódu zařízení](scenario-desktop-acquire-token.md#device-code-flow), [integrované ověřování systému Windows](scenario-desktop-acquire-token.md#integrated-windows-authentication)a [uživatelské jméno a heslo](scenario-desktop-acquire-token.md#username-and-password) z aplikace důvěrné klientské aplikace pomocí toku přihlašovacích údajů klienta, který se používá v [aplikacích DÉMONA](scenario-daemon-overview.md), žádný z nich vyžaduje identifikátor URI přesměrování, který je nakonfigurován jako veřejná klientská aplikace. Chcete-li dosáhnout této konfigurace:

    1. V <a href="https://portal.azure.com/" target="_blank">Azure Portal</a>vyberte svou aplikaci v **Registrace aplikací** a pak vyberte **ověřování**.
    1. V části **Upřesnit nastavení**  >  **Povolit toky veřejného klienta**  >  **Povolte následující toky mobilních a desktopových toků:** vyberte **Ano**.

        :::image type="content" source="media/scenarios/default-client-type.png" alt-text="Povolit nastavení veřejného klienta v podokně ověřování v Azure Portal":::

## <a name="api-permissions"></a>Oprávnění rozhraní API

Aplikace klasické pracovní plochy volají rozhraní API pro přihlášeného uživatele. Potřebují požádat o delegovaná oprávnění. Nemohou žádat o oprávnění aplikace, která jsou zpracovávána pouze v [aplikacích démon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Další kroky

Přejděte k dalšímu článku v tomto scénáři, [Konfigurace kódu aplikace](scenario-desktop-app-configuration.md).
