---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 08/12/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: fc2e2fc05de66de6f428e6b8ca7c94f82003ba2a
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2019
ms.locfileid: "69012082"
---
>[!NOTE]
>V této části najdete pokyny pro [novou registraci aplikace služby Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Pokud máte i nadále starší registraci nativních aplikací, můžete ji použít, pokud je podporovaná. Kromě toho, pokud z nějakého důvodu nový způsob registrace aplikace nefunguje v nastavení, můžete se pokusit vytvořit starší verzi nativní aplikace AAD. Další pokyny najdete v tématu [Registrace aplikace Azure Digital Revlákens pomocí Azure Active Directory starší verze](../articles/digital-twins/how-to-use-legacy-aad.md) . 

1. V [Azure Portal](https://portal.azure.com)otevřete **Azure Active Directory** v levém podokně a otevřete podokno **Registrace aplikací** . Klikněte na tlačítko **Nová registrace** .

    ![Registrovaná aplikace](./media/digital-twins-permissions/aad-app-register.png)

1. Zadejte popisný název pro registraci této aplikace v poli **název** . V části **identifikátor URI přesměrování (volitelné)** vyberte v rozevíracím seznamu na levé straně možnost **veřejný klient (mobilní & plocha)** a zadejte `https://microsoft.com` do textového pole vpravo. Vyberte **Zaregistrovat**.

    ![Vytvořit podokno](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Abyste se ujistili, že [je aplikace zaregistrovaná jako *nativní aplikace*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), otevřete podokno **ověřování** pro registraci vaší aplikace a posuňte se v tomto podokně dolů. V části **výchozí typ klienta** vyberte možnost **Ano** pro **považovat aplikaci za veřejného klienta**. 

    ![Výchozí nativní](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Otevřete podokno **Přehled** vaší registrované aplikace a zkopírujte hodnoty následujících entit do dočasného souboru. Tyto hodnoty použijete ke konfiguraci ukázkové aplikace v následujících oddílech.

    - **ID aplikace (klienta)**
    - **ID adresáře (tenant)**

    ![ID aplikace Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Otevřete podokno **oprávnění rozhraní API** pro registraci vaší aplikace. Vyberte tlačítko **Přidat oprávnění** . V podokně **oprávnění API pro vyžádání** vyberte **rozhraní API moje organizace používá** kartu a pak vyhledejte:
    
    1. **Digitální vlákna Azure**. Vyberte rozhraní API **digitálních vláken Azure** .

        ![Prohledat rozhraní API nebo digitální vlákna Azure](./media/digital-twins-permissions/aad-aap-search-api-dt.png)

    1. Případně můžete vyhledat **inteligentní prostory Azure**. Vyberte rozhraní API **služby Azure Smart Spaces** .

        ![Vyhledat rozhraní API pro inteligentní prostory Azure](./media/digital-twins-permissions/aad-app-search-api.png)

    > [!NOTE]
    > Přesný název, který se zobrazí při hledání, se může lišit v závislosti na tom, do kterého tenanta Azure patříte.

1. Vybrané rozhraní API se zobrazí jako **digitální vlákna Azure** ve stejném PODOKNĚ **oprávnění API pro žádosti** . Vyberte rozevírací seznam **načíst (1)** a potom zaškrtněte políčko **číst. zapsat** . Klikněte na tlačítko **Přidat oprávnění** .

    ![Přidat oprávnění rozhraní API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. V závislosti na nastaveních vaší organizace možná budete muset provést další kroky, abyste tomuto rozhraní API udělili přístup správce. Pro další informace se obraťte na správce. Po schválení přístupu správce se sloupec **požadováno souhlasu správce** v podokně **oprávnění API** zobrazí jako pro vaše rozhraní API podobně jako u následujících:

    ![Přidat oprávnění rozhraní API](./media/digital-twins-permissions/aad-app-admin-consent.png)

