---
title: Nastavení registrace a přihlášení pomocí účtu Google
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů Google ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 48955caddb64069f897078f5e47066d9f11d119b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188136"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Google pomocí služby Azure Active Directory B2C

## <a name="create-a-google-application"></a>Vytvoření aplikace Google

Chcete-li použít účet Google jako [poskytovatele identity](authorization-code-flow.md) ve službě Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci ve službě Google Developers Console. Pokud ještě nemáte účet Google, můžete se [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp)zaregistrovat na adrese .

1. Přihlaste se ke [službě Google Developers Console](https://console.developers.google.com/) pomocí přihlašovacích údajů k účtu Google.
1. V levém horním rohu stránky vyberte seznam projektů a pak vyberte **Nový projekt**.
1. Zadejte **název projektu**, vyberte **Vytvořit**.
1. Ujistěte se, že používáte nový projekt výběrem rozevíracího souboru projektu v levém horním rohu obrazovky, vyberte projekt podle názvu a pak vyberte **Otevřít**.
1. V levé nabídce vyberte **obrazovku Souhlas OAuth,** vyberte **Externí**a pak vyberte **Vytvořit**.
Zadejte **název** aplikace. Do části **Autorizované domény** zadejte *b2clogin.com* a vyberte **Uložit**.
1. V levé nabídce vyberte **Pověření** a pak vyberte **Vytvořit pověření** > **Oauth ID klienta**.
1. V části **Typ aplikace**vyberte **možnost Webová aplikace**.
1. Zadejte **název** aplikace, `https://your-tenant-name.b2clogin.com` zadejte **autorizovaný původ JavaScriptu**a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **autorizované identifikátory URI přesměrování**. Nahraďte `your-tenant-name` se jménem svého tenanta. Při zadávání názvu klienta musíte použít všechna malá písmena, i když je klient definován velkými písmeny v Azure AD B2C.
1. Klikněte na **Vytvořit**.
1. Zkopírujte hodnoty **ID klienta** a **tajný klíč klienta**. Budete je potřebovat k konfiguraci Google jako poskytovatele identity ve vašem tenantovi. **Tajný klíč klienta** je důležité pověření zabezpečení.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurace účtu Google jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Google**.
1. Zadejte **název**. Například *Google*.
1. Pro **ID klienta**zadejte ID klienta aplikace Google, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste nahráli.
1. Vyberte **Uložit**.
