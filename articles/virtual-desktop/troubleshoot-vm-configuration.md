---
title: Poradce při potížích s hostitelem relací Virtuální plochy Windows – Azure
description: Jak vyřešit problémy při konfiguraci virtuálních počítačů hostitele relací Virtuální plochy systému Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 12/03/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: c7d9a5d576ceec301eba7436c1e0af34412ae854
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127586"
---
# <a name="session-host-virtual-machine-configuration"></a>Konfigurace virtuálního počítače hostitele relace

Tento článek slouží k řešení problémů, které máte při konfiguraci virtuálních počítačů (VM) relací windows virtuálních počítačů.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu virtuálníplochy windows a](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) prodiskutujte službu Windows Virtual Desktop s produktovým týmem a aktivními členy komunity.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuální servery nejsou připojeny k doméně

Pokud máte problémy s připojením virtuálních ms k doméně, postupujte podle těchto pokynů.

- Připojte virtuální počítač ručně pomocí procesu [v připojení virtuálního počítače se systémem Windows Server ke spravované doméně](../active-directory-domain-services/join-windows-vm.md) nebo pomocí [šablony připojení k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Zkuste ping název domény z příkazového řádku na virtuálním počítači.
- Zkontrolujte seznam chybových zpráv připojení k doméně v [části Poradce při potížích s chybovými zprávami o připojení k doméně](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Chyba: Nesprávná pověření

**Příčina:** Při zadání přihlašovacích údajů do opravy rozhraní šablony Azure Resource Manager událpřený překlep.

**Oprava:** Provést jednu z následujících akcí k vyřešení.

- Ručně přidejte virtuální chod do domény.
- Po potvrzení přihlašovacích údajů znovu nasadíte šablonu. Viz [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell.md).
- Připojení virtuálních počítače k doméně pomocí šablony pomocí [připojení existujícího virtuálního počítače systému Windows k doméně Služby AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Chyba: Časový výtok čekající na vstup uživatele

**Příčina:** Účet použitý k dokončení připojení k doméně může mít vícefaktorové ověřování (MFA).

**Oprava:** Provést jednu z následujících akcí k vyřešení.

- Dočasně odebrat vícefaktorové finanční a finanční řízení pro účet.
- Použijte účet služby.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Chyba: Účet používaný při zřizování nemá oprávnění k dokončení operace.

**Příčina:** Používaný účet nemá oprávnění k připojení virtuálních účtů k doméně z důvodu dodržování předpisů a předpisů.

**Oprava:** Provést jednu z následujících akcí k vyřešení.

- Použijte účet, který je členem skupiny Administrator.
- Udělte potřebná oprávnění používanému účtu.

### <a name="error-domain-name-doesnt-resolve"></a>Chyba: Název domény se nevyřeší.

**Příčina 1:** Virtuální počítače jsou ve virtuální síti, která není přidružená k virtuální síti (VNET), kde se nachází doména.

**Oprava 1:** Vytvořte partnerský vztah virtuální sítě mezi virtuální sítí, kde byly zřízeny virtuální chod, a virtuální sítí, kde je spuštěn řadič domény (DC). Viz [Vytvoření partnerského vztahu virtuální sítě – Správce prostředků, různá předplatná](../virtual-network/create-peering-different-subscriptions.md).

**Příčina 2:** Při použití služby Azure Active Directory Domain Services (Azure AD DS) nemá virtuální síť aktualizováno nastavení serveru DNS tak, aby ukazovalo na spravované řadiče domény.

**Oprava 2:** Pokud chcete aktualizovat nastavení DNS pro virtuální síť obsahující Azure AD DS, přečtěte si [informace o aktualizaci nastavení DNS pro virtuální síť Azure](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Příčina 3:** Nastavení serveru DNS síťového rozhraní neodkazují na příslušný server DNS ve virtuální síti.

**Oprava 3:** Pomocí jedné z následujících akcí postupujte podle kroků v části [Změna serverů DNS].
- Změňte nastavení serveru DNS síťového rozhraní na **Vlastní** pomocí kroků ze [změny serverů DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers) a zadejte privátní IP adresy serverů DNS ve virtuální síti.
- Změňte nastavení serveru DNS síťového rozhraní na **Dědit z virtuální sítě** pomocí kroků ze [serveru Změnit dns](../virtual-network/virtual-network-network-interface.md#change-dns-servers)a potom změňte nastavení serveru DNS virtuální sítě pomocí kroků ze serveru [Změnit DNS](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Zavaděče virtuální plochy systému Windows a zavaděč virtuální plochy nejsou nainstalovány

Doporučený způsob zřizování virtuálních počítačů používá šablonu fondu fondu hostitelů Azure Resource Manager **Create and Provision Windows Virtual Desktop.** Šablona automaticky nainstaluje agenta Virtuální plochy windows a zavaděč Windows Virtual Desktop Agent.

Podle těchto pokynů potvrďte, že jsou nainstalovány součásti a zkontrolujte chybové zprávy.

1. Zkontrolujte, zda jsou tyto dvě součásti nainstalovány, zaškrtnutím **ovládacího panelu** > **Programy** > **a funkce**. Pokud **Windows Virtual Desktop Agent** a Windows Virtual Desktop Agent **Zavaděč** nejsou viditelné, nejsou nainstalované na virtuálním počítači.
2. Otevřete **Průzkumníka souborů** a přejděte na **soubor C:\Windows\Temp\ScriptLog.log**. Pokud soubor chybí, znamená to, že prostředí PowerShell DSC, které nainstalovalo dvě součásti, nebylo možné spustit v poskytnutém kontextu zabezpečení.
3. Pokud je soubor **C:\Windows\Temp\ScriptLog.log** k dispozici, otevřete jej a zkontrolujte chybové zprávy.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Chyba: Chybí agent Windows Virtual Desktop Agent a Zavaděč Windows Virtual Desktop Agent. C:\Windows\Temp\ScriptLog.log také chybí

**Příčina 1:** Přihlašovací údaje poskytnuté během zadávání šablony Azure Resource Manager byly nesprávné nebo oprávnění byla nedostatečná.

**Oprava 1:** Ručně přidejte chybějící součásti do virtuálních her pomocí [vytvoření fondu hostitelů s prostředím PowerShell](create-host-pools-powershell.md).

**Příčina 2:** Prostředí PowerShell DSC se podařilo spustit a spustit, ale nepodařilo se jej dokončit, protože se nemůže přihlásit k virtuální ploše windows a získat potřebné informace.

**Oprava 2:** Potvrďte položky v následujícím seznamu.

- Ujistěte se, že účet nemá Vícefaktorové finanční problémy.
- Zkontrolujte, zda je název klienta přesný a že klient existuje ve virtuální ploše Windows.
- Potvrďte, že účet má alespoň oprávnění přispěvatele RDS.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Chyba: Ověřování se nezdařilo, chyba v c:\Windows\Temp\ScriptLog.log

**Příčina:** Prostředí PowerShell DSC se podařilo spustit, ale nemohlo se připojit k virtuální ploše windows.

**Oprava:** Potvrďte položky v následujícím seznamu.

- Ručně zaregistrujte virtuální počítače pomocí služby Windows Virtual Desktop.
- Potvrďte, že účet používaný pro připojení k virtuální ploše Windows má oprávnění k vytváření fondů hostitelů.
- Potvrďte, že účet nemá vícefaktorové finanční hod.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent virtuální plochy systému Windows se neregistruje pomocí služby Virtuální plocha systému Windows

Když se agent virtuální plochy Windows poprvé nainstaluje na virtuální počítače hostitele relací (ručně nebo prostřednictvím šablony Azure Resource Manager a Prostředí PowerShell DSC), poskytuje registrační token. Následující část popisuje problémy s odstraňováním potíží, které se vztahují na agenta virtuální plochy systému Windows a token.

### <a name="error-the-status-filed-in-get-rdssessionhost-cmdlet-shows-status-as-unavailable"></a>Chyba: Stav podaný v rutině Get-RdsSessionHost zobrazuje stav jako nedostupný.

![Rutina Get-RdsSessionHost zobrazuje stav jako Nedostupný.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Příčina:** Agent není schopen aktualizovat sám na novou verzi.

**Oprava:** Podle těchto pokynů ručně aktualizujte agenta.

1. Stáhněte si novou verzi agenta na hostitelském virtuálním počítači relace.
2. Spusťte Správce úloh a na kartě Služba zastavte službu RdAgentBootLoader.
3. Spusťte instalační program pro novou verzi agenta virtuální plochy systému Windows.
4. Po zobrazení výzvy k zadání registračního tokenu odeberte položku INVALID_TOKEN a stiskněte další (nový token není vyžadován).
5. Dokončete Průvodce instalací.
6. Otevřete Správce úloh a spusťte službu RdAgentBootLoader.

## <a name="error--windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Chyba: Položka registru IsRegistered agenta virtuální plochy systému Windows zobrazuje hodnotu 0

**Příčina:** Platnost registračního tokenu vypršela nebo byla vygenerována s hodnotou vypršení platnosti 999999.

**Oprava:** Podle těchto pokynů opravíte chybu registru agenta.

1. Pokud již existuje registrační token, odeberte jej pomocí Remove-RDSRegistrationInfo.
2. Generovat nový token s Rds-NewRegistrationInfo.
3. Zkontrolujte, zda je parametr -ExpriationHours nastaven na hodnotu 72 (maximální hodnota je 99999).

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-rdssessionhost"></a>Chyba: Agent virtuální plochy Systému Windows nehlásí prezenční signál při spuštění get-rdssessionhost

**Příčina 1:** Služba RDAgentBootLoader byla zastavena.

**Oprava 1:** Spusťte Správce úloh a pokud karta Služba hlásí zastavený stav pro službu RdAgentBootLoader, spusťte službu.

**Příčina 2:** Port 443 může být uzavřen.

**Oprava 2:** Podle těchto pokynů otevřete port 443.

1. Potvrďte, že port 443 je otevřen stažením nástroje PSPing z [nástrojů Sysinternal](/sysinternals/downloads/psping/).
2. Nainstalujte PSPing na virtuální počítač hostitele relace, kde je spuštěn agent.
3. Otevřete příkazový řádek jako správce a vyjezte následující příkaz:

    ```cmd
    psping rdbroker.wvdselfhost.microsoft.com:443
    ```

4. Potvrďte, že PSPing obdržel informace zpět od RDBroker:

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

## <a name="troubleshooting-issues-with-the-windows-virtual-desktop-side-by-side-stack"></a>Řešení problémů s balíčkem virtuální plochy Windows vedle sebe

Balíček virtuální plochy Windows vedle sebe se automaticky nainstaluje do systému Windows Server 2019. Pomocí Instalační služby společnosti Microsoft (MSI) nainstalujte souběžný zásobník na Microsoft Windows Server 2016 nebo Windows Server 2012 R2. V systému Microsoft Windows 10 je vedle sebe povolen zásobník virtuální plochy windows vedle sebe s **enablesxstackrs.ps1**.

Existují tři hlavní způsoby, jak se zásobník vedle sebe nainstaluje nebo aktivuje na virtuálních počítačích fondu hostitelů relací:

- Se Správcem prostředků Azure vytvoření a zřízení nové šablony **fondu fondu virtuálních desktopů Windows**
- Zahrnutím a povolením na hlavním obrázku
- Nainstalováno nebo povoleno ručně na každém virtuálním počítači (nebo s rozšířeními/PowerShellem)

Pokud máte problémy se zásobníkem virtuální plochy windows vedle sebe, zadejte příkaz **qwinsta** z příkazového řádku a ověřte, zda je zásobník vedle sebe nainstalován nebo povolen.

Výstup **qwinsta** bude seznam **rdp-Sxs** ve výstupu, pokud je nainstalován souběžně stoh.

![Souběžně s ním je nainstalován nebo povolen s qwinsta uvedeným jako rdp-sxs ve výstupu.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Zkontrolujte níže uvedené položky registru a potvrďte, že jejich hodnoty odpovídají. Pokud klíče registru chybí nebo hodnoty jsou neodpovídající, postupujte podle pokynů v [vytvořit fond hostitelů s Prostředím PowerShell](create-host-pools-powershell.md) o tom, jak přeinstalovat souběžný zásobník.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Chyba: O_REVERSE_CONNECT_STACK_FAILURE

![O_REVERSE_CONNECT_STACK_FAILURE kód chyby.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Příčina:** Souběžný zásobník není nainstalovaný na hostitelském virtuálním počítači relace.

**Oprava:** Podle těchto pokynů nainstalujte souběžný zásobník na hostitelský virtuální počítač relace.

1. Pomocí protokolu RDP (RdP) se můžete dostat přímo do virtuálního počítače hostitele relace jako místní správce.
2. Stáhněte a importujte [modul Windows Virtual Desktop PowerShell,](/powershell/windows-virtual-desktop/overview/) který se použije ve své relaci PowerShellu, pokud jste tak ještě neučinili, a pak spusťte tuto rutinu pro přihlášení ke svému účtu:

    ```powershell
    Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
    ```

3. Nainstalujte souběžný zásobník pomocí [funkce Vytvořit fond hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak opravit windows virtuální desktop side-by-side zásobníku, který nefunguje

Existují známé okolnosti, které mohou způsobit selhání souběžného zásobníku:

- Nedosazení správného pořadí kroků pro povolení souběžného zásobníku
- Automatická aktualizace na windows 10 rozšířený univerzální disk (EVD)
- Chybí role Hostitele relací vzdálené plochy (RDSH)
- Běh enablesxsstackrc.ps1 vícekrát
- Spuštění souboru enablesxsstackrc.ps1 v účtu, který nemá oprávnění místního správce

Pokyny v této části vám mohou pomoci odinstalovat zásobník virtuální plochy systému Windows vedle sebe. Po odinstalaci souběžného zásobníku přejděte na "Registrace virtuálního počítače s hostitelským fondem virtuální plochy Windows" v [části Vytvoření fondu hostitelů s prostředím PowerShell](create-host-pools-powershell.md) a přeinstalujte souběžný zásobník.

Virtuální počítač používaný ke spuštění nápravy musí být ve stejné podsíti a doméně jako virtuální počítač s nefunkčním zásobníkem vedle sebe.

Podle těchto pokynů spusťte nápravu ze stejné podsítě a domény:

1. Připojte se standardním protokolem RDP (RDP) ke virtuálnímu počítači, odkud se bude použít oprava.
2. Stáhnout PsExec https://docs.microsoft.com/sysinternals/downloads/psexecz .
3. Rozbalte stažený soubor.
4. Spusťte příkazový řádek jako místní správce.
5. Přejděte do složky, kde byl rozbalen psexec.
6. Z příkazového řádku použijte následující příkaz:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!Note]
    >Název virtuálního počítače je název počítače virtuálního počítače s nefunkční mašitou vedle sebe.

7. Přijměte licenční smlouvu psexec kliknutím na Souhlasím.

    ![Snímek obrazovky s licenční smlouvou softwaru.](media/SoftwareLicenseTerms.png)

    >[!Note]
    >Toto dialogové okno se zobrazí pouze při prvním spuštění systému PsExec.

8. Po otevření relace příkazového řádku na virtuálním počítači s nefunkčním zásobníkem vedle sebe spusťte qwinsta a potvrďte, že je k dispozici položka s názvem RDP-SXS. Pokud ne, zásobník u sebe vedle sebe není k dispozici na virtuálním počítači, takže problém není svázaný s zásobníku vedle sebe.

    ![Příkazový řádek správce](media/AdministratorCommandPrompt.png)

9. Spusťte následující příkaz, který zobrazí seznam součástí společnosti Microsoft nainstalovaných na virtuálním počítači s nefunkčním zásobníkem vedle sebe.

    ```cmd
        wmic product get name
    ```

10. Spusťte níže uvedený příkaz s názvy produktů z výše uvedeného kroku.

    ```cmd
        wmic product where name="<Remote Desktop Services Infrastructure Agent>" call uninstall
    ```

11. Odinstalujte všechny produkty, které začínají programem Vzdálená plocha.

12. Po odinstalaci všech součástí programu Windows Virtual Desktop postupujte podle pokynů operačního systému:

13. Pokud je váš operační systém Windows Server, restartujte virtuální počítač, který měl nefunkční souběžně (buď s portálem Azure nebo z nástroje PsExec).

Pokud je váš operační systém Microsoft Windows 10, pokračujte následujícími pokyny:

14. Z virtuálního virtuálního zařízení se systémem PsExec otevřete Průzkumníka souborů a zkopírujte disablesxsstackrc.ps1 na systémovou jednotku virtuálního zařízení s nefunkčním zásobníkem vedle sebe.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >Název virtuálního počítače je název počítače virtuálního počítače s nefunkční mašitou vedle sebe.

15. Doporučený proces: z nástroje PsExec spusťte prostředí PowerShell a přejděte do složky z předchozího kroku a spusťte disablesxsstackrc.ps1. Případně můžete spustit následující rutiny:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po dokončení spuštění rutiny restartujte virtuální počítač s nefunkčním zásobníkem vedle sebe.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Režim licencování vzdálené plochy není nakonfigurovaný

Pokud se k vícerelaci systému Windows 10 Enterprise přihlásíte pomocí účtu pro správu, může se zobrazit oznámení s upozorněním: "Režim licencování vzdálené plochy není nakonfigurován, služba Vzdálená plocha přestane fungovat za x dní. Na serveru Zprostředkovatel připojení pomocí Správce serveru určete režim licencování vzdálené plochy."

Pokud vyprší časový limit, zobrazí se chybová zpráva s textem "Vzdálená relace byla odpojena, protože pro tento počítač nejsou k dispozici žádné licence pro klientský přístup ke vzdálené ploše."

Pokud se zobrazí některá z těchto zpráv, znamená to, že bitová kopie nemá nainstalovány nejnovější aktualizace systému Windows nebo že nastavujete režim licencování vzdálené plochy prostřednictvím zásad skupiny. Podle pokynů v následujících částech zkontrolujte nastavení zásad skupiny, identifikujte verzi vícerelací Windows 10 Enterprise a nainstalujte odpovídající aktualizaci.  

>[!NOTE]
>Virtuální plocha systému Windows vyžaduje licenci klientského přístupu k počítači RDS (CAL), pokud fond hostitelů obsahuje hostitele relací systému Windows Server. Informace o konfiguraci licence k lautku VP naleznete v [tématu Licencování nasazení služby RdS pomocí licencí klientského přístupu](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Zakázání nastavení zásad skupiny režimu licencování vzdálené plochy

Zkontrolujte nastavení zásad skupiny otevřením Editoru zásad skupiny > ve virtuálním počítači a přechodem na **šablony pro správu,****Licensing** > licencování hostitele > **vzdálené plochy služby Vzdálená plocha služby** > Vzdálená plocha**služby** > **Vzdálená plocha:****Nastavte režim licencování vzdálené plochy**. Pokud je nastavení zásad skupiny **povoleno**, změňte jej na **Zakázáno**. Pokud je již zakázán, nechte to tak, jak je.

>[!NOTE]
>Pokud nastavíte zásady skupiny prostřednictvím domény, zakažte toto nastavení na zásady, které se zaměřují na tyto Windows 10 Enterprise vícerelační virtuální počítače.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Určení, kterou verzi Windows 10 Enterprise používáte

Chcete-li zkontrolovat, jakou verzi Windows 10 Enterprise multi-session máte:

1. Přihlaste se pomocí svého účtu správce.
2. Do vyhledávacího panelu vedle nabídky Start zadejte "O" .
3. Vyberte **Možnost O počítači**.
4. Zkontrolujte číslo vedle "Verze". Číslo by mělo být buď "1809" nebo "1903", jak je znázorněno na následujícím obrázku.

    ![Snímek obrazovky okna specifikací systému Windows. Číslo verze je zvýrazněno modře.](media/windows-specifications.png)

Nyní, když znáte číslo verze, přejděte na příslušnou část.

### <a name="version-1809"></a>Verze 1809

Pokud číslo verze říká "1809", nainstalujte [aktualizaci KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Verze 1903

Znovu nasaďte hostitelský operační systém s nejnovější verzí image Windows 10 verze 1903 z Galerie Azure.

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuální plochou Windows a traseskalace najdete [v tématu Přehled řešení potíží, zpětná vazba a podpora](troubleshoot-set-up-overview.md).
- Informace o řešení problémů při vytváření fondu klientů a hostitelů v prostředí Virtuální plochy systému Windows najdete v [tématu Vytvoření klientského a hostitelského fondu](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače (VM) ve Windows Virtual Desktop najdete v [tématu Konfigurace virtuálního počítače hostitele relací](troubleshoot-vm-configuration.md).
- Informace o řešení problémů s připojením klienta Virtuální plocha systému Windows naleznete v [tématu Připojení služby Windows Virtual Desktop](troubleshoot-service-connection.md).
- Informace o řešení problémů s klienty vzdálené plochy naleznete [v tématu Poradce při potížích s klientem Vzdálené plochy.](troubleshoot-client.md)
- Informace o řešení problémů při používání PowerShellu s Windows Virtual Desktop najdete v [tématu Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Další informace o službě naleznete v tématu [Windows Virtual Desktop environment](environment-setup.md).
- Chcete-li projít kurz řešení potíží, [přečtěte si článek Návod k řešení potíží s nasazením šablon Správce prostředků](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o auditování akcí naleznete v [tématu Audit operace se Správcem prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Informace o akcích k určení chyb během nasazení najdete v [tématu Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
