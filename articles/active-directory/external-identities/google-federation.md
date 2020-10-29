---
title: Přidání Google jako zprostředkovatele identity pro B2B – Azure AD
description: Federovat s Google, aby se uživatelé typu Host mohli přihlašovat k aplikacím Azure AD pomocí vlastních účtů Gmail.
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
ms.openlocfilehash: ff8912794169cf61f394a097248a8476b2e0c0f3
ms.sourcegitcommit: dd45ae4fc54f8267cda2ddf4a92ccd123464d411
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92926231"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Přidat Google jako zprostředkovatele identity pro uživatele typu Host B2B

Nastavením federace s Google můžete pozvaným uživatelům přihlašovat se ke sdíleným aplikacím a prostředkům pomocí vlastních účtů Gmail, aniž byste museli vytvářet účty Microsoft. 

> [!NOTE]
> Google Federation je navržený speciálně pro uživatele gmail. K federovatí s doménami G Suite použijte [přímou federaci](direct-federation.md).

## <a name="what-is-the-experience-for-the-google-user"></a>Jaké jsou možnosti pro uživatele Google?
Po odeslání pozvánky uživatelům služby Google Gmail by měli uživatelé typu Host přístup k vašim sdíleným aplikacím nebo prostředkům pomocí odkazu, který zahrnuje kontext tenanta. Jejich možnosti se liší v závislosti na tom, jestli jsou už přihlášení k Google:
  - Uživatelům typu Host, kteří nejsou přihlášení k Google, se zobrazí výzva k tomu.
  - Uživatelům typu Host, kteří už byli přihlášení k Google, se zobrazí výzva k výběru účtu, který chtějí použít. Musí zvolit účet, který jste použili k jejich pozvání.

Uživatelé typu Host, kteří vidí chybu "hlavička je příliš dlouhá", mohou vymazat soubory cookie nebo otevřít soukromé nebo anonymním okno a pokusit se znovu přihlásit.

![Snímek obrazovky zobrazující přihlašovací stránku Google](media/google-federation/google-sign-in.png)

## <a name="limitations"></a>Omezení

Týmy plně podporují uživatele typu Host Google na všech zařízeních. Uživatelé Google se můžou přihlásit ke týmům ze společného koncového bodu `https://teams.microsoft.com` , jako je.

Společné koncové body jiných aplikací možná nepodporují uživatele Google. Uživatelé typu Host Google se musí přihlásit pomocí odkazu, který obsahuje informace o vašem tenantovi. Následují příklady:
  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://portal.azure.com/<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`

   Pokud se uživatelé Google Guest pokusí použít odkaz jako `https://myapps.microsoft.com` nebo, zobrazí se `https://portal.azure.com` Chyba.

Uživatelům Google hosta můžete také poskytnout přímý odkaz na aplikaci nebo prostředek, pokud tento odkaz obsahuje informace o vašem tenantovi. Například, `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>`. 

## <a name="step-1-configure-a-google-developer-project"></a>Krok 1: konfigurace projektu pro vývojáře Google
Nejdřív vytvořte nový projekt v konzole pro vývojáře Google, abyste získali ID klienta a tajný klíč klienta, které můžete později přidat do Azure Active Directory (Azure AD). 
1. Přejít na rozhraní Google API na adrese https://console.developers.google.com a přihlaste se pomocí svého účtu Google. Doporučujeme použít sdílený týmový účet Google.
2. Pokud budete vyzváni, přijměte podmínky služby.
3. Vytvořit nový projekt: na řídicím panelu vyberte **vytvořit projekt** , zadejte název projektu (například **Azure AD B2B** ) a pak vyberte **vytvořit** : 
   
   ![Snímek obrazovky, který zobrazuje novou stránku projektu.](media/google-federation/google-new-project.png)

4. Na stránce **& služby API** vyberte v novém projektu možnost **Zobrazit** .

5. Na kartě rozhraní API vyberte **Přejít na rozhraní API** . Vyberte **obrazovku pro vyjádření souhlasu OAuth** .

