---
title: Vytvoření fondu tenanta a hostitele v virtuální plochy Windows – Azure
description: Jak řešit problémy při konfiguraci klienta a relace hostitele virtuálního počítače (VM) v prostředí virtuálního klienta Windows.
services: virtual-desktop
author: ChJenk
ms.service: virtual-desktop
ms.topic: troubleshoot
ms.date: 04/08/2019
ms.author: v-chjenk
ms.openlocfilehash: 38d59fb20776470cb683f2a2146838bb217addf7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "64928119"
---
# <a name="tenant-and-host-pool-creation"></a>Vytvoření tenanta a fondu hostitelů

Řešení potíží s problémy, se kterými máte potíže při konfiguraci virtuální plochy Windows relace hostování virtuálních počítačů (VM) pomocí tohoto článku.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Můžeme aktuálně nejsou trvá případy podpory virtuální plochy Windows je ve verzi preview. Přejděte [technické komunitě virtuální plochy Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) fattica virtuální plochy Windows service s produktovým týmem a aktivní komunitě členy.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuální počítače nejsou připojené k doméně

Pokud máte potíže s připojení virtuálních počítačů k doméně, postupujte podle těchto pokynů.

- Připojení virtuálního počítače ručně pomocí procesu v [připojení virtuálního počítače s Windows serverem do spravované domény](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) nebo pomocí [šablony připojení k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Název domény z příkazového řádku na virtuálním počítači příkazu ping.
- Projděte si seznam chybové zprávy připojení k doméně v [řešení potíží s chybové zprávy připojení k doméně](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Chyba: Nesprávné přihlašovací údaje

**Příčina:** Byl proveden, jakmile přihlašovací údaje byly zadány v Azure Resource Manageru, řeší šablony rozhraní překlep.

**Oprava:** Postupujte podle těchto pokynů a opravte pověření.

1. Ručně přidejte virtuální počítače k doméně.
2. Znovu nasaďte, jakmile přihlašovací údaje byly potvrzeny. Zobrazit [vytvořit fond hostitele s prostředím PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Připojení virtuálních počítačů k doméně pomocí šablony s [existujícího virtuálního počítače Windows připojí k doméně AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Chyba: Vypršel časový limit čekání na vstup uživatele

**Příčina:** Účet používaný k dokončení připojení k doméně, může mít ověřování službou Multi-Factor Authentication (MFA).

**Oprava:** Postupujte podle těchto pokynů k dokončení připojení k doméně.

1. Dočasně odebere vícefaktorové ověřování pro účet.
2. Použijte účet služby.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Chyba: Účet použitý během zřizování nemá oprávnění k dokončení operace

**Příčina:** Použitý účet nemá oprávnění k připojení virtuálních počítačů k doméně kvůli dodržování předpisů a nařízení.

**Oprava:** Postupujte podle těchto pokynů.

1. Použijte účet, který je členem skupiny správců.
2. Udělte potřebná oprávnění k účtu se používají.

### <a name="error-domain-name-doesnt-resolve"></a>Chyba: Název domény se nevyřeší.

**1. příčina:** Virtuální počítače jsou ve skupině prostředků, který není přidružený k virtuální síti (VNET), kde se nachází doméně.

**Oprava 1:** Vytvořte Partnerský vztah mezi virtuální sítí, ve kterém byly virtuální počítače zřizovat a virtuální síť, ve kterém je spuštěný řadič domény (DC). Zobrazit [vytvoření partnerského vztahu virtuálních sítí - Resource Manageru, různá předplatná](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions).

**2. příčina:** Při použití AadService (AADS), nebyly nastaveny položky DNS.

**Oprava 2:** Nastavení služby domain services, najdete v článku [povolit Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agent virtuální plochy Windows a Windows virtuální plochy spouštěcí zavaděč nejsou nainstalovány.

Je doporučeným způsobem, jak zřídit virtuální počítače pomocí Azure Resource Manageru **vytvoření a zřízení virtuálního klienta Windows hostování fondu** šablony. Šablona automaticky nainstaluje agenta virtuální plochy Windows a Windows virtuální plochy agenta spouštěcí zavaděč.

Postupujte podle těchto pokynů, abyste potvrdili, že jsou nainstalované komponenty a zkontrolovat chybové zprávy.

1. Zkontrolujte, zda jsou dvě součásti nainstalovány ve vrácení se změnami **ovládací panely** > **programy** > **programy a funkce**. Pokud **virtuální plochy agenta Windows** a **Windows virtuální plochy agenta spouštěcí zavaděč** nejsou viditelné, nejsou nainstalovány na virtuálním počítači.
2. Otevřít **Průzkumníka souborů** a přejděte do **C:\Windows\Temp\scriptlogs.log**. Pokud soubor chybí, znamená, že prostředí PowerShell DSC, nainstalované dvě součásti se nepodařilo spustit v kontextu zabezpečení, které jsou k dispozici.
3. Pokud soubor **C:\Windows\Temp\scriptlogs.log** je k dispozici, otevřete ho a zkontrolovat chybové zprávy.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Chyba: Agent virtuální plochy Windows a Windows virtuální plochy agenta spouštěcí zavaděč nebyly nalezeny. C:\Windows\Temp\scriptlogs.log chybí také

**1. příčina:** Přihlašovací údaje zadané pro šablonu Azure Resource Manageru při zadávání byly nesprávný nebo byla dostatečná oprávnění.

**Oprava 1:** Ručně přidejte chybějící součásti k virtuálním počítačům pomocí [vytvořit fond hostitele s prostředím PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**2. příčina:** PowerShell DSC bylo možné spustit a spustit, ale se nepodařilo dokončit, protože nebylo možné Přihlaste se k virtuálnímu klientovi Windows a získejte potřebné informace.

**Oprava 2:** Zkontrolujte položky v následujícím seznamu.

- Ujistěte se, že účet nemá vícefaktorové ověřování.
- Zkontrolujte, jestli je přesný název tenanta a tenant existuje ve virtuální plochy Windows.
- Potvrďte, že účet má alespoň oprávnění přispěvatele vzdálené plochy.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Chyba: Ověřování selhalo kvůli chybě v C:\Windows\Temp\scriptlogs.log

**Příčina:** PowerShell DSC bylo možné spustit, ale nelze se připojit k virtuálnímu klientovi Windows.

**Oprava:** Zkontrolujte položky v následujícím seznamu.

- Virtuální počítače ručně zaregistrujte do služby Windows virtuálního klienta.
- Potvrďte, že účet používaný pro připojení k virtuálnímu klientovi Windows má oprávnění k tenantovi vytvářet fondy hostitele.
- Potvrďte, že účet nemá vícefaktorové ověřování.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Virtuální plochy agenta Windows není registrace ve službě Windows virtuálního klienta

Při první instalaci agenta virtuální plochy Windows v relaci hostovat virtuální počítače (buď ručně nebo pomocí šablony Azure Resource Manageru a prostředí PowerShell DSC), poskytuje registračního tokenu. Následující část popisuje řešení problémů pro agenta virtuální plochy Windows a token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Chyba: Zobrazí stav zaznamenaná v rutině Get-RdsSessionHost stavu není k dispozici

![Rutina Get-RdsSessionHost zobrazuje stav jako není k dispozici.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Příčina:** Agent není schopen aktualizovat na novou verzi.

**Oprava:** Postupujte podle těchto pokynů můžete ručně aktualizovat agenta.

1. Stáhněte si novou verzi agenta na hostitele relace virtuálního počítače.
2. Spuštění Správce úloh a na kartě službu zastavit službu RDAgentBootLoader.
3. Spusťte instalační program pro novou verzi agenta virtuální plochy Windows.
4. Po zobrazení výzvy pro registrační token odebrat položku INVALID_TOKEN a stiskněte next (nový token není nutné).
5. Dokončení Průvodce instalací.
6. Otevřete Správce úloh a spustit službu RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Chyba:  Položka registru Windows virtuální plochy agenta IsRegistered zobrazuje hodnotu 0

**Příčina:** Registračního tokenu vypršela nebo byl vytvořen s hodnotou vypršení platnosti 999999.

**Oprava:** Postupujte podle těchto pokynů k registru chybu opravit agenta.

1. Pokud je již registračního tokenu, odeberte ho RDSRegistrationInfo odebrat.
2. Vygenerujte nový token s NewRegistrationInfo vzdálené plochy.
3. Potvrďte, že parametr - ExpriationHours je nastaven na 72 (maximální hodnota je 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Chyba: Virtuální Desktop Windows agent nevrací prezenční signál při spuštění Get-RdsSessionHost

**1. příčina:** RDAgentBootLoader služba byla zastavena.

**Oprava 1:** Spuštění Správce úloh a pokud kartu Služba hlásí zastavený stav služby RDAgentBootLoader, spusťte službu.

**2. příčina:** Může zavřít port 443.

**Oprava 2:** Postupujte podle těchto pokynů můžete otevřít port 443.

1. Potvrďte, stáhněte si nástroj PSPing z je otevřený port 443 [Sysinternal nástroje](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Nainstalujte PSPing na hostiteli relace virtuálního počítače, ve kterém je agent spuštěn.
3. Otevřete příkazový řádek jako správce a vydejte následující příkaz:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Potvrďte tyto informace PSPing přijatých z RDBroker:

    ```
    PsPing v2.10 - PsPing - ping, latency, bandwidth measurement utility
    Copyright (C) 2012-2016 Mark Russinovich
    Sysinternals - www.sysinternals.com
    TCP connect to 13.77.160.237:443:
    5 iterations (warmup 1) ping test:
    Connecting to 13.77.160.237:443 (warmup): from 172.20.17.140:60649: 2.00ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60650: 3.83ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60652: 2.21ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60653: 2.14ms
    Connecting to 13.77.160.237:443: from 172.20.17.140:60654: 2.12ms
    TCP connect statistics for 13.77.160.237:443:
    Sent = 4, Received = 4, Lost = 0 (0% loss),
    Minimum = 2.12ms, Maximum = 3.83ms, Average = 2.58ms
    ```

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Řešení potíží s vedle sebe zásobník virtuálního klienta Windows

Vedle sebe zásobník virtuálního klienta Windows se automaticky nainstaluje se Windows Server 2019. Microsoft Installer (MSI) použijte k instalaci zásobníku vedle sebe v Microsoft Windows Server 2016 nebo Windows Server 2012 R2. Pro Microsoft Windows 10, vedle sebe zásobník virtuálního klienta Windows je povolená s **enablesxstackrs.ps1**.

Existují tři hlavní způsoby zásobníku vedle sebe získá nainstalován ani povolen ve fondu relace hostitele virtuálních počítačů:

- S Azure Resource Manageru **vytvoření a zřízení nového fondu hostitele virtuální plochy Windows** šablony
- Probíhá zahrnuté a na hlavní image povolené
- Nainstalovanou nebo zapnutou ručně na každém virtuálním počítači (nebo rozšíření nebo Powershellu)

Pokud máte problémy se zásobníkem vedle sebe virtuální plochy Windows, zadejte **qwinsta** příkazu z příkazového řádku potvrďte, že je nainstalovaná nebo povolená zásobníku vedle sebe.

Výstup **qwinsta** zobrazí seznam **rdp sxs** ve výstupu, pokud je nainstalován a povolen zásobníku vedle sebe.

![Vedle sebe zásobníku nainstalován ani povolen s qwinsta uvedené jako rdp sxs ve výstupu.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Zkontrolujte položky registru uvedené níže a potvrďte, že jejich hodnoty odpovídat. Pokud chybí klíče registru nebo se hodnoty neshodují, postupujte podle pokynů v [vytvořit fond hostitele s prostředím PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) o tom, jak znovu nainstalovat vedle sebe zásobníku.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-oreverseconnectstackfailure"></a>Chyba: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE chybový kód.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Příčina:** Vedle sebe zásobník není nainstalován na hostiteli relace virtuálního počítače.

**Oprava:** Postupujte podle těchto pokynů můžete nainstalovat vedle sebe zásobníku na hostiteli relace virtuálního počítače.

1. Pomocí protokolu RDP (Remote Desktop) můžete získat přímo do relace hostitele virtuálního počítače jako místní správce.
2. Stáhnout a naimportovat [modulu Windows virtuální plochy Powershellu](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.
3. Nainstalovat s použitím vedle sebe zásobníku [vytvořit fond hostitele s prostředím PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>K vyřešení zásobníku vedle sebe virtuální plochy Windows, která nefunguje správně

Jsou známy okolnosti, které může způsobit zásobníku vedle sebe selhání:

- Nesleduje se správné pořadí kroků povolte vedle sebe zásobníku
- Automatická aktualizace pro Windows 10 rozšířená univerzální disk (EVD)
- Chybějící role Hostitel relace vzdálené plochy (RDSH)
- Opakované spouštění enablesxsstackrc.ps1
- Spuštění enablesxsstackrc.ps1 v rámci účtu, který nemá oprávnění místního správce

Pokyny v této části můžete odinstalovat zásobníku vedle sebe virtuální plochy Windows. Po odinstalování zásobníku vedle sebe, přejděte na "Zaregistrovat virtuálního počítače s Windows virtuální plochy hostiteli fondu" [vytvořit fond hostitele s prostředím PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) přeinstalovat zásobníku vedle sebe.

Virtuální počítač používá ke spouštění opravy musí být na stejné podsítě a domény jako virtuální počítač s chybně fungující zásobníku vedle sebe.

Postupujte podle těchto pokynů pro spuštění nápravy ze stejné podsítě a domény:

1. Připojení pomocí standardní protokol RDP (Remote Desktop) k virtuálnímu počítači z použití opravy.
2. Stáhněte si nástroje PsExec z https://docs.microsoft.com/sysinternals/downloads/psexec.
3. Rozbalte stažený soubor.
4. Spusťte příkazový řádek jako místní správce.
5. Přejděte do složky, ve kterém byl PsExec rozbaleny.
6. Z příkazového řádku použijte následující příkaz:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname je název počítače z virtuálního počítače s chybně fungující zásobníku vedle sebe.

7. Přijmout podmínky licenční smlouvy PsExec kliknutím na Souhlasím.

    ![Snímek obrazovky licenční smlouvy software.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Toto dialogové okno se zobrazí jenom při prvním spuštění nástroje PsExec.

8. Po otevření relace příkazového řádku na virtuálním počítači s chybně fungující zásobníku vedle sebe, spusťte qwinsta a potvrďte, že položka s názvem rdp sxs je k dispozici. V opačném případě vedle sebe zásobníku není k dispozici na virtuálním počítači, tak problém není vázaný na zásobníku vedle sebe.

    ![Příkazový řádek správce](media/AdministratorCommandPrompt.png)

9. Spusťte následující příkaz, který zobrazí seznam komponenty nástroje Microsoft nainstalovaný na virtuálním počítači s chybně fungující zásobníku vedle sebe.

    ```cmd
        wmic product get name
    ```

10. Spusťte níže uvedený příkaz s názvy produktů z kroku požadavků.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstalujte všechny produkty, které začínají znakem "Vzdálená plocha."

12. Po odinstalaci všech součástí virtuálního klienta Windows postupujte podle pokynů pro váš operační systém:

13. Pokud váš operační systém Windows Server, restartujte virtuální počítač, který měl nefunkční zásobníku vedle sebe (buď pomocí webu Azure portal nebo pomocí nástroje PsExec).

Pokud váš operační systém Microsoft Windows 10, pokračujte podle následujících pokynů:

14. Z virtuálního počítače s PsExec otevřete Průzkumníka souborů a zkopírujte disablesxsstackrc.ps1 na systémovou jednotku virtuálního počítače pomocí malfunctioned zásobníku vedle sebe.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname je název počítače z virtuálního počítače s chybně fungující zásobníku vedle sebe.

15. Doporučený postup: z nástroje PsExec, spusťte prostředí PowerShell a přejděte do složky v předchozím kroku a spusťte disablesxsstackrc.ps1. Alternativně můžete spustit následující rutiny:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po dokončení rutiny spuštěná, restartujte virtuální počítač s chybně fungující zásobníku vedle sebe.

## <a name="next-steps"></a>Další postup

- Přehled o řešení potíží virtuální plochy Windows a sleduje eskalace, naleznete v tématu [řešení potíží s přehled, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Řešení potíží při vytváření fondu tenanta a hostitele v prostředí virtuálního klienta Windows, naleznete v tématu [Tenanta a hostitele fondu vytváření](troubleshoot-set-up-issues.md).
- Řešení potíží při konfiguraci virtuálního počítače (VM) v virtuální plochy Windows, naleznete v tématu [konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Řešení potíží s připojeními klientů virtuální plochy Windows, naleznete v tématu [klienta připojení ke vzdálené ploše](troubleshoot-client-connection.md).
- Při řešení problémů při použití Powershellu s virtuální plochy Windows, naleznete v tématu [Windows Powershellu virtuální plochy](troubleshoot-powershell.md).
- Další informace o služba ve verzi Preview, najdete v článku [prostředí Windows Desktop Preview](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Absolvovat kurz řešení potíží, najdete v článku [kurzu: Řešení potíží s nasazení šablon Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot).
- Další informace o auditování akcí najdete v tématu [Audit operací pomocí Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích, chcete-li zjistit chyby během nasazení najdete v tématu [zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).