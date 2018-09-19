---
title: Začínáme s ověřováním na základě certifikátů Azure Active Directory
description: Další informace o konfiguraci ověřování na základě certifikátu ve vašem prostředí
services: active-directory
ms.service: active-directory
ms.component: authentication
ms.topic: article
ms.date: 01/15/2018
ms.author: joflore
author: MicrosoftGuyJFlo
manager: mtillman
ms.reviewer: annaba
ms.openlocfilehash: 2f6b9ee7a23341f27272a4614bed6be5ae422ce0
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/19/2018
ms.locfileid: "46297263"
---
# <a name="get-started-with-certificate-based-authentication-in-azure-active-directory"></a>Začínáme s ověřováním na základě certifikátů v Azure Active Directory

Ověřování pomocí certifikátů umožňuje ověřit pomocí služby Azure Active Directory pomocí klientského certifikátu na zařízení s Windows, Android nebo iOS při připojování svůj účet Exchange online a:

- Mobilní aplikace Microsoftu, jako je například Microsoft Outlook a Microsoft Word
- Klienti Exchange ActiveSync (EAS)

Tuto funkci konfiguruje eliminuje nutnost zadat kombinace uživatelského jména a hesla do určité e-mailu a aplikace Microsoft Office na svém mobilním zařízení.

V tomto tématu:

