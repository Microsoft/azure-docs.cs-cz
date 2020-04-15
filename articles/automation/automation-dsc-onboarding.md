---
title: Onboardingové počítače pro správu pomocí konfigurace stavu azure automatizace
description: Jak nastavit počítače pro správu pomocí konfigurace stavu automatizace Azure
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: c718b9a66b378044618c8c52eec3a1a498ace83c
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81383210"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Onboardingové počítače pro správu pomocí konfigurace stavu azure automatizace

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Proč spravovat počítače s azure automation state configuration?

Azure Automation State Configuration je služba správy konfigurace pro uzly konfigurace požadovaného stavu (DSC) v libovolném cloudu nebo místním datovém centru. K přístupu na webu Azure Portal je výběrem **konfigurace stavu (DSC)** v části **Configuration Management**. 

Tato služba umožňuje škálovatelnost napříč tisíci počítači rychle a snadno z centrálního a zabezpečeného umístění. Můžete snadno napalubě počítačů, přiřadit jim deklarativní konfigurace a zobrazit sestavy zobrazující dodržování požadavků každého počítače s požadovaným stavem, který zadáte.

Služba Konfigurace stavu azure automatizace je DSC, co azure automatizace runbooky jsou skriptování PowerShell. Jinými slovy, stejným způsobem, že Azure Automation pomáhá spravovat skripty PowerShell, ale také vám pomůže spravovat konfigurace DSC. Další informace o výhodách používání konfigurace stavu azure automatizace najdete v [tématu Přehled konfigurace stavu automatizace Azure](automation-dsc-overview.md).

Konfigurace stavu azure automatizace slouží ke správě různých počítačů:

- Virtuální počítače Azure
- Virtuální počítače Azure (klasické)
- Fyzické/virtuální počítače s Windows v místním prostředí nebo v cloudu jiném než Azure (včetně instancí AWS EC2)
- Fyzické/virtuální počítače s Linuxem v místním prostředí, v Azure nebo v cloudu jiném než Azure

Pokud nejste připraveni ke správě konfigurace počítače z cloudu, můžete použít Azure Automation State Configuration jako koncový bod pouze sestavy. Tato funkce umožňuje nastavit (push) konfigurace prostřednictvím DSC a zobrazit podrobnosti sestavy v Azure Automation.

