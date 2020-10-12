---
title: Přidat Facebook jako zprostředkovatele identity – Azure AD
description: Federovat s Facebookem umožňuje externím uživatelům (hostům) přihlašovat se k aplikacím Azure AD pomocí vlastních účtů Facebook.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/19/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0b5e1db2c86f6118c3cd333974c9cfd64f747128
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87908652"
---
# <a name="add-facebook-as-an-identity-provider-for-external-identities"></a>Přidat Facebook jako zprostředkovatele identity pro externí identity

Facebook můžete přidat do uživatelských toků samoobslužné registrace (Preview), aby se uživatelé mohli k vašim aplikacím přihlašovat pomocí vlastních účtů Facebook. Pokud chcete uživatelům povolit, aby se přihlásili pomocí Facebooku, nejdřív musíte [Povolit samoobslužnou registraci](self-service-sign-up-user-flow.md) pro vašeho tenanta. Po přidání Facebooku jako poskytovatele identity nastavte pro aplikaci tok uživatele a jako jednu z možností přihlášení vyberte Facebook.

> [!NOTE]
> Uživatelé můžou pomocí svých účtů Facebooku registrovat aplikace pomocí samoobslužných registračních a uživatelských toků. Uživatele nelze pozvat a uplatnit jejich pozvánku pomocí účtu Facebook.

## <a name="create-an-app-in-the-facebook-developers-console"></a>Vytvoření aplikace v konzole pro vývojáře na Facebooku

