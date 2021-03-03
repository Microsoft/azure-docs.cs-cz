---
title: 'Azure Active Directory Connect: řešení bezproblémového jednoduchého Sign-On | Microsoft Docs'
description: Toto téma popisuje, jak řešit Azure Active Directory bezproblémovém jednoduchému Sign-On
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: daveba
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 10/07/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eef58f6e84fb3b4dec947fa3614b6ec1043ff89e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644642"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Řešení potíží s Azure Active Directory bezproblémovému jednoduchému Sign-On

Tento článek vám pomůže najít informace o řešení běžných problémů týkající se Azure Active Directory (Azure AD) bez problémů s jedním Sign-On (bezproblémové jednotné přihlašování).

## <a name="known-issues"></a>Známé problémy

- V několika případech může trvat až 30 minut, než se povolí bezproblémové jednotné přihlašování.
- Pokud ve svém tenantovi zakážete a znovu povolíte bezproblémové přihlašování (SSO), uživatelé nebudou mít k dispozici jednotné přihlašování do mezipaměti protokolu Kerberos, obvykle platnost po dobu 10 hodin.
- Pokud je úspěšné bezproblémové přihlašování, uživatel nemá možnost vybrat **si možnost zůstat přihlášeni**. Z důvodu tohoto chování nefungují [scénáře mapování SharePointu a OneDrivu](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) .
- Microsoft 365 klienti Win32 (Outlook, Word, Excel a další) s verzemi 16.0.8730. xxxx a vyšší se podporují pomocí neinteraktivního toku. Jiné verze se nepodporují. na těchto verzích uživatelé zadají své uživatelské jméno, ale ne hesla, aby se mohli přihlásit. Pro OneDrive budete muset pro tiché přihlašování aktivovat [funkci bezobslužné konfigurace OneDrive](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) .
- Bezproblémové jednotné přihlašování v prohlížeči Firefox nefunguje v privátním režimu procházení.
- Bezproblémové jednotné přihlašování v Internet Exploreru nefunguje, když je zapnutý Rozšířený chráněný režim.
- Bezproblémové jednotné přihlašování nefunguje v privátním režimu procházení na Microsoft Edge (starší verze).
- Bezproblémové jednotné přihlašování nefunguje na mobilních prohlížečích v iOS a Androidu.
- Pokud je uživatel součástí příliš velkého počtu skupin ve službě Active Directory, lístek protokolu Kerberos uživatele bude pravděpodobně příliš velký, aby mohl být zpracován, což způsobí bezproblémové přihlášení k selhání. Požadavky služby Azure AD HTTPS můžou mít hlavičky s maximální velikostí 50 KB; Lístky protokolu Kerberos musí být menší než omezení, aby vyhovovaly dalším artefaktům Azure AD (obvykle 2-5 KB), jako jsou soubory cookie. Naším doporučením je snížit členství uživatele ve skupinách a zkusit to znovu.
- Pokud provádíte synchronizaci 30 nebo více doménových struktur služby Active Directory, nemůžete povolit bezproblémové přihlašování prostřednictvím Azure AD Connect. Jako alternativní řešení můžete funkci v tenantovi [Povolit ručně](#manual-reset-of-the-feature) .
- Přidání adresy URL služby Azure AD ( `https://autologon.microsoftazuread-sso.com` ) do zóny důvěryhodných serverů namísto zóny místního intranetu *znemožní uživatelům přihlášení*.
- Bezproblémové jednotné přihlašování podporuje AES256_HMAC_SHA1 typy šifrování AES128_HMAC_SHA1 a RC4_HMAC_MD5 pro protokol Kerberos. Doporučuje se, aby se typ šifrování pro účet AzureADSSOAcc $ nastavil na AES256_HMAC_SHA1, nebo jeden z typů AES vs. RC4 pro zvýšení zabezpečení. Typ šifrování je uložený v atributu msDS-SupportedEncryptionTypes účtu účtu ve službě Active Directory.  Pokud je typ šifrování účtu AzureADSSOAcc $ nastavený na RC4_HMAC_MD5 a chcete ho změnit na jeden z typů šifrování AES, ujistěte se, že jste nejdřív převzali dešifrovací klíč protokolu Kerberos účtu AzureADSSOAcc $, jak je vysvětleno v [dokumentu nejčastějších dotazů](how-to-connect-sso-faq.md) v příslušné otázce. v opačném případě bezproblémové jednotné přihlašování neproběhne.
-  Pokud máte více než jednu doménovou strukturu s důvěryhodností doménové struktury, povolení jednotného přihlašování v jedné z doménových struktur povolí jednotné přihlašování ve všech důvěryhodných doménových strukturách. Pokud povolíte jednotné přihlašování v doménové struktuře, kde je jednotné přihlašování už povolené, zobrazí se chyba s informací o tom, že jednotné přihlašování je už v doménové struktuře povolené.

## <a name="check-status-of-feature"></a>Stav kontroly funkce

Ujistěte se, že je ve vašem tenantovi stále **povolená** funkce bezproblémového jednotného přihlašování. Stav můžete zjistit tak, že v [centru pro správu Azure Active Directory](https://aad.portal.azure.com/)kliknete na podokno **Azure AD Connect** .

![Centrum pro správu Azure Active Directory: Azure AD Connect podokno](./media/tshoot-connect-sso/sso10.png)

Kliknutím sem zobrazíte všechny doménové struktury služby Active Directory, které jsou povolené pro bezproblémové jednotné přihlašování.

![Centrum pro správu Azure Active Directory: bezproblémové podokno jednotného přihlašování](./media/tshoot-connect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Důvody neúspěšného přihlášení v centru pro správu Azure Active Directory (potřebuje licenci na prémii)

Pokud má tenant přidruženou licenci Azure AD Premium, můžete se také podívat na [sestavu aktivita přihlášení](../reports-monitoring/concept-sign-ins.md) v [centru pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Azure Active Directory: sestava přihlášení](./media/tshoot-connect-sso/sso9.png)

  >  V centru pro [správu Azure Active Directory](https://aad.portal.azure.com/)přejděte na Azure Active Directory **přihlášení** a pak vyberte přihlašovací aktivitu konkrétního uživatele. Vyhledejte pole **kód chyby přihlášení** . Namapujte hodnotu tohoto pole na důvod selhání a rozlišení pomocí následující tabulky:

|Kód chyby přihlášení|Důvod neúspěšného přihlášení|Řešení
| --- | --- | ---
| 81001 | Lístek Kerberos uživatele je příliš velký. | Snižte počet členství uživatele ve skupinách a zkuste to znovu.
| 81002 | Nepovedlo se ověřit lístek Kerberos uživatele. | Viz [Kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81003 | Nepovedlo se ověřit lístek Kerberos uživatele. | Viz [Kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81004 | Pokus o ověření protokolu Kerberos selhal. | Viz [Kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81008 | Nepovedlo se ověřit lístek Kerberos uživatele. | Viz [Kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81009 | Nepovedlo se ověřit lístek Kerberos uživatele. | Viz [Kontrolní seznam pro řešení potíží](#troubleshooting-checklist).
| 81010 | Bezproblémové jednotné přihlašování selhalo, protože vypršela platnost lístku Kerberos uživatele nebo je neplatný. | Uživatel se musí přihlásit ze zařízení připojeného k doméně ve vaší podnikové síti.
| 81011 | Nelze najít objekt uživatele na základě informací v lístku Kerberos uživatele. | K synchronizaci informací o uživateli do Azure AD použijte Azure AD Connect.
| 81012 | Uživatel, který se pokouší přihlásit ke službě Azure AD, se liší od uživatele, který se k zařízení přihlásil. | Uživatel se musí přihlásit z jiného zařízení.
| 81013 | Nelze najít objekt uživatele na základě informací v lístku Kerberos uživatele. |K synchronizaci informací o uživateli do Azure AD použijte Azure AD Connect. 

## <a name="troubleshooting-checklist"></a>Kontrolní seznam pro řešení potíží

Pomocí následujícího kontrolního seznamu můžete vyřešit bezproblémové problémy jednotného přihlašování:

- Ujistěte se, že je funkce bezproblémového jednotného přihlašování povolená v Azure AD Connect. Pokud tuto funkci nemůžete povolit (třeba kvůli blokovanému portu), ujistěte se, že jsou splněné všechny [požadavky](how-to-connect-sso-quick-start.md#step-1-check-the-prerequisites) .
- Pokud jste ve svém tenantovi povolili [připojení ke službě Azure AD](../devices/overview.md) a bezproblémové jednotné přihlašování, zajistěte, aby tento problém nebyl s Azure AD JOIN. JEDNOTNÉ přihlašování z Azure AD má přednost před bezproblémové jednotné přihlašování, pokud je zařízení registrované ve službě Azure AD a připojené k doméně. Pomocí jednotného přihlašování z Azure AD se uživateli zobrazí dlaždice přihlášení, která říká "připojené k Windows".
- Ujistěte se, že adresa URL služby Azure AD ( `https://autologon.microsoftazuread-sso.com` ) je součástí nastavení zóny intranetu uživatele.
- Ujistěte se, že je firemní zařízení připojené k doméně služby Active Directory. _Zařízení nemusí_ být [připojené k Azure AD](../devices/overview.md) , aby fungovalo bez bezproblémového jednotného přihlašování.
- Ujistěte se, že je uživatel přihlášený k zařízení prostřednictvím účtu domény služby Active Directory.
- Zajistěte, aby byl účet uživatele z doménové struktury služby Active Directory, kde bylo nastaveno bezproblémové jednotné přihlašování.
- Ujistěte se, že je zařízení připojené k podnikové síti.
- Ujistěte se, že se čas zařízení synchronizuje s časem v adresáři Active Directory i v řadičích domény a že mezi sebou navzájem do pěti minut.
- Zajistěte, aby `AZUREADSSOACC` byl účet počítače přítomen a povolen v každé doménové struktuře služby AD, u které chcete bezproblémové jednotné přihlašování povolit. Pokud byl účet počítače odstraněný nebo chybí, můžete k jejich opětovnému vytvoření použít [rutiny PowerShellu](#manual-reset-of-the-feature) .
- Vypíše existující lístky protokolu Kerberos na zařízení pomocí `klist` příkazu z příkazového řádku. Ujistěte se, že jsou k dispozici lístky vydané pro `AZUREADSSOACC` účet počítače. Lístky protokolu Kerberos uživatelů jsou obvykle platné po dobu 10 hodin. Ve službě Active Directory můžete mít různá nastavení.
- Pokud jste v tenantovi zakázali a znovu povolili bezproblémové přihlašování (SSO), uživatelé nebudou mít k dispozici jednotné přihlašování, dokud nevyprší platnost lístků protokolu Kerberos uložených v mezipaměti.
- Pomocí příkazu Vyprázdnit existující lístky protokolu Kerberos ze zařízení `klist purge` a zkuste to znovu.
- Chcete-li zjistit, zda existují problémy související s jazykem JavaScript, zkontrolujte protokoly konzoly prohlížeče (v části **vývojářské nástroje**).
- Zkontrolujte [protokoly řadiče domény](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Protokoly řadiče domény

Pokud povolíte v řadiči domény auditování úspěšného přihlášení, pokaždé, když se uživatel přihlásí prostřednictvím bezproblémového přihlašování, zaznamená se položka zabezpečení v protokolu událostí. Tyto události zabezpečení můžete najít pomocí následujícího dotazu. (Vyhledejte událost **4769** spojenou s účtem počítače **AzureADSSOAcc $**.)

```
  <QueryList>
    <Query Id="0" Path="Security">
      <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
    </Query>
  </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Ruční resetování funkce

Pokud se vám řešení potíží nepovedlo, můžete funkci v tenantovi ručně obnovit. Na místním serveru, na kterém běží Azure AD Connect, postupujte podle těchto kroků.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Krok 1: import bezproblémového modulu pro jednotné přihlašování do PowerShellu

1. Nejdřív si stáhněte a nainstalujte [Azure AD PowerShell](/powershell/azure/active-directory/overview).
2. Přejděte do `%programfiles%\Microsoft Azure Active Directory Connect` složky.
3. Pomocí tohoto příkazu Importujte modul prostředí PowerShell pro bezproblémové přihlašování: `Import-Module .\AzureADSSO.psd1` .

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2: Získejte seznam doménových struktur služby Active Directory, na kterých je povolené bezproblémové jednotné přihlašování.

1. Spusťte PowerShell jako správce. V prostředí PowerShell volejte `New-AzureADSSOAuthenticationContext` . Po zobrazení výzvy zadejte přihlašovací údaje globálního správce vašeho tenanta.
2. Volání `Get-AzureADSSOStatus` . Tento příkaz vám poskytne seznam doménových struktur služby Active Directory (podívejte se na seznam domény), na kterém je tato funkce povolená.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3: zakažte bezproblémové přihlašování pro každou doménovou strukturu služby Active Directory, kde jste tuto funkci nastavili.

1. Volání `$creds = Get-Credential` . Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro požadovanou doménovou strukturu služby Active Directory.

   > [!NOTE]
   >Uživatelské jméno pověření správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso. com\johndoe). Část Doména uživatelského jména používáme k vyhledání řadiče domény správce domény pomocí DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Protected Users. V takovém případě se operace nezdaří.

2. Volání `Disable-AzureADSSOForest -OnPremCredentials $creds` . Tento příkaz odebere `AZUREADSSOACC` účet počítače z místního řadiče domény pro tuto konkrétní doménovou strukturu služby Active Directory.
3. Předchozí kroky opakujte pro každou doménovou strukturu služby Active Directory, kde jste tuto funkci nastavili.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: povolení bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory

1. Volání `Enable-AzureADSSOForest` . Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro požadovanou doménovou strukturu služby Active Directory.

   > [!NOTE]
   >Uživatelské jméno pověření správce domény musí být zadáno ve formátu názvu účtu SAM (contoso\johndoe nebo contoso. com\johndoe). Část Doména uživatelského jména používáme k vyhledání řadiče domény správce domény pomocí DNS.

   >[!NOTE]
   >Použitý účet správce domény nesmí být členem skupiny Protected Users. V takovém případě se operace nezdaří.

2. Opakujte předchozí krok pro každou doménovou strukturu služby Active Directory, ve které chcete tuto funkci nastavit.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Povolení funkce ve vašem tenantovi

Chcete-li zapnout funkci ve vašem tenantovi, zavolejte `Enable-AzureADSSO -Enable $true` .
