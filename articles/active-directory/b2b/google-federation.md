---
title: Přidat služby Google jako zprostředkovatele identity pro Azure Active Directory s B2B | Dokumentace Microsoftu
description: Provést federaci se službou Google, aby uživatelé typu Host k přihlášení do aplikace Azure AD pomocí účtu Gmail
services: active-directory
ms.service: active-directory
ms.component: B2B
ms.topic: conceptual
ms.date: 12/17/2018
ms.author: mimart
author: msmimart
manager: daveba
ms.reviewer: mal
ms.openlocfilehash: 329c12f76862addb6d14271f219b48089c615395
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54428728"
---
# <a name="add-google-as-an-identity-provider-for-b2b-guest-users"></a>Přidat služby Google jako zprostředkovatele identity pro uživatele typu Host B2B

Pomocí nastavení federace se službou Google, můžete povolit pozvaným uživatelům se přihlásit do sdílené aplikace a prostředky s jejich vlastními účty Google bez nutnosti vytvářet Accounts Microsoft (MSA) nebo účty služby Azure AD.  
> [!NOTE]
> Uživatelé typu Host Google musíte se přihlásit pomocí odkazu, který zahrnuje kontextu tenanta, třeba `https://myapps.microsoft.com/<tenant id>`. Přímé odkazy k aplikacím a prostředkům také fungovat tak dlouho, dokud zahrnují kontextu tenanta. K přihlášení pomocí koncových bodů, které nemají žádný kontext tenanta v tuto chvíli nedaří uživatele typu Host. Například použití `https://myapps.microsoft.com`, `https://portal.azure.com`, nebo koncový bod pro běžné týmy způsobí chybu.
 
## <a name="what-is-the-experience-for-the-google-user"></a>Co je prostředí pro uživatele služby Google?
Při odeslání pozvánky uživateli služby Google Gmail uživatele typu Host by měl přístup k sdílených aplikací nebo prostředků pomocí odkazu, který zahrnuje kontextu tenanta. Jejich prostředí se liší v závislosti na tom, jestli jsou už jste přihlášení ke Googlu:
  - Pokud není uživatel typu Host přihlášení ke Googlu, jsou vyzváni k přihlášení ke Googlu.
  - Pokud uživatel typu Host už přihlášení ke Googlu, zobrazí se výzva k výběru účtu, který chce použít. Uživatel musí vybrat účet, který jste použili k pozvánku.

Pokud uživatel typu Host se zobrazí chyba "záhlaví příliš dlouhá", zkuste vymazat jejich soubory cookie nebo můžete otevřít soukromou nebo anonymní okno a zkuste se přihlásit znovu.

![Přihlásit se přes Google](media/google-federation/google-sign-in.png)

## <a name="step-1-configure-a-google-developer-project"></a>Krok 1: Konfigurace projektu pro vývojáře Google
Nejprve vytvořte nový projekt v konzole pro vývojáře Google získat klienta, ID a tajný kód klienta, který později můžete přidat do služby Azure AD. 
1. Přejděte na rozhraní API Google na https://console.developers.google.coma přihlaste se pomocí účtu Google. Doporučujeme vám, že používáte účet Google sdílený týmu.
2. Vytvoření nového projektu: Na řídicím panelu vyberte **vytvořit projekt**a pak vyberte **vytvořit**. Na stránce Nový projekt, zadejte **název projektu**a pak vyberte **vytvořit**.
   
   ![Nový projekt Google](media/google-federation/google-new-project.png)

3. Ujistěte se, že je vybrán nový projekt v nabídce Projekt. Pak otevřete nabídku v vlevo nahoře a vyberte **rozhraní API a služby** > **pověření**.

   ![Přihlašovací údaje Google API](media/google-federation/google-api.png)
 
4. Zvolte **obrazovku se souhlasem OAuth** kartu a zadat **název_aplikace**. (Další nastavení ponechte).

   ![Obrazovka pro vyjádření souhlasu Google OAuth](media/google-federation/google-oauth-consent-screen.png)

5. Přejděte **oprávnění domény** části a zadejte microsoftonline.com.

   ![Části oprávnění domény](media/google-federation/google-oauth-authorized-domains.png)

6. Vyberte **Uložit**.

7. Zvolte **pověření** kartu. V **Vytvořte přihlašovací údaje** nabídce zvolte **ID klienta OAuth**.

   ![Přihlašovací údaje Google API](media/google-federation/google-api-credentials.png)

