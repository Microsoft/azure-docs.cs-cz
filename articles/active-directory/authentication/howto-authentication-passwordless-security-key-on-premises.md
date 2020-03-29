---
title: Přihlášení k místním prostředkům (preview) bez hesla – Azure Active Directory
description: Zjistěte, jak povolit přihlášení klíče zabezpečení bez hesla k místním prostředkům pomocí Služby Azure Active Directory (preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: conceptual
ms.date: 03/09/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: librown, aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5b29f84931c169ffe1c2c81d5e32201cbc63fc88
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78942876"
---
# <a name="enable-passwordless-security-key-sign-in-to-on-premises-resources-with-azure-active-directory-preview"></a>Povolení přihlašování pomocí Azure Active Directory (preview) bez hesla

Tento dokument se zaměřuje na povolení ověřování bez hesla do místních prostředků pro prostředí s **připojenou službou Azure AD** i **hybridní mandatní službou Azure AD připojenou k** zařízením s Windows 10. Tato funkce poskytuje bezproblémové jednotné přihlašování (SSO) k místním prostředkům pomocí klíčů zabezpečení kompatibilních s Microsoftem.

|     |
| --- |
| Klíče zabezpečení FIDO2 jsou veřejnou funkcí preview služby Azure Active Directory. Další informace o náhledech najdete v [tématu Doplňkové podmínky použití pro Microsoft Azure Previews.](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)|
|     |

## <a name="sso-to-on-premises-resources-using-fido2-keys"></a>Připráci a přimístním připojení k místním prostředkům pomocí klíčů FIDO2

Azure Active Directory (AD) může vydávat lístky Kerberos udělení lístků (TGTs) pro jednu nebo více domén služby Active Directory. Tato funkce umožňuje uživatelům přihlásit se k systému Windows pomocí moderních přihlašovacích údajů, jako jsou bezpečnostní klíče FIDO2, a získat přístup k tradičním prostředkům založeným na službě Active Directory. Lístky služeb Kerberos a autorizace jsou nadále řízeny místními řadiči domény služby Active Directory.

Objekt serveru Azure AD Kerberos se vytvoří ve vaší místní službě Active Directory a pak se bezpečně publikuje ve službě Azure Active Directory. Objekt není přidružen k žádným fyzickým serverům. Je to jednoduše prostředek, který může služba Azure Active Directory použít ke generování ttů protokolu Kerberos pro vaši doménu služby Active Directory.

![Získání lístku udělení lístku (TGT) z Azure AD a AD DS](./media/howto-authentication-passwordless-on-premises/fido2-ticket-granting-ticket-exchange-process.png)

1. Uživatel se přihlásí ke svému zařízení s Windows 10 pomocí zabezpečovacího klíče FIDO2 a ověří se ve službě Azure AD.
1. Azure AD zkontroluje adresář pro klíč serveru Kerberos odpovídající místní doméně služby AD uživatele.
   1. Azure AD generuje Kerberos TGT pro místní doménu služby AD uživatele. TGT zahrnuje pouze SID uživatele. V TGT nejsou zahrnuta žádná autorizační data.
1. TGT se vrátí klientovi spolu s jejich Token primární aktualizace Azure AD (PRT).
1. Klientský počítač kontaktuje místní řadič domény služby AD a obchoduje s částečným tgt pro plně vytvořené TGT.
1. Klientský počítač má teď Azure AD PRT a úplné TGT služby Active Directory a přístup ke cloudovým i místním prostředkům.

## <a name="requirements"></a>Požadavky

Organizace musí před dokončením kroků v tomto článku provést kroky k [povolení přihlašování bezpečnostního klíče bez hesla k zařízením s Windows 10 (preview).](howto-authentication-passwordless-security-key.md)

Organizace musí také splňovat následující požadavky na software.

- Zařízení musí mít Windows 10 Insider Build 18945 nebo novější.
- Musíte mít verzi 1.4.32.0 nebo novější [azure ad connect](../hybrid/how-to-connect-install-roadmap.md#install-azure-ad-connect).
  - Další informace o dostupných možnostech hybridního ověřování Azure AD najdete v [tématu Výběr správné metody ověřování pro řešení hybridní identity služby Azure Active Directory](../../security/fundamentals/choose-ad-authn.md) a [vyberte typ instalace, který se má použít pro Azure AD Connect](../hybrid/how-to-connect-install-select-installation.md).
- V řadičích domény systému Windows Server musí být nainstalovány následující opravy:
    - Pro Windows Server 2016 -https://support.microsoft.com/help/4534307/windows-10-update-kb4534307
    - Pro Windows Server 2019 -https://support.microsoft.com/help/4534321/windows-10-update-kb4534321

### <a name="supported-scenarios"></a>Podporované scénáře

Scénář podporuje jednotné přihlašování (SSO) v obou následujících scénářích:

- Pro cloudové prostředky, jako je Office 365 a další aplikace s podporou SAML.
- Pro místní prostředky a integrované ověřování windows na webových stránkách. Prostředky mohou zahrnovat weby a weby služby SharePoint, které vyžadují ověřování služby IIS, nebo prostředky, které používají ověřování NTLM.

### <a name="unsupported-scenarios"></a>Nepodporované scénáře

Následující scénáře nejsou podporovány:

- K nasazení doméně připojená k doméně služby AD DS (Windows Server Active Directory Domain Services).
- Scénáře RDP, VDI a Citrix pomocí klíče zabezpečení.
- S/MIME pomocí bezpečnostního klíče.
- "Spustit jako" pomocí bezpečnostního klíče.
- Přihlaste se k serveru pomocí bezpečnostního klíče.

## <a name="create-kerberos-server-object"></a>Vytvoření objektu serveru Kerberos

Správci používají nástroje Prostředí PowerShell ze svého serveru Azure AD Connect k vytvoření objektu serveru Azure AD Kerberos Server v místním adresáři. V každé doméně a doménové struktuře ve vaší organizaci, které obsahují uživatele Azure AD, spusťte následující kroky:

1. Upgradujte na nejnovější verzi Azure AD Connect. Pokyny předpokládají, že jste již nakonfigurovali Azure AD Connect pro podporu hybridního prostředí.
1. Na serveru Azure AD Connect Můžete otevřít výzvu powershellu se zvýšenými oprávněními a přejít na`C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Spusťte následující příkazy prostředí PowerShell a vytvořte nový objekt serveru Azure AD Kerberos v místní doméně Služby Active Directory i v tenantovi Azure Active Directory.

> [!NOTE]
> V `contoso.corp.com` následujícím příkladu nahraďte název domény služby Active Directory v místním prostředí.

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

Nově vytvořený server Azure AD Kerberos Server můžete zobrazit a ověřit pomocí následujícího příkazu:

```powerShell
Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

Tento příkaz vyvydešuje vlastnosti serveru Azure AD Kerberos. Můžete zkontrolovat vlastnosti a ověřit, zda je vše v pořádku.

| Vlastnost | Popis |
| --- | --- |
| ID | Jedinečné ID objektu AD DS DC. Toto ID se někdy označuje jako "slot" nebo "Branch ID". |
| DomainDnsName | Název domény DNS domény služby Active Directory. |
| Účet počítače | Objekt účtu počítače objektu Azure AD Kerberos Server (řadič domény). |
| Uživatelský účet | Zakázaný objekt uživatelského účtu, který obsahuje šifrovací klíč TGT serveru Azure AD Kerberos Server. DN tohoto účtu je`CN=krbtgt_AzureAD,CN=Users,<Domain-DN>` |
| Klíčová verze | Klíčová verze šifrovacího klíče TGT serveru Azure AD Kerberos. Verze je přiřazena při vytvoření klíče. Verze se pak zintálí při každém otočení klíče. Přírůstky jsou založeny na metadatech replikace a pravděpodobně větší než jedna. Například počáteční *KeyVersion* může být *192272*. Při prvním otočení klíče může verze postoupit na *212621*. Důležité ověřit je, že *KeyVersion* pro místní objekt a *CloudKeyVersion* pro objekt cloud jsou stejné. |
| KeyUpdatedOn | Datum a čas, kdy byl aktualizován nebo vytvořen šifrovací klíč TGT serveru Azure AD Kerberos Server. |
| KeyUpdatedFrom | Řadič domény, kde byl naposledy aktualizován šifrovací klíč TGT serveru Azure AD Kerberos Server. |
| CloudId | ID z objektu Azure AD. Musí odpovídat ID výše. |
| CloudDomainDnsName | *DomainDnsName* z objektu Azure AD. Musí odpovídat *DomainDnsName* výše. |
| CloudKeyVersion | *KeyVersion* z objektu Azure AD. Musí odpovídat *KeyVersion* výše. |
| CloudKeyUpdatedon | *KeyUpdatedOn* z objektu Azure AD. Musí odpovídat *KeyUpdatedOn* výše. |

### <a name="rotating-the-azure-ad-kerberos-server-key"></a>Otočení klíče serveru Kerberos azure ad

Azure AD Kerberos Server šifrování krbtgt klíče by měly být otočeny v pravidelných intervalech. Doporučujeme postupovat podle stejného plánu, který používáte k otočení všech ostatních klíčů řadiče domény služby Active Directory.

> [!WARNING]
> Existují další nástroje, které by mohly otáčet klávesy krbtgt, ale musíte použít nástroje uvedené v tomto dokumentu k otočení klíčů krbtgt serveru Azure AD Kerberos. Tím zajistíte, že klíče jsou aktualizovány v místním ad a Azure AD.

```powerShell
Set-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred -RotateServerKey
```

### <a name="removing-the-azure-ad-kerberos-server"></a>Odebrání serveru Kerberos služby Azure AD

Pokud chcete vrátit scénář a odebrat server Azure AD Kerberos z místní služby Active Directory i služby Azure Active Directory, spusťte následující příkaz:

```powerShell
Remove-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential $domainCred
```

### <a name="multi-forest-and-multi-domain-scenarios"></a>Scénáře s více doménovými strukturami a více doménami

Objekt serveru Azure AD Kerberos je reprezentován ve službě Azure AD jako objekt *KerberosDomain.* Každá místní doména služby Active Directory je reprezentována jako jeden objekt *KerberosDomain* ve službě Azure AD.

Vaše organizace má například doménovou strukturu služby Active Directory se dvěma doménami `contoso.com` a `fabrikam.com`. Pokud se rozhodnete povolit Azure AD vydávat Kerberos TgTs pro celou doménovou strukturu, existují dva *objekty KerberosDomain* ve službě Azure AD. Jeden objekt *KerberosDomain* pro `contoso.com` `fabrikam.com`a jeden pro . Pokud máte více doménových struktur služby Active Directory, existuje pro každou doménu v každé doménové struktuře jeden objekt *KerberosDomain.*

Je třeba spustit kroky k [vytvoření objektu serveru Kerberos](#create-kerberos-server-object) v každé doméně a doménové struktuře ve vaší organizaci, které obsahují uživatele Azure AD.

## <a name="known-behavior"></a>Známé chování

Přihlášení pomocí funkce FIDO je blokováno, pokud vypršela platnost hesla. Očekává se, že uživatel resetuje své heslo, než se bude moci přihlásit pomocí FIDO.

## <a name="troubleshooting-and-feedback"></a>Poradce při potížích a zpětná vazba

Pokud chcete při náhledu této funkce sdílet zpětnou vazbu nebo se s nimi setkat, sdílejte je prostřednictvím aplikace Windows Feedback Hub pomocí následujících kroků:

1. Spusťte **Centrum Feedback hub** a ujistěte se, že jste přihlášeni.
1. Odeslat zpětnou vazbu v rámci následující kategorizace:
   - Kategorie: Bezpečnost a ochrana osobních údajů
   - Podkategorie: FIDO
1. Chcete-li zachytit protokoly, použijte možnost **znovu vytvořit můj problém**

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="does-this-work-in-my-on-premises-environment"></a>Funguje to v místním prostředí?

Tato funkce nefunguje pro čisté místní prostředí služby Active Directory Domain Services (AD DS).

### <a name="my-organization-requires-two-factor-authentication-to-access-resources-what-can-i-do-to-support-this-requirement"></a>Moje organizace vyžaduje dvoufaktorové ověřování pro přístup k prostředkům. Co mohu udělat pro podporu tohoto požadavku?

Bezpečnostní klíče přicházejí v různých tvarových faktorech. Obraťte se na výrobce zařízení, který vás zajímá, a prodiskutujte, jak mohou být jejich zařízení povolena pomocí kódu PIN nebo biometrického kódu jako druhého faktoru.

### <a name="can-admins-set-up-security-keys"></a>Mohou správci nastavit bezpečnostní klíče?

Pracujeme na této funkci pro obecnou dostupnost (GA) této funkce.

### <a name="where-can-i-go-to-find-compliant-security-keys"></a>Kde najdu kompatibilní bezpečnostní klíče?

[Bezpečnostní klíče FIDO2](concept-authentication-passwordless.md#fido2-security-keys)

### <a name="what-do-i-do-if-i-lose-my-security-key"></a>Co mám dělat, když ztratím bezpečnostní klíč?

Klíče můžete odebrat z portálu Azure tak, že přejdete na stránku **Informace o zabezpečení** a odeberete klíč zabezpečení.

### <a name="im-not-able-to-use-fido-immediately-after-i-create-a-hybrid-azure-ad-joined-machine"></a>Nemohu použít FIDO ihned po vytvoření hybridního počítače se službou Azure AD

Pokud čisté instalaci hybridní hospodařilo počítač i pro Azure AD, po procesu připojení k doméně a restartování se musíte přihlásit pomocí hesla a čekat na zásady pro synchronizaci, než budete moci použít FIDO k přihlášení.

- Zkontrolujte svůj aktuální `dsregcmd /status` stav zadáním do příkazového okna a zkontrolujte, zda *azureadjoined* a *domainjoined jsou* zobrazeny *ANO*.
- Toto zpoždění je známé omezení pro zařízení připojená k doméně a není specifické pro FIDO.

### <a name="im-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-fido-and-get-a-credential-prompt"></a>Po přihlášení pomocí příkazu FIDO nelze získat službu SSO do síťového prostředku NTLM a získat výzvu k zadání pověření

Ujistěte se, že je opraveno dostatečné množství řadičů domény, které včas odpoví na obsluhu vašeho požadavku na prostředky. Chcete-li zkontrolovat, zda je spuštěn řadič domény, na `nltest /dsgetdc:contoso /keylist /kdc`který je tato funkce spuštěna, zkontrolujte výstup služby .

## <a name="next-steps"></a>Další kroky

[Další informace o bezhelnatí](concept-authentication-passwordless.md)
