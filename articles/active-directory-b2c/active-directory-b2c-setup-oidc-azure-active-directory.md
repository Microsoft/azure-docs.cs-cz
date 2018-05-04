---
title: 'Azure Active Directory B2C: Přidání poskytovatele služby Azure AD pomocí předdefinovaných zásad | Microsoft Docs'
description: Informace o postupu přidání poskytovatele identity Open ID Connect (Azure AD)
services: active-directory-b2c
documentationcenter: ''
author: parakhj
manager: krassk
editor: parakhj
ms.assetid: 7dac9545-d5f1-4136-a04d-1c5740aea499
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: article
ms.devlang: na
ms.date: 04/27/2018
ms.author: parja
ms.openlocfilehash: 52a752df9cf199acf39596f49e7368bce27a8158
ms.sourcegitcommit: 6e43006c88d5e1b9461e65a73b8888340077e8a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2018
---
# <a name="azure-active-directory-b2c-sign-in-using-azure-ad-accounts-through-a-built-in-policy"></a>Azure Active Directory B2C: Přihlaste se pomocí účtů služby Azure AD pomocí předdefinovaných zásad

>[!NOTE]
> Tato funkce je ve verzi public preview. Nepoužívejte funkci v produkčním prostředí.

Tento článek ukazuje, jak povolit přihlášení pro uživatele z konkrétní zásady integrované organizace Azure Active Directory (Azure AD).

## <a name="create-an-azure-ad-app"></a>Vytvoření aplikace Azure AD

Chcete-li povolit přihlášení pro uživatele z konkrétní organizace Azure AD, je nutné zaregistrovat aplikaci v rámci organizační klienta Azure AD.

>[!NOTE]
> Používáme "contoso.com" pro organizační klienta Azure AD a "fabrikamb2c.onmicrosoft.com" jako klienta Azure AD B2C v následujících pokynech.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na horním panelu vyberte svůj účet. Z **Directory** seznamu, vyberte organizační klienta Azure AD, kde bude registrace vaší aplikace (contoso.com).
1. Vyberte **všechny služby** v levém podokně a vyhledejte "Aplikace registrace."
1. Vyberte **Registrace nové aplikace**.
1. Zadejte název pro vaši aplikaci (například `Azure AD B2C App`).
1. Jako typ aplikace vyberte položku **Webová aplikace / webové rozhraní API**.
1. Pro **přihlašovací adresa URL**, zadejte následující adresu URL, kde `yourtenant` je nahrazena název vašeho klienta Azure AD B2C (`fabrikamb2c.onmicrosoft.com`):

    >[!NOTE]
    >Hodnota pro "yourtenant" musí být psaný malými písmeny v **přihlašovací adresa URL**.

    ```Console
    https://login.microsoftonline.com/te/yourtenant.onmicrosoft.com/oauth2/authresp
    ```

1. Uložit ID aplikace, které budete používat v další části jako ID klienta.
1. V části **nastavení** vyberte **klíče**.
1. Zadejte **klíče popis** pod **hesla** tématu a nastavte **doba trvání** na "Je platné stále". 
1. Klikněte na tlačítko **Uložit**a zapište výsledným klíčem **hodnotu**, které budete používat v další části jako sdílený tajný klíč klienta.

## <a name="configure-azure-ad-as-an-identity-provider-in-your-tenant"></a>Konfigurovat Azure AD jako zprostředkovatele identity ve vašem klientovi

1. Na horním panelu vyberte svůj účet. Z **Directory** vyberte klienta Azure AD B2C (fabrikamb2c.onmicrosoft.com).
1. [Přejděte do nabídky nastavení Azure AD B2C](active-directory-b2c-app-registration.md#navigate-to-b2c-settings) na portálu Azure.
1. V nabídce nastavení Azure AD B2C, klikněte na **zprostředkovatelů Identity**.
1. Klikněte na **Přidat** v horní části okna.
1. Zadejte popisný **název** pro konfiguraci poskytovatele identity. Zadejte například "Contoso Azure AD".
1. Klikněte na tlačítko **typ zprostředkovatele Identity**, vyberte **Open ID Connect**a klikněte na tlačítko **OK**.
1. Klikněte na tlačítko **nastavení tohoto zprostředkovatele identity**
1. Pro **adresu url metadat**, zadejte následující adresu URL, kde `yourtenant` je nahrazena název vašeho klienta Azure AD (například `contoso.com`):

    ```Console
    https://login.microsoftonline.com/yourtenant/.well-known/openid-configuration
    ```
1. Pro **ID klienta** a **tajný klíč klienta**, zadejte ID aplikace a klíč z předchozí části.
1. Ponechte výchozí hodnotu pro **oboru**, který by měl být nastaven na `openid`.
1. Ponechte výchozí hodnotu pro **typ odpovědi**, který by měl být nastaven na `code`.
1. Ponechte výchozí hodnotu pro **režim odpovědi**, který by měl být nastaven na `form_post`.
1. Volitelně můžete zadat hodnotu pro **domény** (například `ContosoAD`). Jedná se o hodnotu sloužící k odkazování na tohoto zprostředkovatele identity pomocí *domain_hint* v požadavku. 
1. Klikněte na **OK**.
1. Klikněte na **mapování zprostředkovatele identity deklarací**.
1. Pro **ID uživatele**, zadejte `oid`.
1. Pro **zobrazovaný název**, zadejte `name`.
1. Pro **křestní jméno**, zadejte `given_name`.
1. Pro **Přezdívka**, zadejte `family_name`.
1. Pro **e-mailu**, zadejte `unique_name`
1. Klikněte na tlačítko **OK**a potom **vytvořit** uložte konfiguraci.

## <a name="next-steps"></a>Další postup

Přidejte nově vytvořený poskytovatele identit Azure AD pro předdefinované zásady a napsat svůj názor na [ AADB2CPreview@microsoft.com ](mailto:AADB2CPreview@microsoft.com).
