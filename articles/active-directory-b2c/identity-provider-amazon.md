---
title: Nastavení registrace a přihlášení pomocí účtu Amazon
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů Amazon ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188456"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Amazon pomocí služby Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Vytvoření aplikace Amazon

Chcete-li použít účet Amazon jako [poskytovatele identity](authorization-code-flow.md) ve službě Azure Active Directory B2C (Azure AD B2C), musíte vytvořit aplikaci ve vašem tenantovi, která ji zastupuje. Pokud ještě nemáte účet Amazon, můžete se [https://www.amazon.com/](https://www.amazon.com/)zaregistrovat na adrese .

1. Přihlaste se do [Amazon Developer Center](https://login.amazon.com/) s přihlašovacími údaji svého účtu Amazon.
1. Pokud jste tak ještě neučinili, klikněte na **Zaregistrovat se**, postupujte podle kroků registrace vývojáře a přijměte zásady.
1. Vyberte **možnost Registrovat novou aplikaci**.
1. Zadejte **adresu URL název**, **popis**a oznámení **o zásadách ochrany osobních údajů**a klepněte na tlačítko **Uložit**. Oznámení o ochraně osobních údajů je stránka, kterou spravujete a která uživatelům poskytuje informace o ochraně osobních údajů.
1. V části **Nastavení webu** zkopírujte hodnoty **ID klienta**. Vyberte **Zobrazit tajný klíč,** chcete-li získat tajný klíč klienta a pak jej zkopírovat. Potřebujete oba ke konfiguraci účtu Amazon jako poskytovatele identity ve vašem tenantovi. **Tajný klíč klienta** je důležité pověření zabezpečení.
1. V části **Nastavení webu** vyberte **Upravit**a zadejte `https://your-tenant-name.b2clogin.com` do pole Povolený původ **JavaScriptu** a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **adresách URL allowed return**. Nahraďte `your-tenant-name` se jménem svého tenanta. Při zadávání názvu klienta musíte použít všechna malá písmena, i když je klient definován velkými písmeny v Azure AD B2C.
1. Klikněte na **Uložit**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurace účtu Amazon jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatele identity**a pak vyberte **Amazon**.
1. Zadejte **název**. Například *Amazon*.
1. Pro **ID klienta**zadejte ID klienta aplikace Amazon, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste nahráli.
1. Vyberte **Uložit**.