> [!NOTE]
> Správa virtuálních počítačů Azure s Azure Automation State Configuration je zahrnuta bez příplatku, pokud je nainstalovaná verze rozšíření konfigurace požadovaného stavu virtuálního počítače Azure větší než 2.70. Další informace naleznete na [**stránce S cenami automatizace**](https://azure.microsoft.com/pricing/details/automation/).

Následující části tohoto článku popisují, jak můžete na palubě počítačů uvedených výše konfigurace stavu Azure Automation.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci modulu AZ na pracovníka hybridní sady Runbook najdete [v tématu Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). U vašeho účtu Automation můžete aktualizovat moduly na nejnovější verzi pomocí [funkce Jak aktualizovat moduly Azure PowerShellu v Azure Automation](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Registrace virtuálních počítačů Azure

Konfigurace stavu Azure Automation umožňuje snadno založit virtuální počítače Azure pro správu konfigurace pomocí portálu Azure, šablon Azure Resource Manager nebo PowerShellu. Pod kapotou a bez správce museli vzdálené do virtuálního počítače, rozšíření konfigurace požadovaného stavu virtuálního počítače Azure zaregistruje virtuální počítač s Azure Automation state Configuration. Vzhledem k tomu, že rozšíření Azure běží asynchronně, kroky ke sledování jeho průběhu nebo řešení potíží jsou k dispozici v [části Řešení potíží s virtuálním počítačem Azure onboarding](#troubleshooting-azure-virtual-machine-onboarding) části tohoto článku.

> [!NOTE]
>Nasazení DSC do uzlu Linuxu používá složku **/tmp.** Moduly, `nxautomation` jako jsou dočasně staženy k ověření před jejich instalací v příslušných umístěních. Chcete-li zajistit, aby moduly nainstalovat správně, agent Log Analytics pro Linux potřebuje oprávnění ke čtení a zápisu ve složce **/tmp.**<br><br>
>Agent Log Analytics pro Linux `omsagent` běží jako uživatel. Chcete-li uživateli `omsagent` udělit oprávnění `setfacl -m u:omsagent:rwx /tmp`k zápisu >, spusťte příkaz .

### <a name="onboard-a-vm-using-azure-portal"></a>Napalubí virtuálního počítače pomocí portálu Azure

Chcete-li napalubě konfigurace stavu virtuálního počítače Azure k Azure automation prostřednictvím [portálu Azure:](https://portal.azure.com/)

1. Přejděte na účet Azure Automation, ve kterém chcete napalubě virtuálních počítačích. 

2. Na stránce Konfigurace stavu vyberte kartu **Uzly a** klikněte na **Přidat**.

3. Zvolte virtuální počítač na palubě.

4. Pokud v zařízení není nainstalováno rozšíření požadovaného stavu prostředí PowerShell a je spuštěný stav napájení, klepněte na tlačítko **Připojit**.

5. V **části Registrace**zadejte hodnoty Nástroje pro místní [konfiguraci prostředí PowerShell DSC, které](/powershell/scripting/dsc/managing-nodes/metaConfig) jsou vyžadovány pro případ použití. Volitelně můžete zadat konfiguraci uzlu, kterou chcete přiřadit k virtuálnímu počítače.

![registrace](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Napalubí virtuálního počítače pomocí šablon Azure Resource Manager

Můžete nasadit a napalubě konfigurace stavu virtuálního počítače do Azure Automation state configuration pomocí šablon Azure Resource Manager. Viz [Server spravované službou Konfigurace požadovaného stavu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) pro ukázkovou šablonu, která zapisuje existující konfiguraci stavu virtuálního počítače do konfigurace stavu automatizace Azure. Pokud spravujete škálovací sadu virtuálních počítačů, podívejte se na ukázkovou šablonu v [konfiguraci škálovací sady virtuálních počítačů spravované službou Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Palubní počítače používající Prostředí PowerShell

Rutina [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) v prostředí PowerShell můžete použít k nazabezpečení virtuálních počítačů do konfigurace stavu automatizace Azure. 

> [!NOTE]
>Rutina `Register-AzAutomationDscNode` je implementována v současné době pouze pro počítače se systémem Windows, protože aktivuje pouze rozšíření systému Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrace virtuálních počítačů napříč předplatnými Azure

Nejlepší způsob, jak zaregistrovat virtuální počítače z jiných předplatných Azure, je použít rozšíření DSC v šabloně nasazení Azure Resource Manageru. Příklady jsou k dispozici v [rozšíření Konfigurace požadovaného stavu se šablonami Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Chcete-li najít registrační klíč a registrační adresu URL, které chcete použít jako parametry v šabloně, přečtěte si část [Onboarding bezpečně pomocí registrační](#onboarding-securely-using-registration) části v tomto článku.

## <a name="onboarding-physicalvirtual-windows-machines-on-premises-or-in-a-cloud-other-than-azure-including-aws-ec2-instances"></a>Registrace fyzických/virtuálních počítačů s Windows v místním prostředí nebo v cloudu jiném než Azure (včetně instancí AWS EC2)

K konfiguraci stavu Azure Automation můžete napalubě serverů Windows spuštěných místně nebo v jiných cloudových prostředích. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že nejnovější verze [WMF 5](https://aka.ms/wmf5latest) je nainstalována na počítačích na palubě konfigurace stavu azure automatizace. Kromě toho musí být wmf 5 nainstalován v počítači, který používáte pro operaci připojení.
1. Postupujte podle pokynů v části [Generování metakonfigurací DSC](#generating-dsc-metaconfigurations) a vytvořte složku obsahující požadované metakonfigurace DSC. 
1. Pomocí následující rutiny můžete metakonfigurace prostředí PowerShell DSC vzdáleně použít na počítače, které chcete napalubě. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Pokud nelze použít metakonfigurace Prostředí PowerShell DSC vzdáleně, zkopírujte složku **metakonfigurace** do počítačů, které se zapisujete. Pak přidejte `Set-DscLocalConfigurationManager` kód pro místní volání na počítačích.
1. Pomocí portálu Azure nebo rutiny ověřte, že počítače na palubě se zobrazí jako uzly konfigurace stavu registrované ve vašem účtu Azure Automation.

## <a name="onboarding-physicalvirtual-linux-machines-on-premises-or-in-a-cloud-other-than-azure"></a>Registrace fyzických/virtuálních počítačů s Linuxem v místním prostředí nebo v jiném cloudu než v Azure

Do konfigurace stavu Azure Automation můžete napalubě linuxových serverů spuštěných místně nebo v jiných cloudových prostředích. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že nejnovější verze [konfigurace požadovaného stavu Prostředí PowerShell pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) je nainstalovaná na počítačích na konfigurací do konfigurace stavu azure automatizace.
2. Pokud [výchozí nastavení správce místní konfigurace prostředí PowerShell DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) odpovídá vašemu případu použití a chcete napalubě počítačů tak, aby oba natahovaly z konfigurace stavu automatizace Azure a hlásily se k ní:

   - Na každém počítači s Linuxem na `Register.py` konfiguraci do konfigurace stavu Azure Automation můžete použít připojení pomocí výchozích hodnot Správce místní konfigurace prostředí PowerShell DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Pokud chcete najít registrační klíč a registrační adresu URL pro svůj účet Automation, přečtěte si článek [Bezpečné připojení pomocí registrace](#onboarding-securely-using-registration) v tomto článku.

3. Pokud výchozí hodnoty PowerShell DSC Local Configuration Manager (LCM) neodpovídají vašemu případu použití nebo chcete založit počítače, které se hlásí jenom do konfigurace stavu azure automatizace, postupujte podle kroků 4-7. V opačném případě pokračujte přímo ke kroku 7.

4. Postupujte podle pokynů v části [Generování metakonfigurací DSC](#generating-dsc-metaconfigurations) a vyrobte složku obsahující požadované metakonfigurace dsc.

5. Ujistěte se, že je v počítači používaném pro přivádění nainstalována nejnovější verze [wmf 5.](https://aka.ms/wmf5latest)

6. Přidejte kód takto, chcete-li použít metakonfigurace Prostředí DSC prostředí PowerShell vzdáleně na počítače, které chcete na palubě.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Pokud nelze použít metakonfigurace Prostředí PowerShell DSC vzdáleně, zkopírujte metakonfigurace odpovídající vzdáleným počítačům ze složky popsané v kroku 4 do počítačů s Linuxem.

8. Přidejte kód `Set-DscLocalConfigurationManager.py` pro místní volání na každém počítači s Linuxem do palubní konfigurace azure automation stavu.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Pomocí portálu Azure nebo rutiny, ujistěte se, že počítače na palubě teď zobrazí jako dsc uzly registrované ve vašem účtu Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generování metakonfigurací DSC

Chcete-li zavést libovolný počítač do konfigurace stavu automatizace Azure, můžete vygenerovat [metakonfiguraci DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Tato konfigurace říká agentdsc navádět nebo sestavy konfigurace stavu automatizace Azure. Metakonfigurace DSC pro konfiguraci stavu automatizace Azure můžete vygenerovat buď pomocí konfigurace DSC prostředí PowerShell, nebo pomocí rutin Prostředí PowerShell azure automation.

> [!NOTE]
> Metakonfigurace DSC obsahují tajné kódy potřebné k nazabezpečení počítače k účtu automatizace pro správu. Ujistěte se, že správně chránit všechny metakonfigurace DSC, které vytvoříte, nebo je odstranit po použití.

Podpora proxy pro metakonfigurace je řízena LCM, což je modul Windows PowerShell DSC. LCM běží na všech cílových uzlech a je zodpovědný za volání konfiguračních prostředků, které jsou zahrnuty ve skriptu metakonfigurace DSC. Podporu proxy serveru můžete zahrnout do metakonfigurace zahrnutím definic adresy URL `ConfigurationRepositoryWeb`proxy `ResourceRepositoryWeb`a `ReportServerWeb` pověření proxy podle potřeby v blocích , a blocích. Viz [Konfigurace správce místní konfigurace](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

### <a name="generate-dsc-metaconfigurations-using-a-dsc-configuration"></a>Generování metakonfigurací DSC pomocí konfigurace DSC

1. Otevřete VSCode (nebo váš oblíbený editor) jako správce na počítači v místním prostředí. Počítač musí mít nainstalovanou nejnovější verzi [WMF 5.](https://aka.ms/wmf5latest)
1. Zkopírujte následující skript místně. Tento skript obsahuje konfiguraci Prostředí PowerShell DSC pro vytváření metakonfigurací a příkaz pro zahájení vytvoření metakonfigurace.

    > [!NOTE]
    > Názvy konfigurace uzlu konfigurace stavu jsou rozlišování velkých a malých písmen na webu Azure Portal. Pokud je případ neodpovídající, uzel se nezobrazí na kartě **Uzly.**

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

1. Vyplňte registrační klíč a adresu URL svého účtu Automation a také názvy zařízení na palubě. Všechny ostatní parametry jsou volitelné. Pokud chcete najít registrační klíč a registrační adresu URL svého účtu Automation, přečtěte si část [Bezpečné připojení pomocí registrace.](#onboarding-securely-using-registration)

1. Pokud chcete, aby počítače oznamovaly informace o stavu DSC do konfigurace `ReportOnly` stavu automatizace Azure, ale ne vyžádat konfiguraci nebo moduly Prostředí PowerShell, nastavte parametr na hodnotu true.

1. Pokud `ReportOnly` není nastavena, počítače hlásí informace o stavu DSC konfigurace stavu automatizace Azure a konfigurace vyžádat nebo moduly PowerShell. Nastavte parametry odpovídajícím `ConfigurationRepositoryWeb`způsobem `ResourceRepositoryWeb`v `ReportServerWeb` , a bloky.

1. Spusťte skript. Nyní byste měli mít pracovní adresář s názvem **DscMetaConfigs**, obsahující metakonfigurace Prostředí DSC pro počítače na palubě (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generování metakonfigurací DSC pomocí rutin Azure Automation

Pokud výchozí hodnoty PowerShell DSC LCM odpovídají vašemu případu použití a chcete napalubě počítače navádět se z konfigurace stavu automatizace Azure a sestavy, můžete vygenerovat potřebné metakonfigurace DSC jednodušeji pomocí rutin Azure Automation.

1. Otevřete konzolu PowerShell nebo VSCode jako správce počítače v místním prostředí.
2. Připojte se ke Správci prostředků Azure pomocí [connect-azaccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Stáhněte si metakonfigurace Prostředí PowerShell DSC pro počítače, které chcete nastoupit z účtu Automatizace, ve kterém nastavujete uzly.

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

1. Nyní byste měli mít složku **DscMetaConfigs** obsahující metakonfigurace Prostředí DSC prostředí Pro počítače na palubě (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Bezpečné zapisování pomocí registrace

Počítače můžou bezpečně nastoupit na účet Azure Automation prostřednictvím registračního protokolu WMF 5 DSC. Tento protokol umožňuje uzlu DSC k ověření na server vyžádat nebo sestavy Prostředí PowerShell DSC, včetně konfigurace stavu automatizace Azure. Uzel se zaregistruje na serveru na registrační adrese URL a ověří se pomocí registračního klíče. Během registrace uzel DSC a server vyžádat vyžádat a sestav DSC vyjednat jedinečný certifikát pro uzel použít pro ověřování na serveru po registraci. Tento proces zabraňuje na palubě uzly z osobit jeden druhého, například v případě, že uzel je ohrožena a chová se zlomyslně. Po registraci registrační klíč není použit pro ověřování znovu a je odstraněn z uzlu.

Informace požadované pro registrační protokol konfigurace stavu můžete získat z **klíčů** v části **Nastavení účtu** na webu Azure Portal. 

![Klíče a adresa URL azure automatizace](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Registrační adresa URL je pole URL na stránce Klíče.
- Registrační klíč je hodnota pole **Primární přístupový klíč** nebo pole **Sekundární přístupový klíč** na stránce Klíče. Lze použít buď klíč.

Pro zvýšení zabezpečení můžete kdykoli na stránce Klíče znovu vygenerovat primární a sekundární přístupové klíče účtu Automation. Regenerace klíčů zabraňuje budoucím registracím uzlů používat předchozí klíče.

## <a name="re-registering-a-node"></a>Opětovná registrace uzlu

Po registraci počítače jako uzlu DSC v konfiguraci stavu automatizace Azure, existuje několik důvodů, proč budete muset znovu zaregistrovat tento uzel v budoucnu.

- **Obnovení certifikátu.** U verzí systému Windows Server před systémem Windows Server 2019 každý uzel automaticky vyjedná jedinečný certifikát pro ověřování, jehož platnost vyprší po jednom roce. Pokud vyprší platnost certifikátu bez obnovení, uzel nemůže komunikovat s `Unresponsive`Azure Automation a je označen . V současné době powershellový protokol registrace DSC nemůže automaticky obnovit certifikáty, když se blíží vypršení jejich platnosti a je nutné znovu zaregistrovat uzly po roce. Před opětovnou registrací se ujistěte, že každý uzel je spuštěn WMF 5 RTM. 

    Opětovná registrace provedená 90 dnů nebo méně od vypršení platnosti certifikátu nebo kdykoli po vypršení platnosti certifikátu vede k generování a použití nového certifikátu. Řešení tohoto problému je součástí Windows Server 2019 a novější.

- **Změny hodnot DSC LCM.** Možná budete muset změnit [hodnoty LCM prostředí PowerShell,](/powershell/scripting/dsc/managing-nodes/metaConfig4) které jsou nastaveny `ConfigurationMode`při počáteční registraci uzlu, například . V současné době můžete změnit pouze tyto hodnoty agenta DSC prostřednictvím opětovné registrace. Jedinou výjimkou je hodnota konfigurace uzlu přiřazená uzlu. Můžete to změnit v Azure Automation DSC přímo.

Uzel můžete znovu zaregistrovat stejně jako původně registrovaný uzel pomocí některé z metod registrace popsaných v tomto dokumentu. Před opětovnou registrací není nutné zrušit registraci uzlu z konfigurace stavu automatizace Azure.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Řešení potíží s připnutím virtuálního počítače Azure

Konfigurace stavu Azure Automation umožňuje snadno založit virtuální počítače Azure windows pro správu konfigurace. Pod kapotou rozšíření konfigurace požadovaného stavu virtuálního počítače Azure se používá k registraci virtuálního počítače s azure automation state configuration. Vzhledem k tomu, že rozšíření konfigurace požadovaného stavu virtuálního počítače Azure běží asynchronně, sledování jeho průběhu a řešení potíží s jeho spuštěním může být důležité.

> [!NOTE]
> Jakákoli metoda registrace virtuálního počítače Azure Windows na konfiguraci stavu automatizace Azure, která používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, může trvat až hodinu, než azure automation zobrazí jako registrovaný. Toto zpoždění je způsobeno instalací WMF 5 na virtuálním počítači pomocí rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, které je nutné k nazabezpečení konfigurace stavu virtuálního počítače do azure automation.

Řešení potíží nebo zobrazení stavu rozšíření Konfigurace požadovaného stavu virtuálního počítače Azure:

1. Na webu Azure Portal přejděte na virtuální počítač, který je na palubě.
2. V části **Nastavení**klepněte na **položku Rozšíření** . 
3. Nyní vyberte **DSC** nebo **DSCForLinux**, v závislosti na operačním systému. 
4. Další podrobnosti získáte klepnutím na tlačítko **Zobrazit podrobný stav**.

Další informace o řešení potíží najdete [v tématu řešení potíží s konfigurací požadovaného stavu Azure Automation (DSC).](./troubleshoot/desired-state-configuration.md)

## <a name="next-steps"></a>Další kroky

- Další informace najdete [v tématu Začínáme s azure automation state configuration](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit k cílovým uzlům, najdete [v tématu Kompilace konfigurací v konfiguraci stavu automatizace Azure](automation-dsc-compile.md).
- Odkaz na rutinu prostředí PowerShell naleznete v tématu [Az.Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Informace o cenách najdete v [tématu Ceny konfigurace stavu Azure Automation](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu azure automatizace v kanálu průběžného nasazení najdete v [tématu Příklad použití: Průběžné nasazení do virtuálních počítačů pomocí konfigurace stavu automatizace Azure a Chocolatey](automation-dsc-cd-chocolatey.md).
