---
title: Přihlaste se pomocí e-mailu jako alternativní přihlašovací ID pro Azure Active Directory
description: Přečtěte si, jak nakonfigurovat a povolit uživatelům přihlášení k Azure Active Directory pomocí své e-mailové adresy jako alternativní ID přihlášení (Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 05/22/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: scottsta
ms.openlocfilehash: ed317039e683ef36054d5ace612e09ca75dfa11e
ms.sourcegitcommit: 0b80a5802343ea769a91f91a8cdbdf1b67a932d3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/25/2020
ms.locfileid: "83837385"
---
# <a name="sign-in-to-azure-using-email-as-an-alternate-login-id-preview"></a>Přihlášení k Azure pomocí e-mailu jako alternativního přihlašovacího ID (Preview)

Mnoho organizací chce umožnit uživatelům přihlašovat se ke službě Azure pomocí stejných přihlašovacích údajů, jako je místní prostředí adresáře. U tohoto přístupu, označovaného jako hybridní ověřování, si uživatelé musí pamatovat jenom jednu sadu přihlašovacích údajů.

Některé organizace se nepřesunuly do hybridního ověřování z následujících důvodů:

* Ve výchozím nastavení je hlavní název uživatele Azure Active Directory (Azure AD) pro místní adresář nastavený na stejné hlavní název uživatele (UPN).
* Změna hlavního názvu uživatele (UPN) Azure AD vytvoří neshodu mezi Prem a prostředími Azure, která by mohla způsobit problémy s určitými aplikacemi a službami.
* Organizace nechce, aby se k Azure přihlásili pomocí místního hlavního názvu uživatele (UPN), a to v důsledku obchodních důvodů a dodržování předpisů.

Abyste mohli přejít na hybridní ověřování, můžete teď nakonfigurovat Azure AD tak, aby se uživatelé mohli přihlásit k Azure pomocí e-mailu v ověřené doméně jako alternativní přihlašovací ID. Pokud se například *Contoso* přesměruje na *Fabrikam*, místo toho, abyste se mohli dál přihlašovat pomocí staršího `balas@contoso.com` hlavního názvu uživatele (UPN), se teď dá použít alternativní přihlašovací ID. Pro přístup k aplikaci nebo službám se uživatelé přihlásí k Azure pomocí přiřazeného e-mailu, například `balas@fabrikam.com` .

|     |
| --- |
| Přihlášení k Azure AD s e-mailem jako alternativním přihlašovacím ID je funkce Public Preview služby Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.|
|     |

## <a name="overview-of-azure-ad-sign-in-approaches"></a>Přehled přístupů ke službě Azure AD pro přihlášení

Hlavní názvy uživatelů (UPN) jsou jedinečné identifikátory uživatelského účtu v místním adresáři i ve službě Azure AD. Každý uživatelský účet v adresáři je reprezentován hlavním názvem uživatele (UPN), například `balas@contoso.com` . Ve výchozím nastavení platí, že když synchronizujete prostředí místního Active Directory Domain Services (služba AD DS) se službou Azure AD, hlavní název uživatele (UPN) služby Azure AD se nastaví tak, aby odpovídal místnímu hlavnímu názvu uživatele (UPN).

V mnoha organizacích je dobré nastavit místní hlavní název uživatele (UPN) a hlavní název uživatele služby Azure AD tak, aby odpovídaly. Když se uživatelé přihlásí k aplikacím a službám Azure, použijí si hlavní název uživatele (UPN) Azure AD. Některé organizace ale nemůžou k přihlašování používat odpovídajícího UPN, protože firemní zásady nebo problémy s uživatelským prostředím.

Organizace, které nemůžou používat odpovídající hlavní názvy uživatelů (UPN) ve službě Azure AD, mají několik možností:

* Jedním z možností je nastavit hlavní název uživatele (UPN) Azure AD na jiný způsob, který je založený na obchodních potřebách, například `balas@fabrikam.com` .
    * Ne všechny aplikace a služby jsou ale kompatibilní s použitím jiné hodnoty pro místní hlavní název uživatele (UPN) a hlavní název uživatele (UPN) služby Azure AD.
* Lepším řešením je zajistit, aby se služba Azure AD a místní názvy UPN nastavily na stejnou hodnotu, a nakonfigurovat Azure AD tak, aby se uživatelé mohli přihlásit k Azure pomocí e-mailu jako alternativní přihlašovací ID.

Pomocí e-mailu jako alternativního přihlašovacího ID se uživatelé můžou pořád přihlašovat do Azure zadáním svého hlavního názvu uživatele (UPN), ale můžou se také přihlásit pomocí e-mailu. V případě podpory definujte v místním adresáři v atributu *proxyAddresses* uživatele e-mailovou adresu. Tento atribut *ProxyAddress* podporuje jednu nebo více e-mailových adres.

## <a name="synchronize-sign-in-email-addresses-to-azure-ad"></a>Synchronizace e-mailových přihlašovacích adres do Azure AD

Tradiční ověřování Active Directory Domain Services (služba AD DS) nebo Active Directory Federation Services (AD FS) (AD FS) se provádí přímo v síti a je zpracováváno vaší služba AD DS infrastrukturou. Pomocí hybridního ověřování se uživatelé můžou místo toho přihlašovat přímo do Azure AD.

Pro podporu tohoto přístupu k hybridnímu ověřování synchronizujete své místní služba AD DS prostředí s Azure AD pomocí [Azure AD Connect][azure-ad-connect] a nakonfigurujte ho tak, aby používal synchronizaci hodnot hash hesel (kosmetice) nebo předávací ověřování (PTA).

V obou možnostech konfigurace odešle uživatel své uživatelské jméno a heslo do služby Azure AD, která ověří přihlašovací údaje a vydá lístek. Když se uživatelé přihlásí ke službě Azure AD, nepotřebují, aby vaše organizace mohla hostovat a spravovat infrastrukturu AD FS.

![Diagram hybridní identity Azure AD pomocí synchronizace hodnot hash hesel](media/howto-authentication-use-email-signin/hybrid-password-hash-sync.png)

![Diagram hybridní identity Azure AD pomocí předávacího ověřování](media/howto-authentication-use-email-signin/hybrid-pass-through-authentication.png)

Jeden z atributů uživatele, který je automaticky synchronizován pomocí Azure AD Connect je *proxyAddresses*. Pokud má uživatel e-mailovou adresu definovanou v prostředí Prem služba AD DS jako součást atributu *proxyAddresses* , automaticky se synchronizuje se službou Azure AD. Tato e-mailová adresa se pak dá použít přímo v procesu přihlášení k Azure AD jako alternativní přihlašovací ID.

> [!IMPORTANT]
> Do služby Azure AD se synchronizují jenom e-maily v ověřených doménách pro tenanta. Každý tenant služby Azure AD má jednu nebo více ověřených domén, pro které máte prověřené vlastnictví, a je jednoznačně svázán se svým klientem.
>
> Další informace najdete v tématu [Přidání a ověření vlastního názvu domény ve službě Azure AD][verify-domain].

Další informace najdete v tématu [Volba správné metody ověřování pro řešení hybridní identity Azure AD][hybrid-auth-methods].

## <a name="enable-user-sign-in-with-an-email-address"></a>Povolit přihlášení uživatele pomocí e-mailové adresy

Jakmile se uživatelé s použitým atributem *proxyAddresses* synchronizují do služby Azure AD pomocí Azure AD Connect, je nutné povolit funkci pro uživatele, aby se k vašemu tenantovi přihlásili pomocí e-mailu jako alternativní přihlašovací ID. Tato funkce oznamuje přihlašovacím serverům Azure AD, aby nekontrolovaly jenom přihlašovací jméno proti hodnotám hlavního názvu uživatele (UPN), ale taky proti *proxyAddresses* hodnotám e-mailové adresy.

Během období Preview můžete v současné době povolit jenom přihlášení pomocí e-mailu jako alternativní funkci přihlašovacího ID pomocí PowerShellu. K provedení následujících kroků potřebujete oprávnění *správce tenanta* :

1. Otevřete relaci PowerShellu jako správce a pak nainstalujte modul *AzureADPreview* pomocí rutiny [install-Module][Install-Module] :

    ```powershell
    Install-Module AzureADPreview
    ```

    Pokud se zobrazí výzva, vyberte **Y** pro instalaci nástroje NuGet nebo pro instalaci z nedůvěryhodného úložiště.

1. Přihlaste se k tenantovi Azure AD jako *správce tenanta* pomocí rutiny [Connect-AzureAD][Connect-AzureAD] :

    ```powershell
    Connect-AzureAD
    ```

    Příkaz vrátí informace o vašem účtu, prostředí a ID tenanta.

1. Pomocí rutiny [Get-AzureADPolicy][Get-AzureADPolicy] ověřte, jestli zásady *HomeRealmDiscoveryPolicy* už ve vašem tenantovi existují:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

1. Pokud není aktuálně nakonfigurována žádná zásada, příkaz nevrátí žádnou hodnotu. Pokud se vrátí zásada, přeskočte tento krok a přejděte k dalšímu kroku, abyste aktualizovali existující zásady.

    Pokud chcete do tenanta přidat zásady *HomeRealmDiscoveryPolicy* , použijte rutinu [New-AzureADPolicy][New-AzureADPolicy] a nastavte atribut *AlternateIdLogin* na *Enabled: true* , jak je znázorněno v následujícím příkladu:

    ```powershell
    New-AzureADPolicy -Definition @('{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Po úspěšném vytvoření zásady vrátí příkaz ID zásady, jak je znázorněno v následujícím příkladu výstupu:

    ```powershell
    Id                                   DisplayName                 Type                     IsOrganizationDefault
    --                                   -----------                 ----                     ---------------------
    5de3afbe-4b7a-4b33-86b0-7bbe308db7f7 BasicAutoAccelerationPolicy HomeRealmDiscoveryPolicy True
    ```

1. Pokud už je nakonfigurovaná zásada, ověřte, jestli *AlternateIdLogin*   je povolený atribut AlternateIdLogin, jak je znázorněné v následujícím příkladu výstupu zásady:

    ```powershell
    Id : 5de3afbe-4b7a-4b33-86b0-7bbe308db7f7
    OdataType :
    AlternativeIdentifier :
    Definition : {{"HomeRealmDiscoveryPolicy" :{"AlternateIdLogin":{"Enabled": true}}}}
    DisplayName : BasicAutoAccelerationPolicy
    IsOrganizationDefault : True
    KeyCredentials : {}
    Type : HomeRealmDiscoveryPolicy
    ```

    Pokud zásada existuje, ale atribut *AlternateIdLogin* , který není přítomný nebo povolený, nebo pokud u zásady, kterou chcete zachovat, existují jiné atributy, aktualizujte stávající zásady pomocí rutiny [set-AzureADPolicy][Set-AzureADPolicy] .

    > [!IMPORTANT]
    > Když zásadu aktualizujete, ujistěte se, že jste zahrnuli stará nastavení a nový atribut *AlternateIdLogin* .

    Následující příklad přidá atribut *AlternateIdLogin* a zachová atribut *AllowCloudPasswordValidation* , který již mohl být nastaven:

    ```powershell
    Set-AzureADPolicy -id b581c39c-8fe3-4bb5-b53d-ea3de05abb4b `
        -Definition @('{"HomeRealmDiscoveryPolicy" :{"AllowCloudPasswordValidation":true,"AlternateIdLogin":{"Enabled": true}}}') `
        -DisplayName "BasicAutoAccelerationPolicy" `
        -IsOrganizationDefault $true `
        -Type "HomeRealmDiscoveryPolicy"
    ```

    Potvrďte, že aktualizovaná zásada zobrazuje vaše změny a že je teď povolený atribut *AlternateIdLogin* :

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="test-user-sign-in-with-email"></a>Testování přihlášení uživatele pomocí e-mailu

Pokud chcete otestovat, jestli se uživatelé můžou přihlašovat pomocí e-mailu, přejděte na [https://myprofile.microsoft.com][my-profile] adresu a přihlaste se pomocí uživatelského účtu na základě jejich e-mailové adresy, jako `balas@fabrikam.com` je například hlavní název uživatele (UPN) `balas@contoso.com` . Prostředí pro přihlašování by mělo vypadat stejně jako u přihlašovací události založené na UPN.

## <a name="troubleshoot"></a>Řešení potíží

Pokud uživatelé mají problémy s přihlašovacími událostmi pomocí své e-mailové adresy, přečtěte si následující postup řešení potíží:

1. Ujistěte se, že uživatelský účet má svou e-mailovou adresu nastavenou pro atribut *proxyAddresses* v prostředí Prem služba AD DS.
1. Ověřte, že je konfigurace Azure AD Connect nakonfigurovaná, a úspěšně synchronizuje uživatelské účty z prostředí on-Prem služba AD DS do Azure AD.
1. Potvrďte, že zásada Azure AD *HomeRealmDiscoveryPolicy* má atribut *AlternateIdLogin* nastavený na *Enabled: true*:

    ```powershell
    Get-AzureADPolicy | where-object {$_.Type -eq "HomeRealmDiscoveryPolicy"} | fl *
    ```

## <a name="next-steps"></a>Další kroky

Další informace o hybridní identitě, jako je Aplikace Azure AD proxy nebo Azure AD Domain Services, najdete v tématu [Azure AD Hybrid identity pro přístup a správu úloh Prem][hybrid-overview].

Další informace o hybridních operacích identity najdete v tématu Jak funguje synchronizace [hodnot hash hesel][phs-overview] nebo [předávacího ověřování][pta-overview] .

<!-- INTERNAL LINKS -->
[verify-domain]: ../fundamentals/add-custom-domain.md
[hybrid-auth-methods]: ../hybrid/choose-ad-authn.md
[azure-ad-connect]: ../hybrid/whatis-azure-ad-connect.md
[hybrid-overview]: ../hybrid/cloud-governed-management-for-on-premises.md
[phs-overview]: ../hybrid/how-to-connect-password-hash-synchronization.md
[pta-overview]: ../hybrid/how-to-connect-pta-how-it-works.md

<!-- EXTERNAL LINKS -->
[Install-Module]: /powershell/module/powershellget/install-module
[Connect-AzureAD]: /powershell/module/azuread/connect-azuread
[Get-AzureADPolicy]: /powershell/module/azuread/get-azureadpolicy
[New-AzureADPolicy]: /powershell/module/azuread/new-azureadpolicy
[Set-AzureADPolicy]: /powershell/module/azuread/set-azureadpolicy
[my-profile]: https://myprofile.microsoft.com
