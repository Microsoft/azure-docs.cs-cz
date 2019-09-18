---
title: Nastavení registrace a přihlášení pomocí účtu Google – Azure Active Directory B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Google ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 158bb3b1329d2ad2cbd55ecf44f980966e2546e2
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065176"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Google pomocí Azure Active Directory B2C

## <a name="create-a-google-application"></a>Vytvoření aplikace Google

Pokud chcete použít účet Google jako [poskytovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet Google, můžete se zaregistrovat v [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp).

1. Přihlaste se ke [konzole pro vývojáře Google](https://console.developers.google.com/) pomocí svých přihlašovacích údajů k účtu Google.
1. V levém horním rohu stránky vyberte seznam projekt a pak vyberte **Nový projekt**.
1. Zadejte **název projektu**, klikněte na **vytvořit**a potom se ujistěte, že používáte nový projekt.
1. V nabídce vlevo vyberte **přihlašovací údaje** a pak vyberte **vytvořit přihlašovací údaje** > **ID klienta OAuth**.
1. V části **Typ aplikace**vyberte **Webová aplikace**.
1. Zadejte **název** vaší aplikace, `https://your-tenant-name.b2clogin.com` zadejte v autorizovaných **zdrojích JavaScriptu**a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v autorizovaných **identifikátorech URI pro přesměrování**. Nahraďte `your-tenant-name` názvem vašeho tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
1. Klikněte na možnost **Vytvořit**.
1. Zkopírujte hodnoty **ID klienta** a **tajný klíč klienta**. Obě tyto služby budete potřebovat ke konfiguraci Google jako poskytovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležité bezpečnostní pověření.

## <a name="configure-a-google-account-as-an-identity-provider"></a>Konfigurace účtu Google jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **Google**.
1. Zadejte **název**. Například *Google*.
1. Jako **ID klienta**zadejte ID klienta Google aplikace, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.
