---
title: Nastavení registrace a přihlášení s účtem LinkedIn – Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty LinkedIn ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 04/01/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 5ffe75a58a8cfb2409af3a65ff822023d7ccf5d4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66508478"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení s účtem LinkedIn pomocí Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn

Použít účet LinkedIn jako [zprostředkovatele identity](active-directory-b2c-reference-oauth-code.md) v Azure Active Directory (Azure AD) B2C, budete potřebovat k vytvoření aplikace ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet LinkedIn, získáte ji na [ https://www.linkedin.com/ ](https://www.linkedin.com/).

1. Přihlaste se k [web vývojáře LinkedIn](https://www.developer.linkedin.com/) pomocí svých přihlašovacích údajů účtu LinkedIn.
2. Vyberte **Moje aplikace**a potom klikněte na tlačítko **vytvořit aplikaci**.
3. Zadejte **název společnosti**, **název_aplikace**, **popis aplikace**, **Logo aplikace**, **využívání aplikací** , **Adresu URL webu**, **e-mailová adresa**, a **Telefon do zaměstnání**.
4. Souhlas s **LinkedIn API Terms of Use** a klikněte na tlačítko **odeslat**.
5. Zkopírujte hodnoty **ID klienta** a **tajný kód klienta**. Najdete je v části **ověřovací klíče**. Budete potřebovat oba poskytovatelé konfigurace LinkedIn jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.
6. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **oprávnění adresy URL pro přesměrování**. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C. Vyberte **přidat**a potom klikněte na tlačítko **aktualizace**.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Nakonfigurujte účet LinkedIn jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *LinkedIn*.
6. Vyberte **typ zprostředkovatele identit**vyberte **LinkedIn**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**aplikace účet LinkedIn, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci účtu LinkedIn.

## <a name="migration-from-v10-to-v20"></a>Migrace z verze 1.0 do verze 2.0

Nedávno LinkedIn [aktualizovat své rozhraní API z verze 1.0 k v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Jako součást migrace pouze je schopen získat celé jméno uživatele, LinkedIn během přihlašování Azure AD B2C Pokud se e-mailovou adresu, atributy, které jsou shromažďovány během registrace, uživatel musí ručně zadat e-mailovou adresu a ověřte ho.
