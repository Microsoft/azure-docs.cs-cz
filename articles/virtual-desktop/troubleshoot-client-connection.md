---
title: Řešení potíží s virtuálním počítačem s Windows vzdálené plochy – Azure
description: Řešení problémů při nastavování připojení klienta v prostředí klienta virtuální plochy Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 04/08/2019
ms.author: helohr
ms.openlocfilehash: 9fcc65768db3029461a5823034336bc883379292
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/20/2019
ms.locfileid: "74227686"
---
# <a name="remote-desktop-client-connections"></a>Připojení klientů Vzdálené plochy

Pomocí tohoto článku můžete vyřešit problémy s připojením klienta k virtuální ploše Windows.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="you-cant-open-a-web-client"></a>Nemůžete otevřít webového klienta.

Potvrďte připojení k Internetu tak, že otevřete jiný web. například [www.Bing.com](https://www.bing.com).

Pomocí nástroje **nslookup** potvrďte, že DNS může přeložit plně kvalifikovaný název domény:

```cmd
nslookup rdweb.wvd.microsoft.com
```

Zkuste se připojit pomocí jiného klienta, jako je klient vzdálené plochy pro Windows 7 nebo Windows 10, a zkontrolujte, jestli můžete otevřít webového klienta.

### <a name="error-opening-another-site-fails"></a>Chyba: otevření jiného webového serveru se nezdařilo

**Příčina:** Problémy se sítí nebo výpadky.

**Oprava:** Kontaktujte podporu sítě.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Chyba: Nástroj Nslookup nemůže přeložit název.

**Příčina:** Problémy se sítí nebo výpadky.

**Oprava:** Kontaktovat podporu sítě

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Chyba: nemůžete se připojit, ale ostatní klienti se mohou připojit.

**Příčina:** Prohlížeč se nechová podle očekávání a přestal fungovat.

**Oprava:** Při řešení potíží s prohlížečem postupujte podle těchto pokynů.

1. Restartujte prohlížeč.
2. Vymazat soubory cookie prohlížeče. Informace najdete [v tématu Odstranění souborů cookie v aplikaci Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Smažte mezipaměť prohlížeče. Viz [Vymazat mezipaměť prohlížeče pro prohlížeč](https://binged.it/2RKyfdU).
4. Otevřete prohlížeč v privátním režimu.

## <a name="web-client-stops-responding-or-disconnects"></a>Webový klient přestane reagovat nebo odpojení.

Zkuste se připojit pomocí jiného prohlížeče nebo klienta.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Chyba: jiné prohlížeče a klienti jsou také nefunkční nebo se nedaří otevřít.

**Příčina:** Problémy se sítí nebo operačním systémem nebo výpadky

**Oprava:** Obraťte se na tým podpory.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webový klient uchovává výzvy k zadání přihlašovacích údajů

Pokud webový klient zachovává výzvy k zadání přihlašovacích údajů, postupujte podle těchto pokynů.

1. Ověřte správnost adresy URL webového klienta.
2. Potvrďte, že přihlašovací údaje jsou pro prostředí virtuálních počítačů s Windows svázané s adresou URL.
3. Vymazat soubory cookie prohlížeče. Informace najdete [v tématu Odstranění souborů cookie v aplikaci Internet Explorer](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Smažte mezipaměť prohlížeče. Viz [Vymazat mezipaměť prohlížeče pro prohlížeč](https://binged.it/2RKyfdU).
5. Otevřete prohlížeč v privátním režimu.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient služby Vzdálená plocha pro Windows 7 nebo Windows 10 přestane reagovat nebo ho nejde otevřít.

K vyčištění registrů klientů OOB (out-of-band) použijte následující rutiny PowerShellu.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Přejděte na **%AppData%\RdClientRadc** a odstraňte veškerý obsah.

Odinstalujte a znovu nainstalujte klienta vzdálené plochy pro Windows 7 a Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Řešení potíží s připojením koncových uživatelů

Někdy uživatelé mají přístup ke svým informačním kanálům a místním prostředkům, ale stále mají problémy s konfigurací, dostupností nebo výkonem, které jim brání v přístupu ke vzdáleným prostředkům. V těchto případech uživatel získá zprávy podobné těmto:

![Připojení ke vzdálené ploše chybová zpráva.](media/eb76b666808bddb611448dfb621152ce.png)

![Nelze se připojit k chybové zprávě brány.](media/a8fbb9910d4672147335550affe58481.png)

Použijte tyto obecné pokyny pro odstraňování potíží s kódy chyb připojení klientů.

1. Potvrďte uživatelské jméno a čas, kdy došlo k problému.
2. Otevřete **PowerShell** a navažte připojení k Tenantovi virtuální plochy Windows, ve kterém se nahlásil problém.
3. Potvrďte připojení ke správnému tenantovi pomocí **Get-RdsTenant.**
4. Pomocí rutin **Get-RdsHostPool** a **Get-RdsSessionHost** potvrďte, že řešení potíží se provádí ve správném fondu hostitelů.
5. Spuštěním následujícího příkazu zobrazíte seznam všech neúspěšných aktivit typu připojení pro určený časový interval:

    ```PowerShell
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Pomocí **ActivityId** z předchozího výstupu rutiny spusťte následující příkaz:

    ```PowerShell
    (Get-RdsDiagnosticActivities -TenantName <TenantName> -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Příkaz vytvoří výstup podobný výstupu uvedenému níže. K odstranění hlavní příčiny použijte **ErrorCodeSymbolic** a **ErrorMessage** .

    ```PowerShell
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-o_add_user_to_group_failed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32error_no_such_member"></a>Chyba: O_ADD_USER_TO_GROUP_FAILED/nepovedlo se přidat uživatele = ≤ uživatelské jméno ≥ do skupiny = Uživatelé vzdálené plochy. Důvod: Win32. ERROR_NO_SUCH_MEMBER

**Příčina:** Virtuální počítač není připojený k doméně, ve které je objekt uživatele.

**Oprava:** Přidejte virtuální počítač do správné domény. Viz [připojení virtuálního počítače s Windows serverem ke spravované doméně](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Chyba: Nástroj Nslookup nemůže přeložit název.

**Příčina:** Problémy se sítí nebo výpadky.

**Oprava:** Kontaktovat podporu sítě

### <a name="error-connectionfailedclientprotocolerror"></a>Chyba: ConnectionFailedClientProtocolError

**Příčina:** Virtuální počítače, ke kterým se uživatel pokouší připojit, nejsou připojeni k doméně.

**Oprava:** Připojte všechny virtuální počítače, které jsou součástí fondu hostitelů, do řadiče domény.

### <a name="error-connectionfailedusersidinformationmismatch"></a>Chyba: ConnectionFailedUserSIDInformationMismatch
**Příčina:** Identifikátor SID z tokenu Azure Active Directory uživatele (AD) se neshoduje s identifikátorem SID vráceným řadičem domény při pokusu o povolení uživatele pro vzdálené přihlášení. K této chybě obvykle dochází při pokusu o přihlášení k prostředí Azure Active Directory Domain Servicesu (Azure služba AD DS) s uživatelem původně vytvořeným z Windows Server AD.

**Oprava:** Tento scénář se v tuto chvíli nepodporuje. K virtuálním počítačům virtuálních počítačů s Windows připojeným k Azure služba AD DS se můžou přihlásit jenom uživatelé, kteří se nahlásili z Azure Active Directory.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Uživatel se připojuje, ale nic se nezobrazuje (bez kanálu).

Uživatel může spustit klienty vzdálené plochy a je schopen ho ověřit, ale uživatel nevidí žádné ikony v informačním kanálu webového zjišťování.

Pomocí tohoto příkazového řádku zkontrolujte, jestli se uživateli, který nahlásí problémy, přiřadí ke skupinám aplikací:

```PowerShell
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Potvrďte, že se uživatel přihlašuje se správnými přihlašovacími údaji.

Pokud je webový klient používán, zkontrolujte, zda nejsou k dispozici žádné problémy s přihlašovacími údaji v mezipaměti.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
