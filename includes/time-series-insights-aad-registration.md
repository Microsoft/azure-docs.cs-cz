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
ms.date: 12/06/2019
ms.openlocfilehash: 4d32980e825f12c76b5c8bf8df0673fa82065751
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75460419"
---
1. V [Azure Portal](https://ms.portal.azure.com/)vyberte **Azure Active Directory** > **Registrace aplikací** > **nové registrace**.

   [![registraci nové aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Vaše aplikace se tady zobrazí po registraci.

1. Dejte aplikaci název a vyberte **účty v tomto organizačním adresáři jenom** k určení **podporovaných typů účtů** , které můžou mít přístup k rozhraní API. Zvolte platný identifikátor URI pro přesměrování uživatelů na ověření a pak **Zaregistrujte**.

   [![vytvořit aplikaci v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Důležité informace o Azure Active Directory aplikace se zobrazí v okně **Přehled** vaší aplikace v seznamu. Vyberte svou aplikaci v části **vlastněné aplikace**a pak klikněte na **Přehled**.

   [![zkopírovat ID aplikace](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Zkopírujte **ID aplikace (klienta)** pro použití v klientské aplikaci.

1. Okno **ověřování** určuje nastavení konfigurace pro ověřování. 

    1. **Identifikátory URI pro přesměrování** se musí shodovat s adresou zadanou požadavkem ověřování:

        * Pro aplikace hostované v místním vývojovém prostředí vyberte možnost **veřejný klient (mobilní & Desktop)** . Nezapomeňte nastavit **výchozí typ klienta** na Ano.
        * V případě aplikací s jednou stránkou hostovaných v Azure App Service vyberte **Web**.

    1. Povolte tok implicitního udělení kontrolou **přístupových tokenů** nebo **tokenů ID**.

   [![vytváření identifikátorů URI pro přesměrování a konfigurace implicitního udělení](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klikněte na možnost **Uložit**.

1. Vyberte **certifikáty & tajných klíčů** a **nový tajný klíč klienta** k vytvoření hesla aplikace, které klient může použít k prokázání jeho identity.

   [![vytvoření nového tajného klíče klienta](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Zobrazí se vaše tajné heslo klienta. Zkopírujte klíč do svého oblíbeného textového editoru.

   > [!NOTE]
   > Místo toho máte možnost importovat certifikát. Pro rozšířené zabezpečení se doporučuje certifikát. Chcete-li použít certifikát, vyberte **Odeslat certifikát**.

1. Přidružte svoji aplikaci Azure Active Directory Azure TIme Series Insights. Vyberte **oprávnění rozhraní API** > **přidejte oprávnění** > **rozhraní API moje organizace používá**. 

    [![přidružení rozhraní API k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Do panelu hledání zadejte `Azure Time Series Insights` a pak vyberte `Azure Time Series Insights`.

1. Dále zadejte oprávnění API typu, které vaše aplikace vyžaduje. Ve výchozím nastavení se zvýrazní **delegovaná oprávnění** . Zvolte typ oprávnění a pak vyberte **Přidat oprávnění**.

    [![určit druh oprávnění API, které vaše aplikace vyžaduje](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)