Chcete-li použít účet Facebook jako [poskytovatele identity](identity-providers.md), je nutné vytvořit aplikaci v konzole pro vývojáře na Facebooku. Pokud ještě nemáte účet Facebook, můžete se zaregistrovat [https://www.facebook.com/](https://www.facebook.com) .

> [!NOTE]  
> V krocích 9 a 16 níže použijte následující adresy URL.
> - Do pole **Adresa URL webu** zadejte adresu vaší aplikace, například `https://contoso.com` .
> - Pro **platné identifikátory URI pro přesměrování OAuth**zadejte `https://login.microsoftonline.com/te/<tenant-id>/oauth2/authresp` . `<tenant-ID>`V okně přehled Azure Active Directory najdete.


1. Přihlaste se ke [službě Facebook pro vývojáře](https://developers.facebook.com/) s přihlašovacími údaji k účtu Facebook.
2. Pokud jste to ještě neudělali, musíte se zaregistrovat jako vývojář Facebooku. Provedete to tak, **že vyberete Začínáme** v pravém horním rohu stránky, přijmete zásady Facebooku a dokončíte registrační kroky.
3. Vyberte **Moje aplikace** a pak **vytvořit aplikaci**.
4. Zadejte **Zobrazovaný název** a platný **kontaktní e-mail**.
5. Vyberte **vytvořit ID aplikace**. To může vyžadovat, abyste přijali zásady platformy Facebook a dokončili online kontrolu zabezpečení.
6. Vyberte **Nastavení**  >  **základní**.
7. Vyberte **kategorii**, například Business a stránky. Tuto hodnotu vyžaduje Facebook, ale nepoužívá se pro Azure AD.
8. V dolní části stránky vyberte **Přidat platformu**a pak vyberte **Web**.
9. Do pole **Adresa URL webu**zadejte příslušnou adresu URL (uvedeno výše).
10. Do pole **Adresa URL zásad ochrany osobních údajů**zadejte adresu URL stránky, kde udržujete informace o ochraně osobních údajů pro aplikaci, například `http://www.contoso.com` .
11. Vyberte **Uložit změny**.
12. V horní části stránky zkopírujte hodnotu **ID aplikace**.
13. Vyberte **Zobrazit** a zkopírujte hodnotu **tajného kódu aplikace**. Pomocí obou z nich můžete nakonfigurovat Facebook jako poskytovatele identity ve vašem tenantovi. **Tajný klíč aplikace** je důležité bezpečnostní pověření.
14. Vyberte znaménko plus vedle položky **produkty**a potom vyberte možnost **nastavit** v části **přihlášení do Facebooku**.
15. V části **přihlášení na Facebooku**vyberte **Nastavení**.
16. V poli **platné identifikátory URI pro přesměrování OAuth**zadejte příslušnou adresu URL (uvedeno výše).
17. V dolní části stránky vyberte **Uložit změny** .
18. Pokud chcete, aby byla aplikace Facebook dostupná pro Azure AD, vyberte selektor stavu v pravém horním rohu stránky a zapněte **ho,** aby se aplikace zajistila jako veřejná, a pak vyberte **Přepnout režim**. V tomto okamžiku se stav změní z **vývoje** na **Live**.
    
## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Konfigurace účtu Facebook jako zprostředkovatele identity
Teď nastavíte ID klienta Facebooku a tajný kód klienta, a to tak, že ho zadáte na portálu Azure AD nebo pomocí PowerShellu. Konfiguraci Facebooku si můžete vyzkoušet tak, že se zaregistrujete pomocí toku uživatele v aplikaci, která je povolená pro samoobslužné registrace.

### <a name="to-configure-facebook-federation-in-the-azure-ad-portal"></a>Konfigurace federace Facebooku na portálu Azure AD
1. Přihlaste se k [Azure Portal](https://portal.azure.com) jako globální správce vašeho TENANTA Azure AD.
2. V části **služby Azure**vyberte **Azure Active Directory**.
3. V nabídce vlevo vyberte **externí identity**.
4. Vyberte **všichni zprostředkovatelé identity**a pak vyberte **Facebook**.
5. Jako **ID klienta**zadejte **ID** aplikace Facebook, kterou jste vytvořili dříve.
6. Pro **tajný klíč klienta**zadejte **tajný kód aplikace** , který jste si poznamenali.

   ![Snímek obrazovky se stránkou přidat poskytovatele sociální identity](media/facebook-federation/add-social-identity-provider-page.png)

7. Vyberte **Uložit**.
### <a name="to-configure-facebook-federation-by-using-powershell"></a>Konfigurace federace Facebooku pomocí prostředí PowerShell
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte následující příkaz: `Connect-AzureAD` .
3. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
4. Spusťte následující příkaz: 
   
   `New-AzureADMSIdentityProvider -Type Facebook -Name Facebook -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Použijte ID klienta a tajný klíč klienta z aplikace, kterou jste vytvořili výše v konzole pro vývojáře na Facebooku. Další informace najdete v článku [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 

## <a name="how-do-i-remove-facebook-federation"></a>Návody odebrat federaci Facebooku?
Můžete odstranit nastavení federace pro Facebook. Pokud to uděláte, všichni uživatelé, kteří se zaregistrovali pomocí toků uživatelů s účty Facebook, se už nebudou moct přihlásit. 

### <a name="to-delete-facebook-federation-in-the-azure-ad-portal"></a>Odstranění federace Facebooku na portálu Azure AD: 
1. Přejděte na web [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vyberte **všechny zprostředkovatele identity**.
4. Na řádku **Facebook** vyberte kontextovou nabídku (**...**) a pak vyberte **Odstranit**. 
5. Kliknutím na **Ano** potvrďte odstranění.

### <a name="to-delete-facebook-federation-by-using-powershell"></a>Odstranění federace Facebooku pomocí prostředí PowerShell: 
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte příkaz `Connect-AzureAD`.  
4. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
5. Zadejte následující příkaz:

    `Remove-AzureADMSIdentityProvider -Id Facebook-OAUTH`

   > [!NOTE]
   > Další informace najdete v tématu [Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 

## <a name="next-steps"></a>Další kroky

- [Přidání samoobslužné registrace do aplikace](self-service-sign-up-user-flow.md)
