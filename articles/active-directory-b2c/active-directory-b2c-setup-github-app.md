---
title: Nastavení registrace a přihlášení pomocí účtu GitHub – Azure Active Directory B2C
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účtů GitHub ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74c663d8847c2829a5d9466f8e601dd44593a6f8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065198"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem GitHub pomocí Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace OAuth Githubu

Pokud chcete použít účet GitHub jako [poskytovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet GitHubu, můžete se zaregistrovat v [https://www.github.com/](https://www.github.com/).

1. Přihlaste se k [GitHub pro vývojáře](https://github.com/settings/developers) pomocí svých přihlašovacích údajů Githubu.
1. Vyberte **aplikace OAuth** a pak vyberte **nové aplikace OAuth**.
1. Zadejte **název_aplikace** a **adresa URL domovské stránky**.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **adresu URL zpětného volání autorizace**. Nahraďte `your-tenant-name` s názvem vašeho tenanta Azure AD B2C. Když zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C, používejte jenom malá písmena.
1. Klikněte na tlačítko **zaregistrovat aplikaci**.
1. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Jak přidat zprostředkovatele identity do svého tenanta potřebujete.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Nakonfigurujte účet GitHub jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **GitHub (Preview)** .
1. Zadejte **název**. Například *GitHub*.
1. Pro **ID klienta**zadejte ID klienta aplikace GitHub, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.
