---
title: Konfigurace Amazon v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty Amazon ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: e3b3d66b913b595e68c03b68990d1a4806952579
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443694"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení pro uživatele s účty Amazon
## <a name="create-an-amazon-application"></a>Vytvoření aplikace Amazon
Použití Amazon jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci Amazon a zadejte správné parametry. Budete potřebovat k tomu účtu Amazon. Pokud ho nemáte, můžete získat na [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Přejděte [středisko pro vývojáře Amazon](https://login.amazon.com/) a přihlaste se pomocí přihlašovacích údajů k účtu Amazon.
2. Pokud jste tak již neučinili, klikněte na tlačítko **zaregistrovat**, postupujte podle kroků registrace pro vývojáře a přijměte zásady.
3. Klikněte na tlačítko **registrace nové aplikace**.
   
    ![Registrace nové aplikace na webu Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Zadejte informace o aplikaci (**název**, **popis**, a **URL oznámení o ochraně osobních údajů**) a klikněte na tlačítko **Uložit**.
   
    ![Poskytuje informace o aplikaci pro registraci nové aplikace na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. V **nastavení webu** tématu, zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. (Budete muset kliknout na **zobrazit tajný kód** toto tlačítko.) Je nutné jejich konfigurace Amazon jako zprostředkovatele identity ve vašem tenantovi. Klikněte na tlačítko **upravit** v dolní části. **Tajný kód klienta** je důležitým bezpečnostním pověřením.
   
    ![Poskytuje ID klienta a tajný kód klienta pro novou aplikaci na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Zadejte `https://login.microsoftonline.com` v **povolený JavaScript zdroje** pole a `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **povolené adresy URL vrátit** pole. Nahraďte **{klient}** s názvem vašeho tenanta (například contoso.onmicrosoft.com). Klikněte na **Uložit**. **{Klient}** hodnota je velká a malá písmena.
   
    ![Poskytuje zdroje jazyka JavaScript a vrátí adresy URL pro novou aplikaci na Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Konfigurace Amazon jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "Amzn".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Amazon**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta a tajný kód klienta aplikace Amazon, který jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Amazon.

