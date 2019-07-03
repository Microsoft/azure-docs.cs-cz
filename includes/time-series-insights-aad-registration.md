---
title: zahrnout soubor
description: zahrnout soubor
ms.topic: include
ms.custom: include file
services: time-series-insights
ms.service: time-series-insights
author: kingdomofends
ms.author: adgera
ms.date: 07/02/2019
ms.openlocfilehash: a463e3cf475909c34054717460dc10dbba4ad8f0
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543967"
---
> [!IMPORTANT]
> * Nové **Azure Active Directory** > **registrace aplikací** okno nahrazuje starší **Azure Active Directory** > **aplikace registrace (starší verze)** okno. května 2019.
> * Registrace aplikací vytvořili nebo zobrazí v okně starší verzi se automaticky zobrazí v novém okně.
> * Úplné informace o migraci do nového prostředí registrace aplikace Azure, najdete v článku [registrace aplikace Azure, školení průvodce](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide) a [Azure Active Directory pro rychlý Start](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. V [webu Azure portal](https://ms.portal.azure.com/)vyberte **Azure Active Directory** > **registrace aplikací** > **registrace nové**.

   [![Registrace nové aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-new-application-registration.png)](media/time-series-insights-aad-registration/active-directory-new-application-registration.png#lightbox)

   > [!TIP]
   > Nový panel registrace aplikace Azure Active Directory vám umožní filtrovat zobrazené aplikace tak, že vyberete **ve vlastnictví aplikace**.

    Vaše aplikace budou zde uvedené, až dokončíte registraci.

1. Dejte aplikaci název a vyberte **účty v tomto adresáři organizace jenom** zadat **podporovaných typů účtu** , který může přistupovat k rozhraní API. Vyberte platný identifikátor URI pro přesměrování uživatelům po ověřují, pak **zaregistrovat**.

   [![Vytvoření aplikace v Azure Active Directory](media/time-series-insights-aad-registration/active-directory-registration.png)](media/time-series-insights-aad-registration/active-directory-registration.png#lightbox)

1. Důležité informace o aplikaci Azure Active Directory se zobrazí v seznamu aplikací **přehled** okno. Vyberte aplikaci v rámci **ve vlastnictví aplikace**, pak **přehled**.

   [![Zkopírujte ID aplikace](media/time-series-insights-aad-registration/active-directory-copy-application-id.png)](media/time-series-insights-aad-registration/active-directory-copy-application-id.png#lightbox)

   Kopii vaší **ID aplikace (klient)** používat v klientské aplikaci.

1. **Ověřování** okno určuje nastavení konfigurace důležité ověřování. 

    1. **Identifikátory URI pro přesměrování** musí shodovat s adresou poskytl požadavek na ověření:

        * Aplikace hostované v místním vývojovém prostředí, vyberte **veřejným klientem (mobilních a desktopových)** . Nezapomeňte nastavit **výchozí typ klienta** na Ano.
        * Jednostránkové aplikace hostované ve službě Azure App Service, vyberte **webové**.

    1. Povolit implicitní grant tok tak, že zkontrolujete **tokeny typu ID**.

   [![Vytvořit nový tajný kód klienta](media/time-series-insights-aad-registration/active-directory-auth-blade.png)](media/time-series-insights-aad-registration/active-directory-auth-blade.png#lightbox)

   Klikněte na **Uložit**.

1. Přidružte vaše aplikace Azure Active Directory Azure TIme Series Insights. Vyberte **oprávnění k rozhraní API** > **přidat oprávnění** > **naše organizace bude využívat rozhraní API**. 

    [![Rozhraní API přidružit k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Typ `Azure Time Series Insights` do vyhledávání panelu vyberte `Azure Time Series Insights`.

1. Dále určete typ oprávnění rozhraní API, které vaše aplikace vyžaduje. Ve výchozím nastavení **delegovaná oprávnění** budou zvýrazněny. Pak zvolte typ oprávnění, vyberte **přidat oprávnění**.

    [![Zadejte typ oprávnění rozhraní API, které vaše aplikace vyžaduje](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)