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
ms.openlocfilehash: 0ce9575f078058c821ffffe1b9fe45eed5a4ad94
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101724165"
---
* Po výběru příslušné platformy v kroku 4 Konfigurace nastavení [platformy](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#configure-platform-settings) nakonfigurujte **identifikátory URI přesměrování** a **přístupové tokeny** na bočním panelu napravo od uživatelského rozhraní.

    * **Identifikátory URI pro přesměrování** se musí shodovat s adresou zadanou požadavkem ověřování:

        * Pro aplikace hostované v místním vývojovém prostředí vyberte možnost **veřejný klient (mobilní & Desktop)**. Nezapomeňte nastavit **veřejného klienta** na **Ano**.
        * U aplikací Single-Page hostovaných v Azure App Service vyberte **Web**.

    * Určete, zda je **Adresa URL pro odhlášení** vhodná.

    * Povolte tok implicitního udělení kontrolou **přístupových tokenů** nebo **tokenů ID**.

    [![Vytváření identifikátorů URI pro přesměrování](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png)](media/time-series-insights-aad-registration/active-directory-auth-redirect-uri.png#lightbox)

    Klikněte na **Konfigurovat** a pak na **Uložit**.

* Přidružte Azure Time Series Insights aplikace Azure Active Directory. Vyberte **oprávnění rozhraní API**  >  **Přidat oprávnění**  >  **API moje organizace používá**.

    [![Přidružení rozhraní API k aplikaci Azure Active Directory](media/time-series-insights-aad-registration/active-directory-app-api-permission.png)](media/time-series-insights-aad-registration/active-directory-app-api-permission.png#lightbox)

   Zadejte `Azure Time Series Insights` do panelu hledání a pak vyberte `Azure Time Series Insights` .

* Dále zadejte oprávnění API typu, které vaše aplikace vyžaduje. Ve výchozím nastavení se zvýrazní **delegovaná oprávnění** . Zvolte typ oprávnění a pak vyberte **Přidat oprávnění**.

    [![Zadejte druh oprávnění API, které vaše aplikace vyžaduje.](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png)](media/time-series-insights-aad-registration/active-directory-app-permission-grant.png#lightbox)

* [Přidejte přihlašovací údaje](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app#add-credentials) , pokud aplikace bude volat rozhraní API vašeho prostředí jako samotné. Přihlašovací údaje umožňují, aby se vaše aplikace ověřovala jako samotná a nevyžadovala žádné interakce od uživatele za běhu.