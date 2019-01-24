---
title: Nastavení registrace a přihlášení pomocí účtu Microsoft pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Microsoft ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 1f9deaf29982c4bd753336f51e10a06ced6586f1
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54842525"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Microsoft pomocí Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft

Pokud chcete použít účet Microsoft jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, je potřeba vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet Microsoft, získáte ji na [ https://www.live.com/ ](https://www.live.com/).

1. Přihlaste se k [portál pro registraci aplikací Microsoft](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList) pomocí svých přihlašovacích údajů účtu Microsoft.
2. V pravém horním rohu vyberte **přidat aplikaci**.
3. Zadejte **název** pro vaši aplikaci. Například *MSAapp1*.
4. Vyberte **generovat nové heslo** a ujistěte se, že zkopírujete heslo pro použití při konfiguraci zprostředkovatele identity. Zkopírovat **Id aplikace**. 
5. Vyberte **přidat platformy**a potom a tlačítko **webové**.
4. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v **Přesměrovaly**. Nahraďte `your-tenant-name` s názvem vašeho tenanta.
5. Vyberte **Uložit**.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Nakonfigurujte účet Microsoft jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *MSA*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Account Microsoft**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte Id aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte heslo, které jste si poznamenali jako **tajný kód klienta**aplikace účtu Microsoft, který jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci vašeho účtu Microsoft.

