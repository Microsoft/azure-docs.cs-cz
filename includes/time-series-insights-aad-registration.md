---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 09/24/2019
ms.openlocfilehash: ccfbd16f4db770558f1bc0284860a5f8d9fb8b68
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266938"
---
> [!IMPORTANT]
> * Nové okno **Azure Active Directory** > **Registrace aplikací** nahrazuje starší **Azure Active Directory** > **Registrace aplikací (starší verze)** . 2019. května.
> * V novém okně se automaticky zobrazí Registrace aplikací vytvořené nebo zobrazené v okně starší verze.
> * Podrobné informace o migraci na nové prostředí pro registraci aplikací Azure najdete v [Průvodci školením pro azure registrace aplikací](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) a v článku [rychlý Start Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. V [Azure Portal](https://ms.portal.azure.com/)vyberte **Azure Active Directory** > **Registrace aplikací** > **nové registrace**.

   [![Registrace nové aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Nový panel registrace aplikace Azure Active Directory umožňuje filtrovat zobrazené aplikace tak, že vyberete **vlastní aplikace**.

    Vaše aplikace se tady zobrazí po registraci.

1. Dejte aplikaci název a vyberte **účty v tomto organizačním adresáři jenom** k určení **podporovaných typů účtů** , které můžou mít přístup k rozhraní API. Zvolte platný identifikátor URI pro přesměrování uživatelů na ověření a pak **Zaregistrujte**.

   [![Vytvoření aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Důležité informace o Azure Active Directory aplikace se zobrazí v okně **Přehled** vaší aplikace v seznamu. Vyberte svou aplikaci v části **vlastněné aplikace**a pak klikněte na **Přehled**.

   [![Kopírovat ID aplikace](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Zkopírujte **ID aplikace (klienta)** pro použití v klientské aplikaci.

1. Okno **ověřování** určuje nastavení konfigurace pro ověřování. 

    1. **Identifikátory URI pro přesměrování** se musí shodovat s adresou zadanou požadavkem ověřování:

        * Pro aplikace hostované v místním vývojovém prostředí vyberte možnost **veřejný klient (mobilní & Desktop)** . Nezapomeňte nastavit **výchozí typ klienta** na Ano.
        * V případě aplikací s jednou stránkou hostovaných v Azure App Service vyberte **Web**.

    1. Povolte tok implicitního udělení kontrolou **tokenů ID**.

   [![Vytvoří nový tajný klíč klienta.](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klikněte na **Uložit**.

1. Vyberte **certifikáty & tajných klíčů** a **nový tajný klíč klienta** k vytvoření hesla aplikace, které klient může použít k prokázání jeho identity.

   [![Vytvoří nový tajný klíč klienta.](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Zobrazí se vaše tajné heslo klienta. Zkopírujte klíč do svého oblíbeného textového editoru.

   > [!NOTE]
   > Místo toho máte možnost importovat certifikát. Pro rozšířené zabezpečení se doporučuje certifikát. Chcete-li použít certifikát, vyberte **Odeslat certifikát**.

1. Přidružte svoji aplikaci Azure Active Directory Azure TIme Series Insights.  > Vyberte **oprávnění rozhraní API** **Přidat oprávnění** > **API moje organizace používá**. 

    [![Přidružení rozhraní API k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Zadejte `Azure Time Series Insights` do panelu hledání a pak vyberte `Azure Time Series Insights`.

1. Dále zadejte oprávnění API typu, které vaše aplikace vyžaduje. Ve výchozím nastavení se zvýrazní **delegovaná oprávnění** . Zvolte typ oprávnění a pak vyberte **Přidat oprávnění**.

    [![Zadejte druh oprávnění API, které vaše aplikace vyžaduje.](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)