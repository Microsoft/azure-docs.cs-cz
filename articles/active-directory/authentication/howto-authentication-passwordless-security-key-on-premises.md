---
title: Bezpečnostní klíč nezaložené na heslech – přihlášení k místním prostředkům (Preview) – Azure Active Directory
description: Povolit klíč zabezpečení s nezabezpečenými hesly přihlášení k místním prostředkům (Preview)
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 08/05/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 42c42bb1e76d854723c8a88e9c3e2c104464beb0
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/30/2019
ms.locfileid: "73164858"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-preview"></a>Povolit klíč zabezpečení s nezabezpečenými hesly přihlášení k místním prostředkům (Preview)

Tento dokument se zaměřuje na povolení ověřování bez hesla u místních prostředků pro prostředí, která jsou **připojená k Azure AD** i pro zařízení s Windows 10 **připojená k Azure AD** . Tato funkce poskytuje bezproblémové jednotné přihlašování (SSO) k místním prostředkům pomocí bezpečnostních klíčů kompatibilních s Microsoftem.

|     |
| --- |
| Bezpečnostní klíče FIDO2 jsou funkcí veřejné verze Preview Azure Active Directory. Další informace o verzích Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verze Preview.|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Jednotné přihlašování k místním prostředkům pomocí klíčů FIDO2

Azure Active Directory (AD) může vydávat lístky TGT (TGT) lístku protokolu Kerberos pro jednu nebo více domén služby Active Directory. Tato funkce umožňuje uživatelům přihlašovat se k Windows pomocí moderních přihlašovacích údajů, jako jsou bezpečnostní klíče FIDO2, a přistupovat k tradičním prostředkům založeným na službě Active Directory. Lístky služby Kerberos a autorizace budou nadále řízeny místními řadiči domény služby Active Directory.

Objekt serveru Azure AD Kerberos se vytvoří v místní službě Active Directory a pak bude bezpečně publikovaný na Azure Active Directory. Objekt není přidružen k žádným fyzickým serverům. Je to prostě prostředek, který může Azure Active Directory použít k vygenerování lístků pro udělení lístku protokolu Kerberos (TGT) pro váš Doména služby Active Directory.

![Získání lístku TGT a PRT z Azure AD a služba AD DS](./media/howto-authentication-passwordless-on-premises/fido2-tgt-exchange-process.png)

1. Uživatel se přihlásí k zařízení s Windows 10 pomocí bezpečnostního klíče FIDO2 a provede ověření ve službě Azure AD.
1. Azure AD kontroluje adresář pro klíč serveru protokolu Kerberos, který odpovídá místní doméně AD uživatele.
   1. Azure AD vygeneruje lístek lístku Kerberos pro místní doménu AD uživatele. Lístek TGT obsahuje jenom SID uživatele. V lístku TGT nejsou obsažena žádná autorizační data.
1. Lístek TGT se vrátí klientovi spolu s primárním tokenem pro obnovení služby Azure AD (PRT).
1. Klientský počítač kontaktuje místní řadič domény služby AD a postará se o úplný lístek TGT na plně vytvořený lístek TGT.
1. Klientský počítač má teď Azure AD PRT a úplný lístek TGT služby Active Directory a má přístup k cloudovým i místním prostředkům.

## <a name="requirements"></a>Požadavky

Před dokončením kroků v tomto článku musí organizace provést kroky v článku [Povolení podepsání klíče zabezpečení s neheslem do zařízení s Windows 10 (Preview)](howto-authentication-passwordless-security-key.md) .

Organizace musí splňovat následující požadavky na software.

- Na zařízeních musí běžet Windows 10 Insider Build 18945 nebo novější.
- Upgradujte na nejnovější verzi [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)
- Plně opravené řadiče domény se systémem Windows Server 2016/2019 pro zpracování místního zatížení žádosti o ověření.

### <a name="supported-scenarios"></a>Podporované scénáře

- Scénář podporuje jednotné přihlašování (SSO):
   - Cloudové prostředky, jako je Office 365 a další aplikace s podporou SAML.
   - Místní prostředky a ověřování integrované v systému Windows pro weby, včetně webů a webů SharePointu, které vyžadují ověřování služby IIS nebo prostředky, které používají ověřování pomocí protokolu NTLM.

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře nejsou podporovány:

- Nasazení systému Windows Server Active Directory Domain Services (služba AD DS) připojené k doméně (jenom místní zařízení) **se**nepodporuje.
- Scénáře RDP, VDI a Citrix nejsou **podporovány** pomocí klíče zabezpečení.
- S/MIME není **podporována** použití klíče zabezpečení.
- Příkaz Spustit jako není **podporován** pomocí klíče zabezpečení.
- Přihlášení k serveru pomocí klíče zabezpečení není **podporováno**.

