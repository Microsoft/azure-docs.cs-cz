---
title: Řešení potíží s hostitelem relace virtuálních klientských počítačů s Windows – Azure
description: Jak vyřešit problémy při konfiguraci virtuálních počítačů hostitele relace virtuálních počítačů s Windows.
author: Heidilohr
ms.topic: troubleshooting
ms.date: 05/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f1f2caa194972bb870dc58cfa0bd6ccb2f8db053
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100099910"
---
# <a name="session-host-virtual-machine-configuration"></a>Konfigurace virtuálního počítače hostitele relace

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/troubleshoot-vm-configuration-2019.md).

Tento článek použijte k řešení problémů, které máte při konfiguraci virtuálních počítačů hostitele relace virtuálních počítačů (VM) Windows.

## <a name="provide-feedback"></a>Poskytnutí zpětné vazby

Navštivte [technickou komunitu pro virtuální počítače s Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) , kde můžete diskutovat o službě Virtual Desktop v systému Windows pomocí produktového týmu a aktivních členů komunity.

## <a name="vms-are-not-joined-to-the-domain"></a>Virtuální počítače nejsou připojené k doméně.

Pokud máte problémy s připojením virtuálních počítačů k doméně, postupujte podle těchto pokynů.

- Připojte se k VIRTUÁLNÍmu počítači ručně pomocí procesu [připojení virtuálního počítače s Windows serverem ke spravované doméně](../active-directory-domain-services/join-windows-vm.md) nebo pomocí [šablony připojení k doméně](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).
- Zkuste ověřit název domény z příkazového řádku na virtuálním počítači pomocí příkazu VMM.
- Projděte si seznam chybových zpráv připojení k doméně při [řešení potíží s chybovými zprávami k doméně](https://social.technet.microsoft.com/wiki/contents/articles/1935.troubleshooting-domain-join-error-messages.aspx).

### <a name="error-incorrect-credentials"></a>Chyba: nesprávné přihlašovací údaje

**Příčina:** Došlo k překlepu při zadání přihlašovacích údajů do opravy rozhraní Azure Resource Manager šablony.

**Oprava:** Proveďte jednu z následujících akcí, které je potřeba vyřešit.

- Ručně přidejte virtuální počítače do domény.
- Po potvrzení přihlašovacích údajů znovu nasaďte šablonu. Další informace najdete v tématu [Vytvoření fondu hostitelů pomocí PowerShellu](create-host-pools-powershell.md).
- Připojte virtuální počítače k doméně pomocí šablony s [připojením existujícímu virtuálnímu počítači s Windows k doméně AD](https://azure.microsoft.com/resources/templates/201-vm-domain-join-existing/).

### <a name="error-timeout-waiting-for-user-input"></a>Chyba: časový limit čekání na vstup uživatele

**Příčina:** Účet použitý k dokončení připojení k doméně může mít službu Multi-Factor Authentication (MFA).

**Oprava:** Proveďte jednu z následujících akcí, které je potřeba vyřešit.

- Dočasně odeberte MFA pro účet.
- Použijte účet služby.

### <a name="error-the-account-used-during-provisioning-doesnt-have-permissions-to-complete-the-operation"></a>Chyba: účet použitý během zřizování nemá oprávnění k dokončení operace.

**Příčina:** Použitý účet nemá oprávnění k připojení virtuálních počítačů k doméně z důvodu dodržování předpisů a předpisů.

**Oprava:** Proveďte jednu z následujících akcí, které je potřeba vyřešit.

- Použijte účet, který je členem skupiny správců.
- Udělte potřebná oprávnění k používanému účtu.

### <a name="error-domain-name-doesnt-resolve"></a>Chyba: název domény nejde přeložit.

**Příčina 1:** Virtuální počítače jsou ve virtuální síti, která není přidružená k virtuální síti (VNET), ve které se nachází doména.

**Oprava 1:** Vytvořte partnerský vztah virtuálních sítí mezi virtuální sítí, ve které byly virtuální počítače zřízené a virtuální síť, ve které je spuštěný řadič domény (DC). Přečtěte si téma [vytvoření partnerského vztahu virtuální sítě – Správce prostředků různých předplatných](../virtual-network/create-peering-different-subscriptions.md).

**Příčina 2:** Při použití Azure Active Directory Domain Services (Azure služba AD DS) nemá virtuální síť aktualizované nastavení serveru DNS tak, aby odkazovala na spravované řadiče domény.

**Oprava 2:** Pokud chcete aktualizovat nastavení DNS pro virtuální síť, která obsahuje Azure služba AD DS, přečtěte si téma [aktualizace nastavení DNS pro virtuální síť Azure](../active-directory-domain-services/tutorial-create-instance.md#update-dns-settings-for-the-azure-virtual-network).

**Příčina 3:** Nastavení serveru DNS síťového rozhraní neukazuje na příslušný server DNS ve virtuální síti.

**Oprava 3:** Proveďte jednu z následujících akcí, které je potřeba vyřešit, podle kroků v [změna serverů DNS].
- Změňte nastavení serveru DNS síťového rozhraní na **vlastní** s použitím kroků v části [Změna serverů DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers) a zadejte privátní IP adresy serverů DNS ve virtuální síti.
- Změňte nastavení serveru DNS síťového rozhraní tak, aby **dědilo z virtuální sítě** s použitím kroků v části [Změna serverů DNS](../virtual-network/virtual-network-network-interface.md#change-dns-servers), a potom změňte nastavení serveru DNS virtuální sítě pomocí kroků z části [změnit servery DNS](../virtual-network/manage-virtual-network.md#change-dns-servers).

## <a name="windows-virtual-desktop-agent-and-windows-virtual-desktop-boot-loader-are-not-installed"></a>Agent virtuálního počítače Windows a spouštěcí zavaděč virtuálních počítačů s Windows nejsou nainstalované.

Doporučený způsob, jak zřídit virtuální počítače, je použití šablony pro vytváření Azure Portal. Šablona automaticky nainstaluje agenta virtuální plochy Windows a zaváděcí program Windows Virtual Desktop agent.

Postupujte podle těchto pokynů a ověřte, zda jsou součásti nainstalovány a zda chcete zkontrolovat chybové zprávy.

1. Zkontrolujte, zda jsou tyto dvě součásti nainstalovány, pomocí kontroly v **Ovládacích panelech** programy programy  >    >  **a funkce**. Pokud není nainstalovaný **Agent virtuálního počítače Windows** a **spouštěcí zavaděč agenta virtuálního počítače s Windows** , nejsou nainstalované na virtuálním počítači.
2. Otevřete **Průzkumníka souborů** a přejděte na **C:\Windows\Temp\ScriptLog.log**. Pokud soubor chybí, znamená to, že v zadaném kontextu zabezpečení nebylo možné spustit prostředí PowerShell DSC, které nainstalovalo tyto dvě součásti.
3. Pokud je soubor **C:\Windows\Temp\ScriptLog.log** k dispozici, otevřete jej a vyhledejte chybové zprávy.

### <a name="error-windows-virtual-desktop-agent-and-windows-virtual-desktop-agent-boot-loader-are-missing-cwindowstempscriptloglog-is-also-missing"></a>Chyba: chybí agent virtuálního počítače Windows a spouštěcí zavaděč agenta virtuálního počítače s Windows. Chybí také C:\Windows\Temp\ScriptLog.log

**Příčina 1:** Přihlašovací údaje zadané během vstupu pro šablonu Azure Resource Manager byly nesprávné nebo mají dostatečná oprávnění.

**Oprava 1:** Ručně přidejte chybějící součásti do virtuálních počítačů pomocí [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md).

**Příčina 2:** Prostředí PowerShell DSC se dokázalo spustit a spustit, ale nedokončilo se, protože se nemůže přihlásit k virtuální ploše Windows a získat potřebné informace.

**Oprava 2:** Potvrďte položky v následujícím seznamu.

- Ujistěte se, že účet nemá MFA.
- Potvrďte, že je název fondu hostitelů přesný a fond hostitelů existuje ve virtuálním počítači Windows.
- Potvrďte, že má účet alespoň oprávnění Přispěvatel pro předplatné Azure nebo skupinu prostředků.

### <a name="error-authentication-failed-error-in-cwindowstempscriptloglog"></a>Chyba: ověření se nezdařilo, chyba v C:\Windows\Temp\ScriptLog.log

**Příčina:** Prostředí PowerShell DSC se podařilo spustit, ale nebylo možné se připojit k virtuálnímu počítači s Windows.

**Oprava:** Potvrďte položky v následujícím seznamu.

- Ručně zaregistrujte virtuální počítače pomocí služby Virtual Desktop systému Windows.
- Potvrzení, že účet použitý k připojení k virtuálnímu počítači s Windows má oprávnění k vytváření fondů hostitelů v rámci předplatného Azure nebo skupiny prostředků.
- Potvrďte, že účet nemá MFA.

## <a name="windows-virtual-desktop-agent-is-not-registering-with-the-windows-virtual-desktop-service"></a>Agent virtuálního počítače s Windows se neregistruje ve službě Virtual Desktop systému Windows.

Když je agent virtuálního počítače s Windows nejdřív nainstalovaný na virtuálních počítačích hostitele relace (buď ručně, nebo prostřednictvím šablony Azure Resource Manager a prostředí PowerShell DSC), poskytuje registrační token. Následující část popisuje řešení potíží, které se vztahují k agentovi virtuálního počítače s Windows a tokenu.

### <a name="error-the-status-filed-in-get-azwvdsessionhost-cmdlet-shows-status-as-unavailable"></a>Chyba: stav uložený v rutině Get-AzWvdSessionHost zobrazuje stav jako nedostupný.

> [!div class="mx-imgBorder"]
> ![Rutina Get-AzWvdSessionHost zobrazuje stav jako nedostupné.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Příčina:** Agent se nemůže sám aktualizovat na novou verzi.

**Oprava:** Pomocí těchto pokynů ručně aktualizujete agenta.

1. Stáhněte si novou verzi agenta na virtuálním počítači hostitele relace.
2. Spusťte Správce úloh a na kartě Služba zastavte službu RDAgentBootLoader.
3. Spusťte instalační program pro novou verzi agenta virtuálního počítače s Windows.
4. Po zobrazení výzvy k registraci registračního tokenu odeberte položku INVALID_TOKEN a stiskněte Další (nový token se nevyžaduje).
5. Dokončete Průvodce instalací nástroje.
6. Otevřete Správce úloh a spusťte službu RDAgentBootLoader.

## <a name="error-windows-virtual-desktop-agent-registry-entry-isregistered-shows-a-value-of-0"></a>Chyba: položka registru agenta virtuálního počítače s Windows je v registru zaregistrovaná. zobrazuje se hodnota 0.

**Příčina:** Platnost registračního tokenu vypršela.

**Oprava:** Pomocí těchto pokynů opravíte chybu registru agenta.

1. Pokud už existuje registrační token, odeberte ho pomocí Remove-AzWvdRegistrationInfo.
2. Spuštěním rutiny **New-AzWvdRegistrationInfo** Vygenerujte nový token.
3. Potvrďte, že parametr *-ExpriationTime* je nastavený na 3 dny.

### <a name="error-windows-virtual-desktop-agent-isnt-reporting-a-heartbeat-when-running-get-azwvdsessionhost"></a>Chyba: Agent virtuálních počítačů systému Windows neoznamuje prezenční signál při spuštění Get-AzWvdSessionHost

**Příčina 1:** Služba RDAgentBootLoader byla zastavena.

**Oprava 1:** Spustit Správce úloh a pokud karta služba hlásí stav zastaveno pro službu RDAgentBootLoader, spusťte službu.

**Příčina 2:** Port 443 může být zavřen.

**Oprava 2:** Podle těchto pokynů otevřete port 443.

1. Ověřte, že je port 443 otevřený stažením nástroje PSPing z [nástrojů společnosti Sysinternals](/sysinternals/downloads/psping/).
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

Souběžná sada virtuálních počítačů s Windows je automaticky nainstalována se systémem Windows Server 2019. K instalaci souběžného zásobníku na Microsoft Windows Server 2016 nebo Windows Server 2012 R2 použijte instalační program Microsoft (MSI). V systému Microsoft Windows 10 je souběžná sada virtuálních počítačů s Windows povolena s **enablesxstackrs.ps1**.

Existují tři hlavní způsoby, jak je souběžný zásobník nainstalovaný nebo povolený na virtuálních počítačích fondu hostitelů relace:

- Se šablonou pro vytváření Azure Portal
- Podle zahrnutí a povolení na hlavní imagi
- Instalace nebo povolení ručně na každém virtuálním počítači (nebo s rozšířeními/PowerShellem)

Pokud máte problémy s souběžným zásobníkem virtuálních počítačů s Windows, zadejte příkaz **qwinsta** z příkazového řádku a potvrďte, že je nainstalovaná nebo povolená souběžná sada.

Pokud je nainstalovaná a povolená souběžná sada protokolů, výstup z **qwinsta** se zobrazí ve výstupu **RDP-SxS** .

> [!div class="mx-imgBorder"]
> ![Nainstalovaná nebo povolená Souběžná instalace s qwinsta, která je ve výstupu uvedená jako RDP-SxS.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

Projděte si níže uvedené položky registru a potvrďte, že jejich hodnoty se shodují. Pokud klíče registru chybí nebo se neshodují hodnoty, ujistěte se, že používáte [podporovaný operační systém](troubleshoot-agent.md#error-operating-a-pro-vm-or-other-unsupported-os). Pokud používáte, postupujte podle pokynů v části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md) , jak přeinstalovat souběžný zásobník.

```registry
    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\WinStations\rds-sxs\"fEnableWinstation":DWORD=1

    HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\Terminal
    Server\ClusterSettings\"SessionDirectoryListener":rdp-sxs
```

### <a name="error-o_reverse_connect_stack_failure"></a>Chyba: O_REVERSE_CONNECT_STACK_FAILURE

> [!div class="mx-imgBorder"]
> ![Kód chyby O_REVERSE_CONNECT_STACK_FAILURE.](media/23b8e5f525bb4e24494ab7f159fa6b62.png)

**Příčina:** Souběžná sada není nainstalovaná na virtuálním počítači hostitele relace.

**Oprava:** Při instalaci souběžného zásobníku na virtuálním počítači hostitele relace postupujte podle těchto pokynů.

1. Pomocí protokol RDP (Remote Desktop Protocol) (RDP) se přímo do virtuálního počítače hostitele relace dostanou jako místní správce.
2. Nainstalujte souběžný zásobník pomocí části [Vytvoření fondu hostitelů pomocí prostředí PowerShell](create-host-pools-powershell.md).

## <a name="how-to-fix-a-windows-virtual-desktop-side-by-side-stack-that-malfunctions"></a>Jak opravit souběžný zásobník virtuálních počítačů s Windows, který nefunguje správně

Existují známé okolnosti, které můžou způsobit selhání souběžného zásobníku:

- Není podle správného pořadí kroků pro povolení zásobníku souběžného sdílení.
- Automaticky aktualizovat na rozšířený univerzální disk s Windows 10 (EVD)
- Chybí role Hostitel relace vzdálené plochy (vzdálené relace).
- Spuštění enablesxsstackrc.ps1 několikrát
- Spuštění enablesxsstackrc.ps1 v účtu, který nemá oprávnění místního správce

Pokyny v této části vám pomůžou odinstalovat souběžnou sadu virtuálních počítačů s Windows. Po odinstalování souběžného zásobníku v části [Vytvoření fondu hostitelů s PowerShellem](create-host-pools-powershell.md) pro přeinstalaci souběžného zásobníku použijte možnost zaregistrovat virtuální počítač s fondem hostitelů virtuálních klientů Windows.

Virtuální počítač, který se používá ke spuštění nápravy, musí být ve stejné podsíti a doméně jako virtuální počítač se selháním souběžného zásobníku.

Pokud chcete spustit nápravu ze stejné podsítě a domény, postupujte podle těchto pokynů:

1. Připojte se pomocí protokolu RDP (Standard protokol RDP (Remote Desktop Protocol)) k virtuálnímu počítači, ze kterého se má oprava použít.
2. Stáhněte si PsExec z https://docs.microsoft.com/sysinternals/downloads/psexec .
3. Extrahování staženého souboru.
4. Spusťte příkazový řádek jako místní správce.
5. Přejděte do složky, kde byla PsExec extrahována.
6. Z příkazového řádku použijte následující příkaz:

    ```cmd
            psexec.exe \\<VMname> cmd
    ```

    >[!NOTE]
    >VMname je název počítače virtuálního počítače se selháním souběžného zásobníku.

7. Kliknutím na Souhlasím přijměte licenční smlouvu PsExec.

    > [!div class="mx-imgBorder"]
    > ![Snímek licenční smlouvy na software](media/SoftwareLicenseTerms.png)

    >[!NOTE]
    >Toto dialogové okno se zobrazí jenom při prvním spuštění PsExec.

8. Po otevření relace příkazového řádku na virtuálním počítači se selháním souběžného zásobníku spusťte qwinsta a potvrďte, že je k dispozici položka s názvem RDP-SxS. V takovém případě se souběžný zásobník na virtuálním počítači nenachází, takže problém není vázaný na souběžný zásobník.

    > [!div class="mx-imgBorder"]
    > ![Příkazový řádek správce](media/AdministratorCommandPrompt.png)

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

14. Z virtuálního počítače se systémem PsExec otevřete Průzkumníka souborů a zkopírujte disablesxsstackrc.ps1 do systémové jednotky virtuálního počítače se selháním souběžného zásobníku.

    ```cmd
        \\<VMname>\c$\
    ```

    >[!NOTE]
    >VMname je název počítače virtuálního počítače se selháním souběžného zásobníku.

15. Doporučený postup: z nástroje PsExec spusťte PowerShell a přejděte do složky z předchozího kroku a spusťte disablesxsstackrc.ps1. Případně můžete spustit následující rutiny:

    ```PowerShell
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\ClusterSettings" -Name "SessionDirectoryListener" -Force
    Remove-Item -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations\rdp-sxs" -Recurse -Force
    Remove-ItemProperty -Path "HKLM:\SYSTEM\CurrentControlSet\Control\Terminal Server\WinStations" -Name "ReverseConnectionListener" -Force
    ```

16. Po spuštění rutin restartujte virtuální počítač s nefunkčním zásobníkem souběžného nasdílení.

## <a name="remote-desktop-licensing-mode-isnt-configured"></a>Režim licencování vzdálené plochy není nakonfigurovaný.

Pokud se přihlásíte k používání více relací s Windows 10 Enterprise pomocí účtu správce, může se vám zobrazit oznámení, že "režim licencování vzdálené plochy není nakonfigurovaný, služba Vzdálená plocha přestane během X dnů fungovat. Na serveru zprostředkovatele připojení použijte Správce serveru k určení režimu licencování vzdálené plochy. "

Pokud časový limit vyprší, zobrazí se chybová zpráva oznamující, že Vzdálená relace byla odpojena, protože pro tento počítač nejsou k dispozici žádné licence pro klientský přístup k vzdálené ploše.

Pokud se zobrazí některá z těchto zpráv, znamená to, že bitová kopie nemá nainstalované nejnovější aktualizace systému Windows nebo že nastavujete režim licencování vzdálené plochy prostřednictvím zásad skupiny. Postupujte podle kroků v následujících částech a ověřte nastavení zásad skupiny, identifikujte verzi Windows 10 Enterprise multi-session a nainstalujte odpovídající aktualizaci.

>[!NOTE]
>Virtuální desktop Windows vyžaduje jenom licenci CAL pro klientský přístup (CAL), pokud fond hostitelů obsahuje hostitele relací Windows serveru. Informace o tom, jak nakonfigurovat licence VP CAL, najdete v článku o [licenci nasazení služby Vzdálená plocha pomocí licencí pro klientský přístup](/windows-server/remote/remote-desktop-services/rds-client-access-license/).

### <a name="disable-the-remote-desktop-licensing-mode-group-policy-setting"></a>Zakázat nastavení zásad skupiny pro režim licencování vzdálené plochy

Podívejte se na nastavení zásad skupiny tak, že ve virtuálním počítači otevřete Editor Zásady skupiny a přejdete na **šablony pro správu**  >  **součásti Windows**  >  **Vzdálená plocha**  >  **hostitel relace vzdálené plochy**  >  **licencování**  >  **nastavit režim licencování vzdálené plochy**. Pokud je nastavení zásad skupiny **povolené**, změňte ho na **disabled (zakázáno**). Pokud je už zakázaná, nechte ji tak, jak je.

>[!NOTE]
>Pokud jste v doméně nastavili zásady skupiny, zakažte toto nastavení u zásad, které cílí na tyto virtuální počítače s Windows 10 Enterprise s více relacemi.

### <a name="identify-which-version-of-windows-10-enterprise-multi-session-youre-using"></a>Zjistěte, kterou verzi Windows 10 Enterprise multi-session používáte.

Pokud chcete zjistit, jakou verzi Windows 10 Enterprise máte víc relací:

1. Přihlaste se pomocí účtu správce.
2. Do panelu hledání vedle nabídky Start zadejte "o aplikaci".
3. Vyberte **informace o vašem počítači**.
4. Podívejte se na číslo vedle možnosti verze. Číslo by mělo být buď "1809" nebo "1903", jak je znázorněno na následujícím obrázku.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky okna specifikace systému Windows. Číslo verze je zvýrazněné modře.](media/windows-specifications.png)

Teď, když znáte číslo verze, přejděte k příslušné části.

### <a name="version-1809"></a>Verze 1809

Pokud číslo verze říká 1809, nainstalujte [aktualizaci KB4516077](https://support.microsoft.com/help/4516077).

### <a name="version-1903"></a>Verze 1903

Znovu nasaďte hostitelský operační systém pomocí nejnovější verze image Windows 10 verze 1903 z Galerie Azure.

## <a name="we-couldnt-connect-to-the-remote-pc-because-of-a-security-error"></a>Nepovedlo se nám připojit ke vzdálenému počítači kvůli chybě zabezpečení.

Pokud se uživatelům zobrazí chyba oznamující, že se nám nepovedlo připojit ke vzdálenému počítači kvůli chybě zabezpečení. Pokud to bude pokračovat, požádejte o pomoc správce nebo technickou podporu. "Ověřte všechny existující zásady, které mění výchozí oprávnění RDP. Jedna zásada, která by mohla způsobit zobrazení této chyby, je "povolení přihlášení prostřednictvím zásad zabezpečení služby Vzdálená plocha".

Další informace o těchto zásadách najdete v tématu [Povolení přihlášení prostřednictvím služby Vzdálená plocha](/windows/security/threat-protection/security-policy-settings/allow-log-on-through-remote-desktop-services).

## <a name="next-steps"></a>Další kroky

- Přehled řešení potíží s virtuálním počítačem s Windows a cvičeními eskalace najdete v tématu [věnovaném řešení potíží s přehledem, zpětnou vazbou a podporou](troubleshoot-set-up-overview.md).
- Informace o řešení potíží při vytváření fondu hostitelů v prostředí virtuálních ploch Windows najdete v tématu [Vytvoření fondu prostředí a hostitele](troubleshoot-set-up-issues.md).
- Informace o řešení problémů při konfiguraci virtuálního počítače na virtuálním počítači s Windows najdete v tématu [Konfigurace virtuálního počítače hostitele relace](troubleshoot-vm-configuration.md).
- Informace o řešení potíží souvisejících s agentem virtuálního počítače s Windows nebo s připojením relace najdete v tématu [řešení běžných potíží s agentem virtuálních počítačů s Windows](troubleshoot-agent.md).
- Informace o řešení potíží s klientskými připojeními k virtuální ploše Windows najdete v tématu [připojení ke službě Virtual Desktop systému Windows](troubleshoot-service-connection.md).
- Řešení potíží s klienty vzdálené plochy najdete v tématu [řešení potíží s klientem vzdálené plochy](troubleshoot-client.md) .
- Pokud chcete řešit problémy při používání PowerShellu s virtuálním počítačem s Windows, přečtěte si téma [virtuální plocha Windows PowerShell](troubleshoot-powershell.md).
- Další informace o této službě najdete v tématu [prostředí virtuálních počítačů s Windows](environment-setup.md).
- Kurz řešení potíží najdete v tématu [kurz: řešení potíží s nasazením správce prostředků šablon](../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Další informace o akcích auditování najdete v tématu věnovaném [operacím auditu správce prostředků](../azure-resource-manager/management/view-activity-logs.md).
- Další informace o akcích k určení chyb během nasazení najdete v tématu [Zobrazení operací nasazení](../azure-resource-manager/templates/deployment-history.md).
