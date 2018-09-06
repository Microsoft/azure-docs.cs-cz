---
title: 'Azure Active Directory Connect: Řešení potíží s bezproblémové jednotné přihlašování | Dokumentace Microsoftu'
description: Toto téma popisuje postupy řešení potíží s Azure Active Directory bezproblémové jednotné přihlašování
services: active-directory
author: billmath
ms.reviewer: swkrish
manager: mtillman
ms.assetid: 9f994aca-6088-40f5-b2cc-c753a4f41da7
ms.service: active-directory
ms.workload: identity
ms.topic: article
ms.date: 09/04/2018
ms.component: hybrid
ms.author: billmath
ms.openlocfilehash: 029ba1c936862ef5c5f774dc683c4746e157c4aa
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43781931"
---
# <a name="troubleshoot-azure-active-directory-seamless-single-sign-on"></a>Řešení potíží s Azure Active Directory bezproblémové jednotné přihlašování

Tento článek vám pomůže najít řešení potíží s informace o běžných problémech týkajících se služby Azure Active Directory (Azure AD) bezproblémové jednotné přihlašování (bezproblémové jednotné přihlašování).

## <a name="known-issues"></a>Známé problémy

- V několika případech povolení bezproblémového jednotného přihlašování může trvat až 30 minut.
- Je-li zakázat a znovu povolit bezproblémového jednotného přihlašování ve svém tenantovi, nebudou uživatelé získají jednotné přihlašování do své mezipaměti lístky protokolu Kerberos, obvykle platný po dobu 10 hodin, platnost vypršela.
- Podpora prohlížeče Microsoft Edge není k dispozici.
- Pokud bude úspěšné bezproblémového jednotného přihlašování, uživatel nemá možnost vybrat **neodhlašovat**. Kvůli tomuto chování [Sharepointu a Onedrivu mapování scénářů](https://support.microsoft.com/help/2616712/how-to-configure-and-to-troubleshoot-mapped-network-drives-that-connec) nefungují.
- Klienty Office 365 Win32 (Outlook, Word, Excel a jiné) s verze 16.0.8730.xxxx a vyšší jsou podporovány při použití toku jako neinteraktivní. Jiné verze nejsou podporovány. Tyto verze budou uživatelé zadávat svá uživatelská jména, ale ne hesla k přihlášení. Pro OneDrive, budete muset aktivovat [Onedrivu bezobslužné konfiguraci funkce](https://techcommunity.microsoft.com/t5/Microsoft-OneDrive-Blog/Previews-for-Silent-Sync-Account-Configuration-and-Bandwidth/ba-p/120894) bezobslužné možnosti přihlašování.
- Bezproblémové jednotné přihlašování nefunguje v privátním režimu procházení ve Firefoxu.
- Bezproblémové jednotné přihlašování v aplikaci Internet Explorer nebude fungovat, pokud je zapnuté rozšířené chráněný režim.
- Bezproblémové jednotné přihlašování nefunguje pro mobilní prohlížeče na zařízení s iOS a Android.
- Pokud je uživatel součástí příliš mnoho skupin ve službě Active Directory, lístku Kerberos uživatele budou pravděpodobně příliš velký pro zpracování, a to způsobí, že bezproblémového jednotného přihlašování k selhání. Požadavky služby Azure AD HTTPS může obsahovat záhlaví a maximální velikost 50 KB; Lístky protokolu Kerberos musí být menší než toto omezení tak, aby vyhovovaly jiné artefakty Azure AD (obvykle 2 – 5 KB) jako jsou soubory cookie. Naše doporučení je omezit uživatele členství ve skupinách a zkuste to znovu.
- Pokud se synchronizace 30 nebo více doménovými strukturami Active Directory, nelze povolit bezproblémového jednotného přihlašování pomocí služby Azure AD Connect. Jako alternativní řešení můžete [ručně povolit](#manual-reset-of-the-feature) funkci ve vašem tenantovi.
- Přidat adresu URL služby Azure AD (https://autologon.microsoftazuread-sso.com) do zóny důvěryhodných serverů místo zóny místního intranetu *blokuje uživatele z přihlášení*.
- Zákaz použití **RC4_HMAC_MD5** typ šifrování pro protokol Kerberos v nastavení služby Active Directory přeruší bezproblémového jednotného přihlašování. V nástroji Editor správy zásad skupiny Ujistěte se, že hodnota zásad pro **RC4_HMAC_MD5** pod **konfigurace počítače -> Nastavení Windows -> Nastavení zabezpečení -> Místní zásady -> Možnosti zabezpečení – > "Zabezpečení sítě: konfigurovat typy šifrování povolené pro protokol Kerberos"** je "povoleno".

## <a name="check-status-of-feature"></a>Zkontrolujte stav funkce

Ujistěte se, že funkce bezproblémového jednotného přihlašování je stále **povoleno** ve svém tenantovi. Stav můžete zkontrolovat tak, že přejdete **Azure AD Connect** v podokně [centra pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Azure Active Directory: podokně služby Azure AD Connect](./media/active-directory-aadconnect-sso/sso10.png)

Klikněte na Zobrazit všechny doménové struktury AD, které jsou zapnuty pro bezproblémové jednotné přihlašování.

![Centrum pro správu Azure Active Directory: podokně bezproblémového jednotného přihlašování](./media/active-directory-aadconnect-sso/sso13.png)

## <a name="sign-in-failure-reasons-in-the-azure-active-directory-admin-center-needs-a-premium-license"></a>Důvody selhání přihlášení v Centru pro správu Azure Active Directory (potřebuje licenci Premium)

Pokud váš tenant licenci Azure AD Premium s ním spojená, můžete také prohlédnout [sestavy aktivit přihlašování](../reports-monitoring/concept-sign-ins.md) v [centra pro správu Azure Active Directory](https://aad.portal.azure.com/).

![Centrum pro správu Azure Active Directory: přihlášení](./media/active-directory-aadconnect-sso/sso9.png)

Přejděte do **Azure Active Directory** > **přihlášení** v [centra pro správu Azure Active Directory](https://aad.portal.azure.com/)a pak vyberte aktivit přihlašování konkrétního uživatele. Hledat **Přihlašovací chyba kódu** pole. Mapování hodnotu pole Důvod selhání a řešení pomocí následující tabulky:

|Kód chyby přihlášení|Příčina chyby přihlášení|Řešení
| --- | --- | ---
| 81001 | Lístek Kerberos uživatele je příliš velký. | Snižte počet členství uživatele ve skupinách a zkuste to znovu.
| 81002 | Nelze ověřit lístek Kerberos uživatele. | Zobrazit [řešení potíží s kontrolní seznam](#troubleshooting-checklist).
| 81003 | Nelze ověřit lístek Kerberos uživatele. | Zobrazit [řešení potíží s kontrolní seznam](#troubleshooting-checklist).
| 81004 | Pokus o ověření protokolu Kerberos selhal. | Zobrazit [řešení potíží s kontrolní seznam](#troubleshooting-checklist).
| 81008 | Nelze ověřit lístek Kerberos uživatele. | Zobrazit [řešení potíží s kontrolní seznam](#troubleshooting-checklist).
| 81009 | Nelze ověřit lístek Kerberos uživatele. | Zobrazit [řešení potíží s kontrolní seznam](#troubleshooting-checklist).
| 81010 | Bezproblémové jednotné přihlašování selhalo, protože vypršela platnost lístku Kerberos uživatele nebo je neplatný. | Uživatel musí přihlásit ze zařízení připojeného k doméně uvnitř firemní sítě.
| 81011 | Nepovedlo se najít objekt uživatele na základě informací v lístku Kerberos uživatele. | Pomocí služby Azure AD Connect synchronizuje informace uživatele do služby Azure AD.
| 81012 | Uživatel pokoušel se přihlásit ke službě Azure AD se liší od uživatele, který je přihlášený do zařízení. | Uživatel musí přihlásit z jiného zařízení.
| 81013 | Nepovedlo se najít objekt uživatele na základě informací v lístku Kerberos uživatele. |Pomocí služby Azure AD Connect synchronizuje informace uživatele do služby Azure AD. 

## <a name="troubleshooting-checklist"></a>Řešení potíží s kontrolní seznam

Použijte následující kontrolní seznam k řešení problémů bezproblémového jednotného přihlašování:

- Povolte funkci bezproblémového jednotného přihlašování ve službě Azure AD Connect. Pokud nelze povolit funkci (například kvůli zablokování portů), ujistěte se, že budete mít vše [požadavky](active-directory-aadconnect-sso-quick-start.md#step-1-check-the-prerequisites) na místě.
- Pokud jste povolili obě [Azure AD Join](../active-directory-azureadjoin-overview.md) a bezproblémového jednotného přihlašování ve svém tenantovi, ujistěte se, že problém není s Azure AD Join. Jednotné přihlašování z Azure AD Join má přednost před bezproblémového jednotného přihlašování, pokud je zařízení registrované v Azure AD i připojených k doméně. Uživateli se zobrazí s jednotným Přihlašováním z Azure AD Join dlaždici přihlášení s textem "Připojení k Windows".
- Ujistěte se, že adresa URL služby Azure AD (https://autologon.microsoftazuread-sso.com) je součástí nastavení zóny Intranet uživatele.
- Ujistěte se, že podnikových zařízení je připojené k doméně služby Active Directory. Zařízení _nebude_ musí být [připojeno k Azure AD](../active-directory-azureadjoin-overview.md) pro bezproblémové jednotné přihlašování pro práci.
- Ujistěte se, že uživatel je přihlášen k zařízení pomocí účtu domény služby Active Directory.
- Ujistěte se, že uživatelský účet je z doménové struktury služby Active Directory, kde byl bezproblémové jednotné přihlašování nastavit.
- Ujistěte se, že je zařízení připojené k podnikové síti.
- Ujistěte se, že čas zařízení synchronizované s časem v Active Directory a řadiče domény a že jsou do pěti minut od sebe navzájem.
- Ujistěte se, `AZUREADSSOACCT` účet počítače je k dispozici a povoleno v každé doménové struktuře AD, který chcete bezproblémového jednotného přihlašování povolená. Pokud účet počítače se odstranil nebo chybí, můžete použít [rutin prostředí PowerShell](#manual-reset-of-the-feature) je vytvořit znovu.
- Seznam existujících lístků protokolu Kerberos na zařízení s použitím `klist` z příkazového řádku. Ujistěte se, že lístky vydané pro `AZUREADSSOACCT` účet počítače jsou k dispozici. Lístky protokolu Kerberos uživatele jsou obvykle platné po dobu 10 hodin. Můžete mít různá nastavení ve službě Active Directory.
- Je-li zakázána a znovu povolit bezproblémového jednotného přihlašování ve svém tenantovi, nebudou uživatelé získají jednotné přihlašování do své mezipaměti lístky protokolu Kerberos vypršela.
- Vymazání existujících lístků protokolu Kerberos ze zařízení s použitím `klist purge` příkazu a zkuste to znovu.
- Chcete-li zjistit, zda jsou problémy související s jazyka JavaScript, zkontrolovat protokoly konzoly prohlížeče (v části **vývojářské nástroje**).
- Zkontrolujte [protokolu řadiče domény](#domain-controller-logs).

### <a name="domain-controller-logs"></a>Protokoly řadiče domény

Pokud povolíte auditování úspěšných na vašem řadiči domény a pak pokaždé, když se uživatel přihlásí pomocí bezproblémového jednotného přihlašování, záznam zabezpečení se zaznamená do protokolu událostí. Tyto události zabezpečení získáte pomocí následujícího dotazu. (Vyhledejte událost **4769** přidružené k účtu počítače **AzureADSSOAcc$**.)

```
    <QueryList>
      <Query Id="0" Path="Security">
    <Select Path="Security">*[EventData[Data[@Name='ServiceName'] and (Data='AZUREADSSOACC$')]]</Select>
      </Query>
    </QueryList>
```

## <a name="manual-reset-of-the-feature"></a>Ruční vynulování funkce

Pokud se vám nepomohly řešení potíží, můžete ručně obnovit funkci ve svém tenantovi. Proveďte tyto kroky na místní server, pokud používáte Azure AD Connect.

### <a name="step-1-import-the-seamless-sso-powershell-module"></a>Krok 1: Naimportujte modul Powershellu bezproblémové jednotné přihlašování

1. Nejprve stáhnout a nainstalovat [Azure AD PowerShell](https://docs.microsoft.com/powershell/azure/active-directory/overview).
2. Přejděte `%programfiles%\Microsoft Azure Active Directory Connect` složky.
3. Naimportujte modul Powershellu bezproblémové jednotné přihlašování pomocí tohoto příkazu: `Import-Module .\AzureADSSO.psd1`.

### <a name="step-2-get-the-list-of-active-directory-forests-on-which-seamless-sso-has-been-enabled"></a>Krok 2: Získání seznamu doménových struktur služby Active Directory, na kterých se povolila bezproblémového jednotného přihlašování

1. Spusťte PowerShell jako správce. V prostředí PowerShell, zavolejte `New-AzureADSSOAuthenticationContext`. Po zobrazení výzvy zadejte přihlašovací údaje globálního správce vašeho tenanta.
2. Volání `Get-AzureADSSOStatus`. Tento příkaz vám poskytne seznam doménových struktur služby Active Directory (pohled na seznamu "Domény") na které tato funkce povolená.

### <a name="step-3-disable-seamless-sso-for-each-active-directory-forest-where-youve-set-up-the-feature"></a>Krok 3: Zakázání bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory, kde jste tuto funkci nastavili

1. Volání `$creds = Get-Credential`. Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro určené doménové struktuře služby Active Directory.

    >[!NOTE]
    >Používáme zadané v uživatele hlavní názvy (UPN) uživatelské jméno správce domény (johndoe@contoso.com) formát nebo kvalifikovaný účtu sam formát názvu domény (contoso\janmacek nebo contoso.com\johndoe), se najít odpovídající doménovou strukturu AD. Pokud používáte účtu sam kvalifikovaný název domény, používáme doména uživatelské jméno pro [vyhledejte řadič domény ze správce domény s DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Pokud místo toho použít hlavní název uživatele jsme [přeložit na účtu sam kvalifikovaný název domény](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) před vyhledání vhodné řadiče domény.

2. Volání `Disable-AzureADSSOForest -OnPremCredentials $creds`. Tento příkaz odebere `AZUREADSSOACCT` účet počítače z místního kontroleru domény pro tento konkrétní doménovou strukturu služby Active Directory.
3. Zopakujte předchozí kroky pro každou doménovou strukturu služby Active Directory, kde jste tuto funkci nastavili.

### <a name="step-4-enable-seamless-sso-for-each-active-directory-forest"></a>Krok 4: Povolení bezproblémového jednotného přihlašování pro každou doménovou strukturu služby Active Directory

1. Volání `Enable-AzureADSSOForest`. Po zobrazení výzvy zadejte přihlašovací údaje správce domény pro určené doménové struktuře služby Active Directory.

   >[!NOTE]
   >Používáme zadané v uživatele hlavní názvy (UPN) uživatelské jméno správce domény (johndoe@contoso.com) formát nebo kvalifikovaný účtu sam formát názvu domény (contoso\janmacek nebo contoso.com\johndoe), se najít odpovídající doménovou strukturu AD. Pokud používáte účtu sam kvalifikovaný název domény, používáme doména uživatelské jméno pro [vyhledejte řadič domény ze správce domény s DNS](https://social.technet.microsoft.com/wiki/contents/articles/24457.how-domain-controllers-are-located-in-windows.aspx). Pokud místo toho použít hlavní název uživatele jsme [přeložit na účtu sam kvalifikovaný název domény](https://docs.microsoft.com/windows/desktop/api/ntdsapi/nf-ntdsapi-dscracknamesa) před vyhledání vhodné řadiče domény.

2. Opakujte předchozí krok pro každou doménovou strukturu služby Active Directory, ve které chcete nastavit tuto funkci.

### <a name="step-5-enable-the-feature-on-your-tenant"></a>Krok 5. Povolení této funkce na tenantovi

Chcete-li zapnout funkci ve vašem tenantovi, zavolejte `Enable-AzureADSSO -Enable $true`.
