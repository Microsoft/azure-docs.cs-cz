---
title: Přidání Google jako zprostředkovatele identity pro B2B – Azure AD
description: Federovat s Google, aby se uživatelé typu Host mohli přihlašovat k aplikacím Azure AD pomocí vlastních účtů Gmail.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.reviewer: mal
ms.custom: it-pro, seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: d2e9acda136654a0dd654eded7b4c17e560fc0aa
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105709697"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Přidat Google jako zprostředkovatele identity pro uživatele typu Host B2B

Nastavením federace s Google můžete pozvaným uživatelům přihlašovat se ke sdíleným aplikacím a prostředkům pomocí vlastních účtů Gmail, aniž byste museli vytvářet účty Microsoft.

Po přidání Google jako jedné z možností přihlašování vaší aplikace může uživatel na **přihlašovací** stránce jednoduše zadat e-mail, který používají pro přihlášení k Google, nebo může vybrat **Možnosti přihlášení** a zvolit možnost **Přihlásit se pomocí Google**. V obou případech se budou přesměrovány na přihlašovací stránku Google pro ověřování.

![Možnosti přihlášení pro uživatele Google](media/google-federation/sign-in-with-google-overview.png)

> [!NOTE]
> Google Federation je navržený speciálně pro uživatele gmail. K federovatí s doménami G Suite použijte [přímou federaci](direct-federation.md).

