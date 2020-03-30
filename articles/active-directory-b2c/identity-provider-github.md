---
title: Nastavení registrace a přihlášení pomocí účtu GitHub
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů GitHub ve vašich aplikacích pomocí Služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188201"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu GitHub pomocí Služby Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Vytvoření aplikace GitHub OAuth

Pokud chcete použít účet GitHub jako [poskytovatele identity](authorization-code-flow.md) ve službě Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ji zastupuje. Pokud ještě nemáte účet GitHub, můžete se zaregistrovat na adrese [https://www.github.com/](https://www.github.com/).

1. Přihlaste se k webu [GitHub Developer](https://github.com/settings/developers) pomocí přihlašovacích údajů githubu.
1. Vyberte **OAuth Apps** a pak vyberte **new OAuth App**.
1. Zadejte **název aplikace** a adresu URL domovské **stránky**.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adresu URL zpětného volání autorizace**. Nahraďte `your-tenant-name` se názvem vašeho klienta Azure AD B2C. Při zadávání názvu klienta použijte všechna malá písmena, i když je klient definován velkými písmeny v Azure AD B2C.
1. Klepněte na tlačítko **Registrovat aplikaci**.
1. Zkopírujte hodnoty **ID klienta** a **tajného klíče klienta**. Potřebujete oba přidat zprostředkovatele identity do vašeho tenanta.

## <a name="configure-a-github-account-as-an-identity-provider"></a>Konfigurace účtu GitHub jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **GitHub (Preview).**
1. Zadejte **název**. Například *GitHub*.
1. Pro **ID klienta**zadejte ID klienta aplikace GitHub, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste nahráli.
1. Vyberte **Uložit**.
