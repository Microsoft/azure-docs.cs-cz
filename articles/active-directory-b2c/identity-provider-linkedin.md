---
title: Nastavení registrace a přihlášení pomocí účtu LinkedIn
titleSuffix: Azure AD B2C
description: Poskytněte zákazníkům registraci a přihlášení k účtům LinkedIn ve vašich aplikacích pomocí Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91259454"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu LinkedIn pomocí Azure Active Directory B2C

## <a name="create-a-linkedin-application"></a>Vytvoření aplikace LinkedIn

Pokud chcete použít účet LinkedIn jako [poskytovatele identity](authorization-code-flow.md) v Azure Active Directory B2C (Azure AD B2C), musíte ve svém tenantovi vytvořit aplikaci, která ho bude představovat. Pokud ještě nemáte účet LinkedIn, můžete se zaregistrovat v [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Přihlaste se na [Web vývojářům LinkedIn](https://www.developer.linkedin.com/) pomocí svých přihlašovacích údajů k účtu LinkedIn.
1. Vyberte **Moje aplikace**a pak klikněte na **vytvořit aplikaci**.
1. Zadejte **název aplikace**, **stránku LinkedIn**, **adresu URL zásad ochrany osobních údajů**a **logo aplikace**.
1. Odsouhlaste **podmínky použití rozhraní API** LinkedIn a klikněte na **vytvořit aplikaci**.
1. Vyberte kartu **ověřování** . V části **ověřovací klíče**zkopírujte hodnoty pro **ID klienta** a **tajný klíč klienta**. Obě tyto služby budete potřebovat ke konfiguraci LinkedInu jako poskytovatele identity ve vašem tenantovi. **Tajný kód klienta** je důležité bezpečnostní pověření.
1. Zaškrtněte políčko Upravit tužku vedle **autorizované adresy URL pro přesměrování vaší aplikace**a pak vyberte **Přidat adresu URL pro přesměrování**. Zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` , nahraďte `your-tenant-name` názvem svého tenanta. Při zadávání názvu tenanta musíte použít malá písmena, i když je tenant definovaný velkými písmeny v Azure AD B2C. Vyberte **Aktualizovat**.
2. Ve výchozím nastavení není vaše aplikace LinkedIn schválená pro obory související s přihlášením. Chcete-li požádat o revizi, vyberte kartu **produkty** a potom vyberte možnost **Přihlásit se pomocí LinkedInu**. Po dokončení kontroly budou do aplikace přidány požadované obory.
   > [!NOTE]
   > Rozsahy, které jsou aktuálně povolené pro vaši aplikaci, můžete zobrazit na kartě **ověřování** v části **rozsahy OAuth 2,0** .

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>Konfigurace účtu LinkedIn jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
1. Ujistěte se, že používáte adresář, který obsahuje Azure AD B2C tenanta, a to tak, že v horní nabídce vyberete filtr **adresář + předplatné** a zvolíte adresář, který obsahuje vašeho tenanta.
1. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
1. Vyberte **Zprostředkovatelé identity**a pak vyberte **LinkedIn**.
1. Zadejte **název**. Například *LinkedIn*.
1. Pro **ID klienta**zadejte ID klienta aplikace LinkedIn, kterou jste vytvořili dříve.
1. Pro **tajný klíč klienta**zadejte tajný klíč klienta, který jste si poznamenali.
1. Vyberte **Uložit**.

## <a name="migration-from-v10-to-v20"></a>Migrace z verze 1.0 do verze 2.0

LinkedIn nedávno [aktualizoval svá rozhraní API z verze 1.0 do verze 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). V rámci migrace je Azure AD B2C při registraci možné získat pouze úplné jméno uživatele LinkedIn. Pokud je e-mailová adresa jedním z atributů, které se shromažďují při registraci, uživatel musí ručně zadat e-mailovou adresu a ověřit ji.
