---
title: Připojení klient služby Vzdálená plocha ve Windows Virtual Desktop – Azure
description: Jak řešit problémy při nastavování připojení klientů v prostředí virtuálního klienta Windows tenanta.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 99295fd4581cd81751f7d64b694c853efe51a106
ms.sourcegitcommit: bb85a238f7dbe1ef2b1acf1b6d368d2abdc89f10
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2019
ms.locfileid: "65522942"
---
# <a name="remote-desktop-client-connections"></a>Připojení klientů Vzdálené plochy

Pomocí tohoto článku řešení potíží s připojeními klientů virtuální plochy Windows.

## <a name="provide-feedback"></a>Poslat názor

Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview. Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) fattica virtuální plochy Windows service s produktovým týmem a aktivní komunitě členy.

## <a name="you-cant-open-a-web-client"></a>Nelze otevřít webového klienta

Potvrďte, že existuje připojení k Internetu je tak, že otevřete jiným webovým serverem; například [www.Bing.com](https://www.bing.com).

Použití **nslookup** potvrďte DNS můžete vyřešit plně kvalifikovaný název domény:

    ```cmd
    nslookup rdweb.wvd.microsoft.com
    ```

Zkuste se připojit pomocí jiného klienta, jako je klientem služby Vzdálená plocha pro Windows 7 nebo Windows 10 a zkontrolujte Pokud můžete otevřít webový klient.

### <a name="error-opening-another-site-fails"></a>Chyba: Otevření jiné lokality selže

**Příčina:** Problémy se sítí a/nebo výpadky.

**Oprava:** Obraťte se na podporu sítě.

### <a name="error-nslookup-cannot-resolve-the-name"></a>Chyba: Nslookup nemůže přeložit název

**Příčina:** Problémy se sítí a/nebo výpadky.

**Oprava:** Obraťte se na podporu sítě

### <a name="error-you-cant-connect-but-other-clients-can-connect"></a>Chyba: Nelze připojit, ale ostatní klienti můžou připojit

**Příčina:** Prohlížeč nepracuje jako pracovní očekávaným způsobem a je zastavené.

**Oprava:** Postupujte podle těchto pokynů k řešení potíží s prohlížeči.

1. Restartujte prohlížeč.
2. Soubory cookie v prohlížeči vymazat. Zobrazit [odstranění souborů cookie v Internet Exploreru](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
3. Prohlížeč vymazat mezipaměť. Zobrazit [vymazat mezipaměť prohlížeče pro váš prohlížeč](https://binged.it/2RKyfdU).
4. Otevřít prohlížeč v privátním režimu.

## <a name="web-client-stops-responding-or-disconnects"></a>Webový klient přestane reagovat nebo odpojení

Zkuste se připojit pomocí jiného prohlížeče nebo klienta.

### <a name="error-other-browsers-and-clients-also-malfunction-or-fail-to-open"></a>Chyba: Ostatní prohlížeče a klienti také nebude fungovat správně nebo nemusí otevřít

**Příčina:** Síť a/nebo operace systému potížím nebo výpadkům

**Oprava:** Obraťte se na podporu týmů.

## <a name="web-client-keeps-prompting-for-credentials"></a>Webový klient zachová vás vyzve k zadání přihlašovacích údajů

Pokud webový klient zachová vás vyzve k zadání přihlašovacích údajů, postupujte podle těchto pokynů.

1. Potvrďte správnost adresy URL webového klienta.
2. Potvrďte, že přihlašovací údaje jsou vázané na adresu URL prostředí virtuálního klienta Windows.
3. Soubory cookie v prohlížeči vymazat. Zobrazit [odstranění souborů cookie v Internet Exploreru](https://support.microsoft.com/help/278835/how-to-delete-cookie-files-in-internet-explorer).
4. Prohlížeč vymazat mezipaměť. Zobrazit [vymazat mezipaměť prohlížeče pro váš prohlížeč](https://binged.it/2RKyfdU).
5. Otevřít prohlížeč v privátním režimu.

## <a name="remote-desktop-client-for-windows-7-or-windows-10-stops-responding-or-cannot-be-opened"></a>Klient služby Vzdálená plocha pro Windows 7 nebo Windows 10 přestane reagovat nebo nelze otevřít

Pomocí následujících rutin Powershellu pro vyčištění out-of-band (OOB) klienta Registry.

```PowerShell
Remove-ItemProperty 'HKCU:\Software\Microsoft\Terminal Server Client\Default' - Name FeedURLs

#Remove RdClientRadc registry key
Remove-Item 'HKCU:\Software\Microsoft\RdClientRadc' -Recurse

#Remove all files under %appdata%\RdClientRadc
Remove-Item C:\Users\pavithir\AppData\Roaming\RdClientRadc\* -Recurse
```

Přejděte do **%AppData%\RdClientRadc** a odstranit veškerý obsah.

Odinstalujte a znovu nainstalujte klientem služby Vzdálená plocha pro Windows 7 a Windows 10.

## <a name="troubleshooting-end-user-connectivity"></a>Řešení potíží s připojením koncového uživatele

Někdy uživatelé mohou ke svým informační kanál a místní prostředky, ale ještě další konfigurace, dostupnosti nebo problémy s výkonem, které brání v přístupu k vzdálené prostředky. V těchto případech uživatel získá zprávy podobné těmto:

![Vzdálené ploše chybová zpráva.](media/eb76b666808bddb611448dfb621152ce.png)

![Nelze připojit k bráně chybová zpráva.](media/a8fbb9910d4672147335550affe58481.png)

Postupujte podle těchto obecné pokyny pro řešení potíží pro kódy chyb připojení klienta.

1. Zkontrolujte uživatelské jméno a čas, kdy došlo k problému.
2. Otevřít **Powershellu** a navázat připojení k tenantovi virtuální plochy Windows, kde problém byl nahlášen.
3. Ověřte připojení ke správné tenanta s **Get RdsTenant.**
4. Pomocí **Get-RdsHostPool** a **Get-RdsSessionHost** rutiny, potvrďte, že Poradce při potížích se provádí na správného hostitele fondu.
5. Spusťte následující příkaz k získání seznamu všech neúspěšných aktivit typu připojení pro zadané časové okno:

    ```cmd
     Get-RdsDiagnosticActivities -TenantName <TenantName> -username <UPN> -StartTime
     "11/21/2018 1:07:03 PM" -EndTime "11/21/2018 1:27:03 PM" -Outcome Failure -ActivityType Connection
    ```

6. Použití **ActivityId** z výstupu předchozí rutiny, spusťte následující příkaz:

    ```
    (Get-RdsDiagnosticActivities -TenantName $tenant -ActivityId <ActivityId> -Detailed).Errors
    ```

7. Tento příkaz vytváří výstup podobný výstup najdete níž. Použití **ErrorCodeSymbolic** a **ErrorMessage** řešení potíží s původní příčinu.

    ```
    ErrorSource       : <Source>
    ErrorOperation    : <Operation>
    ErrorCode         : <Error code>
    ErrorCodeSymbolic : <Error code string>
    ErrorMessage      : <Error code message>
    ErrorInternal     : <Internal for the OS>
    ReportedBy        : <Reported by component>
    Time              : <Timestampt>
    ```

### <a name="error-oaddusertogroupfailed--failed-to-add-user--username-to-group--remote-desktop-users-reason-win32errornosuchmember"></a>Chyba: O_ADD_USER_TO_GROUP_FAILED / uživatele se nepodařilo přidat = ≤username≥ do skupiny = Remote Desktop Users. Důvod: Win32.ERROR_NO_SUCH_MEMBER

**Příčina:** Virtuální počítač nebyl byl připojen k doméně, kde je objekt uživatele.

**Oprava:** Přidáte virtuální počítač ke správné doméně. Zobrazit [připojení virtuálního počítače s Windows serverem do spravované domény](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-admin-guide-join-windows-vm-portal).

### <a name="error-nslookup-cannot-resolve-the-name"></a>Chyba: Nslookup nemůže přeložit název

**Příčina:** Problémy se sítí nebo výpadky.

**Oprava:** Obraťte se na podporu sítě

### <a name="error-connectionfailedclientprotocolerror"></a>Chyba: ConnectionFailedClientProtocolError

**Příčina:** Virtuální počítače tohoto uživatele se pokouší o připojení k nejsou připojené k doméně.

**Oprava:** Připojte všechny virtuální počítače, které jsou součástí fondu hostitele k řadiči domény.

## <a name="user-connects-but-nothing-is-displayed-no-feed"></a>Připojení uživatele, ale nezobrazí se (žádný kanál)

Uživatele můžete začít klienty vzdálené plochy a je možné ověřit, ale nezobrazí uživateli všechny ikony v zjišťování webové informačního kanálu.

Potvrďte, že uživatel hlášení problémů byla přiřazena do skupiny aplikací s použitím tohoto příkazového řádku:

```cmd
Get-RdsAppGroupUser <tenantname> <hostpoolname> <appgroupname>
```

Potvrďte, že uživatel je přihlášení pomocí správných přihlašovacích údajů.

Pokud webového klienta se používá, zkontrolujte, zda zde nejsou žádné problémy přihlašovací údaje v mezipaměti.

## <a name="next-steps"></a>Další postup

- Přehled o řešení potíží virtuální plochy Windows a sleduje eskalace, naleznete v tématu [řešení potíží s přehled, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Řešení potíží při vytváření fondu tenanta a hostitele v prostředí virtuálního klienta Windows, naleznete v tématu [Tenanta a hostitele fondu vytváření](troubleshoot-set-up-issues.md).
- Řešení potíží při konfiguraci virtuálního počítače (VM) v virtuální plochy Windows, naleznete v tématu [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Při řešení problémů při použití Powershellu s virtuální plochy Windows, naleznete v tématu [Windows Powershellu virtuální plochy](troubleshoot-powershell.md).
- Další informace o služba ve verzi Preview, najdete v článku [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup?).
- Absolvovat kurz řešení potíží, najdete v článku [kurzu: Řešení potíží s nasazení šablon Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Další informace o auditování akcí najdete v tématu [Audit operací pomocí Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích, chcete-li zjistit chyby během nasazení najdete v tématu [zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).