8. V části **typ aplikace**, zvolte **webovou aplikaci**a potom v části **identifikátory URI pro přesměrování autorizovaní**, zadejte následující identifikátory URI:
   - `https://login.microsoftonline.com` 
   - `https://login.microsoftonline.com/te/<directory id>/oauth2/authresp` <br>(kde `<directory id>` je vaše ID adresáře)
   
    > [!NOTE]
    > Pokud chcete najít ID vašeho adresáře, přejděte na https://portal.azure.coma v části **Azure Active Directory**, zvolte **vlastnosti** a zkopírujte **ID adresáře**.

   ![Vytvoření ID klienta OAuth](media/google-federation/google-create-oauth-client-id.png)

9. Vyberte **Vytvořit**. Zkopírujte ID klienta a tajný kód klienta, které budete používat při přidání poskytovatele identit na portálu Azure AD.

   ![OAuth klienta ID a tajný klíč klienta](media/google-federation/google-auth-client-id-secret.png)

## <a name="step-2-configure-google-federation-in-azure-ad"></a>Krok 2: Konfigurace Google federace ve službě Azure AD 
Teď budete klienta vyberete hodnotu Google ID a tajný kód klienta, tak, že zadáte na portál Azure AD nebo pomocí prostředí PowerShell. Nezapomeňte otestovat konfigurace federace Google pozváním sami pomocí Gmailu adresy a zkusit uplatnění pozvání pomocí pozvaného účtu Google. 

#### <a name="to-configure-google-federation-in-the-azure-ad-portal"></a>Ke konfiguraci federace Google na portálu Azure AD 
1. Přejděte na [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatelé Identity**a potom klikněte na tlačítko **Google** tlačítko.
4. Zadejte název. Zadejte ID klienta a tajný kód klienta, který jste získali dříve. Vyberte **Uložit**. 

   ![Přidat zprostředkovatele identity Google](media/google-federation/google-identity-provider.png)

#### <a name="to-configure-google-federation-by-using-powershell"></a>Ke konfiguraci federace Google pomocí prostředí PowerShell
1. Nainstalujte nejnovější verzi Azure AD PowerShell pro modul grafu ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte následující příkaz: `Connect-AzureAD`.
3. Na řádku přihlášení Přihlaste se pomocí spravovaný účet globálního správce.  
4. Spusťte následující příkaz: 
   
   `New-AzureADMSIdentityProvider -Type Google -Name Google -ClientId [Client ID] -ClientSecret [Client secret]`
 
   > [!NOTE]
   > Použít id klienta a klienta, tajného kódu z aplikace, kterou jste vytvořili v "krok 1: Konfigurace projektu pro vývojáře Google." Další informace najdete v tématu [New-AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/new-azureadmsidentityprovider?view=azureadps-2.0-preview) článku. 
 
## <a name="how-do-i-remove-google-federation"></a>Jak odeberu federační služby Google?
Můžete odstranit nastavení federace služby Google. Pokud tak učiníte, nebudou moct přihlásit Google uživatele typu Host, kteří už využili svou pozvánku, ale můžete poskytnout jim přístup k vašim prostředkům znovu tak, že jejich odstranění z adresáře a znovu vyzývající. 
 
### <a name="to-delete-google-federation-in-the-azure-ad-portal"></a>Chcete-li odstranit federační služby Google na portálu Azure AD: 
1. Přejděte na [Azure Portal](https://portal.azure.com). V levém podokně vyberte **Azure Active Directory**. 
2. Vyberte **organizační vztahy**.
3. Vyberte **zprostředkovatelé Identity**a potom klikněte na tlačítko **Google** tlačítko.
4. Vyberte **Google**a pak vyberte **odstranit**. 
   
   ![Odstranit zprostředkovatele identity v sociálních sítích](media/google-federation/google-social-identity-providers.png)

1. Vyberte **Ano** potvrďte odstranění. 

### <a name="to-delete-google-federation-by-using-powershell"></a>Chcete-li odstranit federační služby Google pomocí prostředí PowerShell: 
1. Nainstalujte nejnovější verzi Azure AD PowerShell pro modul grafu ([AzureADPreview](https://www.powershellgallery.com/packages/AzureADPreview)).
2. Spusťte `Connect-AzureAD`.  
4. Při přihlášení v řádku přihlaste se pomocí spravovaný účet globálního správce.  
5. Zadejte následující příkaz:

    `Remove-AzureADMSIdentityProvider -Id Google-OAUTH`

   > [!NOTE]
   > Další informace najdete v tématu [odebrat AzureADMSIdentityProvider](https://docs.microsoft.com/powershell/module/azuread/Remove-AzureADMSIdentityProvider?view=azureadps-2.0-preview). 