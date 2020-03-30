---
title: Nastavení registrace a přihlášení pomocí účtu LinkedIn
titleSuffix: Azure AD B2C
description: Zajistěte registraci a přihlášení zákazníkům pomocí účtů LinkedIn ve vašich aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188096"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu LinkedIn pomocí služby Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn

Pokud chcete použít účet LinkedIn jako [poskytovatele identity](authorization-code-flow.md) ve službě Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ji zastupuje. Pokud ještě nemáte účet LinkedIn, můžete se zaregistrovat na adrese [https://www.linkedin.com/](https://www.linkedin.com/).

1. Přihlaste se na [web LinkedIn Developers](https://www.developer.linkedin.com/) pomocí přihlašovacích údajů k účtu LinkedIn.
1. Vyberte **Moje aplikace**a klikněte na **Vytvořit aplikaci**.
1. Zadejte **název společnosti**, **název aplikace**, popis **aplikace**, **logo aplikace**, použití **aplikace**, adresu **URL webu**, **firemní e-mail**a **telefonní telefon do zaměstnání**.
1. Souhlasíte s **podmínkami použití rozhraní LinkedIn API** a klepněte na tlačítko **Odeslat**.
1. Zkopírujte hodnoty **ID klienta** a **tajného klíče klienta**. Najdete je v části **Ověřovací klíče**. Budete potřebovat oba k konfiguraci LinkedIn jako poskytovatele identity ve vašem tenantovi. **Tajný klíč klienta** je důležité pověření zabezpečení.
1. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` **adresy URL autorizovaného přesměrování**. Nahraďte `your-tenant-name` se jménem svého tenanta. Při zadávání názvu klienta musíte použít všechna malá písmena, i když je klient definován velkými písmeny v Azure AD B2C. Vyberte **Přidat**a klepněte na tlačítko **Aktualizovat**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurace účtu LinkedIn jako poskytovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje vašeho klienta Azure AD B2C výběrem directory **+ předplatné** filtr v horní nabídce a výběrem adresáře, který obsahuje vašeho klienta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **LinkedIn**.
1. Zadejte **název**. Například *LinkedIn*.
1. Pro **ID klienta**zadejte ID klienta aplikace LinkedIn, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste nahráli.
1. Vyberte **Uložit**.

## <a name="migration-from-v10-to-v20"></a>Migrace z v1.0 na v2.0

LinkedIn nedávno [aktualizoval svá api z v1.0 na v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Jako součást migrace Azure AD B2C je možné získat pouze úplný název uživatele LinkedIn během registrace. Pokud je e-mailová adresa jedním z atributů shromážděných během registrace, musí uživatel ručně zadat e-mailovou adresu a ověřit ji.