## <a name="create-kerberos-server-object"></a>Vytvoření objektu serveru Kerberos

Správci budou pomocí nástrojů PowerShellu ze svého serveru Azure AD Connect vytvořit objekt serveru Azure AD Kerberos ve svém místním adresáři. Tyto kroky budete muset spustit v každé doméně a doménové struktuře ve vaší organizaci, která obsahuje uživatele Azure AD.

1. Upgradujte na nejnovější verzi Azure AD Connect. Tyto pokyny předpokládají, že jste už Azure AD Connect nakonfigurovali pro podporu hybridního prostředí.
1. Na serveru Azure AD Connect otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a přejděte do složky C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\
1. Spusťte následující příkazy PowerShellu pro vytvoření nového objektu serveru Azure AD Kerberos v místní doméně služby Active Directory i v tenantovi Azure Active Directory.

> [!NOTE]
> V následujícím příkladu nahraďte "contoso.corp.com" názvem domény vaší místní služby Active Directory.

```PowerShell
Import-Module ".\AzureAdKerberos.psd1"

# Specify the on-premises Active Directory domain. A new Azure AD
# Kerberos Server object will be created in this Active Directory domain.
$domain = "contoso.corp.com"

# Enter an Azure Active Directory global administrator username and password.
$cloudCred = Get-Credential

# Enter a domain administrator username and password.
$domainCred = Get-Credential

# Create the new Azure AD Kerberos Server object in Active Directory
# and then publish it to Azure Active Directory.
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="viewing-and-verifying-the-azure-ad-kerberos-server"></a>Zobrazení a ověření serveru Azure AD Kerberos

Nově vytvořený server Azure AD Kerberos můžete zobrazit a ověřit pomocí následujícího příkazu. 

```PowerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Tento příkaz zobrazí výstup vlastností serveru Azure AD Kerberos. Můžete zkontrolovat vlastnosti a ověřit, zda je vše v dobrém pořadí.

| Vlastnost | Popis |
| --- | --- |
| ID | Jedinečné ID objektu řadiče domény služby AD. Toto ID se někdy označuje jako "slot" nebo je "ID větve". |
| DomainDnsName | Název domény DNS Doména služby Active Directory. |
| ComputerAccount | Objekt účtu počítače objektu serveru Azure AD Kerberos (řadič domény). |
| UserAccount | Zakázaný objekt uživatelského účtu, který obsahuje šifrovací klíč TGT serveru Azure AD Kerberos. Rozlišující název tohoto účtu bude: <br> CN = krbtgt_AzureAD, CN = uživatelé, < > domén-DN |
| Verze | Klíčová verze šifrovacího klíče TGT serveru Azure AD Kerberos. Verze je přiřazena při vytvoření klíče. Verze se pak zvýší pokaždé, když se klíč otočí. Přírůstky jsou založené na metadatech replikace a pravděpodobně budou větší než jedna. Například počáteční verze může být 192272. Při prvním otočení klíče může verze přejít na 212621. Důležitou věcí, kterou je třeba ověřit, je, že verze CloudKeyVersion pro místní objekt a objekt pro cloudový objekt jsou stejné. |
| KeyUpdatedOn | Datum a čas, kdy se šifrovací klíč TGT serveru Azure AD Kerberos aktualizoval nebo vytvořil. |
| KeyUpdatedFrom | Řadič domény, ve kterém se naposledy aktualizoval šifrovací klíč TGT serveru Azure AD Kerberos |
| CloudId | ID z objektu Azure AD. Musí odpovídat ID výše. |
| CloudDomainDnsName | DomainDnsName z objektu Azure AD. Musí odpovídat DomainDnsName výše. |
| CloudKeyVersion | Verze modulu z objektu Azure AD. Musí odpovídat verzi výše uvedeného. |
| CloudKeyUpdatedOn | KeyUpdatedOn z objektu Azure AD. Musí odpovídat KeyUpdatedOn výše. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotace klíče serveru Azure AD Kerberos

Šifrovací klíče serveru Azure AD Kerberos by se měly pravidelně střídat KRBTGT. Doporučuje se postupovat podle stejného plánu, který použijete k otočení všech ostatních KRBTGT klíčů kontroleru Doména služby Active Directory.

> [!WARNING]
> K dispozici jsou i další nástroje, které by mohly otáčet KRBTGT klíče, ale k otočení KRBTGT klíčů serveru Kerberos služby Azure AD je nutné použít nástroje uvedené v tomto dokumentu. Tím se zajistí, že se klíče aktualizují v místní službě AD i v Azure AD.

```PowerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Odebírá se server Azure AD Kerberos.

Pokud byste chtěli vrátit scénář a odebrat server Azure AD Kerberos z místní služby Active Directory a Azure Active Directory, spusťte následující příkaz.  

```PowerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scénáře s více doménovými strukturami a více doménami

Objekt serveru Azure AD Kerberos je reprezentován ve službě Azure AD jako objekt KerberosDomain. Každá místní doména služby Active Directory je reprezentována jako jeden objekt KerberosDomain ve službě Azure AD.

Vaše organizace má například doménovou strukturu služby Active Directory se dvěma doménami, contoso.com a fabrikam.com. Pokud se rozhodnete, že Azure AD bude pro celou doménovou strukturu vydávat lístky TGT protokolu Kerberos, budou ve službě Azure AD k dispozici dva objekty KerberosDomain. Jeden objekt KerberosDomain pro contoso.com a jeden pro fabrikam.com. Pokud máte více doménových struktur služby Active Directory, budete mít jeden objekt KerberosDomain pro každou doménu v každé doménové struktuře.

Kroky v části [vytvoření objektu serveru Kerberos](#create-kerberos-server-object) v každé doméně a doménové struktuře ve vaší organizaci, která obsahuje uživatele Azure AD, budete muset spustit.

## <a name="known-behavior"></a>Známé chování

Přihlášení pomocí FIDO se zablokuje, pokud vypršela platnost hesla. Očekává se, že uživatel resetuje heslo předtím, než se bude moct přihlásit pomocí FIDO.

## <a name="troubleshooting-and-feedback"></a>Řešení potíží a zpětná vazba

Pokud chcete sdílet zpětnou vazbu nebo narazit na problémy při prohlížení této funkce, sdílejte ji prosím přes aplikaci Centrum zpětné vazby z Windows.

1. Spusťte **Centrum zpětné vazby** a ujistěte se, že jste přihlášení.
1. Odeslat názor v rámci následující kategorizace:
   1. Kategorie: zabezpečení a ochrana osobních údajů
   1. Subcategory: FIDO
1. Pro zachycení protokolů použijte možnost: **znovu vytvořit můj problém**

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-this-work-in-my-on-premises-environment"></a>Funguje to v místním prostředí?

Tato funkce nefunguje pro čistě místní prostředí Active Directory Domain Services (služba AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Má moje organizace pro přístup k prostředkům dva faktory ověřování, co můžu udělat pro podporu tohoto požadavku?

Bezpečnostní klíče přicházejí v nejrůznějších faktorech formy. Obraťte se na výrobce zařízení, aby pomohly pojednávat, jak je možné jejich zařízení povolit pomocí PIN kódu nebo biometriky jako druhý faktor.

### <a name="can-admins-set-up-security-keys"></a>Můžou správci nastavit klíče zabezpečení?

Pracujeme na této funkci pro obecnou dostupnost této funkce (GA).

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Kde můžu přejít k hledání odpovídajících bezpečnostních klíčů?

[FIDO2 klíče zabezpečení](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Jak mám postupovat, když ztratím svůj bezpečnostní klíč?

Klíče můžete z Azure Portal odebrat tak, že přejdete na stránku bezpečnostní údaje a odeberete klíč zabezpečení.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nedaří se mi FIDO hned po vytvoření hybridního počítače připojeného k Azure AD

Pokud chcete vyčistit instalaci hybridního počítače připojeného k Azure AD, připojte se k doméně a restartujte ho, abyste se mohli přihlásit pomocí FIDO a počkat na synchronizaci zásad.

- Zadáním `dsregcmd /status` do příkazového okna ověřte aktuální stav a ověřte, zda `YES``AzureAdJoined` i `DomainJoined`.
- Toto zpoždění je známým omezením pro zařízení připojená k doméně a není specifické pro FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Po přihlášení pomocí FIDO se nepovedlo získat jednotné přihlašování k síťovému prostředku NTLM, aby se zobrazila výzva k zadání přihlašovacích údajů

Zajistěte, aby v čase byly k dispozici dostatek řadičů domény pro doručení vaší žádosti o prostředky. Pokud chcete zjistit, jestli se vám zobrazuje řadič domény, na kterém je spuštěná Tato funkce, podívejte se na výstup Nltest/dsgetdc: contoso/keylist/KDC.

## <a name="next-steps"></a>Další kroky

[Další informace o neheslech](concept-authentication-passwordless.md)
