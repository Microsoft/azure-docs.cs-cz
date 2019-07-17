---
title: zahrnout soubor
description: zahrnout soubor
services: digital-twins
author: dsk-2015
ms.service: digital-twins
ms.topic: include
ms.date: 06/28/2019
ms.author: dkshir
ms.custom: include file
ms.openlocfilehash: 324f41055cf333081f308a3ff533ff7df6b33038
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "67479173"
---
>[!NOTE]
>Tato část obsahuje pokyny, jak [nové registrace aplikace Azure AD](https://docs.microsoft.com/azure/active-directory/develop/quickstart-register-app). Pokud máte starší verzi nativní aplikace pro registraci, můžete ji tak dlouho, dokud se podporuje. Kromě toho pokud z nějakého důvodu nový způsob registrační aplikace nefunguje v nastavení aplikace, může pokusu o vytvoření starší verze nativní aplikaci AAD. Čtení [registraci Azure digitální dvojče aplikace ve službě starší verze služby Azure Active Directory](../articles/digital-twins/how-to-use-legacy-aad.md) další pokyny. 

1. V [webu Azure portal](https://portal.azure.com), otevřete **Azure Active Directory** v levém podokně a pak otevřete **registrace aplikací** podokně. Vyberte **registrace nové** tlačítko.

    ![Zaregistrovanou aplikaci](./media/digital-twins-permissions/aad-app-register.png)

1. Zadejte popisný název pro tuto registraci aplikace v **název** pole. V části **identifikátor URI pro přesměrování (volitelné)** zvolte **veřejným klientem (mobilu)** v rozevíracím seznamu na levé straně a zadejte `https://microsoft.com` do textového pole na pravé straně. Vyberte **Zaregistrovat**.

    ![Vytvořit podokno](./media/digital-twins-permissions/aad-app-reg-create.png)

1. Abyste měli jistotu, že [aplikace je zaregistrovaný jako *nativní aplikaci*](https://docs.microsoft.com/azure/active-directory/develop/scenario-desktop-app-registration), otevřete **ověřování** podokno pro registraci aplikace a potom přejděte dolů v tomto podokně. V **výchozí typ klienta** zvolte **Ano** pro **považovat aplikace veřejným klientem**. 

    ![Výchozí nativní](./media/digital-twins-permissions/aad-app-default-native.png)

1.  Otevřít **přehled** podokně registrované aplikace a zkopírujte hodnoty následující entity do dočasného souboru. Tyto hodnoty použijete ke konfiguraci ukázkovou aplikaci v následujících částech.

    - **ID aplikace (klient)**
    - **ID adresáře (tenant)**

    ![ID aplikace Azure Active Directory](./media/digital-twins-permissions/aad-app-reg-app-id.png)

1. Otevřít **oprávnění k rozhraní API** podokno pro registraci vaší aplikace. Vyberte **přidat oprávnění** tlačítko. V **žádosti rozhraní API oprávnění** podokně, vyberte **naše organizace bude využívat rozhraní API** kartu a potom vyhledejte **inteligentní prostory Azure**. Vyberte **Azure Smart prostory služby** rozhraní API.

    ![Search API](./media/digital-twins-permissions/aad-app-search-api.png)

1. Vybrané rozhraní API se zobrazí jako **digitální dvojče Azure** ve stejném **žádosti rozhraní API oprávnění** podokně. Vyberte **pro čtení (1)** rozevírací seznam a vyberte **Read.Write** zaškrtávací políčko. Vyberte **přidat oprávnění** tlačítko.

    ![Přidání oprávnění pro rozhraní API](./media/digital-twins-permissions/aad-app-req-permissions.png)

1. V závislosti na nastavení vaší organizace může být nutné provést další kroky k udělení přístupu správce k tomuto rozhraní API. Další informace vám poskytne správce. Po schválení je přístup správce **správce souhlasit vyžaduje** sloupec **oprávnění k rozhraní API** podokně se zobrazí podobný následujícímu pro vaše rozhraní API:

    ![Přidání oprávnění pro rozhraní API](./media/digital-twins-permissions/aad-app-admin-consent.png)