6. Vyberte **externí** a pak vyberte **vytvořit** . 

7. Na **obrazovce pro vyjádření souhlasu OAuth** zadejte **název aplikace** :

   ![Snímek obrazovky, na kterém se zobrazuje obrazovka pro vyjádření souhlasu Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Přejděte do části **autorizované domény** a zadejte **microsoftonline.com** :

   ![Snímek obrazovky, který ukazuje oddíl autorizovaných domén.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Vyberte **Uložit** .

10. Vyberte **Pověření** . V nabídce **vytvořit pověření** vyberte **ID klienta OAuth** :

    ![Snímek obrazovky, který zobrazuje nabídku pro vytvoření přihlašovacích údajů rozhraní Google API](media/google-federation/google-api-credentials.png)

11. V části **Typ aplikace** vyberte **Webová aplikace** . Poskytněte aplikaci vhodný název, jako je například **Azure AD B2B** . V části **autorizované identifikátory URI pro přesměrování** zadejte následující identifikátory URI:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(kde `<tenant ID>` je vaše ID tenanta)
   
    > [!NOTE]
    > ID tenanta zjistíte tak, že přejdete na [Azure Portal](https://portal.azure.com). V části **Azure Active Directory** vyberte **vlastnosti** a zkopírujte **ID tenanta** .

    ![Snímek obrazovky, který zobrazuje oddíl autorizovaných identifikátorů URI pro přesměrování](media/google-federation/google-create-oauth-client-id.png)

12. Vyberte **Vytvořit** . Zkopírujte ID klienta a tajný klíč klienta. Budete je používat při přidávání poskytovatele identity v Azure Portal.

    ![Snímek obrazovky zobrazující ID klienta OAuth a tajný klíč klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2: Konfigurace Google federace ve službě Azure AD 
Teď nastavíte ID klienta Google a tajný klíč klienta. K tomu můžete použít Azure Portal nebo PowerShell. Ujistěte se, že jste otestovali konfiguraci Google federace tím, že vás vyzveme sami. Použijte adresu Gmail a zkuste uplatnit pozvánku s pozvaným účtem Google. 

**Konfigurace Google federace v Azure Portal** 
1. Přejděte na web [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory** . 
2. Vyberte **externí identity** .
3. Vyberte **všichni zprostředkovatelé identity** a pak vyberte tlačítko **Google** .
4. Zadejte ID klienta a tajný klíč klienta, které jste získali dříve. Vyberte **Uložit** : 

   ![Snímek obrazovky zobrazující stránku přidat poskytovatele identity Google](media/google-federation/google-identity-provider.png)

**Konfigurace Google federace pomocí prostředí PowerShell**
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte tento příkaz: `Connect-AzureAD`
3. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
4. Spusťte následující příkaz: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Použijte ID klienta a tajný klíč klienta z aplikace, kterou jste vytvořili v části "krok 1: konfigurace projektu pro vývojáře Google". Další informace najdete v tématu [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview). 
 
## <a name="how-do-i-remove-google-federation"></a>Návody odebrat Google Federation?
Můžete odstranit nastavení Google Federation. Pokud to uděláte, uživatelé typu Host Google, kteří už tuto pozvánku provedli, se nebudou moct přihlásit. Můžete jim ale udělit přístup k prostředkům tím, že je odstraníte z adresáře a znovu je zve. 
 
**Odstranění Google federace na portálu Azure AD**
1. Přejděte na web [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory** . 
2. Vyberte **externí identity** .
3. Vyberte **všechny zprostředkovatele identity** .
4. Na řádku **Google** vyberte tlačítko se třemi tečkami ( **...** ) a pak vyberte **Odstranit** . 
   
   ![Snímek obrazovky, který zobrazuje tlačítko Odstranit pro poskytovatele sociální identity](media/google-federation/google-social-identity-providers.png)

1. Kliknutím na **Ano** potvrďte odstranění. 

**Odstranění Google federace pomocí PowerShellu** 
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte `Connect-AzureAD`.  
4. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
5. Zadejte následující příkaz:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Další informace najdete v tématu [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview).
