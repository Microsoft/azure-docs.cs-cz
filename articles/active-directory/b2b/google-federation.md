---
title: Přidání Google jako zprostředkovatele identity pro B2B – Azure AD
description: Federate s Googlem, aby se uživatelé typu Host mohli přihlásit k vašim aplikacím Azure AD pomocí vlastního účtu Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 03/05/2020
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 72c18e48c27942c7bea47931ec79a31af941064e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79126648"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Přidání Google jako poskytovatele identity pro uživatele typu Host B2B

Nastavením federace se společností Google můžete pozvaným uživatelům povolit přihlášení ke sdíleným aplikacím a prostředkům pomocí vlastních účtů Gmail, aniž byste museli vytvářet účty Microsoft (MSA). 

> [!NOTE]
> Federace Google je určena speciálně pro uživatele Gmailu. Chcete-li federovat s doménami G Suite, použijte [funkci přímé federace](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Jaké je prostředí pro uživatele Google?
Když odešlete pozvánku uživateli Služby Google Gmail, měl by mít uživatel typu Host přístup k vašim sdíleným aplikacím nebo prostředkům pomocí odkazu, který obsahuje kontext klienta. Jejich zkušenosti se liší v závislosti na tom, zda jsou již přihlášeni k Googlu:
  - Pokud uživatel typu Host není přihlášen k Googlu, zobrazí se výzva k přihlášení k Googlu.
  - Pokud je uživatel typu Host již přihlášen k Googlu, bude vyzván k výběru účtu, který chce používat. Musí si vybrat účet, který jste použili k jejich pozvání.

Pokud se uživateli typu Host zobrazí chyba "záhlaví je příliš dlouhé", může zkusit vymazat soubory cookie nebo může otevřít soukromé nebo anonymní okno a pokusit se přihlásit znovu.

![Snímek obrazovky s přihlašovací stránkou Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Omezení

Týmy plně podporují uživatele google host na všech zařízeních. Uživatelé Google se můžou k Teams `https://teams.microsoft.com`přihlásit ze společného koncového bodu, jako je .

Běžné koncové body jiných aplikací nemusí uživatele Google podporovat. Uživatelé typu Google, kteří hostují, se musí přihlásit pomocí odkazu, který obsahuje informace o vašem klientovi. Tady jsou příklady:
  * `https://myapps.microsoft.com/?tenantid=<your tenant id>`
  * `https://portal.azure.com/<your tenant id>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Pokud se uživatelé typu Host `https://myapps.microsoft.com` Google `https://portal.azure.com`pokusí použít odkaz, například nebo , zobrazí se chyba.

Uživatelům typu Google můžete také poskytnout přímý odkaz na aplikaci nebo prostředek, pokud `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`tento odkaz obsahuje například informace o vašem klientovi . 

## <a name="step-1-configure-a-google-developer-project"></a>Krok 1: Konfigurace vývojářského projektu Google
Nejprve vytvořte nový projekt v google vývojářské konzoli a získejte ID klienta a tajný klíč klienta, který můžete později přidat do služby Azure AD. 
1. Přejděte na adresu https://console.developers.google.comGoogle API na webu a přihlaste se pomocí svého účtu Google. Doporučujeme používat sdílený tým ový účet Google.
2. Vytvoření nového projektu: Na řídicím panelu vyberte **Vytvořit projekt**a pak vyberte **Vytvořit**. Na stránce Nový projekt zadejte **název projektu**a pak vyberte **Vytvořit**.
   
   ![Snímek obrazovky s novou stránkou projektu pro Google](media/google-federation/google-new-project.png)

3. Ujistěte se, že je v nabídce projektu vybrán nový projekt. Potom v části **API & Services**vyberte obrazovku **souhlasu OAuth**.

4. Vyberte **Externí**a pak vyberte **Vytvořit**. 
5. Na **obrazovce Souhlas OAuth**zadejte **název aplikace**. (Ostatní nastavení ponechte.)

   ![Snímek obrazovky s možností obrazovky souhlasu Google OAuth](media/google-federation/google-oauth-consent-screen.png)

6. Přejděte do části **Autorizované domény** a zadejte microsoftonline.com.

   ![Snímek obrazovky s částí Autorizované domény](media/google-federation/google-oauth-authorized-domains.png)

7. Vyberte **Uložit**.

8. Zvolte **Pověření**. V nabídce **Vytvořit přihlašovací údaje** zvolte **ID klienta OAuth**.

   ![Snímek obrazovky s možností vytvoření přihlašovacích údajů rozhraní Google](media/google-federation/google-api-credentials.png)

9. V části **Typ aplikace**zvolte **Webová aplikace**a potom v části **Autorizované identifikátory URI přesměrování**zadejte následující identifikátory URI:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(kde `<directory id>` je vaše id adresáře)
   
     > [!NOTE]
     > Pokud chcete najít ID https://portal.azure.comadresáře, přejděte na a v části **Azure Active Directory**zvolte **Vlastnosti** a zkopírujte **ID adresáře**.

   ![Snímek obrazovky s částí Autorizované identifikátory URI přesměrování](media/google-federation/google-create-oauth-client-id.png)

10. Vyberte **Vytvořit**. Zkopírujte ID klienta a tajný klíč klienta, který použijete při přidání zprostředkovatele identity na portálAzure AD.

   ![Snímek obrazovky s ID klienta OAuth a tajným tajemstvím klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2: Konfigurace federace Google ve službě Azure AD 
Teď nastavíte ID klienta Google a tajný klíč klienta, a to buď zadáním na portál Azure AD nebo pomocí PowerShellu. Nezapomeňte otestovat konfiguraci federací Google tak, že se budete zvát pomocí adresy Gmailu a pokusíte se pozvánku uplatnit pomocí pozvaného účtu Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Konfigurace federace Google na portálu Azure AD 
1. Přejděte na [portál Azure](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **Zprostředkovatelé identity**a klikněte na tlačítko **Google.**
4. Zadejte název. Poté zadejte ID klienta a tajný klíč klienta, který jste získali dříve. Vyberte **Uložit**. 

   ![Snímek obrazovky se stránkou Přidat zprostředkovatele identity Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Konfigurace federace Google pomocí PowerShellu
1. Nainstalujte nejnovější verzi modulu Azure AD PowerShell pro graf[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Spusťte následující `Connect-AzureAD`příkaz: .
3. Na výzvu k přihlášení se přihlaste pomocí spravovaného účtu globálního správce.  
4. Spusťte následující příkaz: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Použijte ID klienta a tajný klíč klienta z aplikace, kterou jste vytvořili v části Krok 1: Konfigurace vývojářského projektu Google. Další informace naleznete v článku [New-AzureADMSIdentityProvider.](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) 
 
## <a name="how-do-i-remove-google-federation"></a>Jak odstraním federaci Google?
Nastavení federace Google můžete smazat. Pokud tak učiníte, uživatelé google host, kteří již pozvánku uplatnili, se nebudou moci přihlásit, ale můžete jim znovu udělit přístup k vašim zdrojům tak, že je smažete z adresáře a znovu je pozvete. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Odstranění federace Google na portálu Azure AD: 
1. Přejděte na [portál Azure](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatele identity**.
4. Na řádku **Google** vyberte kontextovou nabídku (**...**) a pak vyberte **Odstranit**. 
   
   ![Snímek obrazovky s možností Odstranit pro poskytovatele sociální identity](media/google-federation/google-social-identity-providers.png)

1. Chcete-li odstranění potvrdit, vyberte **možnost Ano.** 

### <a name="to-delete-google-federation-by-using-powershell"></a>Odstranění federace Google pomocí PowerShellu: 
1. Nainstalujte nejnovější verzi modulu Azure AD PowerShell pro graf[(AzureADPreview).](https://www.powershellgallery.com/packages/AzureADPreview)
2. Spusťte `Connect-AzureAD`.  
4. V případě výzvy pro přihlášení se přihlaste pomocí spravovaného účtu globálního správce.  
5. Zadejte následující příkaz:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Další informace naleznete v [tématu Remove-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 
