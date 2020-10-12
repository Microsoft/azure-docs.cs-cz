---
title: Nastavení registrace a přihlášení pomocí účtu GitHubu
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům GitHub ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "85388183"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu GitHubu pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace GitHub OAuth

Pokud chcete použít účet GitHub jako [poskytovatele identity](authorization-code-flow.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet GitHubu, můžete se zaregistrovat v [https://www.github.com/](https://www.github.com/) .

1. Přihlaste se k webu pro [vývojáře na GitHubu](https://github.com/settings/developers) pomocí vašich přihlašovacích údajů GitHubu.
1. Vyberte **aplikace OAuth** a pak vyberte **Nová aplikace OAuth**.
1. Zadejte **název aplikace** a **adresu URL domovské stránky**.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adresu URL zpětného volání autorizace**. Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C. Při zadávání názvu tenanta používejte všechna malá písmena, a to i v případě, že je tenant v Azure AD B2C definovaný velkými písmeny.
1. Klikněte na **Registrovat aplikaci**.
1. Zkopírujte hodnoty **ID klienta** a **tajný klíč klienta**. K přidání poskytovatele identity k vašemu tenantovi potřebujete obojí.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurace účtu GitHub jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **GitHub (Preview)**.
1. Zadejte **název**. Například *GitHub*.
1. Pro **ID klienta**zadejte ID klienta aplikace GitHub, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.
