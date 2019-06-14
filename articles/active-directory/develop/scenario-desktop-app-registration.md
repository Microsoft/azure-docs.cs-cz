---
title: Aplikace klasické pracovní plochy, že volání webových rozhraní API (registrace aplikace) – platforma identit Microsoft
description: Zjistěte, jak vytvářet aplikace klasické pracovní plochy, že volání webových rozhraní API (registrace aplikace)
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
ms.date: 04/18/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5ab2701a82da0b8f7bc4e23a3d947be905593e85
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67057217"
---
# <a name="desktop-app-that-calls-web-apis---app-registration"></a>Aplikace klasické pracovní plochy, že volání webového rozhraní API – registrace aplikace

Tento článek obsahuje zvláštností registrace aplikace pro aplikace klasické pracovní plochy.

## <a name="supported-accounts-types"></a>Typy podporovaných účtů

Typy účtů, které jsou podporovány v aplikaci klasické pracovní plochy závisí na prostředí chcete nahoru světla, a proto u těchto toků chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro interaktivní získání tokenu

Pokud vaše aplikace klasické pracovní plochy používá interaktivní ověřování, se můžete přihlásit uživatele z libovolného [typ účtu](quickstart-register-app.md#register-a-new-application-using-the-azure-portal)

### <a name="audience-for-desktop-app-silent-flows"></a>Cílová skupina pro aplikace klasické pracovní plochy tiché toků

- Pokud máte v úmyslu použít ověření integrované Windows nebo uživatelského jména a hesla, vaše aplikace potřebuje k přihlášení uživatelů do vašeho vlastního tenanta (obchodní Vývojář) nebo v Azure Active directory organizace (scénář nezávislých výrobců softwaru). Tyto toky ověřování nejsou podporovány pro osobní účty Microsoft
- Pokud chcete použít tok kódu zařízení, nemůžete se přihlásit uživatele pomocí svých osobních účtů Microsoft ještě
- Pokud přihlášení uživatelů se sociálními identitami předáním B2C autority a zásad, můžete použít jenom ověřování interaktivní a uživatelského jména hesla.

## <a name="redirect-uris"></a>Identifikátory URI pro přesměrování

Znovu identifikátory URI pro použití v aplikaci klasické pracovní plochy pro přesměrování, bude záviset na tok, který chcete použít.

- Pokud používáte **interaktivní ověřování** nebo **toku kódu zařízení**, budete chtít použít `https://login.microsoftonline.com/common/oauth2/nativeclient`. Dosáhnete kliknutím na odpovídající adresu URL v této konfiguraci **ověřování** oddílu pro vaši aplikaci
  
  > [!IMPORTANT]
  > Dnes MSAL.NET používá jiný identifikátor URI pro přesměrování ve výchozím nastavení v desktopové aplikace běžící na Windows (`urn:ietf:wg:oauth:2.0:oob`). V budoucnu budete chceme změnit toto výchozí nastavení, a proto vám doporučujeme použít `https://login.microsoftonline.com/common/oauth2/nativeclient`

- Pokud vaše aplikace používá pouze ověření integrované Windows, uživatelské jméno a heslo, není nutné zaregistrovat na identifikátor URI přesměrování pro aplikaci. Ve skutečnosti tyto toky provést výměnu zpráv pro koncový bod v2.0 Microsoft identity platform a vaše aplikace nebude volána zpět na žádné konkrétní identifikátor URI. 
- Aby bylo možné rozlišit zařízení toku kódu, integrované ověřování Windows a uživatelského jména a hesla z toku aplikace důvěrnému klientovi, který nemá identifikátory URI pro přesměrování buď (klienta přihlašovacích údajů tok používaný v aplikacích démon), budete muset express vaše aplikace je aplikace veřejným klientem. Tato konfigurace zajišťuje tak, že přejdete **ověřování** pro vaši aplikaci a v části **upřesňující nastavení** podsekce, zvolte **Ano**, na otázku **Považovat aplikace veřejným klientem** (v **výchozí typ klienta** odstavci)

  ![Povolit veřejné klienta](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Oprávnění k rozhraní API

Desktopové aplikace volat rozhraní API jménem přihlášeného uživatele. Budou muset požadovat delegovaná oprávnění. Jejich nemohou požadovat oprávnění aplikací (které jsou zpracovávány pouze v [démon aplikace](scenario-daemon-overview.md))

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Aplikace klasické pracovní plochy – konfigurace aplikace](scenario-desktop-app-configuration.md)
