---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 11/11/2019
ms.custom: include file
ms.openlocfilehash: 832c0e6080b82f3c38beaf051669fbdacd37081c
ms.sourcegitcommit: a170b69b592e6e7e5cc816dabc0246f97897cb0c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/14/2019
ms.locfileid: "74100825"
---
>[!NOTE]
>V této části najdete pokyny k [registraci aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v nabídce rozbalitelné vlevo a otevřete podokno **Registrace aplikací** . 

    [![výběr podokna Azure Active Directory](./media/digital-twins-permissions/select-aad-pane.png)](./media/digital-twins-permissions/select-aad-pane.png#lightbox)

1. Klikněte na tlačítko **+ Nová registrace** .

    [![klikněte na tlačítko Nová registrace.](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Zadejte popisný název pro registraci této aplikace v poli **název** . V části **identifikátor URI přesměrování (volitelné)** vyberte v rozevírací nabídce na levé straně možnost **veřejný klient/nativní (mobilní & plocha)** a do textového pole napravo zadejte `https://microsoft.com`. Vyberte **Zaregistrovat**.

    [![vytvořit podokno](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Abyste se ujistili, že [je aplikace zaregistrovaná jako **veřejný klient**](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), otevřete podokno **ověřování** pro registraci vaší aplikace a posuňte se v tomto podokně dolů. V části **výchozí typ klienta** zvolte možnost **Ano** pro **považovat aplikaci za veřejného klienta**a stiskněte **Uložit**.

    Ověřte **přístupové tokeny** a povolte nastavení **oauth2AllowImplicitFlow** v manifestu. JSON.

    [![nastavení konfigurace veřejného klienta](./media/digital-twins-permissions/aad-public-client.png)](./media/digital-twins-permissions/aad-public-client.png#lightbox)

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

1. Rozhraní API po výběru se zobrazí jako **digitální vlákna Azure** ve stejném PODOKNĚ **oprávnění API pro žádosti** . Vyberte možnost **číst** rozevírací seznam a potom zaškrtněte políčko **číst. zapsat** . Klikněte na tlačítko **Přidat oprávnění** .

    [![Přidání oprávnění rozhraní API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. V závislosti na nastaveních vaší organizace možná budete muset provést další kroky, abyste tomuto rozhraní API udělili přístup správce. Pro další informace se obraťte na správce. Po schválení přístupu správce se v podokně **oprávnění API** zobrazí sloupec **požadováno souhlasu správce** , ve kterém se zobrazí vaše oprávnění. 

    [schválení souhlasu správce ![](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Ověřte, že se zobrazí **digitální vlákna Azure** .
