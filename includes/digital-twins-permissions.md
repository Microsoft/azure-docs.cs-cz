---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 02/03/2020
ms.custom: include file
ms.openlocfilehash: cfe3eb4c0ac1378b7c519b3b34094945612d8508
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77029094"
---
>[!NOTE]
>V této části najdete pokyny k [registraci aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v nabídce rozbalitelné vlevo a otevřete podokno **Registrace aplikací** . 

    [![výběr podokna Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Klikněte na tlačítko **+ Nová registrace** .

    [![klikněte na tlačítko Nová registrace.](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Zadejte popisný název pro registraci této aplikace v poli **název** . 

    1. V části **URI přesměrování (volitelné)** zadejte do textového pole `https://microsoft.com`.     

    1. Ověřte, které účty a klienti podporuje vaše aplikace Azure Active Directory.

    1. Vyberte **Zaregistrovat**.

    [![vytvořit podokno](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Okno **ověřování** určuje nastavení konfigurace pro ověřování. 

    1. Přidejte **identifikátory URI pro přesměrování** a nakonfigurujte **přístupové tokeny** tak, že vyberete **+ Přidat platformu**.

    1. Vyberte **Ano** , pokud chcete určit, že aplikace je **veřejným klientem**.

    1. Ověřte, které účty a klienti podporuje vaše aplikace Azure Active Directory.

    [![nastavení konfigurace veřejného klienta](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Po výběru příslušné platformy nakonfigurujte **identifikátory URI přesměrování** a **přístupové tokeny** na bočním panelu napravo od uživatelského rozhraní.

    1. **Identifikátory URI pro přesměrování** se musí shodovat s adresou zadanou požadavkem ověřování:

        * Pro aplikace hostované v místním vývojovém prostředí vyberte možnost **veřejný klient (mobilní & Desktop)** . Nezapomeňte nastavit **veřejného klienta** na **Ano**.
        * V případě aplikací s jednou stránkou hostovaných v Azure App Service vyberte **Web**.

    1. Určete, zda je **Adresa URL pro odhlášení** vhodná.

    1. Povolte tok implicitního udělení kontrolou **přístupových tokenů** nebo **tokenů ID**.
                
    [![konfigurace identifikátorů URI pro přesměrování](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klikněte na **Konfigurovat**a pak na **Uložit**.

1.  Otevřete podokno **Přehled** vaší registrované aplikace a zkopírujte hodnoty následujících entit do dočasného souboru. Tyto hodnoty použijete ke konfiguraci ukázkové aplikace v následujících oddílech.

    - **ID aplikace (klienta)**
    - **ID adresáře (tenant)**

    [ID aplikace ![Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Otevřete podokno **oprávnění rozhraní API** pro registraci vaší aplikace. Klikněte na tlačítko **+ Přidat oprávnění** . V podokně **oprávnění API pro vyžádání** vyberte **rozhraní API moje organizace používá** kartu a pak vyhledejte jednu z následujících možností:
    
    1. `Azure Digital Twins`. Vyberte rozhraní API **digitálních vláken Azure** .

        [rozhraní API pro vyhledávání ![nebo digitální vlákna Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Případně vyhledejte `Azure Smart Spaces Service`. Vyberte rozhraní API **služby Azure Smart Spaces** .

        [rozhraní API pro hledání ![pro inteligentní prostory Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Název a ID rozhraní API služby Azure AD, které se zobrazí, závisí na vašem tenantovi:
    > * Testovací klient a účty zákazníka by měli hledat `Azure Digital Twins`.
    > * Jiné účty Microsoft by měly hledat `Azure Smart Spaces Service`.

1. Po výběru se rozhraní API zobrazí jako **digitální vlákna Azure** ve stejném PODOKNĚ **oprávnění API pro žádosti** . Vyberte možnost **číst** rozevírací seznam a potom zaškrtněte políčko **číst. zapsat** . Klikněte na tlačítko **Přidat oprávnění** .

    [![Přidání oprávnění rozhraní API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. V závislosti na nastaveních vaší organizace možná budete muset provést další kroky, abyste tomuto rozhraní API udělili přístup správce. Pro další informace se obraťte na správce. Po schválení přístupu správce se v podokně **oprávnění API** zobrazí sloupec **požadováno souhlasu správce** , ve kterém se zobrazí vaše oprávnění. 

    [schválení souhlasu správce ![](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Ověřte, že se zobrazí **digitální vlákna Azure** .
