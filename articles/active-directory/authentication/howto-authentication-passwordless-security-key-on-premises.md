---
title: Klíč zabezpečení s nezabezpečenými hesly k místním prostředkům – Azure Active Directory
description: Naučte se, jak povolit klíč zabezpečení nezaloženého na heslech k místním prostředkům pomocí Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 02/22/2021
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: db1b559bb4f6a1f8866116c287df5b814500210b
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101647468"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory"></a>Povolení nezabezpečeného přihlášení k místním prostředkům pomocí klíče zabezpečení bez hesla Azure Active Directory 

Tento dokument se zaměřuje na povolení ověřování bez hesla u místních prostředků pro prostředí, která jsou **připojená k Azure AD** i pro zařízení s Windows 10 **připojená k Azure AD** . Tato funkce poskytuje bezproblémové jednotné přihlašování (SSO) k místním prostředkům pomocí bezpečnostních klíčů kompatibilních s Microsoftem.

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Jednotné přihlašování k místním prostředkům pomocí klíčů FIDO2

Azure Active Directory (AD) může vydávat lístky TGT (TGT) lístku protokolu Kerberos pro jednu nebo více domén služby Active Directory. Tato funkce umožňuje uživatelům přihlašovat se k Windows pomocí moderních přihlašovacích údajů, jako jsou bezpečnostní klíče FIDO2, a přistupovat k tradičním prostředkům založeným na službě Active Directory. Lístky služby Kerberos a autorizace se nadále řídí místními řadiči domény služby Active Directory.

Objekt serveru Azure AD Kerberos se vytvoří v místní službě Active Directory a potom se bezpečně publikuje do Azure Active Directory. Objekt není spojen s žádnými fyzickými servery. Je to prostě prostředek, který může Azure Active Directory použít k vygenerování TGT protokolu Kerberos pro Doména služby Active Directory.

