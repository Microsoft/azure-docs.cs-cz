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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77029094"
---
>[!NOTE]
>Tato část obsahuje pokyny pro [registraci aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app).

1. Na [webu Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** z rozbalitelné levé nabídky a potom otevřete podokno Registrace **aplikací.** 

    [![Výběr podokna Služby Azure Active Directory](./media/digital-twins-permissions/azure-portal-select-aad-pane.png)](./media/digital-twins-permissions/azure-portal-select-aad-pane.png#lightbox)

1. Vyberte tlačítko **+ Nová registrace.**

    [![Vybrat tlačítko Nová registrace](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Do pole **Název** uveďte popisný název pro registraci této aplikace. 

    1. V části **Přesměrování identifikátoru URI (volitelné)** zadejte `https://microsoft.com` do textového pole.     

    1. Ověřte, které účty a klienti jsou podporovány vaší aplikací Azure Active Directory.

    1. Vyberte **Zaregistrovat**.

    [![Vytvořit podokno](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Okno **Ověřování** určuje důležitá nastavení konfigurace ověřování. 

    1. Přidejte **identifikátory URI přesměrování** a nakonfigurujte **přístupové tokeny** výběrem **možnosti + Přidat platformu**.

    1. Výběrem **možnosti Ano** určete, že aplikace je **veřejným klientem**.

    1. Ověřte, které účty a klienti jsou podporovány vaší aplikací Azure Active Directory.

    [![Nastavení konfigurace veřejného klienta](./media/digital-twins-permissions/aad-configure-public-client.png)](./media/digital-twins-permissions/aad-configure-public-client.png#lightbox)

1. Po výběru příslušné platformy nakonfigurujte **identifikátory URI přesměrování** a **přístupové tokeny** na bočním panelu napravo od uživatelského rozhraní.

    1. **Přesměrování identifikátorů URI** se musí shodovat s adresou zadanou požadavkem na ověření:

        * U aplikací hostovaných v místním vývojovém prostředí vyberte **možnost Veřejný klient (mobilní & stolní počítače).** Ujistěte se, že nastavit **veřejného klienta** na **Ano**.
        * Pro jednostránkové aplikace hostované ve službě Azure App Service vyberte **Web**.

    1. Určete, zda je vhodná **adresa URL odhlášení.**

    1. Povolte implicitní tok udělení zaškrtnutím **tokenů aplikace Access** nebo **tokenů ID**.
                
    [![Konfigurace identifikátorů URI přesměrování](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png)](./media/digital-twins-permissions/aad-app-configure-redirect-uris.png#lightbox)

    Klepněte na tlačítko **Konfigurovat**a potom **na tlačítko Uložit**.

1.  Otevřete podokno **Přehled** registrované aplikace a zkopírujte hodnoty následujících entit do dočasného souboru. Tyto hodnoty použijete ke konfiguraci ukázkové aplikace v následujících částech.

    - **ID aplikace (klienta)**
    - **ID adresáře (tenanta)**

    [![ID aplikace Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Otevřete podokno **oprávnění rozhraní API** pro registraci aplikace. Vyberte + Přidat tlačítko **oprávnění.** V podokně **Oprávnění rozhraní API vyberte** **kartu Rozhraní API, která moje organizace používá,** a vyhledejte jednu z následujících možností:
    
    1. `Azure Digital Twins`. Vyberte rozhraní **API pro digitální dvojčata Azure.**

        [![Rozhraní API pro vyhledávání nebo digitální dvojčata Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Případně vyhledejte `Azure Smart Spaces Service`. Vyberte rozhraní **API služby Azure Smart Spaces** Service.

        [![Hledání rozhraní API pro inteligentní prostory Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Název a ID rozhraní API Azure AD, které se zobrazí, závisí na vašem tenantovi:
    > * Testovací klienta a účty `Azure Digital Twins`zákazníků by měl y hledat .
    > * Ostatní účty Microsoft `Azure Smart Spaces Service`by měly vyhledat .

1. Buď rozhraní API se po výběru zobrazí jako **digitální dvojčata Azure** ve stejném podokně **oprávnění rozhraní API požadavků.** Vyberte rozevírací políčko **Číst** a zaškrtněte políčko **Číst.Write.** Vyberte tlačítko **Přidat oprávnění.**

    [![Přidání oprávnění rozhraní API](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. V závislosti na nastavení vaší organizace může být nutné podniknout další kroky k udělení přístupu správce k tomuto rozhraní API. Další informace získáte od správce. Po schválení přístupu správce zobrazí váš přístup v podokně oprávnění rozhraní API sloupec **Požadovaný souhlas správce** v podokně oprávnění rozhraní **API.** 

    [![Schválení souhlasu správce](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

    Ověřte, že se zobrazí **Azure Digital Twins.**
