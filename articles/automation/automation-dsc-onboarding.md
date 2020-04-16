---
title: Palubní počítače pro správu pomocí konfigurace stavu azure automatizace
description: Tento článek popisuje, jak nastavit počítače pro správu pomocí konfigurace stavu automatizace Azure.
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 04/15/2020
manager: carmonm
ms.openlocfilehash: 4fc9f701f4f832deb1fed34d7ac9f888521d3830
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406201"
---
# <a name="onboard-machines-for-management-by-azure-automation-state-configuration"></a>Palubní počítače pro správu pomocí konfigurace stavu azure automatizace

Azure Automation State Configuration je služba správy konfigurace pro uzly konfigurace požadovaného stavu (DSC) v cloudových nebo místních datových centrech. Ke službě na webu Azure Portal můžete získat přístup tak, že v části **Správa konfigurace**vyberete **konfiguraci stavu (DSC).** 

Díky konfiguraci stavu azure automatizace můžete rychle a snadno dosáhnout škálovatelnosti napříč tisíci počítačů z centrálního a zabezpečeného umístění. Můžete snadno napalubě počítače, přiřadit jim deklarativní konfigurace a zobrazit sestavy, které ukazují, že každý počítač splňuje stav, který jste zadali.

Služba Konfigurace stavu azure automatizace je DSC, co azure automatizace runbooky jsou skriptování PowerShell. Jinými slovy, stejným způsobem, že Azure Automation pomáhá spravovat skripty PowerShell, ale také vám pomůže spravovat konfigurace DSC. Další informace o výhodách používání konfigurace stavu azure automatizace najdete v [tématu Přehled konfigurace stavu automatizace Azure](automation-dsc-overview.md).

Ke správě různých počítačů můžete použít konfiguraci stavu automatizace Azure:

- Azure Virtual Machines
- Virtuální počítače Azure (klasické)
- Fyzické/virtuální počítače s Windows v místním prostředí nebo v cloudu jiném než Azure (včetně amazonských webových služeb [AWS] elastického výpočetního cloudu [EC2] instancí)
- Fyzické/virtuální počítače s Linuxem v místním prostředí, v Azure nebo v cloudu jiném než Azure

Pokud nejste připraveni ke správě konfigurace počítače z cloudu, můžete použít Azure Automation State Configuration jako koncový bod pouze sestavy. Tato funkce umožňuje nastavit nebo push, konfigurace prostřednictvím DSC a zobrazit podrobnosti sestavy v Azure Automation.

