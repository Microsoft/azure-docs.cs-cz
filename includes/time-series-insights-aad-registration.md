---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.date: 10/02/2020
ms.openlocfilehash: 7de4dc21391f7dbd817c56ce51606a808cf9e3c4
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2020
ms.locfileid: "91665803"
---
1. V [Azure Portal](https://ms.portal.azure.com/)vyberte **Azure Active Directory**  >  **Registrace aplikací**  >  **nové registrace**.

   [![Registrace nové aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Vaše aplikace se tady zobrazí po registraci.

1. Dejte aplikaci název a vyberte **účty v tomto organizačním adresáři jenom** k určení **podporovaných typů účtů** , které můžou mít přístup k rozhraní API. Pokud vytváříte [veřejnou klientskou aplikaci](https://docs.microsoft.com/azure/active-directory/develop/msal-client-application-configuration#redirect-uri), přidejte platný identifikátor URI přesměrování a pak ho **Zaregistrujte**.

   [![Vytvoření aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Důležité informace o Azure Active Directory aplikace se zobrazí v okně **Přehled** vaší aplikace v seznamu. Vyberte svou aplikaci v části **vlastněné aplikace**a pak klikněte na **Přehled**.

   [![Kopírovat ID aplikace](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Zkopírujte **ID aplikace (klienta)** pro použití v klientské aplikaci.

1. Okno **ověřování** určuje nastavení konfigurace pro ověřování.

    1. Přidejte **identifikátory URI pro přesměrování** a nakonfigurujte **přístupové tokeny** tak, že vyberete **+ Přidat platformu**.

    1. Určete, jestli je aplikace **veřejným klientem** , nebo ne, a to výběrem možnosti **Ano** nebo **ne**.

    1. Ověřte, které účty a klienti jsou podporované.

    [![Konfigurace implicitního udělení](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Po výběru příslušné platformy nakonfigurujte **identifikátory URI přesměrování** a **přístupové tokeny** na bočním panelu napravo od uživatelského rozhraní.

    1. **Identifikátory URI pro přesměrování** se musí shodovat s adresou zadanou požadavkem ověřování:

        * Pro aplikace hostované v místním vývojovém prostředí vyberte možnost **veřejný klient (mobilní & Desktop)**. Nezapomeňte nastavit **veřejného klienta** na **Ano**.
        * V případě aplikací s jednou stránkou hostovaných v Azure App Service vyberte **Web**.

    1. Určete, zda je **Adresa URL pro odhlášení** vhodná.

    1. Povolte tok implicitního udělení kontrolou **přístupových tokenů** nebo **tokenů ID**.

    [![Vytváření identifikátorů URI pro přesměrování](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klikněte na **Konfigurovat**a pak na **Uložit**.

1. Vyberte **certifikáty & tajných klíčů** a **nový tajný klíč klienta** k vytvoření hesla aplikace, které může klientská aplikace použít k prokázání jeho identity.

   [![Vytvoří nový tajný klíč klienta.](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Zobrazí se vaše tajné heslo klienta. Zkopírujte klíč do svého oblíbeného textového editoru.

   > [!NOTE]
   > Místo toho máte možnost importovat certifikát. Pro rozšířené zabezpečení se doporučuje certifikát. Chcete-li použít certifikát, vyberte **Odeslat certifikát**.

1. Přidružte Azure Time Series Insights aplikace Azure Active Directory. Vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**  >  **API moje organizace používá**.

    [![Přidružení rozhraní API k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Zadejte `Azure Time Series Insights` do panelu hledání a pak vyberte `Azure Time Series Insights` .

1. Dále zadejte oprávnění API typu, které vaše aplikace vyžaduje. Ve výchozím nastavení se zvýrazní **delegovaná oprávnění** . Zvolte typ oprávnění a pak vyberte **Přidat oprávnění**.

    [![Zadejte druh oprávnění API, které vaše aplikace vyžaduje.](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
