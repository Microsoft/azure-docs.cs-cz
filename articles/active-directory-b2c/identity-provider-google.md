---
title: Nastavení registrace a přihlášení pomocí účtu Google
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům Google ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 72e4de1473766d50512453ae38b6033ff0c5b73d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388030"
---
# <a name="set-up-sign-up-and-sign-in-with-a-google-account-using-azure-active-directory-b2c"></a>Nastavte si registraci a přihlaste se pomocí účtu Google pomocí Azure Active Directory B2C

## <a name="create-a-google-application"></a>Vytvoření aplikace Google

Pokud chcete použít účet Google jako [poskytovatele identity](authorization-code-flow.md) v Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci v konzole pro vývojáře Google. Pokud ještě nemáte účet Google, můžete se zaregistrovat v [https://accounts.google.com/SignUp](https://accounts.google.com/SignUp) .

1. Přihlaste se ke [konzole pro vývojáře Google](https://console.developers.google.com/) pomocí svých přihlašovacích údajů k účtu Google.
1. V levém horním rohu stránky vyberte seznam projekt a pak vyberte **Nový projekt**.
1. Zadejte **název projektu**, vyberte **vytvořit**.
1. Ujistěte se, že používáte nový projekt výběrem rozevírací nabídky projekt v levém horním rohu obrazovky, vyberte svůj projekt podle názvu a pak vyberte **otevřít**.
1. V nabídce vlevo vyberte **obrazovku pro vyjádření souhlasu OAuth** , vyberte **externí**a pak vyberte **vytvořit**.
Zadejte **název** vaší aplikace. V části **autorizované domény** zadejte *B2clogin.com* a vyberte **Uložit**.
1. V nabídce vlevo vyberte **přihlašovací údaje** a pak vyberte **vytvořit přihlašovací údaje**  >  **ID klienta OAuth**.
1. V části **Typ aplikace**vyberte **Webová aplikace**.
1. Zadejte **název** vaší aplikace, zadejte `https://your-tenant-name.b2clogin.com` v **autorizovaných zdrojích JavaScriptu**a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **autorizovaných identifikátorech URI pro přesměrování**. Nahraďte `your-tenant-name` názvem vašeho tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C.
1. Klikněte na **Vytvořit**.
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