> [!NOTE]
> Správa virtuálních počítačů Azure s Azure Automation State Configuration je zahrnuta bez příplatku, pokud je vaše nainstalované rozšíření konfigurace požadovaného stavu virtuálního počítače Azure verze 2.70 nebo novější. Další informace naleznete v [tématu Automation pricing](https://azure.microsoft.com/pricing/details/automation/).

Následující části tohoto článku popisují, jak můžete na palubě dříve uvedených počítačů konfigurace stavu azure automatizace.

## <a name="onboard-azure-vms"></a>Na palubě virtuálních počítačů Azure

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ pro hybridní pracovník runbooku najdete v [tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [modulů Update Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

Vzhledem k tomu, že rozšíření Azure běží asynchronně, poskytujeme kroky ke sledování jeho průběhu nebo řešení potíží v [části Poradce při potížích s virtuálním počítačem Azure v](#troubleshoot-azure-virtual-machine-onboarding) tomto článku.

> [!NOTE]
> Nasazení DSC do uzlu Linuxu používá složku */tmp.* Moduly, `nxautomation` jako jsou dočasně staženy k ověření před jejich instalací. Chcete-li zajistit, že moduly jsou nainstalovány správně, agent Log Analytics pro Linux potřebuje oprávnění ke čtení a zápisu ve složce */tmp.*<br><br>
> Agent Log Analytics pro Linux běží jako uživatel *omsagent.* Chcete-li uživateli *omsagent* udělit `setfacl -m u:omsagent:rwx /tmp`oprávnění k zápisu, spusťte příkaz .

### <a name="onboard-vms-by-using-the-azure-portal"></a>Palubní virtuální počítače pomocí portálu Azure

Chcete-li na palubě virtuálních počítačů Azure na Azure Automation konfigurace stavu prostřednictvím [portálu Azure:](https://portal.azure.com/)

1. Přejděte na účet Azure Automation, ve kterém chcete na palubě virtuálních počítačů. 

1. Na stránce **Konfigurace stavu** vyberte kartu **Uzly** a pak vyberte **Přidat**.

1. Zvolte virtuální počítač na palubě.

1. Pokud v zařízení není nainstalováno rozšíření Požadované stav prostředí PowerShell a je spuštěný stav napájení, vyberte **Připojit**.

1. V **části Registrace**zadejte hodnoty [místního nástroje konfigurace prostředí PowerShell DSC (LCM)](/powershell/scripting/dsc/managing-nodes/metaConfig) požadované pro případ použití. Volitelně můžete zadat konfiguraci uzlu, kterou chcete přiřadit k virtuálnímu počítače.

![Podokno registrace virtuálních společností](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-vms-by-using-azure-resource-manager-templates"></a>Palubní virtuální počítače pomocí šablon Azure Resource Manager

Virtuální počítač můžete nasadit a nasadit do konfigurace stavu automatizace Azure pomocí šablon Azure Resource Manager. Ukázková šablona, která zapisuje existující konfiguraci stavu virtuálního počítače do Azure Automation, najdete v [tématu Server spravovaný službou Konfigurace požadovaného stavu](https://azure.microsoft.com/resources/templates/101-automation-configuration/). Pokud spravujete škálovací sadu virtuálních počítačů, podívejte se na ukázkovou šablonu v [konfiguraci škálovací sady virtuálních počítačů spravované službou Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-vms-by-using-powershell"></a>Palubní virtuální počítačky pomocí PowerShellu

Rutina [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) v prostředí PowerShell můžete použít k nazabezpečení virtuálních počítačů do konfigurace stavu automatizace Azure. 

> [!NOTE]
>Rutina `Register-AzAutomationDscNode` je implementována v současné době pouze pro počítače se systémem Windows, protože rutina aktivuje pouze rozšíření systému Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrace virtuálních počítačů napříč předplatnými Azure

Nejlepší způsob, jak zaregistrovat virtuální počítače z jiných předplatných Azure, je použít rozšíření DSC v šabloně nasazení Azure Resource Manageru. Příklady jsou k dispozici v [rozšíření Konfigurace požadovaného stavu se šablonami Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Chcete-li najít registrační klíč a registrační adresu URL, které chcete použít jako parametry v [šabloně,](#onboard-securely-by-using-registration) přečtěte si téma Palubní zabezpečené zabezpečení pomocí oddílu registrace v tomto článku.

## <a name="onboard-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Palubní fyzické/virtuální počítače s Windows v místním prostředí nebo v cloudu jiném než Azure

Můžete na palubě serverů Windows, které běží místně nebo v jiných cloudových prostředích, včetně instancí AWS EC2, do konfigurace stavu automatizace Azure. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že nejnovější verze [rozhraní Windows Management Framework 5](https://aka.ms/wmf5latest) je nainstalována na počítačích, které mají být na palubě konfigurace stavu azure automatizace. Kromě toho musí být rozhraní Windows Management Framework 5 nainstalováno v počítači, který používáte pro operaci připojení.
1. Chcete-li vytvořit složku obsahující požadované metakonfigurace dsc, postupujte podle pokynů v části [Generovat metakonfigurace dsc.](#generate-dsc-metaconfigurations) 
1. Chcete-li metakonfigurace prostředí PowerShell DSC vzdáleně použít na počítače, které chcete napalubě použít, použijte následující rutinu: 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Pokud nelze použít metakonfigurace Prostředí PowerShell DSC vzdáleně, zkopírujte složku **metakonfigurace** do počítačů, které se zapisujete. Pak přidejte kód pro volání [Set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) místně na počítačích.

1. Na webu Azure Portal nebo pomocí rutin ověřte, že počítače na palubě se zobrazí jako uzly konfigurace stavu registrované ve vašem účtu Azure Automation.

## <a name="onboard-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Palubní fyzické/virtuální počítače s Linuxem v místním prostředí nebo v jiném cloudu než v Azure

Do konfigurace stavu Azure Automation můžete napalubě linuxových serverů spuštěných místně nebo v jiných cloudových prostředích. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že nejnovější verze [konfigurace požadovaného stavu Prostředí PowerShell pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) je nainstalována na počítačích, které mají být na palubě konfigurace stavu Azure Automation.
1. Pokud [výchozí hodnoty Prostředí PowerShell DSC LCM](/powershell/scripting/dsc/managing-nodes/metaConfig4) odpovídají vašemu případu použití a chcete napalubě počítače tak, aby oba navádět a sestavy konfigurace stavu Azure Automation, postupujte takto:

   a. Na každém počítači s Linuxem na `Register.py` připojení k Azure Automation state Configuration, použijte k jejich připojení pomocí výchozích výchozích nastavení PowerShell DSC Local Configuration Manager.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   b. Chcete-li najít registrační klíč a registrační adresu URL pro váš účet Automation, podívejte [se na palubní bezpečně pomocí registrační](#onboard-securely-by-using-registration) části tohoto článku.  
   c. Přeskočte ke kroku 4.
   
1. Pokud výchozí hodnoty PowerShell DSC LCM neodpovídají případu použití nebo pokud chcete napalubě počítače, které hlásí pouze konfigurace stavu Azure Automation, postupujte podle kroků ad. V opačném případě pokračujte přímo ke kroku d.

    a. Chcete-li vytvořit složku obsahující požadované metakonfigurace DSC, postupujte podle pokynů v části [Generovat metakonfigurace dsc.](#generate-dsc-metaconfigurations)

    b. Ujistěte se, že je v počítači, který se používá pro přiřazování, nainstalována nejnovější verze [rozhraní Windows Management Framework 5.](https://aka.ms/wmf5latest)

    c. Chcete-li metakonfigurace prostředí PowerShell DSC vzdáleně použít na počítače, které chcete na palubě použít, přidejte následující kód:

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

    d. Pokud nelze použít metakonfigurace Prostředí PowerShell DSC vzdáleně, zkopírujte metakonfigurace odpovídající vzdáleným počítačům ze složky popsané v kroku 3a do počítačů s Linuxem.

1. Přidejte kód pro volání *Set-DscLocalConfigurationManager.py* místně na každém počítači s Linuxem, který má být na palubě konfigurace stavu Azure Automation.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

1. Pomocí portálu Azure nebo rutiny, ujistěte se, že počítače na palubě teď zobrazí jako uzly DSC registrované ve vašem účtu Azure Automation.

## <a name="generate-dsc-metaconfigurations"></a>Generovat metakonfigurace DSC

Chcete-li zavést libovolný počítač do konfigurace stavu automatizace Azure, můžete vygenerovat [metakonfiguraci DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Tato konfigurace říká agentovi DSC, aby natáhl nebo sestavu do konfigurace stavu azure automatizace. Metakonfigurace DSC pro konfiguraci stavu automatizace Azure můžete vygenerovat pomocí konfigurace DSC prostředí PowerShell nebo rutin prostředí Azure Automation PowerShell.

> [!NOTE]
> Metakonfigurace DSC obsahují tajné klíče, které potřebujete pro připojení počítače k účtu automatizace pro správu. Ujistěte se, že správně chránit všechny metakonfigurace DSC, které vytvoříte, nebo je odstranit po použití.

Podpora proxy pro metakonfigurace je řízena LCM, což je modul Windows PowerShell DSC. LCM běží na všech cílových uzlech a je zodpovědný za volání konfiguračních prostředků, které jsou zahrnuty ve skriptu metakonfigurace DSC. 

Podporu proxy serveru můžete zahrnout do metakonfigurace zahrnutím definic adresy URL `ConfigurationRepositoryWeb`proxy `ResourceRepositoryWeb`a `ReportServerWeb` pověření proxy podle potřeby v blocích , a blocích. Viz [Konfigurace správce místní konfigurace](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-by-using-a-dsc-configuration"></a>Generovat metakonfigurace DSC pomocí konfigurace DSC

1. Otevřete visual studio kód (nebo váš oblíbený editor) jako správce na počítači v místním prostředí. Počítač musí mít nainstalovanou nejnovější verzi [rozhraní Windows Management Framework 5.](https://aka.ms/wmf5latest)
1. Zkopírujte následující skript místně. Tento skript obsahuje konfiguraci Prostředí PowerShell DSC pro vytváření metakonfigurací a příkaz pro zahájení vytvoření metakonfigurace.

    > [!NOTE]
    > Názvy konfigurace uzlu konfigurace stavu jsou rozlišování velkých a malých písmen na webu Azure Portal. Pokud je případ neodpovídající, uzel se nezobrazí na kartě **Uzly.**

   ```powershell
   # The DSC configuration that will generate metaconfigurations.
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

    # Create the metaconfigurations.
    # NOTE: DSC Node Configuration names are case sensitive in the portal.
    # TODO: Edit the following as needed for your use case.
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

   # Use PowerShell splatting to pass parameters to the DSC configuration being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   DscMetaConfigs @Params
   ```

1. Vyplňte registrační klíč a adresu URL svého účtu Automation a také názvy zařízení na palubě. Všechny ostatní parametry jsou volitelné. Pokud chcete najít registrační klíč a registrační adresu URL pro svůj účet Automation, podívejte se [na bezpečné zabezpečení na palubě pomocí registrační](#onboard-securely-by-using-registration) sekce.

1. Pokud chcete, aby počítače oznamovaly informace o stavu DSC do konfigurace `ReportOnly` stavu automatizace Azure, ale ne vyžádat konfiguraci nebo moduly Prostředí PowerShell, nastavte parametr na *hodnotu true*.

1. Pokud `ReportOnly` není nastavena, počítače hlásí informace o stavu DSC konfigurace stavu automatizace Azure a konfigurace vyžádat nebo moduly PowerShell. Nastavte parametry odpovídajícím `ConfigurationRepositoryWeb`způsobem `ResourceRepositoryWeb`v `ReportServerWeb` , a bloky.

1. Spusťte skript. Nyní byste měli mít pracovní adresář s názvem *DscMetaConfigs*, který obsahuje metakonfigurace Prostředí PowerShell DSC pro počítače na palubě (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-by-using-azure-automation-cmdlets"></a>Generování metakonfigurací DSC pomocí rutin Azure Automation

Pokud výchozí hodnoty PowerShell DSC LCM odpovídají vašemu případu použití a chcete napalubě počítače navádět se z konfigurace stavu automatizace Azure a sestavy, můžete vygenerovat potřebné metakonfigurace DSC jednodušeji pomocí rutin Azure Automation.

1. Otevřete konzolu PowerShellnebo kód sady Visual Studio jako správce počítače v místním prostředí.
1. Připojte se ke Správci prostředků Azure pomocí [connect-azaccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
1. Z účtu Automatizace, ve kterém nastavujete uzly, stáhněte metakonfigurace PowerShell DSC pro počítače, které chcete na palubě.

   ```powershell
   # Define the parameters for Get-AzAutomationDscOnboardingMetaconfig using PowerShell Splatting.
   $Params = @{
       ResourceGroupName = 'ContosoResources'; # The name of the Resource Group that contains your Azure Automation account
       AutomationAccountName = 'ContosoAutomation'; # The name of the Azure Automation account where you want a node on-boarded to
       ComputerName = @('web01', 'web02', 'sql01'); # The names of the computers that the metaconfiguration will be generated for
       OutputFolder = "$env:UserProfile\Desktop\";
   }
   # Use PowerShell splatting to pass parameters to the Azure Automation cmdlet being invoked.
   # For more info about splatting, run: Get-Help -Name about_Splatting.
   Get-AzAutomationDscOnboardingMetaconfig @Params
   ```

1. Nyní byste měli mít složku *DscMetaConfigs,* která obsahuje metakonfigurace DSC prostředí PowerShell pro počítače, které chcete na palubě (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboard-securely-by-using-registration"></a>Bezpečně napalubě pomocí registrace

Počítače lze bezpečně založit k účtu Azure Automation prostřednictvím registračního protokolu Windows Management Framework 5 DSC. Tento protokol umožňuje uzlu DSC k ověření na server vyžádat nebo sestavy Prostředí PowerShell DSC, včetně konfigurace stavu automatizace Azure. Uzel se zaregistruje na serveru na adrese URL registrace a ověří se pomocí registračního klíče. 

Během registrace uzel DSC a server vyžádat vyžádat/sestav DSC vyjednat jedinečný certifikát pro uzel použít pro ověřování na server post-registrace. Tento proces zabraňuje na palubě uzly z osobit jeden druhého (například pokud uzel je ohrožena a chová se zlomyslně). 

Po registraci registrační klíč není znovu použit pro ověřování a je odstraněn z uzlu.

Informace, které jsou požadovány pro registrační protokol konfigurace státu, získáte na webu Azure portal a v části **Nastavení účtu**vyberte **Položku Klíče**. 

![Klíče a adresa URL azure automatizace v podokně Klíče](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- **Adresa URL registrace**: Hodnota v poli **URL.**
- **Registrační klíč**: Hodnota v poli **Primární přístupový klíč** nebo V poli **sekundárního přístupového klíče.** Můžete použít buď klíč.

Pro zvýšení zabezpečení můžete kdykoli v podokně **Klíče** obnovit primární a sekundární přístupové klíče účtu automation. Regenerace klíčů zabraňuje budoucím registracím uzlů používat předchozí klíče.

## <a name="re-register-a-node"></a>Opětovná registrace uzlu

Po registraci počítače jako uzlu DSC v konfiguraci stavu automatizace Azure, budete muset znovu zaregistrovat tento uzel v budoucnu z některého z následujících důvodů:

- **Obnovení certifikátu**: U verzí systému Windows Server starších než Windows Server 2019 každý uzel automaticky vyjedná jedinečný certifikát pro ověřování, jehož platnost vyprší po jednom roce. Pokud vyprší platnost certifikátu bez obnovení, uzel nemůže komunikovat s Azure Automation a je označen jako *nereagující*. 

  V současné době powershellový protokol registrace DSC nemůže automaticky obnovit certifikáty, když se blíží vypršení jejich platnosti a je nutné znovu zaregistrovat uzly po roce. Před opětovnou registrací se ujistěte, že v každém uzlu je spuštěna architektura Windows Management Framework 5 RTM. 

    Opětovná registrace provedena 90 nebo méně dní od vypršení platnosti certifikátu nebo kdykoli po vypršení platnosti certifikátu, což vede k generování a použití nového certifikátu. Řešení tohoto problému je součástí Windows Server 2019 a novější.

- **Změny hodnot DSC LCM**: Možná budete muset změnit [hodnoty LCM prostředí PowerShell DSC,](/powershell/scripting/dsc/managing-nodes/metaConfig4) `ConfigurationMode`které byly nastaveny při počáteční registraci uzlu (například). V současné době můžete změnit tyto hodnoty agenta DSC pouze prostřednictvím opětovné registrace. Jedinou výjimkou je hodnota konfigurace uzlu přiřazená uzlu. Tuto hodnotu můžete změnit přímo v Azure Automation DSC.

Uzel můžete znovu zaregistrovat tak, jak jste jej původně zaregistrovali, pomocí některé z metod registrace, které jsou popsány v tomto dokumentu. Před opětovnou registrací není nutné zrušit registraci uzlu z konfigurace stavu azure automatizace.

## <a name="troubleshoot-azure-virtual-machine-onboarding"></a>Poradce při potížích s připisováním virtuálního počítače Azure

Konfigurace stavu Azure Automation umožňuje snadno založit virtuální počítače Azure windows pro správu konfigurace. Pod kapotou rozšíření konfigurace požadovaného stavu virtuálního počítače Azure se používá k registraci virtuálního počítače s azure automation state configuration. Vzhledem k tomu, že rozšíření konfigurace požadovaného stavu virtuálního počítače Azure běží asynchronně, sledování jeho průběhu a řešení potíží s jeho spuštěním může být důležité.

> [!NOTE]
> Jakákoli metoda registrace virtuálního počítače Azure Windows na konfiguraci stavu automatizace Azure, která používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, může trvat až hodinu, než azure automation zobrazí jako registrovaný. Toto zpoždění vyplývá z instalace rozhraní Windows Management Framework 5 na virtuálním počítači rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, které je nutné k napalubě konfigurace stavu virtuálního počítače do azure automatizace stavu.

Řešení potíží nebo zobrazení stavu rozšíření Konfigurace požadovaného stavu virtuálního počítače Azure:

1. Na webu Azure Portal přejděte na virtuální počítač, který se na palubě.
2. V části **Nastavení** vyberte **Rozšíření**. 
3. V závislosti na operačním systému vyberte **DSC** nebo **DSCForLinux**. 
4. Další informace o rozšíření získáte výběrem možnosti **Zobrazit podrobný stav**.

Další informace o řešení potíží najdete [v tématu Řešení problémů s konfigurací požadovaného stavu Azure Automation (DSC).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Další kroky

- Další informace najdete v tématu [Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete [v tématu průběžné nasazení do virtuálních počítačů pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).
