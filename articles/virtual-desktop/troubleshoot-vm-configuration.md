---
title: Vytvoření fondu tenanta a hostitele ve virtuální ploše Windows – Azure
description: Jak vyřešit problémy při konfiguraci virtuálního počítače hostitele a hostitele relace v prostředí virtuálních počítačů s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 07/10/2019
ms.author: helohr
ms.openlocfilehash: 0e32c81f37a8b81511cd009dfddbcc546aee1797
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876740"
---
# <a name="tenant-and-host-pool-creation"></a>Vytvoření tenanta a fondu hostitelů

Tento článek použijte k řešení problémů, které máte při konfiguraci virtuálních počítačů hostitele relace virtuálních počítačů (VM) Windows.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

V současné době nepřijímáme případy podpory, ale virtuální počítač s Windows je ve verzi Preview. Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuální počítače nejsou připojené k doméně.

Pokud máte problémy s připojením virtuálních počítačů k doméně, postupujte podle těchto pokynů.

- Připojte se k VIRTUÁLNÍmu počítači ručně pomocí procesu [připojení virtuálního počítače s Windows serverem ke spravované doméně](https://docs.microsoft.com/azure/active-directory-domain-services/Active-directory-ds-admin-guide-join-windows-vm-portal) nebo pomocí [šablony připojení k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Zkuste na virtuálním počítači testovat název domény z příkazového řádku.
- Projděte si seznam chybových zpráv připojení k doméně při [řešení potíží s chybovými zprávami k doméně](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Chyba: Nesprávné přihlašovací údaje

**Způsobit** Došlo k překlepu při zadání přihlašovacích údajů do opravy rozhraní Azure Resource Manager šablony.

**Opravit** Pokud chcete opravit přihlašovací údaje, postupujte podle těchto pokynů.

1. Ručně přidejte virtuální počítače do domény.
2. Znovu nasadit po potvrzení přihlašovacích údajů. Další informace najdete v tématu [Vytvoření fondu hostitelů pomocí PowerShellu](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).
3. Připojte virtuální počítače k doméně pomocí šablony s [připojením existujícímu virtuálnímu počítači s Windows k doméně AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Chyba: Časový limit čekání na vstup uživatele

**Způsobit** Účet použitý k dokončení připojení k doméně může mít službu Multi-Factor Authentication (MFA).

**Opravit** Dokončete připojení k doméně podle těchto pokynů.

1. Dočasně odeberte MFA pro účet.
2. Použijte účet služby.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Chyba: Účet použitý během zřizování nemá oprávnění k dokončení operace.

**Způsobit** Použitý účet nemá oprávnění k připojení virtuálních počítačů k doméně z důvodu dodržování předpisů a předpisů.

**Opravit** Postupujte podle těchto pokynů.

1. Použijte účet, který je členem skupiny správců.
2. Udělte potřebná oprávnění k používanému účtu.

### <a name="error-domain-name-doesnt-resolve"></a>Chyba: Název domény nejde přeložit.

**Příčina 1:** Virtuální počítače jsou ve skupině prostředků, která není přidružená k virtuální síti (VNET), ve které se nachází doména.

**Oprava 1:** Vytvořte partnerský vztah virtuálních sítí mezi virtuální sítí, ve které byly virtuální počítače zřízené a virtuální síť, ve které je spuštěný řadič domény (DC). Přečtěte si téma [vytvoření partnerského vztahu virtuální sítě – Správce prostředků různých](https://docs.microsoft.com/azure/virtual-network/create-peering-different-subscriptions)předplatných.

**Příčina 2:** Při použití AadService (AADS) nejsou nastaveny položky DNS.

**Oprava 2:** Pokud chcete nastavit doménové služby, přečtěte si téma [povolení Azure Active Directory Domain Services](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-dns).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agent virtuálního počítače Windows a spouštěcí zavaděč virtuálních počítačů s Windows nejsou nainstalované.

Doporučený způsob, jak zřídit virtuální počítače, je pomocí Azure Resource Manager **vytvořit a zřídit šablonu fondu hostitelů virtuálních počítačů s Windows** . Šablona automaticky nainstaluje agenta virtuální plochy Windows a zaváděcí program Windows Virtual Desktop agent.

Postupujte podle těchto pokynů a ověřte, zda jsou součásti nainstalovány a zda chcete zkontrolovat chybové zprávy.

1. Zkontrolujte, zda jsou tyto dvě součásti nainstalovány, pomocí kontroly v **Ovládacích panelech** >  > programy programy**a funkce**. Pokud není nainstalovaný **Agent virtuálního počítače Windows** a **spouštěcí zavaděč agenta virtuálního počítače s Windows** , nejsou nainstalované na virtuálním počítači.
2. Otevřete **Průzkumníka souborů** a přejděte na **C:\Windows\Temp\scriptlogs.log**. Pokud soubor chybí, znamená to, že v zadaném kontextu zabezpečení nebylo možné spustit prostředí PowerShell DSC, které nainstalovalo tyto dvě součásti.
3. Pokud je soubor **C:\Windows\Temp\scriptlogs.log** k dispozici, otevřete jej a vyhledejte chybové zprávy.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptlogslog-is-also-missing"></a>Chyba: Chybí agent virtuálního počítače Windows a spouštěcí zavaděč agenta virtuálního počítače s Windows. Chybí také C:\Windows\Temp\scriptlogs.log

**Příčina 1:** Přihlašovací údaje zadané během vstupu pro šablonu Azure Resource Manager byly nesprávné nebo mají dostatečná oprávnění.

**Oprava 1:** Ručně přidejte chybějící součásti do virtuálních počítačů pomocí [Vytvoření fondu hostitelů pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

**Příčina 2:** Prostředí PowerShell DSC se dokázalo spustit a spustit, ale nedokončilo se, protože se nemůže přihlásit k virtuální ploše Windows a získat potřebné informace.

**Oprava 2:** Potvrďte položky v následujícím seznamu.

- Ujistěte se, že účet nemá MFA.
- Ověřte, že je název tenanta přesný a že tenant existuje ve virtuálním počítači Windows.
- Potvrďte, že má účet alespoň oprávnění Přispěvatel VP.

### <a name="error-authentication-failed-error-in-cwindowstempscriptlogslog"></a>Chyba: Ověřování se nezdařilo, chyba v C:\Windows\Temp\scriptlogs.log

**Způsobit** Prostředí PowerShell DSC se podařilo spustit, ale nebylo možné se připojit k virtuálnímu počítači s Windows.

**Opravit** Potvrďte položky v následujícím seznamu.

- Ručně zaregistrujte virtuální počítače pomocí služby Virtual Desktop systému Windows.
- Potvrzení, že účet použitý pro připojení k virtuálnímu počítači s Windows má oprávnění k vytváření fondů hostitelů v tenantovi.
- Potvrďte, že účet nemá MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent virtuálního počítače s Windows se neregistruje ve službě Virtual Desktop systému Windows.

Když je agent virtuálního počítače s Windows nejdřív nainstalovaný na virtuálních počítačích hostitele relace (buď ručně, nebo prostřednictvím šablony Azure Resource Manager a prostředí PowerShell DSC), poskytuje registrační token. Následující část popisuje problémy týkající se odstraňování potíží vztahujících se k agentovi virtuálních počítačů s Windows a k tokenu.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Chyba: Stav uložený v rutině Get-RdsSessionHost zobrazuje stav jako nedostupný.

![Rutina Get-RdsSessionHost zobrazuje stav jako nedostupné.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Způsobit** Agent se nemůže sám aktualizovat na novou verzi.

**Opravit** Pomocí těchto pokynů ručně aktualizujete agenta.

1. Stáhněte si novou verzi agenta na virtuálním počítači hostitele relace.
2. Spusťte Správce úloh a na kartě Služba zastavte službu RDAgentBootLoader.
3. Spusťte instalační program pro novou verzi agenta virtuálního počítače s Windows.
4. Po zobrazení výzvy k registraci registračního tokenu odeberte položku INVALID_TOKEN a stiskněte Next (nový token se nevyžaduje).
5. Dokončete Průvodce instalací nástroje.
6. Otevřete Správce úloh a spusťte službu RDAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Chyba:  Položka registru agenta virtuálních počítačů s Windows je v registru zaregistrovaná. zobrazuje se hodnota 0.

**Způsobit** Registrační token vypršel nebo byl vygenerován s hodnotou vypršení platnosti 999999.

**Opravit** Pomocí těchto pokynů opravíte chybu registru agenta.

1. Pokud už existuje registrační token, odeberte ho pomocí Remove-RDSRegistrationInfo.
2. Vygenerujte nový token pomocí RDS-NewRegistrationInfo.
3. Potvrďte, že parametr-ExpriationHours je nastavený na 72 (maximální hodnota je 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Chyba: Agent virtuálních počítačů s Windows neoznamuje prezenční signál při spuštění Get-RdsSessionHost.

**Příčina 1:** Služba RDAgentBootLoader byla zastavena.

**Oprava 1:** Spustit Správce úloh a pokud karta služba hlásí stav zastaveno pro službu RDAgentBootLoader, spusťte službu.

**Příčina 2:** Port 443 může být zavřen.

**Oprava 2:** Podle těchto pokynů otevřete port 443.

1. Ověřte, že je port 443 otevřený stažením nástroje PSPing z [nástrojů společnosti Sysinternals](https://docs.microsoft.com/sysinternals/downloads/psping).
2. Nainstalujte PSPing na virtuálním počítači hostitele relace, kde je spuštěný agent.
3. Otevřete příkazový řádek jako správce a vydejte následující příkaz:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Potvrďte, že PSPing obdržela informace z RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Řešení potíží se souběžným zásobníkem virtuálních počítačů s Windows

Souběžná sada virtuálních počítačů s Windows je automaticky nainstalována se systémem Windows Server 2019. K instalaci souběžného zásobníku na Microsoft Windows Server 2016 nebo Windows Server 2012 R2 použijte instalační program Microsoft (MSI). V systému Microsoft Windows 10 je souběžná sada virtuálních počítačů s Windows zapnutá pomocí **enablesxstackrs. ps1**.

Existují tři hlavní způsoby, jak je souběžný zásobník nainstalovaný nebo povolený na virtuálních počítačích fondu hostitelů relace:

- Pomocí Azure Resource Manager **vytvořit a zřídit novou šablonu fondu hostitelů virtuálních počítačů s Windows**
- Podle zahrnutí a povolení na hlavní imagi
- Instalace nebo povolení ručně na každém virtuálním počítači (nebo s rozšířeními/PowerShellem)

Pokud máte problémy s souběžným zásobníkem virtuálních počítačů s Windows, zadejte příkaz **qwinsta** z příkazového řádku a potvrďte, že je nainstalovaná nebo povolená souběžná sada.

Pokud je nainstalovaná a povolená souběžná sada protokolů, výstup z **qwinsta** se zobrazí ve výstupu **RDP-SxS** .

![Nainstalovaná nebo povolená Souběžná instalace s qwinsta, která je ve výstupu uvedená jako RDP-SxS.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Projděte si níže uvedené položky registru a potvrďte, že jejich hodnoty se shodují. Pokud klíče registru chybí nebo se neshodují s hodnotami, postupujte podle pokynů v části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) , jak přeinstalovat souběžný zásobník.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Chyba: O_REVERSE_CONNECT_STACK_FAILURE

![Kód chyby O_REVERSE_CONNECT_STACK_FAILURE](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Způsobit** Souběžná sada není nainstalovaná na virtuálním počítači hostitele relace.

**Opravit** Při instalaci souběžného zásobníku na virtuálním počítači hostitele relace postupujte podle těchto pokynů.

1. Pomocí protokol RDP (Remote Desktop Protocol) (RDP) se přímo do virtuálního počítače hostitele relace dostanou jako místní správce.
2. Pokud jste to ještě neudělali, Stáhněte a importujte [modul PowerShellu pro virtuální plochu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , který chcete použít v relaci PowerShellu.
3. Nainstalujte souběžný zásobník pomocí části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak opravit souběžný zásobník virtuálních počítačů s Windows, který nefunguje správně

Existují známé okolnosti, které můžou způsobit selhání souběžného zásobníku:

- Není podle správného pořadí kroků pro povolení zásobníku souběžného sdílení.
- Automaticky aktualizovat na rozšířený univerzální disk s Windows 10 (EVD)
- Chybí role Hostitel relace vzdálené plochy (vzdálené relace).
- Spuštění enablesxsstackrc. ps1 několikrát
- Spuštění enablesxsstackrc. ps1 v účtu, který nemá oprávnění místního správce

Pokyny v této části vám pomůžou odinstalovat souběžnou sadu virtuálních počítačů s Windows. Po odinstalování souběžného zásobníku v části [Vytvoření fondu hostitelů s PowerShellem](https://docs.microsoft.com/azure/virtual-desktop/create-host-pools-powershell) pro přeinstalaci souběžného zásobníku použijte možnost zaregistrovat virtuální počítač s fondem hostitelů virtuálních klientů Windows.

Virtuální počítač, který se používá ke spuštění nápravy, musí být ve stejné podsíti a doméně jako virtuální počítač se selháním souběžného zásobníku.

Pokud chcete spustit nápravu ze stejné podsítě a domény, postupujte podle těchto pokynů:

1. Připojte se pomocí protokolu RDP (Standard protokol RDP (Remote Desktop Protocol)) k virtuálnímu počítači, ze kterého se má oprava použít.
2. Stáhněte si PsExec https://docs.microsoft.com/sysinternals/downloads/psexec z.
3. Extrahování staženého souboru.
4. Spusťte příkazový řádek jako místní správce.
5. Přejděte do složky, kde byla PsExec extrahována.
6. Z příkazového řádku použijte následující příkaz:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >VMname je název počítače virtuálního počítače se selháním souběžného zásobníku.

7. Kliknutím na Souhlasím přijměte licenční smlouvu PsExec.

    ![Snímek licenční smlouvy na software](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Toto dialogové okno se zobrazí jenom při prvním spuštění PsExec.

8. Po otevření relace příkazového řádku na virtuálním počítači se selháním souběžného zásobníku spusťte qwinsta a potvrďte, že je k dispozici položka s názvem RDP-SxS. V takovém případě se souběžný zásobník na virtuálním počítači nenachází, takže problém není vázaný na souběžný zásobník.

    ![Příkazový řádek správce](media/AdministratorCommandPrompt.png)

9. Spusťte následující příkaz, ve kterém se zobrazí seznam součástí, které jsou nainstalované na virtuálním počítači s nefunkčním zásobníkem souběžných souběžných aplikací.

    ```cmd
        wmic product get name
    ```

10. Spusťte níže uvedený příkaz s názvy produktů z kroku výše.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstalujte všechny produkty, které začínají na vzdálené ploše.

12. Po odinstalaci všech součástí virtuálního počítače s Windows postupujte podle pokynů pro váš operační systém:

13. Pokud je váš operační systém Windows Server, restartujte virtuální počítač, u kterého došlo k selhání souběžného zásobníku (buď pomocí Azure Portal, nebo z nástroje PsExec).

Pokud používáte operační systém Microsoft Windows 10, pokračujte podle následujících pokynů:

14. Z virtuálního počítače se systémem PsExec otevřete Průzkumníka souborů a zkopírujte disablesxsstackrc. ps1 na systémovou jednotku virtuálního počítače s nefunkčním zásobníkem souběžných souběžných sestavení.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname je název počítače virtuálního počítače se selháním souběžného zásobníku.

15. Doporučený postup: z nástroje PsExec spusťte PowerShell a přejděte do složky z předchozího kroku a spusťte disablesxsstackrc. ps1. Případně můžete spustit následující rutiny:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po spuštění rutin restartujte virtuální počítač s nefunkčním zásobníkem souběžného nasdílení.

## <a name="remote-licensing-model-is-not-configured"></a>Model vzdálených licencí není nakonfigurovaný.

Pokud se přihlásíte k používání více relací s Windows 10 Enterprise pomocí účtu správce, může se vám zobrazit oznámení, že "režim licencování vzdálené plochy není nakonfigurovaný, služba Vzdálená plocha přestane během X dnů fungovat. Na serveru zprostředkovatele připojení použijte Správce serveru k určení režimu licencování vzdálené plochy. " Pokud se zobrazí tato zpráva, znamená to, že musíte ručně nakonfigurovat režim licencování na **jednotlivé uživatele**.

Postup ruční konfigurace režimu licencování:  

1. Přejděte do pole Hledat v **nabídce Start** a pak vyhledejte a otevřete **gpedit. msc** pro přístup k místnímu editoru Zásady skupiny. 
2. Přejít na **konfiguraci** > počítače**šablony pro správu** > **součásti**Windows Remote DesktopService > hostitel relace vzdálené plochy >  >  **Licencování**. 
3. Vyberte možnost **nastavit režim licencování vzdálené plochy** a změnit ji na **uživatele**.

V současnosti se díváte na oznámení a časový limit období odkladu a plánujeme je vyřešit v budoucí aktualizaci. 

## <a name="next-steps"></a>Další postup

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Pokud chcete řešit problémy při vytváření tenanta a fondu hostitelů v prostředí virtuálních počítačů s Windows, přečtěte si téma [vytváření fondů klientů a hostitelů](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta k virtuální ploše Windows najdete v tématu [připojení klientů vzdálené plochy](troubleshoot-client-connection.md).
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o službě verze Preview najdete v tématu [prostředí verze Preview pro virtuální počítače s Windows](https://docs.microsoft.com/azure/virtual-desktop/environment-setup).
- Postup při řešení potíží najdete v [kurzu: Řešení potíží s nasazeními](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-tutorial-troubleshoot)šablon Správce prostředků.
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-audit).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-deployment-operations).