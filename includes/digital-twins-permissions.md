---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
ms.topic: include
ms.date: 09/30/2019
ms.custom: include file
ms.openlocfilehash: 880c2a811df7b4d41760676d4d6e0153e9384f38
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/04/2019
ms.locfileid: "71949761"
---
>[!NOTE]
>V této části najdete pokyny pro [novou registraci aplikace služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Pokud máte i nadále starší registraci nativních aplikací, můžete ji použít, pokud je podporovaná. Kromě toho, pokud z nějakého důvodu nový způsob registrace aplikace nefunguje v nastavení, můžete se pokusit vytvořit starší verzi nativní aplikace AAD. Další pokyny najdete v tématu [Registrace aplikace Azure Digital Revlákens pomocí Azure Active Directory starší verze](../articles/digital-twins/how-to-use-legacy-aad.md) . 

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **Registrace aplikací** . Klikněte na tlačítko **Nová registrace** .

    [zaregistrováno @no__t – 1App](./media/digital-twins-permissions/aad-app-register.png)](./media/digital-twins-permissions/aad-app-register.png#lightbox)

1. Zadejte popisný název pro registraci této aplikace v poli **název** . V části **identifikátor URI přesměrování (volitelné)** vyberte v rozevíracím seznamu na levé straně možnost **veřejný klient (mobilní & plocha)** a do textového pole napravo zadejte `https://microsoft.com`. Vyberte **Registrovat**.

    [@no__t – podokno 1Create](./media/digital-twins-permissions/aad-app-reg-create.png)](./media/digital-twins-permissions/aad-app-reg-create.png#lightbox)

1. Abyste se ujistili, že [je aplikace zaregistrovaná jako *nativní aplikace*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), otevřete podokno **ověřování** pro registraci vaší aplikace a posuňte se v tomto podokně dolů. V části **výchozí typ klienta** vyberte možnost **Ano** pro **považovat aplikaci za veřejného klienta**. 

    [@no__t – 1Default Native](./media/digital-twins-permissions/aad-app-default-native.png)](./media/digital-twins-permissions/aad-app-default-native.png#lightbox)

1.  Otevřete podokno **Přehled** vaší registrované aplikace a zkopírujte hodnoty následujících entit do dočasného souboru. Tyto hodnoty použijete ke konfiguraci ukázkové aplikace v následujících oddílech.

    - **ID aplikace (klienta)**
    - **ID adresáře (tenant)**

    [@no__t – ID aplikace služby Active Directory pro 1Azure](./media/digital-twins-permissions/aad-app-reg-app-id.png)](./media/digital-twins-permissions/aad-app-reg-app-id.png#lightbox)

1. Otevřete podokno **oprávnění rozhraní API** pro registraci vaší aplikace. Vyberte tlačítko **Přidat oprávnění** . V podokně **oprávnění API pro vyžádání** vyberte **rozhraní API moje organizace používá** kartu a pak vyhledejte:
    
    1. `Azure Digital Twins`. Vyberte rozhraní API **digitálních vláken Azure** .

        [@no__t – rozhraní 1Search API nebo digitální vlákna Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)](./media/digital-twins-permissions/aad-aap-search-api-dt.png#lightbox)

    1. Případně můžete vyhledat @no__t – 0. Vyberte rozhraní API **služby Azure Smart Spaces** .

        [@no__t – rozhraní 1Search API pro inteligentní prostory Azure](./media/digital-twins-permissions/aad-app-search-api.png)](./media/digital-twins-permissions/aad-app-search-api.png#lightbox)

    > [!IMPORTANT]
    > Název a ID rozhraní API služby Azure AD, které se zobrazí, závisí na vašem tenantovi:
    > * Testovací klient a účty zákazníka by měli hledat `Azure Digital Twins`.
    > * Jiné účty Microsoft by měly hledat `Azure Smart Spaces Service`.

1. Vybrané rozhraní API se zobrazí jako **digitální vlákna Azure** ve stejném PODOKNĚ **oprávnění API pro žádosti** . Vyberte rozevírací seznam **načíst (1)** a potom zaškrtněte políčko **číst. zapsat** . Klikněte na tlačítko **Přidat oprávnění** .

    [@no__t – oprávnění rozhraní API pro 1Add](./media/digital-twins-permissions/aad-app-req-permissions.png)](./media/digital-twins-permissions/aad-app-req-permissions.png#lightbox)

1. V závislosti na nastaveních vaší organizace možná budete muset provést další kroky, abyste tomuto rozhraní API udělili přístup správce. Pro další informace se obraťte na správce. Po schválení přístupu správce se sloupec **požadováno souhlasu správce** v podokně **oprávnění API** zobrazí jako pro vaše rozhraní API podobně jako u následujících:

    [@no__t – oprávnění rozhraní API pro 1Add](./media/digital-twins-permissions/aad-app-admin-consent.png)](./media/digital-twins-permissions/aad-app-admin-consent.png#lightbox)

