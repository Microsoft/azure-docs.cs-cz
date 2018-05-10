---
title: Začínáme s ověřováním na základě certifikátu služby Azure Active Directory
description: Informace o konfiguraci ověřování pomocí certifikátů ve vašem prostředí
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: db2c19bdc303f6f7773772dd7873878ceb892cc3
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/08/2018
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Začínáme s ověřováním na základě certifikátů ve službě Azure Active Directory

Ověřování pomocí certifikátů umožňuje službou Azure Active Directory se ověřit klientský certifikát na zařízení s Windows, Android nebo iOS při připojování váš účet systému Exchange online:

- Mobilní aplikace společnosti Microsoft, jako je například Microsoft Outlook a Microsoft Word
- Klienti Exchange ActiveSync (EAS)

Konfigurace tato funkce eliminuje potřebu zadejte kombinace uživatelského jména a hesla do určité e-mailu a aplikace Microsoft Office na vašem mobilním zařízení.

V tomto tématu:

- Poskytuje postup pro konfiguraci a využít ověřování pomocí certifikátů pro uživatele klientů v rámci plánů Office 365 Enterprise, Business, Education a US Government. Tato funkce je dostupná ve verzi preview v Číně Office 365, US Government obrany a US Government Federal plány.
- Předpokládá, že již máte [infrastruktury veřejných klíčů (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) a [služby AD FS](connect/active-directory-aadconnectfed-whatis.md) nakonfigurované.

## <a name="requirements"></a>Požadavky

Konfigurace ověřování pomocí certifikátů, musí platit následující příkazy:

- Ověřování pomocí certifikátů (CBA) je podporována pouze pro federovaném prostředí pro aplikace prohlížeče nebo nativní klienty, kteří používají moderní ověřování (ADAL). Jedinou výjimkou je Exchange Active Sync (EAS) pro Exchange Online (EXO), který můžete použít pro federované a spravované účty.
- Kořenové certifikační autority a jakékoliv zprostředkující certifikační autority musí být nakonfigurované v Azure Active Directory.
- Seznam odvolaných certifikátů (CRL), může být odkazováno prostřednictvím internetové adresy URL musí mít každý certifikační autority.
- Musí mít alespoň jednu certifikační autoritu nakonfigurován v Azure Active Directory. Související kroky v najdete [konfigurace certifikačních autorit](#step-2-configure-the-certificate-authorities) části.
- U klientů Exchange ActiveSync klienta musí mít certifikát směrovatelné e-mailovou adresu uživatele v systému Exchange online v hlavní název nebo název RFC822 hodnota pole alternativní název subjektu. Azure Active Directory mapuje RFC822 hodnota atributu adresu proxy serveru v adresáři.
- Klientské zařízení musí mít přístup k alespoň jednu certifikační autority, která vydává certifikáty klienta.
- Klientský certifikát pro ověřování klientů musí být vydán pro vašeho klienta.

## <a name="step-1-select-your-device-platform"></a>Krok 1: Vyberte platformu zařízení

Jako první krok pro platformu zařízení, která vás budete muset zkontrolujte následující položky:

- Podpora mobilních aplikacích Office
- Požadavky na konkrétní implementace

Pro tyto platformy zařízení existuje související informace:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurace certifikačních autorit

Konfigurace certifikačních autorit ve službě Azure Active Directory, pro každý certifikační autority, odešlete následující soubory:

* Na server veřejnou část certifikátu v *.cer* formátu
* Internetový bod adresy URL, kde se nacházejí seznamy odvolaných certifikátů (CRL)

Schéma pro certifikační autority vypadá takto:

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

Pro konfiguraci, můžete použít [Azure Active Directory PowerShell verze 2](/powershell/azure/install-adv2?view=azureadps-2.0):

1. Spusťte prostředí Windows PowerShell s oprávněními správce.
2. Nainstalujte verzi modulu Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) nebo vyšší.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Jako první krok konfigurace budete muset navázat spojení se váš klient. Při připojení ke klientovi existuje, můžete zkontrolovat, přidat, odstranit a upravit důvěryhodných certifikačních autorit, které jsou definované ve vašem adresáři.

### <a name="connect"></a>Připojení

Chcete-li navázat spojení se váš klient, použijte [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) rutiny:

    Connect-AzureAD

### <a name="retrieve"></a>Načtení

Chcete-li načíst důvěryhodných certifikačních autorit, které jsou definovány v adresáři, použijte [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) rutiny.

    Get-AzureADTrustedCertificateAuthority

### <a name="add"></a>Přidat

Chcete-li vytvořit důvěryhodné certifikační autority, použijte [New-AzureADTrustedCertificateAuthority](/powershell/module/azuread/new-azureadtrustedcertificateauthority?view=azureadps-2.0) rutiny a nastavte **crlDistributionPoint** správnou hodnotu atributu:

    $cert=Get-Content -Encoding byte "[LOCATION OF THE CER FILE]"
    $new_ca=New-Object -TypeName Microsoft.Open.AzureAD.Model.CertificateAuthorityInformation
    $new_ca.AuthorityType=0
    $new_ca.TrustedCertificate=$cert
    $new_ca.crlDistributionPoint=”<CRL Distribution URL>”
    New-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $new_ca

### <a name="remove"></a>Odebrat

Chcete-li odebrat důvěryhodné certifikační autority, použijte [odebrat AzureADTrustedCertificateAuthority](/powershell/module/azuread/remove-azureadtrustedcertificateauthority?view=azureadps-2.0) rutiny:

    $c=Get-AzureADTrustedCertificateAuthority
    Remove-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[2]

### <a name="modify"></a>Úpravy

Chcete-li upravit důvěryhodné certifikační autority, použijte [Set-AzureADTrustedCertificateAuthority](/powershell/module/azuread/set-azureadtrustedcertificateauthority?view=azureadps-2.0) rutiny:

    $c=Get-AzureADTrustedCertificateAuthority
    $c[0].AuthorityType=1
    Set-AzureADTrustedCertificateAuthority -CertificateAuthorityInformation $c[0]

## <a name="step-3-configure-revocation"></a>Krok 3: Konfigurace odvolání

Azure Active Directory k odvolání certifikátu klienta, načte seznam odvolaných certifikátů (CRL) z adresy URL odesílané jako součást informace o certifikační autoritě a zapíše do mezipaměti. Poslední publikovat časové razítko (**datum účinnosti** vlastnost) v seznamu CRL, které se používá k zajištění seznam CRL je stále platný. Seznam CRL odkazuje pravidelně odvolat přístup k certifikáty, které jsou součástí seznamu.

Pokud více rychlých odvolání je potřeba (například pokud uživatel ztratí zařízení), můžete zrušena autorizační token uživatele. Zneplatní autorizačním tokenem, nastavte **StsRefreshTokenValidFrom** pole pro tento konkrétní uživatel pomocí prostředí Windows PowerShell. Je nutné aktualizovat **StsRefreshTokenValidFrom** pole pro každého uživatele, který chcete odvolat přístup.

Chcete, aby odvolání potrvají, musíte nastavit **datum účinnosti** seznamu CRL na datum po hodnotu, která nastavuje **StsRefreshTokenValidFrom** a ujistěte se, daný certifikát je v seznamu CRL.

Následující kroky popisují proces pro aktualizaci nebo zneplatnění autorizační token nastavením **StsRefreshTokenValidFrom** pole.

**Postup konfigurace odvolání:**

1. Připojte se pomocí přihlašovacích údajů správce ke službě MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Načtěte aktuální hodnotu StsRefreshTokensValidFrom pro uživatele:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Nakonfigurujte novou hodnotu StsRefreshTokensValidFrom pro uživatele, který se rovná aktuální časové razítko:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Datum, které nastavíte, musí být v budoucnu. Pokud není datum v budoucnosti, **StsRefreshTokensValidFrom** není nastavena vlastnost. Pokud je datum v budoucnosti **StsRefreshTokensValidFrom** je nastaven na aktuální čas (není datum uvedené pomocí příkazu Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Krok 4: Testování konfigurace

### <a name="testing-your-certificate"></a>Testování vašeho certifikátu

Jako první test konfigurace, pokuste se přihlásit k [Outlook Web Access](https://outlook.office365.com) nebo [SharePoint Online](https://microsoft.sharepoint.com) pomocí vaší **prohlížeč na zařízení**.

Pokud vaše přihlášení úspěšné, pak víte, že:

- Uživatelský certifikát zřízená testovací zařízení
- Služba AD FS je správně nakonfigurovaná.

### <a name="testing-office-mobile-applications"></a>Testování mobilních aplikacích Office

**K testování ověřování pomocí certifikátů na vaše mobilní aplikace Office:**

1. Na testovací zařízení nainstalujte mobilní aplikace Office (třeba OneDrive).
3. Spuštění aplikace.
4. Zadejte uživatelské jméno a potom vyberte certifikát uživatele, který chcete použít.

Můžete by měla být úspěšně přihlášeni.

### <a name="testing-exchange-activesync-client-applications"></a>Testování aplikací klienta Exchange ActiveSync

Pro přístup k Exchange ActiveSync (EAS) prostřednictvím ověřování pomocí certifikátů, musí být k dispozici pro aplikaci profilem EAS obsahující certifikát klienta.

Profilu EAS, musí obsahovat tyto informace:

- Uživatelský certifikát má být použit pro ověřování

- Koncový bod EAS (například outlook.office365.com)

Profilu EAS můžete nakonfigurovat a umístit na zařízení prostřednictvím využití správy mobilních zařízení (MDM) jako je například Intune nebo ručně umístění certifikátu v profilu EAS na zařízení.

### <a name="testing-eas-client-applications-on-android"></a>Testování EAS klientské aplikace v systému Android

**K testování ověřování pomocí certifikátu:**

1. Konfigurace profilu EAS v aplikaci, která splňuje požadavky v předchozím oddílu.
2. Otevřete aplikaci a ověřte, zda je synchronizace e-mailu.

## <a name="next-steps"></a>Další postup

[Další informace o použít ověřování založené na certifikátech na zařízeních s Androidem.](active-directory-certificate-based-authentication-android.md)

[Další informace o použít ověřování založené na certifikátech na zařízeních s iOS.](active-directory-certificate-based-authentication-ios.md)