---
title: Přidání poskytovatele služby Azure AD pomocí předdefinovaných zásad v Azure Active Directory B2C | Dokumentace Microsoftu
description: Zjistěte, jak přidat poskytovatele identity Open ID Connect (Azure AD).
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/27/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 70d3a19b715052fe658102929a1c29cf3db2d595
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37443714"
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: Přihlaste se pomocí účtů služby Azure AD prostřednictvím předdefinovaných zásad

>[!NOTE]
> Tato funkce je ve verzi public preview. Nepoužívejte tuto funkci v produkčním prostředí.

V tomto článku se dozvíte, jak povolit přihlášení pro uživatele z konkrétní integrované zásady služby Azure Active Directory (Azure AD) organizace.

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Povolit přihlášení pro uživatele z konkrétní organizace služby Azure AD, budete muset zaregistrovat aplikaci v rámci organizační tenanta Azure AD.

>[!NOTE]
> Používáme "contoso.com" pro organizační tenanta Azure AD a "fabrikamb2c.onmicrosoft.com" jako tenant Azure AD B2C v následujících pokynech.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet. Z **Directory** klikněte na položku organizační tenanta Azure AD, kde se registrace vaší aplikace (contoso.com).
1. Vyberte **všechny služby** v levém podokně a vyhledejte "Registrace aplikací."
1. Vyberte **Registrace nové aplikace**.
1. Zadejte název pro vaši aplikaci (například `Azure AD B2C App`).
1. Jako typ aplikace vyberte položku **Webová aplikace / webové rozhraní API**.
1. Pro **přihlašovací adresa URL**, zadejte následující adresu URL, kde `yourtenant` se nahradí názvem vašeho tenanta Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Hodnota pro "yourtenant" musí být malými písmeny v **přihlašovací adresa URL**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Uložení ID aplikace, kterou použijete v další části jako ID klienta.
1. V části **nastavení** okně vyberte **klíče**.
1. Zadejte **Popis klíče** pod **hesla** tématu a nastavte **doba trvání** na "Je platné stále". 
1. Klikněte na tlačítko **Uložit**a poznamenejte si výsledným klíčem **hodnota**, kterou použijete v další části jako tajný kód klienta.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurace služby Azure AD jako zprostředkovatele identity ve vašem tenantovi

1. Na horním panelu vyberte svůj účet. Z **Directory** , zvolte tenanta Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Přejděte do nabídky nastavení Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na webu Azure Portal.
1. V nabídce nastavení Azure AD B2C, klikněte na **zprostředkovatelé Identity**.
1. Klikněte na **Přidat** v horní části okna.
1. Zadejte popisný **název** pro konfigurace zprostředkovatele identity. Zadejte například "Contoso Azure AD".
1. Klikněte na tlačítko **typ zprostředkovatele identit**vyberte **Open ID Connect**a klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nastavit tohoto zprostředkovatele identity**
1. Pro **adresa url metadat**, zadejte následující adresu URL, kde `yourtenant` se nahradí názvem vašeho tenanta Azure AD (například `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Pro **ID klienta** a **tajný kód klienta**, zadejte ID aplikace a klíč v předchozí části.
1. Ponechte výchozí hodnotu pro **oboru**, který by měl být nastaven na `openid`.
1. Ponechte výchozí hodnotu pro **typ odpovědi**, který by měl být nastaven na `code`.
1. Ponechte výchozí hodnotu pro **režim odpovědi**, který by měl být nastaven na `form_post`.
1. Volitelně můžete zadat hodnotu pro **domény** (třeba `ContosoAD`). Jedná se o hodnotu použít k odkazování na tento zprostředkovatelů identity pomocí *domain_hint* v požadavku. 
1. Klikněte na **OK**.
1. Klikněte na **mapování deklarací od zprostředkovatele identity**.
1. Pro **ID uživatele**, zadejte `oid`.
1. Pro **zobrazovaný název**, zadejte `name`.
1. Pro **křestní jméno**, zadejte `given_name`.
1. Pro **příjmení**, zadejte `family_name`.
1. Pro **e-mailu**, zadejte `unique_name`
1. Klikněte na tlačítko **OK**a potom **vytvořit** uložte konfiguraci.

## <a name="next-steps"></a>Další postup

Přidejte nově vytvořený zprostředkovatele identity Azure AD, aby integrované zásady a poskytnou zpětnou vazbu k [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
