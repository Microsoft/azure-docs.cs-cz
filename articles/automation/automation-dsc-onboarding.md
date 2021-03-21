---
title: Povolit konfiguraci stavu Azure Automation
description: V tomto článku se dozvíte, jak nastavit počítače pro správu pomocí konfigurace stavu Azure Automation.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c0dc68bd7dacf0cd7f4be9732d45831e2dbb712c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98896999"
---
# <a name="enable-azure-automation-state-configuration"></a>Povolit konfiguraci stavu Azure Automation

V tomto tématu se dozvíte, jak můžete nastavit počítače pro správu pomocí konfigurace stavu Azure Automation. Podrobnosti o této službě najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md).

## <a name="enable-azure-vms"></a>Povolení virtuálních počítačů Azure

Konfigurace stavu Azure Automation umožňuje snadno povolit virtuální počítače Azure pro správu konfigurace pomocí Azure Portal, Azure Resource Manager šablon nebo PowerShellu. V digestoři a bez správce, který se musí vzdáleně přidružit k virtuálnímu počítači, registruje rozšíření konfigurace požadovaného stavu virtuálního počítače Azure konfiguraci stavu Azure Automation. Vzhledem k tomu, že se rozšíření Azure spouští asynchronně, postup sledování jeho průběhu je k dispozici v [části stav kontroly nastavení virtuálního počítače](#check-status-of-vm-setup).

> [!NOTE]
>Nasazení DSC na uzel Linux používá složku **adresáře/TMP** . Moduly, jako `nxautomation` jsou dočasně staženy k ověření před jejich instalací do příslušných umístění. Aby se zajistilo, že se moduly správně nainstalují, musí mít agent Log Analytics pro Linux oprávnění ke čtení a zápisu ve složce **adresáře/TMP** .<br><br>
>Agent Log Analytics pro Linux se spouští jako `omsagent` uživatel. Pokud chcete uživateli udělit oprávnění k zápisu >`omsagent` , spusťte příkaz `setfacl -m u:omsagent:rwx /tmp` .

### <a name="enable-a-vm-using-azure-portal"></a>Povolení virtuálního počítače pomocí Azure Portal

Povolení konfigurace virtuálního počítače Azure pomocí [Azure Portal](https://portal.azure.com/):

1. Přejděte na účet Azure Automation, ve kterém chcete virtuální počítače povolit. 

2. Na stránce Konfigurace stavu vyberte kartu **uzly** a pak klikněte na **Přidat**.

3. Vyberte virtuální počítač, který chcete povolit.

4. Pokud na počítači není nainstalovaná požadovaná rozšíření stavu prostředí PowerShell a stav napájení je spuštěno, klikněte na **připojit**.

5. V části **registrace** zadejte [hodnoty pro místní Configuration Manager PowerShellu DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) vyžadované pro váš případ použití. Volitelně můžete zadat konfiguraci uzlu, která se přiřadí k virtuálnímu počítači.

![povoluje se virtuální počítač](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="enable-a-vm-using-azure-resource-manager-templates"></a>Povolení virtuálního počítače pomocí šablon Azure Resource Manager

Pomocí šablon Azure Resource Manager můžete nainstalovat a povolit virtuální počítač pro konfiguraci stavu. Příklad šablony, která umožňuje existujícímu virtuálnímu počítači pro konfiguraci stavu, najdete v tématu [Server spravovaný službou pro konfiguraci požadovaného stavu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Pokud spravujete sadu škálování virtuálního počítače, podívejte se na příklad šablony v [konfiguraci sady škálování virtuálního počítače spravované pomocí Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="enable-machines-using-powershell"></a>Povolení počítačů pomocí prostředí PowerShell

Pomocí rutiny [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) v PowerShellu můžete povolit virtuální počítače pro konfiguraci stavu. 

> [!NOTE]
>`Register-AzAutomationDscNode`Rutina je implementována v současné době pouze pro počítače s Windows, protože aktivuje pouze rozšíření systému Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrace virtuálních počítačů napříč předplatnými Azure

Nejlepším způsobem, jak registrovat virtuální počítače z jiných předplatných Azure, je použít rozšíření DSC v šabloně nasazení Azure Resource Manager. Příklady jsou k dispozici v [rozšíření konfigurace požadovaného stavu pomocí šablon Azure Resource Manager](../virtual-machines/extensions/dsc-template.md).

Pokud chcete najít registrační klíč a registrační adresu URL, které se použijí jako parametry v šabloně, přečtěte si téma [povolení zabezpečeného počítače pomocí registrace](#enable-machines-securely-using-registration).

## <a name="enable-physicalvirtual-windows-machines"></a>Povolit fyzické a virtuální počítače s Windows

Můžete povolit servery Windows spuštěné místně nebo v jiných cloudových prostředích (včetně instancí AWS EC2), aby se mohla Azure Automation konfigurace stavu. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že na počítačích je nainstalovaná nejnovější verze [WMF 5](https://aka.ms/wmf5latest) , která umožňuje konfiguraci stavu. Kromě toho musí být na počítači, který používáte pro povolení počítačů, nainstalované WMF 5.
1. Podle pokynů v části [generování DSC metaconfigurations](#generate-dsc-metaconfigurations) vytvořte složku, která obsahuje požadované metaconfigurations DSC. 
1. Pomocí následující rutiny použijte vzdálenou aplikaci PowerShell DSC metaconfigurations na počítače, které chcete povolit. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Pokud nemůžete použít metaconfigurations prostředí PowerShell DSC vzdáleně, zkopírujte složku **metaconfigurations** do počítačů, které povolíte. Pak přidejte kód pro volání [set-DscLocalConfigurationManager](/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager) místně na počítačích.
1. Pomocí rutin Azure Portal a ověřte, že se počítače zobrazují jako uzly Konfigurace stavu registrované ve vašem účtu Azure Automation.

## <a name="enable-physicalvirtual-linux-machines"></a>Povolit fyzické a virtuální počítače se systémem Linux

Pro konfiguraci stavu můžete povolit servery Linux spuštěné místně nebo v jiných cloudových prostředích. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že na počítačích je nainstalovaná nejnovější verze [prostředí PowerShell pro konfiguraci požadovaného stavu pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) , aby bylo možné konfigurovat stav.
2. Pokud se ve [výchozím nastavení místní Configuration Manager PowerShellu pro DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) shodují s vaším případem použití, a chcete povolit počítače tak, aby využívaly konfiguraci a nahlásily stav:

   - V každém počítači se systémem Linux, který chcete povolit, použijte `Register.py` k povolení nastavení počítače s místními Configuration Manager PowerShellu pro DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Pokud chcete najít registrační klíč a registrační adresu URL pro váš účet Automation, přečtěte si téma [povolení zabezpečeného počítače pomocí registrace](#enable-machines-securely-using-registration).

3. Pokud se výchozí hodnoty prostředí PowerShell DSC Local Configuration Manager (LCM) neshodují s vaším případem použití, nebo chcete povolit počítače, které nahlásí pouze Azure Automation konfiguraci stavu, postupujte podle kroků 4-7. V opačném případě pokračujte přímo na krok 7.

4. Postupujte podle pokynů v části [generování DSC metaconfigurations](#generate-dsc-metaconfigurations) a vytvořte složku obsahující požadované metaconfigurations DSC.

5. Ujistěte se, že je v počítači, který se používá k povolení konfigurace stavu vašich počítačů, nainstalovaná nejnovější verze [WMF 5](https://aka.ms/wmf5latest) .

6. Následujícím způsobem přidejte kód pro použití metaconfigurations prostředí PowerShell DSC vzdáleně na počítačích, které chcete povolit.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Pokud nemůžete použít metaconfigurations prostředí PowerShell DSC vzdáleně, zkopírujte metaconfigurations odpovídající vzdáleným počítačům ze složky popsané v kroku 4 do počítačů se systémem Linux.

8. Přidejte kód pro volání `Set-DscLocalConfigurationManager.py` lokálně na každém počítači se systémem Linux, aby bylo možné povolit konfiguraci stavu.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Pomocí rutin Azure Portal nebo zajistěte, aby se počítače, které se mají teď zobrazovat, zobrazovaly jako uzly DSC zaregistrované ve vašem účtu Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Vygenerovat DSC metaconfigurations

Pokud chcete povolit libovolný počítač pro konfiguraci stavu, můžete vygenerovat [Metaconfiguration DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Tato konfigurace oznamuje agentovi DSC, aby načetl zprávu z nebo do konfigurace stavu Azure Automation. Metaconfiguration DSC můžete vygenerovat pro konfiguraci stavu Azure Automation pomocí konfigurace PowerShellu DSC nebo rutin Azure Automation PowerShellu.

> [!NOTE]
> DSC metaconfigurations obsahují tajné kódy potřebné k povolení počítače v účtu Automation pro správu. Ujistěte se, že jste správně chránili všechny metaconfigurationsy DSC, které jste vytvořili, nebo je po použití odstraňte.

Podpora proxy serveru pro metaconfigurations se řídí [místním Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaconfig), což je modul Windows PowerShell DSC. LCM běží na všech cílových uzlech a zodpovídá za volání prostředků konfigurace, které jsou součástí skriptu DSC metaconfiguration. Do metaconfiguration můžete zahrnout podporu proxy tak, že zahrnete definice `ProxyURL` a `ProxyCredential` vlastnosti podle potřeby v `ConfigurationRepositoryWeb` blocích, `ResourceRepositoryWeb` a `ReportServerWeb` . Příkladem nastavení adresy URL je `ProxyURL = "http://172.16.3.6:3128";` . `ProxyCredential`Vlastnost je nastavena na `PSCredential` objekt, jak je popsáno v tématu [Správa přihlašovacích údajů v Azure Automation](shared-resources/credentials.md). 

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generování DSC metaconfigurations pomocí konfigurace DSC

1. Otevřete VSCode (nebo oblíbený editor) jako správce na počítači v místním prostředí. Počítač musí mít nainstalovanou nejnovější verzi [WMF 5](https://aka.ms/wmf5latest) .
1. Zkopírujte následující skript místně. Tento skript obsahuje konfiguraci PowerShellu DSC pro vytvoření metaconfigurations a příkaz pro vytvoření metaconfiguration.

    > [!NOTE]
    > Názvy konfigurací uzlu Konfigurace stavu rozlišují velká a malá písmena v Azure Portal. Pokud se případ neshoduje, uzel se nezobrazí na kartě **uzly** .

   ```powershell
   # The DSC configuration that will generate metaconfigurations
   [DscLocalConfigurationManager()]
   Configuration DscMetaConfigs
   {
        param
        (
            [Parameter(Mandatory=$True)]
            [String]$RegistrationUrl,

            [Parameter(Mandatory=$True)]
            [String]$RegistrationKey,

            [Parameter(Mandatory=$True)]
            [String[]]$ComputerName,

            [Int]$RefreshFrequencyMins = 30,

            [Int]$ConfigurationModeFrequencyMins = 15,

            [String]$ConfigurationMode = 'ApplyAndMonitor',

            [String]$NodeConfigurationName,

            [Boolean]$RebootNodeIfNeeded= $False,

            [String]$ActionAfterReboot = 'ContinueConfiguration',

            [Boolean]$AllowModuleOverwrite = $False,

            [Boolean]$ReportOnly
        )

        if(!$NodeConfigurationName -or $NodeConfigurationName -eq '')
        {
            $ConfigurationNames = $null
        }
        else
        {
            $ConfigurationNames = @($NodeConfigurationName)
        }

        if($ReportOnly)
        {
            $RefreshMode = 'PUSH'
        }
        else
        {
            $RefreshMode = 'PULL'
        }

        Node $ComputerName
        {
            Settings
            {
                RefreshFrequencyMins           = $RefreshFrequencyMins
                RefreshMode                    = $RefreshMode
                ConfigurationMode              = $ConfigurationMode
                AllowModuleOverwrite           = $AllowModuleOverwrite
                RebootNodeIfNeeded             = $RebootNodeIfNeeded
                ActionAfterReboot              = $ActionAfterReboot
                ConfigurationModeFrequencyMins = $ConfigurationModeFrequencyMins
            }

            if(!$ReportOnly)
            {
            ConfigurationRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl          = $RegistrationUrl
                    RegistrationKey    = $RegistrationKey
                    ConfigurationNames = $ConfigurationNames
                }

                ResourceRepositoryWeb AzureAutomationStateConfiguration
                {
                    ServerUrl       = $RegistrationUrl
                    RegistrationKey = $RegistrationKey
                }
            }

            ReportServerWeb AzureAutomationStateConfiguration
            {
                ServerUrl       = $RegistrationUrl
                RegistrationKey = $RegistrationKey
            }
        }
   }

    # Create the metaconfigurations
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: edit the below as needed for your use case
   $Params = @{
        RegistrationUrl = '<fill me in>';
        RegistrationKey = '<fill me in>';
        ComputerName = @('<some VM to onboard>', '<some other VM to onboard>');
        NodeConfigurationName = 'SimpleConfig.webserver';
        RefreshFrequencyMins = 30;
        ConfigurationModeFrequencyMins = 15;
        RebootNodeIfNeeded = $False;
        AllowModuleOverwrite = $False;
        ConfigurationMode = 'ApplyAndMonitor';
        ActionAfterReboot = 'ContinueConfiguration';
        ReportOnly = $False;  # Set to $True to have machines only report to AA DSC but not pull from it
   }

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   DscMetaConfigs @Params
   ```

1. Zadejte registrační klíč a adresu URL pro váš účet Automation a názvy počítačů, které chcete povolit. Všechny ostatní parametry jsou volitelné. Pokud chcete najít registrační klíč a registrační adresu URL pro váš účet Automation, přečtěte si téma [povolení zabezpečeného počítače pomocí registrace](#enable-machines-securely-using-registration).

1. Pokud chcete, aby počítače nahlásily informace o stavu DSC do konfigurace stavu Azure Automation, ale ne z konfigurace Pull nebo modulů prostředí PowerShell, nastavte `ReportOnly` parametr na hodnotu true.

1. Pokud není `ReportOnly` nastavené, počítače nahlásí informace o stavu DSC do konfigurace stavu Azure Automation a konfigurace pro vyžádání obsahu nebo moduly prostředí PowerShell. Nastavte parametry odpovídajícím způsobem `ConfigurationRepositoryWeb` v `ResourceRepositoryWeb` blocích, a `ReportServerWeb` .

1. Spusťte skript. Teď byste měli mít pracovní složku s názvem **DscMetaConfigs**, která obsahuje PowerShell DSC metaconfigurations pro počítače, které chcete povolit (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generování DSC metaconfigurations pomocí rutin Azure Automation

Pokud se výchozí hodnoty prostředí PowerShell DSC shodují s vaším případem použití a chcete, aby počítače umožňovaly načítat a nahlásit Azure Automation konfiguraci stavu, můžete potřebné DSC vygenerovat snadněji pomocí rutin Azure Automation.

1. Otevřete konzolu PowerShellu nebo VSCode jako správce na počítači v místním prostředí.
2. Připojte se k Azure Resource Manager pomocí [Connect-AzAccount](/powershell/module/Az.Accounts/Connect-AzAccount).
3. Stáhněte si prostředí PowerShell DSC metaconfigurations pro počítače, které chcete povolit z účtu Automation, ve kterém nastavujete uzly.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked
   # For more info about splatting, run: Get-Help -Name about_Splatting
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Teď byste měli mít složku **DscMetaConfigs** , která obsahuje PowerShell DSC metaconfigurations pro počítače, které chcete povolit (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="enable-machines-securely-using-registration"></a>Bezpečné zapnutí počítačů pomocí registrace

Počítače můžete bezpečně povolit pro účet Azure Automation prostřednictvím registračního protokolu WMF 5 DSC. Tento protokol umožňuje, aby se uzel DSC ověřil na server pro vyžádání obsahu nebo na serveru sestav PowerShellu DSC, včetně konfigurace stavu Azure Automation. Uzel se registruje u serveru v registrační adrese URL a ověřuje se pomocí registračního klíče. V průběhu registrace vyjednávat uzel DSC a server pro vyžádání/sestavování DSC jedinečný certifikát, který má uzel použít k ověřování po registraci serveru. Tento proces zabraňuje povolenému zosobnění uzlů, například pokud dojde k ohrožení bezpečnosti uzlu a se chová škodlivým způsobem. Po registraci se registrační klíč znovu nepoužívá pro ověřování a z uzlu se odstraní.

Informace požadované pro registrační protokol konfigurace stavu můžete získat z **klíčů** v části **nastavení účtu** v Azure Portal. 

![Klíče a adresa URL služby Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adresa URL registrace je pole Adresa URL na stránce klíče.
- Registrační klíč je hodnota pole **Primární přístupový klíč** nebo **sekundární přístupový klíč** na stránce klíče. Lze použít buď klíč.

Pro zvýšení zabezpečení můžete na stránce klíče kdykoli znovu vygenerovat primární a sekundární přístupové klíče účtu Automation. Opětovné vygenerování klíče brání v budoucí registraci uzlu v použití předchozích klíčů.

## <a name="re-register-a-node"></a>Znovu zaregistrovat uzel

Po registraci počítače jako uzlu DSC v konfiguraci stavu Azure Automation existuje několik důvodů, proč možná budete muset uzel v budoucnu znovu zaregistrovat.

- **Obnovení certifikátu.** U verzí Windows serveru před Windows serverem 2019 každý uzel automaticky vyjednává jedinečný certifikát pro ověřování, jehož platnost vyprší po jednom roce. Pokud certifikát vyprší bez obnovení, uzel nebude schopen komunikovat s Azure Automation a je označen `Unresponsive` . V současné době nemůže registrační protokol pro PowerShell DSC automaticky obnovovat certifikáty, když se blíží vypršení platnosti, a musíte uzly znovu zaregistrovat po roce. Před opakovaným registrací se ujistěte, že na každém uzlu je spuštěný WMF 5 RTM. 

    Opakovaná registrace provedla 90 dní nebo méně z času vypršení platnosti certifikátu nebo kdykoli po uplynutí doby vypršení platnosti certifikátu dojde k vygenerování a použití nového certifikátu. Řešení tohoto problému je součástí Windows serveru 2019 a novějších verzí.

- **Změny hodnot LCM v DSC.** Možná budete muset změnit [hodnoty prostředí POWERSHELL DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) , které nastavíte při počáteční registraci uzlu, například `ConfigurationMode` . V současné době můžete tyto hodnoty agenta DSC změnit pouze pomocí opětovné registrace. Jedinou výjimkou je hodnota konfigurace uzlu přiřazená uzlu. Tuto možnost můžete v Azure Automation DSC změnit přímo.

Uzel můžete znovu zaregistrovat stejně, jako jste předtím zaregistrovali uzel, a to pomocí kterékoli z metod popsaných v tomto dokumentu. Před opětovnou registrací není nutné zrušit registraci uzlu v konfiguraci stavu Azure Automation.

## <a name="check-status-of-vm-setup"></a>Stav kontroly nastavení virtuálního počítače

Konfigurace stavu umožňuje snadno povolit virtuální počítače Azure s Windows pro správu konfigurace. V digestoři se k registraci virtuálního počítače s konfigurací stavu Azure Automation používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure. Vzhledem k tomu, že se rozšíření konfigurace požadovaného stavu virtuálního počítače Azure spouští asynchronně, sledování průběhu a řešení potíží s jeho spuštěním může být důležité.

> [!NOTE]
> Jakákoli metoda povolení virtuálních počítačů Azure s Windows pro konfiguraci stavu, která používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, může trvat až hodinu, než Azure Automation zobrazí virtuální počítače jako zaregistrované. Tato prodleva je způsobená instalací WMF 5 na VIRTUÁLNÍm počítači pomocí rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, které je nutné k povolení konfigurace virtuálních počítačů.

Zobrazení stavu rozšíření konfigurace požadovaného stavu virtuálního počítače Azure:

1. V Azure Portal přejděte na virtuální počítač, který je povolený.
2. V části **Nastavení** klikněte na **rozšíření** . 
3. V závislosti na vašem operačním systému teď vyberte **DSC** nebo **DSCForLinux**. 
4. Pro další podrobnosti můžete kliknout na **zobrazit podrobný stav**.

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu Začínáme [s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací DSC v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](/powershell/module/az.automation).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [Nastavení průběžného nasazování pomocí čokolády](automation-dsc-cd-chocolatey.md).
- Informace o řešení potíží najdete v tématu [řešení potíží s konfigurací stavu Azure Automation](./troubleshoot/desired-state-configuration.md).
