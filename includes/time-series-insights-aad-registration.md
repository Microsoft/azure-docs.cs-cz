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
ms.date: 02/03/2020
ms.openlocfilehash: 5be6e7937a6e1f710b8e2576a9058963413fb6c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76984529"
---
1. Na [webu Azure Portal](https://ms.portal.azure.com/)vyberte**Registrace** > aplikací **Azure Active Directory** > **Nová registrace**.

   [![Registrace nové aplikace ve službě Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

    Vaše aplikace bude uvedena zde po registraci.

1. Pojmenujte aplikaci a **vyberte účty v tomto organizačním adresáři, abyste** **určili pouze podporované typy účtů,** které mohou přistupovat k rozhraní API. Zvolte platný identifikátor URI, na který chcete uživatele přesměrovat po jejich ověření, a **pak register**.

   [![Vytvoření aplikace ve službě Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Důležité informace o aplikaci Azure Active Directory se zobrazují v okně **Přehled** uvedené aplikace. Vyberte aplikaci v části **Vlastněné aplikace**a pak **Přehled**.

   [![Zkopírovat ID aplikace](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Zkopírujte **ID aplikace (klienta)** pro použití v klientské aplikaci.

1. Okno **Ověřování** určuje důležitá nastavení konfigurace ověřování. 

    1. Přidejte **identifikátory URI přesměrování** a nakonfigurujte **přístupové tokeny** výběrem **možnosti + Přidat platformu**.

    1. Výběrem možnosti **Ano** nebo **Ne**zjistěte, jestli je aplikace **veřejným klientem** nebo ne.

    1. Ověřte, které účty a klienti jsou podporovány.

    [![Konfigurovat implicitní udělení](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

1. Po výběru příslušné platformy nakonfigurujte **identifikátory URI přesměrování** a **přístupové tokeny** na bočním panelu napravo od uživatelského rozhraní.

    1. **Přesměrování identifikátorů URI** se musí shodovat s adresou zadanou požadavkem na ověření:

        * U aplikací hostovaných v místním vývojovém prostředí vyberte **možnost Veřejný klient (mobilní & stolní počítače).** Ujistěte se, že nastavit **veřejného klienta** na **Ano**.
        * Pro jednostránkové aplikace hostované ve službě Azure App Service vyberte **Web**.

    1. Určete, zda je vhodná **adresa URL odhlášení.**

    1. Povolte implicitní tok udělení zaškrtnutím **tokenů aplikace Access** nebo **tokenů ID**.

    [![Vytvořit identifikátory URI přesměrování](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klepněte na tlačítko **Konfigurovat**a potom **na tlačítko Uložit**.

1. Vyberte **Certifikáty & tajných kódů** a pak **Nový tajný klíč klienta** k vytvoření hesla aplikace, které může klientská aplikace použít k prokázání své identity.

   [![Vytvoření nového tajného klíče klienta](media/time-series-insights-aad-registration/active-directory-application-keys-save.png)](media/time-series-insights-aad-registration/active-directory-application-keys-save.png#lightbox)

   Poté se zobrazí tajné heslo klienta. Zkopírujte klíč do svého oblíbeného textového editoru.

   > [!NOTE]
   > Místo toho můžete importovat certifikát. Pro lepší zabezpečení se doporučuje certifikát. Chcete-li certifikát použít, vyberte **možnost Odeslat certifikát**.

1. Přidružte aplikaci Azure Active Directory Azure TIme Series Insights. Vyberte **oprávnění** > rozhraní**API: : Přidání** > rozhraní**API oprávnění, která moje organizace používá**. 

    [![Přidružení rozhraní API k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Zadejte `Azure Time Series Insights` do vyhledávacího `Azure Time Series Insights`panelu a vyberte .

1. Dále zadejte druh oprávnění rozhraní API, které vaše aplikace vyžaduje. Ve výchozím nastavení budou **delegovaná oprávnění** zvýrazněna. Vyberte typ oprávnění a vyberte **Přidat oprávnění**.

    [![Určení oprávnění rozhraní API, které vaše aplikace vyžaduje](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)
