---
title: Ověřování založené na certifikátech – služba Azure Active Directory
description: Přečtěte si, jak ve vašem prostředí konfigurovat ověřování založené na certifikátech.
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: annaba
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4b57c4f474b0b9def08005f32f48225d36ea8cf1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74848829"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Začínáme s ověřováním pomocí certifikátů v Azure Active Directory

Ověřování na základě certifikátu umožňuje ověření službou Azure Active Directory pomocí klientského certifikátu na zařízení s Windows, Androidem nebo iOS při připojování online účtu Exchange k:

- Mobilní aplikace Microsoft, jako je Microsoft Outlook a Microsoft Word
- Klienti Exchange ActiveSync (EAS)

Konfigurace této funkce eliminuje potřebu zadávat kombinaci uživatelského jména a hesla do určitých aplikací pošty a sady Microsoft Office v mobilním zařízení.

Toto téma:

- Poskytuje postup konfigurace a využití ověřování na základě certifikátů pro uživatele klientů v plánech Office 365 Enterprise, Business, Education a US Government. Tato funkce je k dispozici ve verzi Preview v Office 365 China, US Government Defense a US Government Federal plans.
- Předpokládá, že již máte nakonfigurovanou [infrastrukturu veřejných klíčů (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) a [ad fs.](../hybrid/how-to-connect-fed-whatis.md)

## <a name="requirements"></a>Požadavky

Chcete-li nakonfigurovat ověřování založené na certifikátech, musí být splněny následující příkazy:

- Ověřování založené na certifikátech (CBA) je podporováno pouze pro federovaná prostředí pro aplikace prohlížeče, nativní klienty používající moderní ověřování (ADAL) nebo knihovny MSAL. Jedinou výjimkou je Exchange Active Sync (EAS) pro Exchange Online (EXO), který lze použít pro federované a spravované účty.
- Kořenová certifikační autorita a všechny zprostředkující certifikační autority musí být nakonfigurované ve službě Azure Active Directory.
- Každá certifikační autorita musí mít seznam odvolaných certifikátů (CRL), na který lze odkazovat prostřednictvím adresy URL směřující k Internetu.
- Ve službě Azure Active Directory musíte mít nakonfigurovanou alespoň jednu certifikační autoritu. Související kroky najdete v části [Konfigurace certifikačních autorit.](#step-2-configure-the-certificate-authorities)
- U klientů Exchange ActiveSync musí mít klientský certifikát směrovatelnou e-mailovou adresu uživatele v exchange online v poli Hlavní název nebo Název RFC822 v poli Alternativní název subjektu. Služba Azure Active Directory mapuje hodnotu RFC822 na atribut Proxy Address v adresáři.
- Klientské zařízení musí mít přístup alespoň k jedné certifikační autoritě, která vydává klientské certifikáty.
- Klientský certifikát pro ověřování klienta musí být vydán klientovi.

>[!IMPORTANT]
>Maximální velikost seznamu odvolaných kódů pro službu Azure Active Directory pro úspěšné stažení a mezipaměť je 20 MB a doba potřebná ke stažení seznamu odvolaných hodnot nesmí přesáhnout 10 sekund.  Pokud služba Azure Active Directory nemůže stáhnout seznam CRL, ověřování na základě certifikátů pomocí certifikátů vydaných odpovídající certifikační autoritou se nezdaří. Doporučené postupy k zajištění crl soubory jsou v rámci omezení velikosti je zachovat životnost certifikátu v rozumných mezích a vyčistit certifikáty, jejichž platnost vypršela. 

## <a name="step-1-select-your-device-platform"></a>Krok 1: Vyberte platformu zařízení

Jako první krok pro platformu zařízení, která vás zajímá, je třeba zkontrolovat následující:

- Podpora mobilních aplikací Office
- Zvláštní prováděcí požadavky

Související informace existují pro následující platformy zařízení:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurace certifikačních úřadů

Chcete-li nakonfigurovat certifikační autority ve službě Azure Active Directory, nahrajte pro každou certifikační autoritu následující:

* Veřejná část certifikátu ve formátu *CER*
* Adresy URL adres pro připojení k internetu, na kterých jsou umístěny seznamy odvolaných certifikátů (CRL)

Schéma certifikační autority vypadá takto:

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

Pro konfiguraci můžete použít [prostředí Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Spusťte prostředí Windows PowerShell s oprávněními správce.
2. Nainstalujte modul Azure AD verze [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) nebo vyšší.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Jako první krok konfigurace je třeba navázat připojení s tenantem. Jakmile existuje připojení k tenantovi, můžete zkontrolovat, přidat, odstranit a upravit důvěryhodné certifikační autority, které jsou definovány ve vašem adresáři.

### <a name="connect"></a>Připojení

Chcete-li navázat spojení s tenantem, použijte rutinu [Connect-AzureAD:](/powershell/module/azuread/connect-azuread?view=azureadps-2.0)

    Connect-AzureAD

### <a name="retrieve"></a>Načíst

Chcete-li načíst důvěryhodné certifikační autority, které jsou definovány ve vašem adresáři, použijte rutinu [Get-AzureADTrustedCertificateAuthority.](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0)

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Přidat

Chcete-li vytvořit důvěryhodnou certifikační autoritu, použijte rutinu [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) a nastavte atribut **crlDistributionPoint** na správnou hodnotu:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint="<CRL Distribution URL>"
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Odebrat

Chcete-li odebrat důvěryhodnou certifikační autoritu, použijte rutinu [Odebrat AzureADTrustedCertificateAuthority:](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Modify

Chcete-li upravit důvěryhodnou certifikační autoritu, použijte rutinu [Set-AzureADTrustedCertificateAuthority:](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0)

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Krok 3: Konfigurace odvolání

Chcete-li odvolat klientský certifikát, služba Azure Active Directory načte seznam odvolaných certifikátů (CRL) z adres URL odeslaných jako součást informací certifikační autority a uloží jej do mezipaměti. Poslední časové razítko publikování (Vlastnost**Datum platnosti)** v seznamu odvolaných certifikátů se používá k zajištění toho, aby byl seznam ODVOLatel stále platný. Seznam odvolaných certifikátů je pravidelně odkazován na odvolání přístupu k certifikátům, které jsou součástí seznamu.

Pokud je vyžadováno okamžité odvolání (například pokud uživatel ztratí zařízení), může být token autorizace uživatele zrušen. Chcete-li zrušit platnost autorizačního tokenu, nastavte pole **StsRefreshTokenValidFrom** pro tohoto konkrétního uživatele pomocí prostředí Windows PowerShell. Je nutné aktualizovat pole **StsRefreshTokenValidFrom** pro každého uživatele, pro kterého chcete odvolat přístup.

Chcete-li zajistit, aby odvolání přetrvávalo, je nutné nastavit **datum účinnosti** seznamu odvolaných certifikátů na datum po hodnotě nastavené **službou StsRefreshTokenValidFrom** a zajistit, aby daný certifikát byl v seznamu odvolaných certifikátů.

Následující kroky popisují proces aktualizace a neplatnosti autorizačního tokenu nastavením pole **StsRefreshTokenValidFrom.**

**Postup konfigurace odvolání:**

1. Spojte se s přihlašovacími údaji správce ke službě MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Načíst aktuální hodnotu StsRefreshTokensValidFrom pro uživatele:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Nakonfigurujte novou hodnotu StsRefreshTokensValidFrom pro uživatele, která se rovná aktuálnímu časovému razítku:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Datum, které jste nastavili, musí být v budoucnosti. Pokud datum není v budoucnu, **StsRefreshTokensValidFrom** vlastnost není nastavena. Pokud je datum v budoucnosti, **StsRefreshTokensValidFrom** je nastavena na aktuální čas (není datum uvedené příkazem Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Krok 4: Testování konfigurace

### <a name="testing-your-certificate"></a>Testování certifikátu

Jako první konfigurační test byste se měli pokusit přihlásit k [Outlook Web Accessu](https://outlook.office365.com) nebo [SharePointu Online](https://microsoft.sharepoint.com) pomocí prohlížeče na **zařízení**.

Pokud je vaše přihlášení úspěšné, pak víte, že:

- Uživatelský certifikát byl zřízen do testovacího zařízení.
- AD FS je správně nakonfigurován

### <a name="testing-office-mobile-applications"></a>Testování mobilních aplikací Office

**Testování ověřování na základě certifikátu v mobilní aplikaci Office:**

1. Na testovací zařízení nainstalujte mobilní aplikaci Office (například OneDrive).
3. Spusťte aplikaci.
4. Zadejte své uživatelské jméno a vyberte uživatelský certifikát, který chcete použít.

Měli byste být úspěšně přihlášeni.

### <a name="testing-exchange-activesync-client-applications"></a>Testování klientských aplikací Exchange ActiveSync

Chcete-li získat přístup k aplikaci Exchange ActiveSync (EAS) prostřednictvím ověřování na základě certifikátu, musí být pro aplikaci k dispozici profil EAS obsahující klientský certifikát.

Profil EAS musí obsahovat tyto informace:

- Uživatelský certifikát, který má být použit pro ověřování

- Koncový bod EAS (například outlook.office365.com)

Profil EAS lze nakonfigurovat a umístit do zařízení pomocí správy mobilních zařízení (MDM), jako je Intune, nebo ručním umístěním certifikátu do profilu EAS v zařízení.

### <a name="testing-eas-client-applications-on-android"></a>Testování klientských aplikací EAS v systému Android

**Ověření certifikátu:**

1. Nakonfigurujte profil EAS v aplikaci, který splňuje požadavky v předchozí části.
2. Otevřete aplikaci a ověřte, zda je pošta synchronizována.

## <a name="next-steps"></a>Další kroky

[Další informace o ověřování na základě certifikátu na zařízeních se systémem Android.](active-directory-certificate-based-authentication-android.md)

[Další informace o ověřování na základě certifikátu na zařízeních se systémem iOS.](active-directory-certificate-based-authentication-ios.md)
