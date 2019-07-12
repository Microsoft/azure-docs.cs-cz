---
title: Nastavení registrace a přihlášení pomocí účtu Microsoft – Azure Active Directory B2C
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty Microsoft ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 82c1be335bfd39d641f0203116e68a4cb4c0a674
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/08/2019
ms.locfileid: "67654216"
---
# <a name="set-up-sign-up-and-sign-in-with-a-microsoft-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu Microsoft pomocí Azure Active Directory B2C

## <a name="create-a-microsoft-account-application"></a>Vytvoření aplikace účtu Microsoft

Chcete-li použít účet Microsoft jako [zprostředkovatele identity](active-directory-b2c-reference-oidc.md) v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci v tenantovi Azure AD. Tenant Azure AD není stejný jako vašeho tenanta Azure AD B2C. Pokud ještě nemáte účet Microsoft, můžete jej získat na [ https://www.live.com/ ](https://www.live.com/).

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Ujistěte se, že používáte adresáře, který obsahuje váš tenant Azure AD kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant Azure AD.
1. Zvolte **všechny služby** v horním levém horním rohu webu Azure portal a poté vyhledejte a vyberte **registrace aplikací**.
1. Vyberte **registrace nové**.
1. Zadejte **název** pro vaši aplikaci. Například *MSAapp1*.
1. V části **podporovaných typů účtu**vyberte **účty v jakékoli organizaci adresáři a osobní účty Microsoft (třeba Skype, Xbox, Outlook.com)** . Tuto možnost, zaměřuje nejširší sadu identit společnosti Microsoft.

   Další informace o výběr typu jiný účet, najdete v části [rychlý start: Registrace aplikace s platformou identity Microsoft](../active-directory/develop/quickstart-register-app.md).
1. V části **identifikátor URI pro přesměrování (volitelné)** vyberte **webové** a zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` v textovém poli. Nahraďte `your-tenant-name` názvem vašeho tenanta Azure AD B2C.
1. Vyberte **zaregistrovat**
1. Záznam **ID aplikace (klient)** zobrazený na stránce Přehled aplikace. Budete potřebovat při konfiguraci zprostředkovatele identity v další části.
1. Vyberte **certifikáty a tajné kódy**
1. Klikněte na tlačítko **nový tajný kód klienta**
1. Zadejte **popis** pro tajný klíč, například *heslo aplikace 1*a potom klikněte na tlačítko **přidat**.
1. Zaznamenat heslo aplikace je znázorněno **hodnotu** sloupce. Budete potřebovat při konfiguraci zprostředkovatele identity v další části.

## <a name="configure-a-microsoft-account-as-an-identity-provider"></a>Nakonfigurujte účet Microsoft jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
1. Zadejte **název**. Zadejte například *MSA*.
1. Vyberte **typ zprostředkovatele identit**vyberte **Account Microsoft**a klikněte na tlačítko **OK**.
1. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace (klient), který jste si poznamenali dříve v **ID klienta** textové pole a zadejte tajný kód klienta, který jste si poznamenali v **klienta tajný kód** textového pole.
1. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci vašeho účtu Microsoft.
