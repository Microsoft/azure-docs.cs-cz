---
title: Ověřování založené na certifikátech – Azure Active Directory
description: Naučte se konfigurovat ověřování na základě certifikátů ve vašem prostředí.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 11/21/2019
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 6db7037cbcad335db77784ecfa624f08e88b1e83
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744427"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Začínáme s ověřováním pomocí certifikátů v Azure Active Directory

Ověřování na základě certifikátu umožňuje ověření pomocí Azure Active Directory s klientským certifikátem na zařízení s Windows, Androidem nebo iOS při připojování účtu Exchange Online k:

- Mobilní aplikace Microsoftu, jako je Microsoft Outlook a Microsoft Word
- Klienti Exchange ActiveSync (EAS)

Konfigurace této funkce eliminuje nutnost zadat kombinaci uživatelského jména a hesla k určitým e-mailovým a systém Microsoft Office aplikacím na vašem mobilním zařízení.

Toto téma:

- Poskytuje postup pro konfiguraci a používání ověřování založeného na certifikátech pro uživatele klientů v plánech Office 365 Enterprise, Business, školství a USA. Tato funkce je dostupná ve verzi Preview v Office 365 Čína, obrany státní správy USA a federálních plánech pro státní správu USA.
- Předpokládá, že už máte [infrastrukturu veřejného klíče (PKI)](/previous-versions/windows/it-pro/windows-server-2012-R2-and-2012/hh831740(v=ws.11)) a [AD FS](../hybrid/how-to-connect-fed-whatis.md) nakonfigurovanou.

## <a name="requirements"></a>Požadavky

Chcete-li nakonfigurovat ověřování na základě certifikátu, musí být splněny následující příkazy:

- Ověřování založené na certifikátech (certifikátů) je podporované jenom pro federované prostředí pro aplikace v prohlížeči, nativní klienty používající moderní ověřování (ADAL) nebo knihovny MSAL. Jedinou výjimkou je Exchange Active Sync (EAS) pro Exchange Online (EXO), která se dá použít pro federované a spravované účty.
- Kořenová certifikační autorita a jakékoli zprostředkující certifikační autority musí být nakonfigurované v Azure Active Directory.
- Každá certifikační autorita musí mít seznam odvolaných certifikátů (CRL), na který se dá odkazovat přes internetovou adresu URL.
- V Azure Active Directory musíte mít nakonfigurovanou aspoň jednu certifikační autoritu. Související kroky najdete v části [Konfigurace certifikačních autorit](#step-2-configure-the-certificate-authorities) .
- U klientů Exchange ActiveSync musí mít klientský certifikát směrovatelné e-mailové adresy uživatele v Exchangi Online buď v hlavním názvu, nebo v hodnotě názvu RFC822 pole Alternativní název subjektu. Azure Active Directory mapuje hodnotu RFC822 na atribut adresy proxy v adresáři.
- Klientské zařízení musí mít přístup k alespoň jedné certifikační autoritě, která vydává klientské certifikáty.
- Klientský certifikát pro ověření klienta musí být vystavený klientovi.

>[!IMPORTANT]
>Maximální velikost seznamu CRL pro Azure Active Directory pro úspěšné stažení a ukládání do mezipaměti je 20MB a doba potřebná ke stažení seznamu odvolaných certifikátů nesmí přesáhnout 10 sekund.  Pokud Azure Active Directory nemůže stáhnout seznam CRL, ověřování na základě certifikátů pomocí certifikátů vydaných příslušnou certifikační autoritou se nezdaří. Osvědčené postupy pro zajištění, že se soubory CRL nacházejí v rámci omezení velikosti, je zachování životnosti certifikátů v rámci přiměřených limitů a vyčištění certifikátů s vypršenou platností.

## <a name="step-1-select-your-device-platform"></a>Krok 1: vyberte platformu zařízení

Jako první krok pro platformu zařízení, o které se zajímáte, je potřeba zkontrolovat následující:

- Podpora mobilních aplikací Office
- Specifické požadavky implementace

Související informace existují pro následující platformy zařízení:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurace certifikačních autorit

Ke konfiguraci certifikačních autorit v Azure Active Directory pro každou certifikační autoritu nahrajte následující:

* Veřejná část certifikátu, ve formátu *. cer*
* Internetové adresy URL, kde se nacházejí seznamy odvolaných certifikátů (CRL)

Schéma certifikační autority vypadá takto:

```csharp
    class TrustedCAsForPasswordlessAuth
    {
       CertificateAuthorityInformation[] certificateAuthorities;
    }

    class CertificateAuthorityInformation

    {
        CertAuthorityType authorityType;
        X509Certificate trustedCertificate;
        string crlDistributionPoint;
        string deltaCrlDistributionPoint;
        string trustedIssuer;
        string trustedIssuerSKI;
    }

    enum CertAuthorityType
    {
        RootAuthority = 0,
        IntermediateAuthority = 1
    }
```

Pro konfiguraci můžete použít [Azure Active Directory PowerShell verze 2](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0):

1. Spusťte prostředí Windows PowerShell s oprávněními správce.
2. Nainstalujte modul Azure AD verze [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) nebo novější.

```powershell
    Install-Module -Name AzureAD –RequiredVersion 2.0.0.33
```

Jako první krok konfigurace musíte navázat spojení s vaším klientem. Jakmile existuje připojení k vašemu tenantovi, můžete si prohlédnout, přidat, odstranit a upravit důvěryhodné certifikační autority, které jsou definovány ve vašem adresáři.

### <a name="connect"></a>Připojení

K navázání spojení s vaším klientem použijte rutinu [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) :

```azurepowershell
    Connect-AzureAD
```

### <a name="retrieve"></a>Stahovat

K načtení důvěryhodných certifikačních autorit, které jsou definovány ve vašem adresáři, použijte rutinu [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) .

```azurepowershell
    Get-AzureADTrustedCertificateAuthority
```

### <a name="add"></a>Přidání

Chcete-li vytvořit důvěryhodnou certifikační autoritu, použijte rutinu [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) a nastavte atribut **crlDistributionPoint** na správnou hodnotu:

```azurepowershell
    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca
```

### <a name="remove"></a>Odebrat

K odebrání důvěryhodné certifikační autority použijte rutinu [Remove-AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]
```

### <a name="modify"></a>Modify

Chcete-li upravit důvěryhodnou certifikační autoritu, použijte rutinu [set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) :

```azurepowershell
    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]
