---
title: Konfigurace Amazon v Azure Active Directory B2C | Microsoft Docs
description: Zadejte registrace a přihlášení k příjemce s účty Amazon v aplikacích, které jsou zabezpečené službou Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 12/06/2016
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 9b3464e246a6b672362583ee4446a6146cc3b3d8
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2018
ms.locfileid: "34709746"
---
# <a name="azure-active-directory-b2c-provide-sign-up-and-sign-in-to-consumers-with-amazon-accounts"></a>Azure Active Directory B2C: Zadejte registrace a přihlášení k příjemce s účty Amazon
## <a name="create-an-amazon-application"></a>Vytvoření aplikace Amazon
Pokud chcete používat službu Amazon jako poskytovatel identit v Azure Active Directory (Azure AD) B2C, musíte k vytvoření aplikace Amazon a poskytne mu se správné parametry. Je nutné k tomu účtu Amazon. Pokud nemáte, můžete ho na získat [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Přejděte na [středisku pro vývojáře Amazon](https://login.amazon.com/) a přihlaste se pomocí přihlašovacích údajů účtu Amazon.
2. Pokud jste tak již neučinili, klikněte na tlačítko **zaregistrovat**, postupujte podle kroků registrace developer a přijměte zásady.
3. Klikněte na tlačítko **zaregistrujte novou aplikaci**.
   
    ![Registrace nové aplikace na webu Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-new-app.png)
4. Zadejte informace o aplikaci (**název**, **popis**, a **URL oznámení o ochraně osobních údajů**) a klikněte na tlačítko **Uložit**.
   
    ![Poskytuje informace o aplikaci pro registraci novou aplikaci v Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-register-app.png)
5. V **nastavení webové** část, zkopírujte hodnoty z **ID klienta** a **tajný klíč klienta**. (Je třeba kliknout na **zobrazit tajný klíč** tlačítko zobrazení tohoto.) Je nutné oba dva konfigurace Amazon jako zprostředkovatele identity ve vašem klientovi. Klikněte na tlačítko **upravit** v dolní části. **Tajný klíč klienta** je důležitým bezpečnostním pověřením.
   
    ![Poskytnutí ID klienta a tajný klíč klienta pro novou aplikaci v Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-client-secret.png)
6. Zadejte `https://login.microsoftonline.com` v **povolené zdroje JavaScript** pole a `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **povoleno vrátit adresy URL** pole. Nahraďte **{klient}** s názvem vašeho klienta (například contoso.onmicrosoft.com). Klikněte na **Uložit**. **{Klient}** hodnota je malá a velká písmena.
   
    ![Poskytnutí zdroje JavaScript a vrátit adresy URL pro novou aplikaci v Amazon](./media/active-directory-b2c-setup-amzn-app/amzn-urls.png)

## <a name="configure-amazon-as-an-identity-provider-in-your-tenant"></a>Konfigurace Amazon jako zprostředkovatele identity ve vašem klientovi
1. Postupujte podle těchto kroků [přejděte do okna s funkcemi B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
2. Na okno s funkcemi B2C, klikněte na tlačítko **zprostředkovatelů Identity**.
3. Klikněte na **Přidat** v horní části okna.
4. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "Amzn".
5. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Amazon**a klikněte na tlačítko **OK**.
6. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity** a zadejte ID klienta a tajný klíč klienta aplikace Amazon, kterou jste vytvořili dříve.
7. Klikněte na tlačítko **OK** a pak klikněte na **vytvořit** uložte konfiguraci Amazon.

