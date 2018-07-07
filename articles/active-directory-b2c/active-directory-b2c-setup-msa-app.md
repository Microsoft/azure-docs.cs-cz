---
title: Nastavení registrace a přihlášení pomocí účtu Microsoft pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Microsoft ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/05/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 16e4dbac4c8146b048d4d9b76544677a6111e2a5
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37900825"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Microsoft pomocí Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft

Pokud chcete použít účet Microsoft jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, je potřeba vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Microsoft, získáte ji na [ https://www.live.com/ ](https://www.live.com/).

1. Přihlaste se k [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) pomocí svých přihlašovacích údajů účtu Microsoft.
2. V pravém horním rohu vyberte **přidat aplikaci**.
3. Zadejte **název** pro vaši aplikaci a klikněte na **vytvořit**.
4. Na stránce pro registraci, zkopírujte hodnotu **Id aplikace**. Použijte ke konfiguraci účtu Microsoft jako zprostředkovatele identity ve vašem tenantovi.
5. Vyberte **přidat platformy**a potom a tlačítko **webové**.
6. Zadejte `https://login.microsoftonline.com/te/{tenant}/oauth2/authresp` v **Přesměrovaly**. Nahraďte **{klient}** s názvem vašeho tenanta (například contosob2c.onmicrosoft.com).
7. Vyberte **generovat nové heslo** pod **tajných klíčů aplikací**. Kopírovat nové heslo, zobrazí na obrazovce. Když je potřebujete a nakonfigurujte účet Microsoft jako zprostředkovatele identity ve vašem tenantovi. Toto heslo je důležitým bezpečnostním pověřením.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Nakonfigurujte účet Microsoft jako zprostředkovatele identity

1. Přihlaste se k [webu Azure portal](https://portal.azure.com/) jako globální správce tenanta Azure AD B2C.
2. Přepněte v pravém horním rohu portálu Azure Portal na adresář, který obsahuje tenanta Azure AD B2C, a ujistěte se tak, že používáte správný adresář. Vyberte informace o předplatném a pak **Přepnout adresář**. 

    ![Přepnutí na tenanta Azure AD B2C](./media/active-directory-b2c-setup-msa-app/switch-directories.png)

    Vyberte adresář, který obsahuje vašeho tenanta.

    ![Výběr adresáře](./media/active-directory-b2c-setup-msa-app/select-directory.png)

3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *MSA*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Account Microsoft**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte heslo, které jste si poznamenali jako **tajný kód klienta**aplikace účtu Microsoft, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci vašeho účtu Microsoft.

