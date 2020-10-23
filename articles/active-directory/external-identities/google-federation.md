---
title: Přidání Google jako zprostředkovatele identity pro B2B – Azure AD
description: Federovat s Google, aby se uživatelé typu Host mohli přihlašovat k aplikacím Azure AD pomocí vlastního účtu Gmail
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/11/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: a8c07a6a67e97deafa2f611b297574a801aa356d
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/23/2020
ms.locfileid: "92441942"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Přidat Google jako zprostředkovatele identity pro uživatele typu Host B2B

Nastavením federace s Google můžete pozvaným uživatelům přihlašovat se ke sdíleným aplikacím a prostředkům pomocí vlastních účtů Gmail, aniž byste museli vytvářet účty Microsoft (účty spravované služby). 

> [!NOTE]
> Google Federation je navržený speciálně pro uživatele gmail. K federovatí s doménami G Suite použijte [funkci Direct Federation](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Jaké jsou možnosti pro uživatele Google?
Když odešlete pozvánku uživateli Google Gmail, měl by uživatel typu Host přistupovat ke sdíleným aplikacím nebo prostředkům pomocí odkazu, který zahrnuje kontext tenanta. Jejich možnosti se liší v závislosti na tom, jestli jsou už přihlášení k Google:
  - Pokud uživatel typu Host není přihlášený k Google, zobrazí se výzva k přihlášení do Google.
  - Pokud je uživatel typu host již přihlášen k Google, zobrazí se výzva k výběru účtu, který chtějí použít. Musí zvolit účet, který jste použili k jejich pozvání.

Pokud uživatel typu Host uvidí chybu "záhlaví je příliš dlouhá", může zkusit vymazat soubory cookie nebo otevřít soukromé nebo anonymním okno a pokusit se znovu přihlásit.

![Snímek obrazovky zobrazující přihlašovací stránku Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Omezení

Týmy plně podporují uživatele typu Host Google na všech zařízeních. Uživatelé Google se můžou přihlásit ke týmům ze společného koncového bodu `https://teams.microsoft.com` , jako je.

Společné koncové body jiných aplikací možná nepodporují uživatele Google. Uživatelé typu Host Google se musí přihlásit pomocí odkazu, který obsahuje informace o vašem tenantovi. Tady jsou příklady:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Pokud se uživatelé Google Guest pokusí použít odkaz `https://myapps.microsoft.com` , například nebo, zobrazí se `https://portal.azure.com` Chyba.

Uživatelům Google hosta můžete také poskytnout přímý odkaz na aplikaci nebo prostředek, pokud tento odkaz obsahuje vaše informace o tenantovi, například `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` . 

## <a name="step-1-configure-a-google-developer-project"></a>Krok 1: konfigurace projektu pro vývojáře Google
Nejdřív vytvořte nový projekt v konzole pro vývojáře Google, abyste získali ID klienta a tajný klíč klienta, které můžete později přidat do Azure AD. 
1. Přejít na rozhraní Google API na adrese https://console.developers.google.com a přihlaste se pomocí svého účtu Google. Doporučujeme použít sdílený týmový účet Google.
2. Pokud se zobrazí výzva, přijměte podmínky služby.
3. Vytvořit nový projekt: na řídicím panelu vyberte **vytvořit projekt**, zadejte název projektu (například Azure AD B2B) a pak vyberte **vytvořit**. 
   
   ![Snímek obrazovky s novou stránkou projektu pro Google](media/google-federation/google-new-project.png)

4. Na stránce **rozhraní api & služby** , která vám teď prezentuje, klikněte na **Zobrazit** pod novým projektem.

5. Na kartě rozhraní API klikněte na **Přejít na rozhraní API** . Vyberte **obrazovku pro vyjádření souhlasu OAuth**.

6. Vyberte **externí**a pak vyberte **vytvořit**. 

7. Na **obrazovce pro vyjádření souhlasu OAuth**zadejte **název aplikace**. 

   ![Snímek obrazovky s možností zobrazení souhlasu Google OAuth](media/google-federation/google-oauth-consent-screen.png)

8. Přejděte do části **autorizované domény** a zadejte microsoftonline.com.

   ![Snímek obrazovky se sekcí autorizovaných domén](media/google-federation/google-oauth-authorized-domains.PNG)

9. Vyberte **Uložit**.

10. Vyberte **přihlašovací údaje**. V nabídce **vytvořit pověření** vyberte **ID klienta OAuth**.

    ![Snímek obrazovky s možností vytvoření přihlašovacích údajů rozhraní Google API](media/google-federation/google-api-credentials.png)

11. V části **Typ aplikace**zvolte možnost **Webová aplikace** , zadejte vhodný název, například "Azure AD B2B" a potom v části **autorizované identifikátory URI pro přesměrování**zadejte následující identifikátory URI:
    - `https://login.microsoftonline.com` 
    - `https://login.microsoftonline.com/te/<tenant id>/oauth2/authresp` <br>(kde `<tenant id>` je vaše ID tenanta)
   
    > [!NOTE]
    > ID tenanta zjistíte tak, že přejdete na https://portal.azure.com a v části **Azure Active Directory**zvolíte **vlastnosti** a zkopírujete **ID tenanta**.

    ![Snímek obrazovky s povoleným oddílem identifikátorů URI pro přesměrování](media/google-federation/google-create-oauth-client-id.png)

12. Vyberte **Vytvořit**. Zkopírujte ID klienta a tajný klíč klienta, které budete používat při přidávání zprostředkovatele identity na portálu Azure AD.

    ![Snímek obrazovky zobrazující ID klienta OAuth a tajný klíč klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2: Konfigurace Google federace ve službě Azure AD 
Teď nastavíte ID klienta Google a tajný kód klienta, a to tak, že ho zadáte na portálu Azure AD nebo pomocí PowerShellu. Ujistěte se, že jste otestovali konfiguraci Google Federation, a to tak, že na adresu Gmail pozvánku vyzkoušíte a zkusíte uplatnit pozvání k vašemu pozvanému účtu Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Konfigurace Google Federation na portálu Azure AD 
1. Přejděte na web [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vyberte **všichni zprostředkovatelé identity**a pak klikněte na tlačítko **Google** .
4. Pak zadejte ID klienta a tajný klíč klienta, které jste získali dříve. Vyberte **Uložit**. 

   ![Snímek obrazovky se stránkou přidat poskytovatele identity Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Konfigurace Google federace pomocí prostředí PowerShell
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte následující příkaz: `Connect-AzureAD` .
3. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
4. Spusťte následující příkaz: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Použijte ID klienta a tajný klíč klienta z aplikace, kterou jste vytvořili v části "krok 1: konfigurace projektu pro vývojáře Google". Další informace najdete v článku [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) . 
 
## <a name="how-do-i-remove-google-federation"></a>Návody odebrat Google Federation?
Můžete odstranit nastavení Google Federation. Pokud to uděláte, uživatelé typu Host Google, kteří už tuto pozvánku nastavili, se nebudou moct přihlásit, ale můžete jim dát přístup k prostředkům tak, že je odstraníte z adresáře a znovu je zvete. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Pokud chcete odstranit Google Federation na portálu Azure AD: 
1. Přejděte na web [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vyberte **všechny zprostředkovatele identity**.
4. Na řádku **Google** vyberte kontextovou nabídku (**...**) a pak vyberte **Odstranit**. 
   
   ![Snímek obrazovky znázorňující možnost odstranění pro poskytovatele sociální identity](media/google-federation/google-social-identity-providers.png)

1. Kliknutím na **Ano** potvrďte odstranění. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Odstranění Google federace pomocí prostředí PowerShell: 
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Je nutné spustit `Connect-AzureAD`.  
4. Na příkazovém řádku přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
5. Zadejte následující příkaz:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Další informace najdete v tématu [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).