- Nabízí postup pro konfiguraci a využít ověřování pomocí certifikátů pro uživatele tenantů v plánech Office 365 Enterprise, obchodní, vzdělávání a státní správu USA. Tato funkce je dostupná ve verzi preview v plánech Office 365 China, obrany státní správy USA a US Government federální.
- Předpokládá, že už máte [infrastruktury veřejných klíčů (PKI)](https://go.microsoft.com/fwlink/?linkid=841737) a [služby AD FS](../hybrid/how-to-connect-fed-whatis.md) nakonfigurované.

## <a name="requirements"></a>Požadavky

Konfigurace ověřování pomocí certifikátů, musí platit následující příkazy:

- Ověřování pomocí certifikátu (certifikátů) je podporována pouze pro federativní prostředí pro aplikace prohlížeče nebo nativní klienty používající moderní ověřování (ADAL). Jedinou výjimkou je Exchange Active Sync (EAS) pro Exchange Online (EXO), který lze použít u federovaných a spravovaných účtů.
- Kořenová certifikační autorita a všechny zprostředkující certifikační autority musí být nakonfigurovány v Azure Active Directory.
- Každou certifikační autority musí mít seznam odvolaných certifikátů (CRL), který může být odkazováno prostřednictvím internetového adresy URL.
- Musí mít alespoň jeden certifikační autorita nakonfigurovaná ve službě Azure Active Directory. Související postup v najdete [konfigurace certifikační autority](#step-2-configure-the-certificate-authorities) oddílu.
- U klientů Exchange ActiveSync klientský certifikát musí mít směrovatelné e-mailovou adresu uživatele v systému Exchange online v hlavní název nebo název RFC822 hodnota pole alternativní název subjektu. Azure Active Directory mapuje RFC822 hodnotu atributu adresa proxy serveru v adresáři.
- Klientské zařízení musí mít přístup k alespoň jeden certifikační autority, která vydává certifikáty klienta.
- Klientský certifikát pro ověřování klientů musí být vydán pro vašeho klienta.

## <a name="step-1-select-your-device-platform"></a>Krok 1: Vyberte platformu zařízení

Jako první krok pro platformu zařízení, na kterých vám záleží je potřeba zkontrolovat následující:

- Podpora mobilních aplikací Office
- Požadavky na konkrétní implementaci

Související informace existuje pro zařízení s následujícími platformami:

- [Android](active-directory-certificate-based-authentication-android.md)
- [iOS](active-directory-certificate-based-authentication-ios.md)

## <a name="step-2-configure-the-certificate-authorities"></a>Krok 2: Konfigurace certifikační autority

Konfigurace certifikačních autorit ve službě Azure Active Directory, pro každou certifikační autority, odešlete následující:

* Veřejnou část certifikátu v *.cer* formátu
* Internetové adresy URL ve kterém se nacházejí seznamy odvolaných certifikátů (CRL)

Schéma pro certifikační autority by měl vypadat takto:

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
2. Nainstalovat verzi modulu Azure AD [2.0.0.33](https://www.powershellgallery.com/packages/AzureAD/2.0.0.33) nebo vyšší.

        Install-Module -Name AzureAD –RequiredVersion 2.0.0.33

Jako první krok konfigurace budete muset navázat spojení se váš tenant. Co nejdříve po připojení k vašemu tenantovi existuje, můžete zkontrolovat, přidat, odstranit a upravit důvěryhodných certifikačních autorit, které jsou definovány ve vašem adresáři.

### <a name="connect"></a>Připojení

Pokud chcete navázat spojení se váš tenant, použijte [Connect-AzureAD](/powershell/module/azuread/connect-azuread?view=azureadps-2.0) rutiny:

    Connect-AzureAD

### <a name="retrieve"></a>Načíst

Pokud chcete načíst důvěryhodných certifikačních autorit, které jsou definovány ve vašem adresáři, použijte [Get-AzureADTrustedCertificateAuthority](/powershell/module/azuread/get-azureadtrustedcertificateauthority?view=azureadps-2.0) rutiny.

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

K odvolání klientského certifikátu, Azure Active Directory načte seznam odvolaných certifikátů (CRL) z adresy URL odeslán jako součást certifikátu autority informace a zapíše jej do mezipaměti. Poslední publikování časové razítko (**datum účinnosti** vlastnost) v seznamu CRL, které se používá k zajištění seznam CRL je stále platný. Seznam odvolaných certifikátů se pravidelně odkazuje odvolat přístup k certifikáty, které jsou součástí seznamu.

Pokud více rychlé odvolání je potřeba (například, pokud uživatel ztratí zařízení), lze zrušit autorizační token uživatele. Platnost autorizačního tokenu, nastavte **StsRefreshTokenValidFrom** pole pro tento konkrétní uživatel pomocí Windows Powershellu. Je nutné aktualizovat **StsRefreshTokenValidFrom** pole pro každého uživatele, které chcete odvolat přístup.

Chcete-li zajistit, aby odvolání nevyřeší, je nutné nastavit **datum účinnosti** seznamu CRL na datum po hodnoty nastavené v **StsRefreshTokenValidFrom** a seznam CRL, musí daný certifikát.

Následující kroky popisují proces pro aktualizaci nebo zrušení platnosti autorizačního tokenu tím, že nastavíte **StsRefreshTokenValidFrom** pole.

**Postup konfigurace odvolání:**

1. Připojte se pomocí přihlašovacích údajů správce ke službě MSOL:

        $msolcred = get-credential
        connect-msolservice -credential $msolcred

2. Načte aktuální hodnotu StsRefreshTokensValidFrom pro uživatele:

        $user = Get-MsolUser -UserPrincipalName test@yourdomain.com`
        $user.StsRefreshTokensValidFrom

3. Nakonfigurujte novou hodnotu StsRefreshTokensValidFrom pro uživatele, který se rovná aktuální časové razítko:

        Set-MsolUser -UserPrincipalName test@yourdomain.com -StsRefreshTokensValidFrom ("03/05/2016")

Datum, které nastavíte, musí být v budoucnu. Pokud není datum v budoucnosti, **StsRefreshTokensValidFrom** není nastavena vlastnost. Pokud je datum v budoucnosti, **StsRefreshTokensValidFrom** je nastavena na aktuální čas (ne datum uvedené pomocí příkazu Set-MsolUser).

## <a name="step-4-test-your-configuration"></a>Krok 4: Testování konfigurace

### <a name="testing-your-certificate"></a>Testování certifikátu

Jako první Konfigurace testu, snažte se přihlaste se k [Outlook Web Access](https://outlook.office365.com) nebo [Sharepointu Online](https://microsoft.sharepoint.com) pomocí vaší **prohlížeč na zařízení**.

Pokud vaše přihlášení úspěšné, pak víte, že:

- Uživatelský certifikát zřízený testovací zařízení
- Služba AD FS je správně nakonfigurovaná.

### <a name="testing-office-mobile-applications"></a>Testování mobilních aplikací Office

**K otestování ověřování prostřednictvím certifikátu na vaše mobilní aplikace Office:**

1. Na testovacím zařízení instalace mobilní aplikace Office (třeba Onedrivu).
3. Spusťte aplikaci.
4. Zadejte své uživatelské jméno a pak vyberte certifikát uživatele, který chcete použít.

By měl být úspěšném přihlášení.

### <a name="testing-exchange-activesync-client-applications"></a>Testování aplikací klienta Exchange ActiveSync

Pro přístup k protokolu Exchange ActiveSync (EAS) prostřednictvím ověřování pomocí certifikátů, musí být dostupné pro aplikace EAS profil, který obsahuje certifikát klienta.

Profilu EAS musí obsahovat následující informace:

- Uživatelský certifikát má být použit pro ověřování

- Koncový bod EAS (třeba outlook.office365.com)

Profilu EAS můžete nakonfigurovat a umístí na zařízení prostřednictvím využívání správy mobilních zařízení (MDM) jako je například Intune nebo ručně umístěním certifikátu v profilu EAS na zařízení.

### <a name="testing-eas-client-applications-on-android"></a>Testování aplikací klienta EAS v Androidu

**Otestovat ověření certifikátu:**

1. Konfigurace profilu EAS v aplikaci, která splňuje požadavky uvedené v předchozí části.
2. Otevřete aplikaci a ověřte, zda je synchronizace e-mailu.

## <a name="next-steps"></a>Další postup

[Další informace o ověřování na základě certifikátů na zařízeních s Androidem.](active-directory-certificate-based-authentication-android.md)

[Další informace o ověřování na základě certifikátů na zařízeních s Iosem.](active-directory-certificate-based-authentication-ios.md)