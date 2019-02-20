---
title: Nastavení registrace a přihlášení s účtem GitHub – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účtů GitHub ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: f1c1ac91c08fe27445f4b9631500543d1d0287bd
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427271"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem GitHub pomocí Azure Active Directory B2C

> [!NOTE]
> Tato funkce je ve verzi Preview.
> 

Chcete-li použít účet GitHub jako [zprostředkovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure Active Directory (Azure AD) B2C, budete potřebovat k vytvoření aplikace ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet GitHub, můžete získat na [ https://www.github.com/ ](https://www.github.com/).

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace OAuth Githubu

1. Přihlaste se k [GitHub pro vývojáře](https://github.com/settings/developers) pomocí svých přihlašovacích údajů Githubu.
2. Vyberte **aplikace OAuth** a pak vyberte **nové aplikace OAuth**.
3. Zadejte **název_aplikace** a **adresa URL domovské stránky**.
4. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **adresu URL zpětného volání autorizace**. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C. Když zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C, používejte jenom malá písmena.
5. Klikněte na tlačítko **zaregistrovat aplikaci**.
6. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Jak přidat zprostředkovatele identity do svého tenanta potřebujete.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Nakonfigurujte účet GitHub jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Githubu*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Githubu (Preview)** a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace účtu Githubu, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci účtu GitHub.
