---
title: Známé problémy a řešení potíží s klíči pro zabezpečení Hybrid FIDO2 – Azure Active Directory
description: Přečtěte si o některých známých problémech a způsobech řešení potíží s heslem hybridního FIDO2 zabezpečení klíčů pomocí Azure Active Directory (Preview).
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/19/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: aakapo
ms.collection: M365-identity-device-management
ms.openlocfilehash: ea034974e85febcffeb2494d87b666a39e524eb1
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/06/2020
ms.locfileid: "96743271"
---
# <a name="troubleshooting-for-hybrid-deployments-of-fido2-security-keys-in-azure-ad-preview"></a>Řešení potíží s hybridními nasazeními klíčů zabezpečení FIDO2 ve službě Azure AD (Preview)

Tento článek popisuje Nejčastější dotazy k hybridním zařízením připojeným k Azure AD a k přihlašování k prostředkům v Prem. Pomocí této funkce bez hesla můžete povolit ověřování Azure AD na zařízeních s Windows 10 pro zařízení připojená k hybridní službě Azure AD pomocí klíčů zabezpečení FIDO2. Uživatelé se můžou na svých zařízeních přihlašovat pomocí moderních přihlašovacích údajů, jako jsou FIDO2 klíče, a přistupovat k tradičním prostředkům založeném na Active Directory Domain Services (služba AD DS) s bezproblémové jednotné přihlašování (SSO) k Prem prostředkům.

Podporovány jsou následující scénáře pro uživatele v hybridním prostředí:

* Přihlaste se k hybridnímu zařízení připojenému k Azure AD pomocí klíčů zabezpečení FIDO2 a získejte přístup k přihlašování k prostředkům na Prem.
* Přihlaste se k zařízením připojeným k Azure AD pomocí klíčů zabezpečení FIDO2 a získejte přístup k přihlašování k prostředkům na Prem.

Pokud chcete začít s klíči zabezpečení FIDO2 a s hybridním přístupem k místním prostředkům, přečtěte si následující články:

* [Klíče zabezpečení s nezabezpečenými hesly](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hesla](howto-authentication-passwordless-security-key-windows.md)
* [Místní heslo](howto-authentication-passwordless-security-key-on-premises.md)

> [!NOTE]
> Bezpečnostní klíče FIDO2 jsou funkcí veřejné verze Preview Azure Active Directory. Další informace o verzi Preview najdete v tématu [doplňujících podmínek použití pro Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)verze Preview.

## <a name="known-issues"></a>Známé problémy

