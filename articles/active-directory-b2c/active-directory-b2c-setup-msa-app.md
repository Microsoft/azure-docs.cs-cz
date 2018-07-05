---
title: Konfigurace účtu Microsoftu v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zadejte registrace a přihlášení pro uživatele s účty Microsoft ve svých aplikacích, které jsou zabezpečené pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c788b14a99125a208390cd4f8ead338efed06933
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37444163"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-microsoft-accounts"></a>Azure Active Directory B2C: Registrace a poskytněte přihlášení uživatelé s účty Microsoft
## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft
Pokud chcete použít účet Microsoft jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci účtu Microsoft a zadejte správné parametry. Budete potřebovat účet Microsoft k tomu. Pokud ho nemáte, můžete získat na [ https://www.live.com/ ](https://www.live.com/).

1. Přejděte [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) a přihlaste se pomocí přihlašovacích údajů účtu Microsoft.
2. Klikněte na tlačítko **přidat aplikaci**.
   
    ![Microsoft account – přidání nové aplikace](./media/active-directory-b2c-setup-msa-app/msa-add-new-app.png)
3. Zadejte **název** pro vaši aplikaci a klikněte na **vytvořit aplikaci**.
   
    ![Účet Microsoft – název aplikace](./media/active-directory-b2c-setup-msa-app/msa-app-name.png)
4. Zkopírujte hodnotu **Id aplikace**. Budete potřebovat a nakonfigurovat účet Microsoft jako zprostředkovatele identity ve vašem tenantovi.
   
    ![Účet Microsoft – Id aplikace](./media/active-directory-b2c-setup-msa-app/msa-app-id.png)
5. Klikněte na **přidat platformy** a zvolte **webové**.
   
    ![Microsoft account - přidat platformu](./media/active-directory-b2c-setup-msa-app/msa-add-platform.png)
   
    ![Účet Microsoft – Web](./media/active-directory-b2c-setup-msa-app/msa-web.png)
6. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **identifikátory URI přesměrování** pole. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com).
   
    ![Účet Microsoft – adresy URL pro přesměrování](./media/active-directory-b2c-setup-msa-app/msa-redirect-url.png)
7. Klikněte na **generovat nové heslo** pod **tajných klíčů aplikací** oddílu. Kopírovat nové heslo, zobrazí na obrazovce. Budete potřebovat a nakonfigurovat účet Microsoft jako zprostředkovatele identity ve vašem tenantovi. Toto heslo je důležitým bezpečnostním pověřením.
   
    ![Microsoft account – vygenerování nového hesla](./media/active-directory-b2c-setup-msa-app/msa-generate-new-password.png)
   
    ![Účet Microsoft - nové heslo](./media/active-directory-b2c-setup-msa-app/msa-new-password.png)
8. Zaškrtněte políčko s textem **podpora Live SDK** pod **rozšířené možnosti** oddílu. Klikněte na **Uložit**.
   
    ![Účet Microsoft – podpora Live SDK](./media/active-directory-b2c-setup-msa-app/msa-live-sdk-support.png)

## <a name="configure-microsoft-account-as-an-identity-provider-in-your-tenant"></a>Nakonfigurujte účet Microsoft jako zprostředkovatele identity ve vašem tenantovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure portal.
2. V okně funkcí B2C, klikněte na **zprostředkovatelé Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "MSA".
5. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **účtu Microsoft**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity** a zadejte Id aplikace a heslo aplikace účtu Microsoft, který jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci vašeho účtu Microsoft.

