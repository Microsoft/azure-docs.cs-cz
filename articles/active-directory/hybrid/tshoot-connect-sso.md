---
title: 'Azure Active Directory Connect: Poradce při potížích s bezproblémovým jednotném přihlašování | Dokumenty společnosti Microsoft'
description: Toto téma popisuje, jak řešit potíže s bezproblémovým jednotném přihlašování služby Azure Active Directory.
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 759748124893a8f906a4bc336f835546202b0b62
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049495"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Poradce při potížích s bezproblémovým jednotném přihlašování služby Azure Active Directory

Tento článek vám pomůže najít informace o řešení běžných problémů týkajících se Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování).

## <a name="known-issues"></a>Známé problémy

- V několika případech povolení bezproblémové jednotné přihlašovací mokříž může trvat až 30 minut.
- Pokud zakážete a znovu povolíte bezproblémové jednotné přihlašování ve vašem tenantovi, uživatelé nezískají jednotné přihlašování, dokud jejich lístky Kerberos uložené v mezipaměti, obvykle platné po dobu 10 hodin, nevyprší.
- Pokud bezproblémové jednotné přihlašovat, uživatel nemá možnost vybrat **zachovat přihlášení**. Kvůli tomuto chování [nefungují scénáře mapování SharePointu a OneDrivu.](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec)
- Klienti Office 365 Win32 (Outlook, Word, Excel a další) s verzemi 16.0.8730.xxxx a vyššími jsou podporováni pomocí neinteraktivního toku. Jiné verze nejsou podporovány. v těchto verzích uživatelé zadají svá uživatelská jména, ale ne hesla, aby se přihlásili. Pro OneDrive budete muset aktivovat [funkci tiché konfigurace OneDrivu](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) pro tiché přihlašování.
- Bezproblémové jednotné přihlašování nefunguje v režimu anonymního prohlížení ve Firefoxu.
- Bezproblémové jednotné přihlašování nefunguje v aplikaci Internet Explorer, když je zapnutý rozšířený chráněný režim.
- Bezproblémové jednotné přihlašoviště nefunguje v mobilních prohlížečích v systémech iOS a Android.
- Pokud je uživatel součástí příliš mnoha skupin ve službě Active Directory, lístek protokolu Kerberos uživatele bude pravděpodobně příliš velký pro zpracování, což způsobí selhání bezproblémového jednotného přihlašování. Azure AD HTTPS požadavky mohou mít hlavičky s maximální velikostí 50 kB; Lístky protokolu Kerberos musí být menší než tento limit, aby bylo možné přizpůsobit další artefakty Azure AD (obvykle 2 – 5 kB), jako jsou například soubory cookie. Doporučujeme omezit členství uživatelů ve skupinách a zkusit to znovu.
- Pokud synchronizujete 30 nebo více doménových struktur služby Active Directory, nemůžete povolit bezproblémové jednotné přihlašování prostřednictvím služby Azure AD Connect. Jako řešení můžete [ručně povolit](#manual-reset-of-the-feature) funkci ve vašem tenantovi.
- Přidání adresy URL služby`https://autologon.microsoftazuread-sso.com`Azure AD ( ) do zóny Důvěryhodné servery namísto zóny Místní intranet *blokuje uživatelům přihlášení*.
- Bezproblémové jednotné přihlašování podporuje typy šifrování AES256_HMAC_SHA1, AES128_HMAC_SHA1 a RC4_HMAC_MD5 pro protokol Kerberos. Doporučuje se, aby typ šifrování pro účet AzureADSSOAcc$ je nastaven na AES256_HMAC_SHA1 nebo jeden z typů AES vs RC4 pro zvýšení zabezpečení. Typ šifrování je uložen v atributu msDS-SupportedEncryptionTypes účtu ve službě Active Directory.  Pokud je typ šifrování účtu AzureADSSOAcc$ nastaven na RC4_HMAC_MD5 a chcete jej změnit na jeden z typů šifrování AES, ujistěte se, že nejprve přejdete dešifrovací klíč Kerberos účtu AzureADSSOAcc$, jak je vysvětleno v [dokumentu FAQ](how-to-connect-sso-faq.md) pod příslušnou otázkou, jinak se bezproblémové jednotné přihlašování nestane.

## <a name="check-status-of-feature"></a>Zkontrolovat stav funkce

Ujistěte se, že bezešvé jednotné přihlašování funkce je stále **povolena** na vašem tenantovi. Stav můžete zkontrolovat tak, že přejdete do podokna **Azure AD Connect** v [Centru pro správu Služby Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Azure Active Directory: Podokno Azure AD Connect](./media/tshoot-connect-sso/sso10.png)

Kliknutím zobrazíte všechny doménové struktury služby AD, které byly povoleny pro bezproblémové jednotné přihlašování.

![Centrum pro správu Služby Azure Active Directory: Podokno bezproblémového jednotného přihlašování](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Důvody selhání přihlášení v Centru pro správu Služby Azure Active Directory (vyžaduje licenci Premium)

Pokud má váš tenant přidruženou licenci Azure AD Premium, můžete se taky podívat na [sestavu přihlašovacích aktivit](../reports-monitoring/concept-sign-ins.md) v [Centru pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Služby Azure Active Directory: Sestava přihlášení](./media/tshoot-connect-sso/sso9.png)

Přejděte na přihlašovací údaje **služby Azure Active Directory** > v [Centru pro správu Služby Azure Active Directory](https://aad.portal.azure.com/)a vyberte aktivitu přihlášení konkrétního uživatele.**Sign-ins** Vyhledejte pole **KÓD CHYBY PŘIHLÁŠENÍ.** Namapujte hodnotu tohoto pole na důvod selhání a rozlišení pomocí následující tabulky:

|Kód chyby přihlášení|Důvod selhání přihlášení|Řešení
| --- | --- | ---
| 81001 | Lístek Kerberos uživatele je příliš velký. | Snižte počet členství uživatele ve skupinách a zkuste to znovu.
| 81002 | Lístek kerberos uživatele nelze ověřit. | Podívejte se na [kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81003 | Lístek kerberos uživatele nelze ověřit. | Podívejte se na [kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81004 | Pokus o ověření protokolu Kerberos selhal. | Podívejte se na [kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81008 | Lístek kerberos uživatele nelze ověřit. | Podívejte se na [kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81009 | Lístek kerberos uživatele nelze ověřit. | Podívejte se na [kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81010 | Bezproblémové jednotné přihlašování selhalo, protože vypršela platnost lístku Kerberos uživatele nebo je neplatný. | Uživatel se musí přihlásit ze zařízení připojení k doméně v podnikové síti.
| 81011 | Nelze najít objekt uživatele na základě informací v lístku protokolu Kerberos uživatele. | Pomocí Služby Azure AD Connect synchronizujte informace o uživateli do služby Azure AD.
| 81012 | Uživatel, který se pokouší přihlásit k Azure AD, se liší od uživatele, který je přihlášen k zařízení. | Uživatel se musí přihlásit z jiného zařízení.
| 81013 | Nelze najít objekt uživatele na základě informací v lístku protokolu Kerberos uživatele. |Pomocí Služby Azure AD Connect synchronizujte informace o uživateli do služby Azure AD. 

## <a name="troubleshooting-checklist"></a>Kontrolní seznam pro řešení potíží

Pomocí následujícího kontrolního seznamu můžete řešit problémy s bezproblémovým jednotném přihlašovatce:

- Ujistěte se, že je ve službě Azure AD Connect povolená funkce jednotného přihlašování. Pokud tuto funkci nemůžete povolit (například kvůli blokovanému portu), ujistěte se, že máte všechny [požadavky](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) na místě.
- Pokud jste povolili připojení [Azure AD](../active-directory-azureadjoin-overview.md) a bezproblémové jednotné přihlašování ve vašem tenantovi, ujistěte se, že problém není s připojením Azure AD. Jednotné přihlašovací služby z připojení Azure AD má přednost před bezproblémovým jednotném přihlašovacím systémem, pokud je zařízení registrované ve službě Azure AD i ve spojení s doménou. S přihlašování z Azure AD Připojit se uživatel vidí přihlašovací dlaždice, která říká, že "Připojení k Windows".
- Ujistěte se, že`https://autologon.microsoftazuread-sso.com`adresa URL Azure AD ( ) je součástí nastavení zóny intranet uživatele.
- Zkontrolujte, zda je podnikové zařízení připojeno k doméně služby Active Directory. Zařízení _nemusí_ být Azure [AD připojen pro](../active-directory-azureadjoin-overview.md) bezproblémové jednotné přihlašovací služby pro práci.
- Ujistěte se, že je uživatel přihlášen k zařízení prostřednictvím účtu domény služby Active Directory.
- Ujistěte se, že účet uživatele pochází z doménové struktury služby Active Directory, kde bylo nastaveno bezproblémové jednotné přihlašování.
- Zkontrolujte, zda je zařízení připojeno k podnikové síti.
- Ujistěte se, že je čas zařízení synchronizován s časem ve službě Active Directory i řadiči domény a zda jsou od sebe do pěti minut.
- Ujistěte `AZUREADSSOACC` se, že účet počítače je k dispozici a povolen v každé doménové struktuře služby AD, která má být povolena bezešvé jednotné přihlašování. Pokud byl účet počítače odstraněn nebo chybí, můžete je znovu vytvořit pomocí [rutin prostředí PowerShell.](#manual-reset-of-the-feature)
- Seznam existujících lístků protokolu Kerberos `klist` v zařízení pomocí příkazu z příkazového řádku. Ujistěte se, že `AZUREADSSOACC` jsou k dispozici lístky vydané pro účet počítače. Lístky na protokol Kerberos uživatelů jsou obvykle platné po dobu 10 hodin. Ve službě Active Directory je pravděpodobně odlišná nastavení.
- Pokud jste ve vašem tenantovi zakázali a znovu povolili bezproblémové jednotné přihlašování, uživatelé nezískají možnosti jednotného přihlašování, dokud jejich lístky Kerberos uložené v mezipaměti nevyprší.
- Vyčistěte existující lístky protokolu Kerberos `klist purge` ze zařízení pomocí příkazu a akci opakujte.
- Chcete-li zjistit, zda se jedná o problémy související s JavaScriptem, přečtěte si protokoly konzoly prohlížeče (v části **Nástroje pro vývojáře).**
- Zkontrolujte [protokoly řadiče domény](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Protokoly řadičů domény

Pokud povolíte auditování úspěchu na řadiči domény, pak pokaždé, když se uživatel přihlásí prostřednictvím bezproblémového jednotného přihlašování, je do protokolu událostí zaznamenána položka zabezpečení. Tyto události zabezpečení můžete najít pomocí následujícího dotazu. (Vyhledejte událost **4769** přidruženou k účtu počítače **AzureADSSOAcc$.)**

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Ruční resetování funkce

Pokud řešení potíží nepomohlo, můžete funkci v tenantovi resetovat ručně. Postupujte podle těchto kroků na místním serveru, kde používáte Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Krok 1: Import bezešvého modulu PowerShell u jednotného přihlašování

1. Nejprve stáhněte a nainstalujte [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Přejděte `%programfiles%\Microsoft Azure Active Directory Connect` do složky.
3. Importujte bezešvý modul Prostředí SSO PowerShell pomocí tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2: Získání seznamu doménových struktur služby Active Directory, ve kterých bylo povoleno bezproblémové jednotné přihlašování

1. Spusťte PowerShell jako správce. V Prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext`. Po zobrazení výzvy zadejte pověření globálního správce klienta.
2. Zavolejte `Get-AzureADSSOStatus`. Tento příkaz poskytuje seznam doménových struktur služby Active Directory (podívejte se na seznam Domén), ve kterém byla tato funkce povolena.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3: Zakázání bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory, ve které jste tuto funkci nastavili

1. Zavolejte `$creds = Get-Credential`. Po zobrazení výzvy zadejte pověření správce domény pro zamýšlenou doménovou strukturu služby Active Directory.

   > [!NOTE]
   >Uživatelské jméno správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso.com\johndoe). Část uživatelského jména domény používáme k vyhledání řadiče domény správce domény pomocí služby DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Chránění uživatelé. Pokud ano, operace se nezdaří.

2. Zavolejte `Disable-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz odebere účet `AZUREADSSOACC` počítače z místního řadiče domény pro tuto konkrétní doménovou strukturu služby Active Directory.
3. Opakujte předchozí kroky pro každou doménovou strukturu služby Active Directory, ve které jste tuto funkci nastavili.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Povolení bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory

1. Zavolejte `Enable-AzureADSSOForest`. Po zobrazení výzvy zadejte pověření správce domény pro zamýšlenou doménovou strukturu služby Active Directory.

   > [!NOTE]
   >Uživatelské jméno správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso.com\johndoe). Část uživatelského jména domény používáme k vyhledání řadiče domény správce domény pomocí služby DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Chránění uživatelé. Pokud ano, operace se nezdaří.

2. Opakujte předchozí krok pro každou doménovou strukturu služby Active Directory, ve které chcete funkci nastavit.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Povolení funkce v tenantovi

Chcete-li zapnout funkci v `Enable-AzureADSSO -Enable $true`tenantovi, zavolejte .