![Získání lístku TGT pro udělování lístků (TGT) z Azure AD a služba AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Uživatel se přihlásí k zařízení s Windows 10 pomocí bezpečnostního klíče FIDO2 a provede ověření ve službě Azure AD.
1. Azure AD kontroluje adresář pro klíč serveru protokolu Kerberos, který odpovídá místní doméně AD uživatele.
   1. Azure AD vygeneruje lístek lístku Kerberos pro místní doménu AD uživatele. Lístek TGT obsahuje jenom SID uživatele. V lístku TGT nejsou obsažena žádná autorizační data.
1. Lístek TGT se vrátí klientovi spolu s primárním tokenem pro obnovení služby Azure AD (PRT).
1. Klientský počítač kontaktuje místní řadič domény služby AD a postará se o úplný lístek TGT na plně vytvořený lístek TGT.
1. Klientský počítač má teď Azure AD PRT a úplný lístek TGT služby Active Directory a má přístup k cloudovým i místním prostředkům.

## <a name="requirements"></a>Požadavky

Před dokončením kroků v tomto článku musí organizace provést postup, aby na [zařízeních s Windows 10 nemuseli přihlašovat bezpečnostní klíč s heslem](howto-authentication-passwordless-security-key.md) .

Organizace musí splňovat i tyto požadavky na software.

- Na zařízeních musí běžet Windows 10 verze 2004 nebo novější.
- Musíte mít verzi 1.4.32.0 nebo [Azure AD Connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect)novější.
  - Další informace o dostupných možnostech hybridního ověřování Azure AD najdete v tématu [Volba správné metody ověřování pro Azure Active Directory řešení hybridní identity](../hybrid/choose-ad-authn.md) a [Výběr typu instalace, který se má použít pro Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- Na řadičích domény se systémem Windows Server musí být nainstalovány následující opravy:
    - Pro Windows Server 2016 – https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Pro Windows Server 2019 – https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Podporované scénáře

Scénář podporuje jednotné přihlašování (SSO) v obou těchto scénářích:

- Pro cloudové prostředky, jako jsou Microsoft 365 a další aplikace podporující SAML.
- Pro místní prostředky a Windows-Integrated ověřování pro weby. Prostředky můžou zahrnovat weby a weby SharePointu, které vyžadují ověřování IIS, a prostředky, které používají ověřování NTLM.

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře nejsou podporovány:

- Nasazení Windows serveru Active Directory Domain Services (služba AD DS) připojené k doméně (jenom místní zařízení).
- Scénáře RDP, VDI a Citrix využívají bezpečnostní klíč.
- S/MIME používá bezpečnostní klíč.
- "Spustit jako" pomocí bezpečnostního klíče.
- Přihlaste se k serveru pomocí klíče zabezpečení.

## <a name="create-kerberos-server-object"></a>Vytvoření objektu serveru Kerberos

Správci pomocí nástrojů PowerShellu ze svého Azure AD Connect serveru vytvoří objekt serveru Azure AD Kerberos ve svém místním adresáři. V každé doméně a doménové struktuře ve vaší organizaci, která obsahuje uživatele Azure AD, spusťte následující kroky:

1. Upgradujte na nejnovější verzi Azure AD Connect. Tyto pokyny předpokládají, že jste už Azure AD Connect nakonfigurovali pro podporu hybridního prostředí.
1. Na serveru Azure AD Connect otevřete příkazový řádek prostředí PowerShell se zvýšenými oprávněními a přejděte do `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Spusťte následující příkazy PowerShellu pro vytvoření nového objektu serveru Azure AD Kerberos v místní doméně služby Active Directory i v tenantovi Azure Active Directory.

> [!NOTE]
> `contoso.corp.com`V následujícím příkladu nahraďte názvem místní domény služby Active Directory.

```powerShell
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

Nově vytvořený server Azure AD Kerberos můžete zobrazit a ověřit pomocí následujícího příkazu:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Tento příkaz vypíše vlastnosti serveru Azure AD Kerberos. Můžete zkontrolovat vlastnosti a ověřit, zda je vše v dobrém pořadí.

| Vlastnost | Popis |
| --- | --- |
| ID | Jedinečné ID objektu služba AD DSho řadiče domény. Toto ID se někdy označuje jako "slot" nebo je "ID větve". |
| DomainDnsName | Název domény DNS Doména služby Active Directory. |
| ComputerAccount | Objekt účtu počítače objektu serveru Azure AD Kerberos (řadič domény). |
| UserAccount | Zakázaný objekt uživatelského účtu, který obsahuje šifrovací klíč TGT serveru Azure AD Kerberos. Rozlišující název tohoto účtu je `CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Verze | Klíčová verze šifrovacího klíče TGT serveru Azure AD Kerberos. Verze je přiřazena při vytvoření klíče. Verze se pak zvýší pokaždé, když se klíč otočí. Přírůstky jsou založené na metadatech replikace a jsou nejspíš větší než jedna. Například počáteční *verze* může být *192272*. Při prvním otočení klíče může verze přejít na *212621*. Důležitou věcí, kterou je třeba ověřit, je, že *verze* *CloudKeyVersion* pro místní objekt a objekt pro cloudový objekt jsou stejné. |
| KeyUpdatedOn | Datum a čas, kdy se šifrovací klíč TGT serveru Azure AD Kerberos aktualizoval nebo vytvořil |
| KeyUpdatedFrom | Řadič domény, kde se naposledy aktualizoval šifrovací klíč TGT serveru Azure AD Kerberos |
| CloudId | ID z objektu Azure AD. Musí odpovídat ID výše. |
| CloudDomainDnsName | *DomainDnsName* z objektu Azure AD. Musí odpovídat *DomainDnsName* výše. |
| CloudKeyVersion | *Verze* modulu z objektu Azure AD. Musí odpovídat *verzi* výše uvedeného. |
| CloudKeyUpdatedOn | *KeyUpdatedOn* z objektu Azure AD. Musí odpovídat *KeyUpdatedOn* výše. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Rotace klíče serveru Azure AD Kerberos

Šifrovací klíče serveru Azure AD Kerberos by se měly pravidelně střídat KRBTGT. Doporučuje se postupovat podle stejného plánu, který použijete k otočení všech ostatních KRBTGT klíčů kontroleru Doména služby Active Directory.

> [!WARNING]
> K dispozici jsou i další nástroje, které by mohly otáčet KRBTGT klíče, ale k otočení KRBTGT klíčů serveru Kerberos služby Azure AD je nutné použít nástroje uvedené v tomto dokumentu. Tím se zajistí, že se klíče aktualizují v místní službě AD i v Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Odebírá se server Azure AD Kerberos.

Pokud byste chtěli vrátit scénář a odebrat server Azure AD Kerberos z místní služby Active Directory a Azure Active Directory, spusťte následující příkaz:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scénáře s více doménovými strukturami a více doménami

Objekt serveru Azure AD Kerberos je reprezentován ve službě Azure AD jako objekt *KerberosDomain* . Každá místní doména služby Active Directory je reprezentována jako jeden objekt *KerberosDomain* ve službě Azure AD.

Vaše organizace má například doménovou strukturu služby Active Directory se dvěma doménami `contoso.com` a `fabrikam.com` . Pokud se rozhodnete, že Azure AD povolí vystavení TGT protokolu Kerberos pro celou doménovou strukturu, ve službě Azure AD jsou dva objekty *KerberosDomain* . Jeden objekt *KerberosDomain* pro `contoso.com` a jeden pro `fabrikam.com` . Pokud máte více doménových struktur služby Active Directory, je pro každou doménu v každé doménové struktuře jeden objekt *KerberosDomain* .

Musíte spustit kroky pro [vytvoření objektu serveru Kerberos](#create-kerberos-server-object) v každé doméně a doménové struktuře ve vaší organizaci, která obsahuje uživatele Azure AD.

## <a name="known-behavior"></a>Známé chování

Přihlášení pomocí FIDO se zablokuje, pokud vypršela platnost hesla. Očekává se, že uživatel resetuje heslo předtím, než se bude moct přihlásit pomocí FIDO.

## <a name="troubleshooting-and-feedback"></a>Řešení potíží a zpětná vazba

Pokud chcete sdílet zpětnou vazbu nebo narazit na problémy s touto funkcí, sdílejte ji přes aplikaci Windows Feedback Center pomocí následujících kroků:

1. Spusťte **Centrum zpětné vazby** a ujistěte se, že jste přihlášení.
1. Odeslat názor v rámci následující kategorizace:
   - Kategorie: zabezpečení a ochrana osobních údajů
   - Subcategory: FIDO
1. Chcete-li zachytit protokoly, použijte možnost k **opětovnému vytvoření problému**.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-this-work-in-my-on-premises-environment"></a>Funguje to v místním prostředí?

Tato funkce nefunguje pro čistě místní prostředí Active Directory Domain Services (služba AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moje organizace vyžaduje pro přístup k prostředkům dva faktory ověřování. Co můžu na podporu tohoto požadavku udělat?

Bezpečnostní klíče přicházejí v nejrůznějších faktorech formy. Obraťte se na výrobce zařízení, aby pomohly diskutovat, jak je možné jejich zařízení povolit pomocí PIN kódu nebo biometriky jako druhý faktor.

### <a name="can-admins-set-up-security-keys"></a>Můžou správci nastavit klíče zabezpečení?

Pracujeme na této funkci pro obecnou dostupnost této funkce (GA).

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Kde můžu přejít k hledání odpovídajících bezpečnostních klíčů?

[FIDO2 klíče zabezpečení](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Jak mám postupovat, když ztratím svůj bezpečnostní klíč?

Klíče z Azure Portal můžete odebrat tak, že přejdete na stránku **informace o zabezpečení** a odeberete klíč zabezpečení.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nedaří se mi FIDO hned po vytvoření hybridního počítače připojeného k Azure AD

Pokud chcete vyčistit počítač připojený k hybridní službě Azure AD po připojení k doméně a restartování, musíte se přihlásit heslem a počkat, než se zásada synchronizuje, aby se mohl použít FIDO k přihlášení.

- Zadáním `dsregcmd /status` příkazu do příkazového okna a kontrolou, že *AzureAdJoined* i *DomainJoined* zobrazují *hodnotu Ano*, Projděte si aktuální stav.
- Toto zpoždění je známým omezením pro zařízení připojená k doméně a není specifické pro FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Po přihlášení pomocí FIDO se nepovedlo získat jednotné přihlašování k síťovému prostředku NTLM, aby se zobrazila výzva k zadání přihlašovacích údajů

Zajistěte, aby v čase byly k dispozici dostatek řadičů domény pro doručení vaší žádosti o prostředky. Pokud chcete zjistit, jestli se zobrazuje řadič domény, na kterém je spuštěná funkce, přečtěte si výstup `nltest /dsgetdc:contoso /keylist /kdc` .

## <a name="next-steps"></a>Další kroky

[Další informace o neheslech](concept-authentication-passwordless.md)
