---
title: Registrace aplikací klasické pracovní plochy, které volají webová rozhraní API – Microsoft Identity Platform | Azure
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
ms.openlocfilehash: 6796ac42a10d3b976b23f5af1418b1789011d61b
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440945"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Aplikace klasické pracovní plochy, která volá webová rozhraní API: registrace aplikace

Tento článek popisuje specifickou registraci aplikací pro desktopovou aplikaci.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů podporované v desktopové aplikaci závisí na zkušenostech, které chcete vysvětlit. Z důvodu tohoto vztahu jsou podporované typy účtů závislé na tokůch, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro získání interaktivního tokenu

Pokud vaše aplikace klasické pracovní plochy používá interaktivní ověřování, můžete se přihlásit z libovolného [typu účtu](quickstart-register-app.md).

### <a name="audience-for-desktop-app-silent-flows"></a>Cílová skupina pro tiché toky aplikace klasické pracovní plochy

- Pokud chcete použít integrované ověřování systému Windows nebo uživatelské jméno a heslo, vaše aplikace musí přihlašovat uživatele ve vašem vlastním tenantovi, například pokud jste vývojář obchodní aplikace (LOB). Nebo v Azure Active Directory organizacích musí vaše aplikace přihlašovat uživatele ve vašem vlastním tenantovi, pokud se jedná o scénář nezávislého výrobce softwaru. Tyto toky ověřování nejsou podporované pro osobní účty Microsoft.
- Pokud chcete používat tok kódu zařízení, nemůžete zatím přihlašovat uživatele pomocí osobních účtů Microsoftu.
- Pokud se přihlašujete uživatelům pomocí sociálních identit, které předají autoritu a zásadu pro B2C (Business-to-Commerce), můžete použít jenom interaktivní ověřování pomocí uživatelského jména a hesla.

## <a name="redirect-uris"></a>Identifikátory URI pro přesměrování

Identifikátor URI pro přesměrování, který se má použít v desktopové aplikaci, závisí na toku, který chcete použít.

- Pokud používáte interaktivní ověřování nebo tok kódu zařízení, použijte `https://login.microsoftonline.com/common/oauth2/nativeclient` . Chcete-li dosáhnout této konfigurace, vyberte odpovídající adresu URL v části **ověřování** pro vaši aplikaci.

  > [!IMPORTANT]
  > V současné době MSAL.NET používá jiný identifikátor URI pro přesměrování ve výchozím nastavení v aplikacích klasické pracovní plochy, které běží ve Windows ( `urn:ietf:wg:oauth:2.0:oob` ). V budoucnu bude vhodné změnit toto výchozí nastavení, proto doporučujeme, abyste používali `https://login.microsoftonline.com/common/oauth2/nativeclient` .

- Pokud vytváříte nativní cíl-C nebo aplikaci SWIFT pro macOS, zaregistrujte identifikátor URI přesměrování na základě identifikátoru sady prostředků vaší aplikace v následujícím formátu: msauth. <your.app.bundle.id>://auth. nahraďte <your.app.bundle.id> s identifikátorem sady prostředků vaší aplikace.
- Pokud vaše aplikace používá integrované ověřování systému Windows nebo uživatelské jméno a heslo, nemusíte pro svoji aplikaci registrovat identifikátor URI přesměrování. Tyto toky dovedou k přenosu koncového bodu Microsoft Identity Platform v 2.0. Vaše aplikace se nebude volat zpátky na žádný konkrétní identifikátor URI.
- Chcete-li odlišit tok kódu zařízení, integrované ověřování systému Windows a uživatelské jméno a heslo z toku důvěrné klientské aplikace, které nemají identifikátory URI pro přesměrování (tok přihlašovacích údajů klienta použitý v aplikacích démon), musíte vyjádřit, že vaše aplikace je veřejná klientská aplikace. Pokud chcete dosáhnout této konfigurace, přečtěte si část **ověřování** pro vaši aplikaci. V podčásti **Upřesnit nastavení** v části **výchozí typ klienta** vyberte **Ano** , aby se **aplikace považovala za veřejného klienta**.

  ![Povolení veřejného klienta](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Aplikace klasické pracovní plochy volají rozhraní API pro přihlášeného uživatele. Potřebují požádat o delegovaná oprávnění. Nemohou žádat o oprávnění aplikace, která jsou zpracovávána pouze v [aplikacích démon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Desktopová aplikace: Konfigurace aplikace](scenario-desktop-app-configuration.md)