```

## <a name="step-3-configure-revocation"></a>Krok 3: konfigurace odvolání

Pro odvolání klientského certifikátu Azure Active Directory načte seznam odvolaných certifikátů (CRL) z adres URL odeslaných jako součást informací o certifikační autoritě a uloží ji do mezipaměti. Poslední časové razítko publikování (vlastnost **Datum účinnosti** ) v seznamu odvolaných certifikátů se používá k zajištění, že seznam odvolaných certifikátů je stále platný. Seznam odvolaných certifikátů je pravidelně odkazován k odvolání přístupu k certifikátům, které jsou součástí seznamu.

Je-li vyžadováno více okamžitých odvolání (například pokud uživatel ztratí zařízení), je možné zrušit platnost autorizačního tokenu uživatele. Pro zrušení platnosti autorizačního tokenu nastavte pole **StsRefreshTokenValidFrom** pro tohoto konkrétního uživatele pomocí Windows PowerShellu. Musíte aktualizovat pole **StsRefreshTokenValidFrom** pro každého uživatele, pro kterého chcete přístup odvolat.

Chcete-li zajistit, že odvolání bude trvat dál, je nutné nastavit **Datum začátku** seznamu CRL na datum po hodnotě nastavené hodnotou **StsRefreshTokenValidFrom** a ověřit, zda je příslušný certifikát v seznamu CRL.

Následující kroky popisují proces aktualizace a devalidace autorizačního tokenu nastavením pole **StsRefreshTokenValidFrom** .

**Konfigurace odvolání:**

1. Připojte se s přihlašovacími údaji správce ke službě MSOL:

```powershell
        $msolcred = get-credential
        connect-msolservice -credential $msolcred
```

2. Načíst aktuální hodnotu StsRefreshTokensValidFrom pro uživatele:

```powershell
        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom
```

3. Nakonfigurujte novou hodnotu StsRefreshTokensValidFrom pro uživatele, která se rovná aktuálnímu časovému razítku:

```powershell
        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")
```

Datum, které nastavíte, musí být v budoucnosti. Pokud datum není v budoucnu, vlastnost **StsRefreshTokensValidFrom** není nastavena. Pokud je datum v budoucnosti, **StsRefreshTokensValidFrom** je nastaveno na aktuální čas (nikoli datum uvedené Set-MsolUser příkazu).

## <a name="step-4-test-your-configuration"></a>Krok 4: otestování konfigurace

### <a name="testing-your-certificate"></a>Testování certifikátu

Jako první test konfigurace byste se měli pokusit přihlásit k [aplikaci Outlook Web Access](https://outlook.office365.com) nebo [SharePoint Online](https://microsoft.sharepoint.com) pomocí **prohlížeče v zařízení**.

Pokud je vaše přihlášení úspěšné, pak víte, že:

- Certifikát uživatele se zřídil pro vaše testovací zařízení.
- AD FS je správně nakonfigurovaný.

### <a name="testing-office-mobile-applications"></a>Testování mobilních aplikací Office

**Testování ověřování založeného na certifikátech v mobilní aplikaci Office:**

1. Na testovacím zařízení nainstalujte mobilní aplikaci Office (například OneDrive).
3. Spusťte aplikaci.
4. Zadejte své uživatelské jméno a pak vyberte certifikát uživatele, který chcete použít.

Měli byste být úspěšně přihlášení.

### <a name="testing-exchange-activesync-client-applications"></a>Testování klientských aplikací Exchange ActiveSync

Chcete-li získat přístup k protokolu Exchange ActiveSync (EAS) prostřednictvím ověřování založeného na certifikátech, musí být pro aplikaci k dispozici profil EAS obsahující certifikát klienta.

Profil EAS musí obsahovat následující informace:

- Uživatelský certifikát, který se má použít k ověřování

- Koncový bod EAS (například outlook.office365.com)

Profil EAS se dá nakonfigurovat a umístit do zařízení prostřednictvím využití správy mobilních zařízení (MDM), jako je Intune, nebo ručně umístit certifikát do profilu EAS na zařízení.

### <a name="testing-eas-client-applications-on-android"></a>Testování klientských aplikací EAS na Androidu

**Testování ověřování certifikátů:**

1. Nakonfigurujte v aplikaci profil EAS, který splňuje požadavky uvedené v předchozí části.
2. Otevřete aplikaci a ověřte, že je pošta synchronizovaná.

## <a name="next-steps"></a>Další kroky

[Další informace o ověřování pomocí certifikátů na zařízeních s Androidem](active-directory-certificate-based-authentication-android.md)

[Další informace o ověřování pomocí certifikátů na zařízeních s iOS](active-directory-certificate-based-authentication-ios.md)