> [!IMPORTANT]
> **Od 4. ledna 2021** je Google [zastaralá podpora přihlašování v nástroji WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Pokud používáte Google Federation nebo samoobslužnou registraci pomocí služby Gmail, měli byste [testovat kompatibilitu vašich obchodních nativních aplikací](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="what-is-the-experience-for-the-google-user"></a>Jaké jsou možnosti pro uživatele Google?

Když uživatel Google uplatňuje vaše pozvání, jejich prostředí se liší podle toho, jestli jsou už přihlášená k Google:

- Uživatelům typu Host, kteří nejsou přihlášení k Google, se zobrazí výzva k tomu.
- Uživatelům typu Host, kteří už byli přihlášení k Google, se zobrazí výzva k výběru účtu, který chtějí použít. Musí zvolit účet, který jste použili k jejich pozvání.

Uživatelé typu Host, kteří vidí chybu "hlavička je příliš dlouhá", mohou vymazat soubory cookie nebo otevřít soukromé nebo anonymním okno a pokusit se znovu přihlásit.

![Snímek obrazovky zobrazující přihlašovací stránku Google](media/google-federation/google-sign-in.png)

## <a name="sign-in-endpoints"></a>Koncové body přihlášení

Uživatelé typu Host Google se teď můžou přihlašovat k aplikacím pro více tenantů nebo Microsoftu pomocí [společného koncového bodu](redemption-experience.md#redemption-and-sign-in-through-a-common-endpoint) (jinými slovy Obecná adresa URL aplikace, která neobsahuje váš kontext tenanta). Během procesu přihlašování uživatel typu Host zvolí **Možnosti přihlášení** a pak vybere možnost **Přihlásit se k organizaci**. Uživatel pak zadá název vaší organizace a pokračuje v přihlašování pomocí přihlašovacích údajů Google.

Uživatelé typu Host Google můžou také používat koncové body aplikací, které obsahují informace o vašem tenantovi, například:

  * `https://myapps.microsoft.com/?tenantid=<your tenant ID>`
  * `https://myapps.microsoft.com/<your verified domain>.onmicrosoft.com`
  * `https://portal.azure.com/<your tenant ID>`

Uživatelům Google hosta můžete také poskytnout přímý odkaz na aplikaci nebo prostředek tím, že zahrnete informace o vašem tenantovi, například `https://myapps.microsoft.com/signin/Twitter/<application ID?tenantId=<your tenant ID>` .

## <a name="deprecation-of-webview-sign-in-support"></a>Vyřazení podpory přihlašování v nástroji WebView

Od 4. ledna 2021 je Google [zastaralá podpora vloženého přihlášení k webu WebView](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html). Pokud používáte Google Federation nebo [samoobslužnou registraci pomocí služby Gmail](identity-providers.md), měli byste testovat kompatibilitu vašich obchodních nativních aplikací. Pokud vaše aplikace obsahují obsah WebView, který vyžaduje ověření, uživatelé Google Gmail se nebudou moci ověřit. Níže jsou uvedené známé scénáře, které budou mít vliv na uživatele Gmail:

- Aplikace pro Windows, které používají vložené WebView nebo WebAccountManager (WAM) ve starších verzích Windows.
- Další nativní aplikace, které jste vyvinuli pro ověřování pomocí vložené architektury prohlížeče.

Tato změna nemá vliv na:

- Aplikace pro Windows, které používají vložené WebView nebo WebAccountManager (WAM) na nejnovějších verzích Windows
- Aplikace Microsoft iOS
- Identity G Suite, například pokud používáte [přímou federaci](direct-federation.md) založenou na SAML s G Suite

Pokračujeme v testování různých platforem a scénářů a tento článek aktualizujeme odpovídajícím způsobem.
### <a name="to-test-your-apps-for-compatibility"></a>Testování kompatibility aplikací

1. Pokud chcete zjistit, jestli jsou vaše aplikace ovlivněné, postupujte podle [pokynů pro Google](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) .
2. Pomocí Fiddler nebo jiného testovacího nástroje během přihlašování vloží hlavičku a použijte externí identitu Google k otestování přihlášení:

   1. Přidání účtů Google-Accounts-check-OAuth-Login: true do hlaviček požadavků HTTP při odeslání požadavků do accounts.google.com.
   1. Pokuste se přihlásit k aplikaci zadáním adresy Gmail na přihlašovací stránce accounts.google.com.
   1. Pokud se přihlášení nezdaří a zobrazí se chyba, například "tento prohlížeč nebo aplikace nemusí být zabezpečené", vaše externí identity Google budou zablokovány pro přihlášení.

3. Problém vyřešte provedením jedné z následujících akcí:

   - Pokud vaše aplikace pro Windows používá vložené WebView nebo WebAccountManager (WAM) ve starší verzi Windows, aktualizujte na nejnovější verzi Windows.
   - Upravte své aplikace tak, aby se k přihlášení používal prohlížeč systému. Podrobnosti najdete v tématu [vložené webové uživatelské rozhraní vs-System](../develop/msal-net-web-browsers.md#embedded-vs-system-web-ui) v dokumentaci k MSAL.NET.  


## <a name="step-1-configure-a-google-developer-project"></a>Krok 1: konfigurace projektu pro vývojáře Google
Nejdřív vytvořte nový projekt v konzole pro vývojáře Google, abyste získali ID klienta a tajný klíč klienta, které můžete později přidat do Azure Active Directory (Azure AD). 
1. Přejít na rozhraní Google API na adrese https://console.developers.google.com a přihlaste se pomocí svého účtu Google. Doporučujeme použít sdílený týmový účet Google.
2. Pokud budete vyzváni, přijměte podmínky služby.
3. Vytvořit nový projekt: v levém horním rohu stránky vyberte seznam projektu a pak na stránce **Vyberte projekt** vyberte **Nový projekt**.
4. Na stránce **Nový projekt** zadejte název projektu (například **Azure AD B2B**) a pak vyberte **vytvořit**: 
   
   ![Snímek obrazovky, který zobrazuje novou stránku projektu.](media/google-federation/google-new-project.png)

4. Na stránce **& služby API** vyberte v novém projektu možnost **Zobrazit** .

5. Na kartě rozhraní API vyberte **Přejít na rozhraní API** . Vyberte **obrazovku pro vyjádření souhlasu OAuth**.

6. Vyberte **externí** a pak vyberte **vytvořit**. 

7. Na **obrazovce pro vyjádření souhlasu OAuth** zadejte **název aplikace**:

   ![Snímek obrazovky, na kterém se zobrazuje obrazovka pro vyjádření souhlasu Google OAuth.](media/google-federation/google-oauth-consent-screen.png)

8. Přejděte do části **autorizované domény** a zadejte **microsoftonline.com**:

   ![Snímek obrazovky, který ukazuje oddíl autorizovaných domén.](media/google-federation/google-oauth-authorized-domains.PNG)

9. Vyberte **Uložit**.

10. Vyberte **Pověření**. V nabídce **vytvořit pověření** vyberte **ID klienta OAuth**:

    ![Snímek obrazovky, který zobrazuje nabídku pro vytvoření přihlašovacích údajů rozhraní Google API](media/google-federation/google-api-credentials.png)

11. V části **Typ aplikace** vyberte **Webová aplikace**. Poskytněte aplikaci vhodný název, jako je například **Azure AD B2B**. V části **autorizované identifikátory URI pro přesměrování** zadejte následující identifikátory URI:
    - `https://login.microsoftonline.com`
    - `https://login.microsoftonline.com/te/<tenant ID>/oauth2/authresp` <br>(kde `<tenant ID>` je vaše ID tenanta)
   
    > [!NOTE]
    > ID tenanta zjistíte tak, že přejdete na [Azure Portal](https://portal.azure.com). V části **Azure Active Directory** vyberte **vlastnosti** a zkopírujte **ID tenanta**.

    ![Snímek obrazovky, který zobrazuje oddíl autorizovaných identifikátorů URI pro přesměrování](media/google-federation/google-create-oauth-client-id.png)

12. Vyberte **Vytvořit**. Zkopírujte ID klienta a tajný klíč klienta. Budete je používat při přidávání poskytovatele identity v Azure Portal.

    ![Snímek obrazovky zobrazující ID klienta OAuth a tajný klíč klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2: Konfigurace Google federace ve službě Azure AD 
Teď nastavíte ID klienta Google a tajný klíč klienta. K tomu můžete použít Azure Portal nebo PowerShell. Ujistěte se, že jste otestovali konfiguraci Google federace tím, že vás vyzveme sami. Použijte adresu Gmail a zkuste uplatnit pozvánku s pozvaným účtem Google. 

**Konfigurace Google federace v Azure Portal** 
1. Přejděte na [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vyberte **všichni zprostředkovatelé identity** a pak vyberte tlačítko **Google** .
4. Zadejte ID klienta a tajný klíč klienta, které jste získali dříve. Vyberte **Uložit**: 

   ![Snímek obrazovky zobrazující stránku přidat poskytovatele identity Google](media/google-federation/google-identity-provider.png)

**Konfigurace Google federace pomocí prostředí PowerShell**
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte tento příkaz: `Connect-AzureAD`
3. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
4. Spusťte následující příkaz: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId <client ID> -ClientSecret <client secret>`
 
   > [!NOTE]
   > Použijte ID klienta a tajný klíč klienta z aplikace, kterou jste vytvořili v části "krok 1: konfigurace projektu pro vývojáře Google". Další informace najdete v tématu [New-AzureADMSIdentityProvider](/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview&preserve-view=true). 
 
## <a name="how-do-i-remove-google-federation"></a>Návody odebrat Google Federation?
Můžete odstranit nastavení Google Federation. Pokud to uděláte, uživatelé typu Host Google, kteří už tuto pozvánku provedli, se nebudou moct přihlásit. Můžete jim ale udělit přístup k prostředkům tím, že je odstraníte z adresáře a znovu je dodáte. 
 
**Odstranění Google federace na portálu Azure AD**
1. Přejděte na [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **externí identity**.
3. Vyberte **všechny zprostředkovatele identity**.
4. Na řádku **Google** vyberte tlačítko se třemi tečkami (**...**) a pak vyberte **Odstranit**. 
   
   ![Snímek obrazovky, který zobrazuje tlačítko Odstranit pro poskytovatele sociální identity](media/google-federation/google-social-identity-providers.png)

1. Kliknutím na **Ano** potvrďte odstranění. 

**Odstranění Google federace pomocí PowerShellu** 
1. Nainstalujte nejnovější verzi Azure AD PowerShellu pro modul Graph ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte `Connect-AzureAD`.  
4. V příkazovém řádku pro přihlášení se přihlaste pomocí účtu spravovaného globálního správce.  
5. Zadejte následující příkaz:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Další informace najdete v tématu [Remove-AzureADMSIdentityProvider](/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview&preserve-view=true).
