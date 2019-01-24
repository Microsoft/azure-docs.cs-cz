---
title: Nastavení registrace a přihlášení pomocí účtu sítě Facebook pomocí Azure Active Directory B2C | Dokumentace Microsoftu
description: Zaregistrujte se a přihlaste se poskytují zákazníkům s účty služby Facebook ve svých aplikacích pomocí služby Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/11/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 855b43c39ac8bc3a1b6bb818a9770897e983c2ea
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54843595"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Nastavení registrace a přihlášení pomocí účtu sítě Facebook pomocí Azure Active Directory B2C

## <a name="create-a-facebook-application"></a>Vytvořit aplikaci pro Facebook

Použít účet Facebook jako zprostředkovatele identity v Azure Active Directory (Azure AD) B2C, budete muset vytvořit aplikaci ve vašem tenantovi, který ho zastupuje. Pokud ještě nemáte účet na Facebooku, získáte ji na [ https://www.facebook.com/ ](https://www.facebook.com/).

1. Přihlaste se k [Facebook pro vývojáře](https://developers.facebook.com/) pomocí svých přihlašovacích údajů účtu sítě Facebook.
2. Pokud jste tak již neučinili, musíte zaregistrovat jako vývojář sítě Facebook. Chcete-li to provést, vyberte **zaregistrovat** v pravém horním rohu stránky, přijmout zásady na Facebooku a dokončete registraci.
3. Vyberte **Moje aplikace** a potom klikněte na tlačítko **přidejte novou aplikaci**. 
4. Zadejte **zobrazovaný název** a platný **E-mail kontaktu**.
5. Klikněte na tlačítko **vytvoření ID aplikace**. To může vyžadovat přijmout zásady platformy Facebooku a proveďte kontrolu zabezpečení online.
6. Vyberte **nastavení** > **základní**.
7. Zvolte **kategorie**, například `Business and Pages`. Tato hodnota je potřeba pomocí Facebooku, ale není možné použít u Azure AD B2C.
8. V dolní části stránky vyberte **přidat platformy**a pak vyberte **webu**.
9. V **adresa URL webu**, zadejte `https://your-tenant-name.b2clogin.com/` nahrazení `your-tenant-name` s názvem vašeho tenanta. Zadejte adresu URL, **adresa URL zásad ochrany osobních údajů**, například `http://www.contoso.com`. Adresa URL zásad je stránka, které spravujete, k poskytnutí informací o ochraně osobních údajů pro vaši aplikaci.
10. Vyberte **uložit změny**.
11. V horní části stránky, zkopírujte hodnotu **ID aplikace**. 
12. Klikněte na tlačítko **zobrazit** a zkopírujte hodnotu **tajný kód aplikace**. Obou z nich použijete ke konfiguraci sítě Facebook jako zprostředkovatele identity ve vašem tenantovi. **Tajný kód aplikace** je důležitým bezpečnostním pověřením.
13. Vyberte **produkty**a pak vyberte **nastavení** pod **přihlášení k Facebooku**.
14. Vyberte **nastavení** pod **přihlášení k Facebooku**.
15. V **identifikátory URI pro přesměrování OAuth platný**, zadejte `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`. Nahraďte `your-tenant-name` s názvem vašeho tenanta. Klikněte na tlačítko **uložit změny** v dolní části stránky.
16. K zajištění dostupnosti sítě Facebook aplikace Azure AD B2C, klepněte na volič stavu v horní části stránky a znovu je zapnout **na** zveřejnit aplikaci, a potom klikněte na **potvrdit**.  V tomto okamžiku by měl stav změnit z **vývoj** k **Live**.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurace účtu sítě Facebook jako zprostředkovatele identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) jako globální správce vašeho tenanta Azure AD B2C.
2. Ujistěte se, že používáte adresáře, který obsahuje vašeho tenanta Azure AD B2C kliknutím **filtr adresářů a předplatných** v horní nabídce a výběrem adresáře, který obsahuje váš tenant. 
3. Zvolte **Všechny služby** v levém horním rohu portálu Azure Portal a vyhledejte a vyberte **Azure AD B2C**.
4. Vyberte **zprostředkovatelé Identity**a pak vyberte **přidat**.
5. Zadejte **název**. Zadejte například *Facebook*.
6. Vyberte **typ zprostředkovatele identit**vyberte **Facebook**a klikněte na tlačítko **OK**.
7. Vyberte **nastavit tohoto zprostředkovatele identity** a zadejte ID aplikace, které jste si poznamenali dříve, jako **ID klienta** a zadejte tajný kód aplikace, které jste si poznamenali jako **tajný kód klienta** z aplikace sítě Facebook, kterou jste vytvořili dříve.
8. Klikněte na tlačítko **OK** a potom klikněte na tlačítko **vytvořit** uložte konfiguraci sítě Facebook.
