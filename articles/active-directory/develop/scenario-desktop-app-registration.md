---
title: Registrace aplikací klasické pracovní plochy, které volají webová rozhraní API – platforma identit Microsoftu | Azure
description: Přečtěte si, jak vytvořit desktopovou aplikaci, která volá webová API (registrace aplikace)
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
ms.openlocfilehash: 599603ba867e21694392e38e9692280f010e08eb
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885153"
---
# <a name="desktop-app-that-calls-web-apis-app-registration"></a>Desktopová aplikace, která volá webová API: Registrace aplikací

Tento článek popisuje podrobnosti o registraci aplikace pro desktopovou aplikaci.

## <a name="supported-account-types"></a>Podporované typy účtu

Typy účtů podporované v desktopové aplikaci závisí na prostředí, které chcete rozsvítit. Z důvodu tohoto vztahu podporované typy účtů závisí na toky, které chcete použít.

### <a name="audience-for-interactive-token-acquisition"></a>Cílová skupina pro interaktivní pořízení tokenu

Pokud vaše desktopová aplikace používá interaktivní ověřování, můžete se přihlásit k uživatelům z libovolného [typu účtu](quickstart-register-app.md#register-a-new-application-using-the-azure-portal).

### <a name="audience-for-desktop-app-silent-flows"></a>Cílová skupina pro tiché toky desktopových aplikací

- Chcete-li použít integrované ověřování systému Windows nebo uživatelské jméno a heslo, musí vaše aplikace přihlásit uživatele ve vlastním tenantovi, například pokud jste vývojář obchodního podniku (LOB). Nebo v organizacích Azure Active Directory, vaše aplikace potřebuje přihlásit uživatele ve vašem vlastním tenantovi, pokud se jedná o scénář ISV. Tyto toky ověřování nejsou podporovány pro osobní účty Microsoft.
- Pokud chcete používat tok kódu zařízení, nemůžete ještě přihlašovat uživatele pomocí jejich osobních účtů Microsoft.
- Pokud přihlašujete uživatele pomocí sociálních identit, které projdou autoritou a zásadami "Business-to-commerce) (Business To-Commerce), můžete použít pouze interaktivní ověřování a ověřování pomocí uživatelského jména a hesla.

## <a name="redirect-uris"></a>Přesměrovat identifikátory URI

Identifikátory URI přesměrování, které se mají používat v desktopové aplikaci, závisí na toku, který chcete použít.

- Pokud používáte interaktivní ověřování nebo `https://login.microsoftonline.com/common/oauth2/nativeclient`tok kódu zařízení, použijte . Chcete-li dosáhnout této konfigurace, vyberte odpovídající adresu URL v části **Ověřování** pro vaši aplikaci.
  
  > [!IMPORTANT]
  > Dnes MSAL.NET používá ve výchozím nastavení jiný identifikátor URI`urn:ietf:wg:oauth:2.0:oob`přesměrování v aplikacích klasické pracovní plochy spuštěné v systému Windows ( ). V budoucnu budeme chtít toto výchozí nastavení změnit, `https://login.microsoftonline.com/common/oauth2/nativeclient`proto doporučujeme použít program .

- Pokud vytváříte nativní aplikaci Objective-C nebo Swift pro macOS, zaregistrujte identifikátor URI přesměrování na základě identifikátoru sady vaší aplikace v následujícím formátu: msauth.<your.app.bundle.id>://auth. Nahraďte <your.app.bundle.id> s identifikátorem sady.
- Pokud vaše aplikace používá jenom integrované ověřování systému Windows nebo uživatelské jméno a heslo, nemusíte registrovat identifikátor URI přesměrování pro vaši aplikaci. Tyto toky provést odezvu na platformě identit microsoftu v2.0 koncový bod. Vaše aplikace nebude volána zpět na žádné konkrétní identifikátor URI.
- Chcete-li rozlišit tok kódu zařízení, integrované ověřování systému Windows a uživatelské jméno a heslo od důvěrného toku klientské aplikace, který nemá ani rozhraní URI přesměrování (tok pověření klienta používaný v aplikacích daemon), musíte vyjádřit, že vaše aplikace je veřejná klientská aplikace. Chcete-li dosáhnout této konfigurace, přejděte k části **Ověřování** pro vaši aplikaci. V podčásti **Upřesnit nastavení** vyberte v odstavci **Výchozí typ klienta** možnost **Ano** pro **léčbu aplikace jako veřejného klienta**.

  ![Povolit veřejného klienta](media/scenarios/default-client-type.png)

## <a name="api-permissions"></a>Oprávnění rozhraní API

Aplikace klasické pracovní plochy volají rozhraní API pro přihlášeného uživatele. Musí požádat o delegovaná oprávnění. Nemohou požadovat oprávnění aplikací, které jsou zpracovávány pouze v [aplikacích daemon](scenario-daemon-overview.md).

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Aplikace Pro stolní počítače: Konfigurace aplikace](scenario-desktop-app-configuration.md)