* [Uživatelé se nemůžou přihlašovat pomocí klíčů zabezpečení FIDO2, protože Windows Hello Face je moc rychlé a je výchozím mechanismem přihlášení.](#users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism)
* [Uživatelé nemůžou používat klíče zabezpečení FIDO2 hned po vytvoření hybridního počítače připojeného k Azure AD](#users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine)
* [Po přihlášení pomocí bezpečnostního klíče FIDO2 a přijetí výzvy k zadání přihlašovacích údajů uživatelé nemůžou získat jednotné přihlašování k síťovému prostředku NTLM.](#users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt)

### <a name="users-are-unable-to-sign-in-using-fido2-security-keys-as-windows-hello-face-is-too-quick-and-is-the-default-sign-in-mechanism"></a>Uživatelé se nemůžou přihlašovat pomocí klíčů zabezpečení FIDO2, protože Windows Hello Face je moc rychlé a je výchozím mechanismem přihlášení.

Windows Hello Face je zamýšlené nejlepší prostředí pro zařízení, ve kterém je uživatel zaregistrovaný. Klíče zabezpečení FIDO2 jsou určené pro použití na sdílených zařízeních nebo na tom, kde registrace Windows Hello pro firmy je bariéra.

Pokud Windows Hello Face brání uživatelům ve scénáři přihlášení k bezpečnostnímu klíči FIDO2, můžou uživatelé vypnout registraci obličeje tím, že v **nastavení > Sign-In možnosti**.

### <a name="users-arent-able-to-use-fido2-security-keys-immediately-after-they-create-a-hybrid-azure-ad-joined-machine"></a>Uživatelé nemůžou používat klíče zabezpečení FIDO2 hned po vytvoření hybridního počítače připojeného k Azure AD

Po dokončení procesu připojení k doméně a restartování při čisté instalaci hybridního počítače připojeného k Azure AD se musíte přihlásit pomocí hesla a počkat, než se zásada synchronizuje, abyste mohli použít FIDO2 bezpečnostní klíč k přihlášení.

Toto chování je známým omezením pro zařízení připojená k doméně a není specifické pro bezpečnostní klíče FIDO2.

Chcete-li zjistit aktuální stav, použijte `dsregcmd /status` příkaz. Ověřte, že *AzureAdJoined* i *DomainJoined* zobrazují *Ano*.

### <a name="users-are-unable-to-get-sso-to-my-ntlm-network-resource-after-signing-in-with-a-fido2-security-key-and-receiving-a-credential-prompt"></a>Po přihlášení pomocí bezpečnostního klíče FIDO2 a přijetí výzvy k zadání přihlašovacích údajů uživatelé nemůžou získat jednotné přihlašování k síťovému prostředku NTLM.

Ujistěte se, že je dostatek řadičů domény na reakci v čase na doručení vaší žádosti o prostředky. Pokud chcete zjistit, jestli se vám zobrazí server, na kterém je spuštěná funkce, Projděte si výstup `nltest /dsgetdc:<dc name> /keylist /kdc`

Pokud se vám s touto funkcí můžete podívat na řadič domény, může se stát, že se heslo uživatele od přihlášení změnilo nebo se stane jiný problém. Shromažďovat protokoly, jak je popsáno v následující části, aby tým podpory Microsoftu mohl ladit.

## <a name="troubleshoot"></a>Řešení potíží

Existují dvě oblasti pro řešení potíží s [klientem v okně](#windows-client-issues)nebo [problémy s nasazením](#deployment-issues).

### <a name="windows-client-issues"></a>Problémy s klientem Windows

Pokud chcete shromažďovat data, která pomáhají při řešení potíží s přihlašováním k Windows nebo při přístupu k místním prostředkům ze zařízení s Windows 10, proveďte následující kroky:

1. Otevřete aplikaci **centrum Feedback** . Ujistěte se, že je vaše jméno v levé dolní části aplikace, a pak vyberte **vytvořit novou položku zpětné vazby**.

    Jako typ položky zpětné vazby vyberte možnost *problém*.

1. Vyberte kategorii *zabezpečení a ochrana osobních údajů a* potom podkategorii *Fido* .
1. Přepněte zaškrtávací políčko pro *Odeslat připojené soubory a diagnostiku Microsoftu spolu se svou zpětnou vazbou*.
1. Vyberte znovu *vytvořit moje problémy* a pak *Spustit zachytávání*.
1. Zamkněte a odemkněte počítač s klíčem zabezpečení FIDO2. Pokud k problému dojde, zkuste se odemknout s ostatními přihlašovacími údaji.
1. Vraťte se do **centra pro zpětnou vazbu**, vyberte **Zastavit zachytávání** a odešlete svůj názor.
1. Přejít na stránku *Zpětná vazba* a pak na kartu *Moje zpětná vazba* . Vyberte svou nedávno odeslanou zpětnou vazbu.
1. Kliknutím na tlačítko *sdílet* v pravém horním rohu získáte odkaz na zpětnou vazbu. Tento odkaz zahrňte, když otevřete případ podpory nebo odpovíte na inženýra přiřazený existujícímu případu podpory.

Shromažďují se následující protokoly událostí a informace o klíči registru:

**Klíče registru**

* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\FIDO [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Policies\Microsoft\PasswordForWork\* [ \* ]*
* *HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Policies\PasswordForWork\* [ \* ]*

**Diagnostické informace**

* Živý výpis stavu jádra
* Shromáždit informace o balíčku AppX
* Soubory kontextu UIF

**Protokoly událostí**

* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-AAD%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-WebAuthN%40Operational.evtx*
* *%SystemRoot%\System32\winevt\Logs\Microsoft-Windows-HelloForBusiness%40Operational.evtx*

### <a name="deployment-issues"></a>Problémy s nasazením

Pokud chcete řešit problémy s nasazením serveru Kerberos Azure AD, použijte nový modul PowerShellu, který je součástí nástroje Azure AD Connect.

#### <a name="viewing-the-logs"></a>Zobrazení protokolů

Rutiny PowerShellu serveru Azure AD používají stejné protokolování jako standardní Průvodce Azure AD Connect. Chcete-li zobrazit informace o informacích a podrobnosti o chybách z rutin, proveďte následující kroky:

1. Na serveru Azure AD Connect přejděte na adresu `C:\ProgramData\AADConnect\` . Tato složka je ve výchozím nastavení skrytá.
1. Otevřete a zobrazte nejnovější `trace-*.log` soubor umístěný v adresáři.

#### <a name="viewing-the-azure-ad-kerberos-server-objects"></a>Zobrazení objektů serveru Kerberos Azure AD

Pokud chcete zobrazit objekty serveru Azure AD Kerberos a ověřit, jestli jsou v dobrém pořadí, proveďte následující kroky:

1. Na serveru Azure AD Connect otevřete PowerShell a přejděte na `C:\Program Files\Microsoft Azure Active Directory Connect\AzureADKerberos\`
1. Spusťte následující příkazy PowerShellu, abyste zobrazili Server Azure AD Kerberos z Azure AD i z místního služba AD DS.

    Nahraďte *Corp.contoso.com* názvem vaší místní domény služba AD DS.

    ```powershell
    Import-Module ".\AzureAdKerberos.psd1"

    # Specify the on-premises AD DS domain.
    $domain = "corp.contoso.com"

    # Enter an Azure Active Directory Global Admin username and password.
    $cloudCred = Get-Credential

    # Enter a Domain Admin username and password.
    $domainCred = Get-Credential

    # Get the Azure AD Kerberos Server Object
    Get-AzureADKerberosServer -Domain $domain -CloudCredential $cloudCred -DomainCredential
    $domainCred
    ```

Příkaz vypíše vlastnosti serveru Azure AD Kerberos z Azure AD i z místního služba AD DS. Zkontrolujte vlastnosti a ověřte, zda je vše v dobrém pořadí. Vlastnosti ověřte pomocí následující tabulky.

První sada vlastností je z objektů v místním prostředí služba AD DS. Druhá polovina (vlastnosti, které začínají na * Cloud * *), je z objektu serveru Kerberos v Azure AD:

| Vlastnost           | Popis  |
|--------------------|--------------|
| Id                 | Jedinečné *ID* objektu služba AD DS řadiče domény. |
| DomainDnsName      | Název domény DNS služba AD DS domény |
| ComputerAccount    | Objekt účtu počítače objektu serveru Azure AD Kerberos (řadič domény). |
| UserAccount        | Zakázaný objekt uživatelského účtu, který obsahuje šifrovací klíč TGT serveru Azure AD Kerberos. Rozlišující název tohoto účtu je *CN = krbtgt_AzureAD, CN = Users, <Domain-DN>* |
| Verze         | Klíčová verze šifrovacího klíče TGT serveru Azure AD Kerberos. Verze je přiřazena při vytvoření klíče. Verze se pak zvýší pokaždé, když se klíč otočí. Přírůstky jsou založené na metadatech replikace a pravděpodobně budou větší než jedna.<br /><br /> Například počáteční *verze* může být *192272*. Při prvním otočení klíče může verze přejít na *212621*.<br /><br /> Důležitou věcí, kterou je třeba ověřit, je, že *verze* *CloudKeyVersion* pro místní objekt a objekt pro cloudový objekt jsou stejné. |
| KeyUpdatedOn       | Datum a čas, kdy se šifrovací klíč TGT serveru Azure AD Kerberos aktualizoval nebo vytvořil |
| KeyUpdatedFrom     | Řadič domény, kde se naposledy aktualizoval šifrovací klíč TGT serveru Azure AD Kerberos |
| CloudId            | *ID* z objektu Azure AD. Musí odpovídat *ID* výše. |
| CloudDomainDnsName | *DomainDnsName* z objektu Azure AD. Musí odpovídat *DomainDnsName* výše. |
| CloudKeyVersion    | *Verze* modulu z objektu Azure AD. Musí odpovídat *verzi* výše uvedeného. |
| CloudKeyUpdatedOn  | *KeyUpdatedOn* z objektu Azure AD. Musí odpovídat *KeyUpdatedOn* výše. |

## <a name="next-steps"></a>Další kroky

Pokud chcete začít s klíči zabezpečení FIDO2 a s hybridním přístupem k místním prostředkům, přečtěte si následující články:

* [Klíče zabezpečení FIDO2 bez hesla](howto-authentication-passwordless-security-key.md)
* [Windows 10 bez hesla](howto-authentication-passwordless-security-key-windows.md)
* [Místní prostředí bez hesla](howto-authentication-passwordless-security-key-on-premises.md)
