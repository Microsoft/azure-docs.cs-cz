---
title: Nastavení registrace a přihlášení pomocí účtu Amazon pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Amazon ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 5ded04f564439be332152bfad0fc571fe8eaf8c2
ms.sourcegitcommit: c29d7ef9065f960c3079660b139dd6a8348576ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/12/2018
ms.locfileid: "44720969"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Amazon pomocí Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Vytvoření aplikace Amazon

K použití účtu Amazon jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Amazon získáte ji na [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Přihlaste se k [středisko pro vývojáře Amazon](https://login.amazon.com/) pomocí svých přihlašovacích údajů účtu Amazon.
2. Pokud jste tak již neučinili, klikněte na tlačítko **zaregistrovat**, postupujte podle kroků registrace pro vývojáře a přijměte zásady.
3. Vyberte **registrace nové aplikace**.
4. Zadejte **název**, **popis**, a **URL oznámení o ochraně osobních údajů**a potom klikněte na tlačítko **Uložit**. Oznámení o ochraně osobních údajů je stránka, která spravujete, který poskytuje informace o ochraně osobních údajů pro uživatele.
5. V **nastavení webu** tématu, zkopírujte hodnoty **ID klienta**. Vyberte **zobrazit tajný kód** získat tajný kód klienta a potom ho zkopírujte. Je třeba obou z nich při konfiguraci účtu Amazon jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležitým bezpečnostním pověřením.
6. V **nastavení webu** vyberte **upravit**a pak zadejte `https://your-tenant-name.b2clogin.com` v **povolený JavaScript zdroje** a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **povoleno Vrátí adresy URL**. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Budete muset použít jenom malá písmena. Pokud zadáte název vašeho klienta i v případě, že klient je definována s velká písmena v Azure AD B2C.
7. Klikněte na **Uložit**.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Konfigurace účtu Amazon jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-amzn-app/switch-directories.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Amazon*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Amazon**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID klienta, který jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód klienta, který jste si poznamenali jako **tajný kód klienta**Amazon aplikace, kterou jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci Amazon.

