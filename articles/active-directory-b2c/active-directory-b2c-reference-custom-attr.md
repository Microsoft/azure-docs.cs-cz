---
title: Definování uživatelských atributů, které v Azure Active Directory B2C | Dokumentace Microsoftu
description: Definujte vlastní atributy pro vaši aplikaci v Azure Active Directory B2C ke shromažďování informací o vašich zákazníků.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: d5ef77ab0bbf00d4ddbb05b7a38516e3c3e7d800
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/12/2018
ms.locfileid: "38968770"
---
# <a name="define-custom-attributes-in-azure-active-directory-b2c"></a>Definování uživatelských atributů, které v Azure Active Directory B2C

 Každá aplikace určených pro zákazníky má jedinečné požadavky, které je potřeba shromáždit informace. Váš tenant Azure Active Directory (Azure AD) B2C se dodává s integrovanou sadou informací uložených v atributech, například křestní jméno, příjmení, Město a PSČ. S Azure AD B2C můžete rozšířit sadu atributů, které jsou uložené na každý účet zákazníka. 
 
 Můžete vytvořit vlastní atributy [webu Azure portal](https://portal.azure.com/) a používat je ve vaší zásady registrace, zásady registrace nebo přihlášení nebo zásady upravování profilu. Může číst a zapisovat pomocí těchto atributů [Azure AD Graph API](active-directory-b2c-devquickstarts-graph-dotnet.md). Použití vlastních atributů v Azure AD B2C [Azure AD Graph API adresáře schématu rozšíření](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-directory-schema-extensions).

## <a name="create-a-custom-attribute"></a>Vytvoření vlastního atributu

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-reference-custom-attr/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-reference-custom-attr/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **atributy uživatele**a pak vyberte **přidat**.
5. Zadejte **název** pro vlastní atribut (například "ShoeSize")
6. Zvolte **datový typ**. Pouze **řetězec**, **logická**, a **Int** jsou k dispozici.
7. Volitelně můžete zadat **popis** k informačním účelům. 
8. Klikněte na možnost **Vytvořit**.

Vlastní atribut je nyní k dispozici v seznamu **atributy uživatele** a pro použití ve vašich zásadách. Vlastní atribut je jen pro vytvoří při prvním se používá ve všech zásad, a ne v případě, že ho přidáte do seznamu **atributy uživatele**.

## <a name="use-a-custom-attribute-in-your-policy"></a>Použít vlastní atribut ve svojí zásadě

1. Ve vašem tenantovi Azure AD B2C vyberte **zásady registrace nebo přihlášení**.
2. Vyberte zásadu (například "B2C_1_SignupSignin") a otevřete ho. 
3. Klikněte na **Upravit**.
4. Vyberte **atributy registrace** a pak vyberte vlastní atribut (například "ShoeSize"). Klikněte na **OK**.
5. Vyberte **deklarace identit aplikace** a pak vyberte vlastní atribut. Klikněte na **OK**.
6. Klikněte na **Uložit**.

Můžete použít **spustit nyní** funkce na zásadu, kterou chcete ověřit uživatelské prostředí. Teď byste měli vidět **ShoeSize** v seznam atributů shromážděných během registrace cesty a vidět ji v tokenu odesílaných zpět do aplikace.

