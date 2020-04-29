---
title: Připojování počítačů pro správu podle konfigurace stavu Azure Automation
description: Nastavení počítačů pro správu pomocí konfigurace stavu Azure Automation
services: automation
ms.service: automation
ms.subservice: dsc
author: mgoedtel
ms.author: magoedte
ms.topic: conceptual
ms.date: 12/10/2019
manager: carmonm
ms.openlocfilehash: d4e008a0bd43f10b01d78a1c388f1ca6fee983ab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81457735"
---
# <a name="onboarding-machines-for-management-by-azure-automation-state-configuration"></a>Připojování počítačů pro správu podle konfigurace stavu Azure Automation

## <a name="why-manage-machines-with-azure-automation-state-configuration"></a>Proč spravovat počítače s konfigurací stavu Azure Automation?

Konfigurace stavu Azure Automation je služba správy konfigurace pro požadované uzly Konfigurace stavu (DSC) v jakémkoli cloudu nebo v místním datacentru. V Azure Portal k němu v části **Správa konfigurace**vyberte **Konfigurace stavu (DSC)** . 

Tato služba umožňuje snadno a rychle využít škálovatelnost v tisících počítačů z centrálního a bezpečného umístění. Můžete snadno připojit počítače, přiřazovat jim deklarativní konfigurace a zobrazovat sestavy, které zobrazují kompatibilitu jednotlivých počítačů s požadovaným stavem, který zadáte.

Služba konfigurace stavu Azure Automation je DSC, které Azure Automation Runbooky využívají ke skriptování prostředí PowerShell. Jinými slovy, a to stejným způsobem, jakým Azure Automation pomáhá spravovat skripty prostředí PowerShell, vám také pomůže spravovat konfigurace DSC. Další informace o výhodách použití konfigurace stavu Azure Automation najdete v tématu [Přehled konfigurace stavu Azure Automation](automation-dsc-overview.md).

Konfigurace stavu Azure Automation se dá použít ke správě různých počítačů:

- Virtuální počítače Azure
- Virtuální počítače Azure (klasické)
- Fyzické nebo virtuální počítače s Windows v místním prostředí nebo v jiném cloudu než Azure (včetně instancí AWS EC2)
- Fyzické nebo virtuální počítače se systémem Linux v místním prostředí, v Azure nebo v jiném cloudu než Azure

Pokud nejste připraveni ke správě konfigurace počítače z cloudu, můžete použít konfiguraci Azure Automationho stavu jako koncový bod pouze sestavy. Tato funkce umožňuje nastavení (push) konfigurací prostřednictvím DSC a zobrazení podrobností o vytváření sestav v Azure Automation.

> [!NOTE]
> Správa virtuálních počítačů Azure s konfigurací stavu Azure Automation je zahrnutá bez dalších poplatků, pokud je nainstalovaná verze rozšíření konfigurace požadovaného stavu virtuálního počítače Azure větší než 2,70. Další informace najdete na [**stránce s cenami pro automatizaci**](https://azure.microsoft.com/pricing/details/automation/).

V následujících částech tohoto článku se naučíte, jak můžete připojit počítače uvedené výše k Azure Automation konfigurace stavu.

>[!NOTE]
>Tento článek je aktualizovaný a využívá nový modul Az Azure PowerShellu. Můžete dál využívat modul AzureRM, který bude dostávat opravy chyb nejméně do prosince 2020. Další informace o kompatibilitě nového modulu Az a modulu AzureRM najdete v tématu [Seznámení s novým modulem Az Azure PowerShellu](https://docs.microsoft.com/powershell/azure/new-azureps-module-az?view=azps-3.5.0). Pokyny k instalaci nástroje AZ Module Hybrid Runbook Worker najdete v tématu [Instalace modulu Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-3.5.0). Pro váš účet Automation můžete aktualizovat moduly na nejnovější verzi pomocí [postupu aktualizace modulů Azure PowerShell v Azure Automation](automation-update-azure-modules.md).

## <a name="onboarding-azure-vms"></a>Připojování virtuálních počítačů Azure

Konfigurace stavu Azure Automation umožňuje snadno připojit virtuální počítače Azure ke správě konfigurace pomocí Azure Portal, šablon Azure Resource Manager nebo PowerShellu. V digestoři a bez správce, který se musí vzdáleně přidružit k virtuálnímu počítači, registruje rozšíření konfigurace požadovaného stavu virtuálního počítače Azure konfiguraci stavu Azure Automation. Vzhledem k tomu, že se rozšíření Azure spouští asynchronně, postup sledování jeho průběhu nebo odstraňování potíží je k dispozici v části [řešení potíží s virtuálním počítačem Azure](#troubleshooting-azure-virtual-machine-onboarding) v tomto článku.

> [!NOTE]
>Nasazení DSC na uzel Linux používá složku **adresáře/TMP** . Moduly, jako `nxautomation` jsou dočasně staženy k ověření před jejich instalací do příslušných umístění. Aby se zajistilo, že se moduly správně nainstalují, musí mít agent Log Analytics pro Linux oprávnění ke čtení a zápisu ve složce **adresáře/TMP** .<br><br>
>Agent Log Analytics pro Linux se spouští jako `omsagent` uživatel. Pokud chcete `omsagent` uživateli udělit oprávnění k zápisu >, spusťte příkaz `setfacl -m u:omsagent:rwx /tmp`.

### <a name="onboard-a-vm-using-azure-portal"></a>Připojení virtuálního počítače pomocí Azure Portal

Připojení virtuálního počítače Azure k Azure Automation konfiguraci stavu prostřednictvím [Azure Portal](https://portal.azure.com/):

1. Přejděte na účet Azure Automation, ve kterém se budou virtuální počítače připojit. 

2. Na stránce Konfigurace stavu vyberte kartu **uzly** a pak klikněte na **Přidat**.

3. Vyberte virtuální počítač, který se má připojit.

4. Pokud na počítači není nainstalovaná požadovaná rozšíření stavu prostředí PowerShell a stav napájení je spuštěno, klikněte na **připojit**.

5. V části **registrace**zadejte [hodnoty pro místní Configuration Manager PowerShellu DSC](/powershell/scripting/dsc/managing-nodes/metaConfig) vyžadované pro váš případ použití. Volitelně můžete zadat konfiguraci uzlu, která se přiřadí k virtuálnímu počítači.

![registrace](./media/automation-dsc-onboarding/DSC_Onboarding_6.png)

### <a name="onboard-a-vm-using-azure-resource-manager-templates"></a>Připojení virtuálního počítače pomocí šablon Azure Resource Manager

Pomocí šablon Azure Resource Manager můžete nasadit a připojit virtuální počítač k Azure Automation konfigurace stavu. Příklad šablony, která připojí existující virtuální počítač k Azure Automation konfiguraci stavu, najdete v části [Server spravovaný službou pro konfiguraci požadovaného stavu](https://azure.microsoft.com/resources/templates/101-automation-configuration/) . Pokud spravujete sadu škálování virtuálního počítače, podívejte se na příklad šablony v [konfiguraci sady škálování virtuálního počítače spravované pomocí Azure Automation](https://azure.microsoft.com/resources/templates/201-vmss-automation-dsc/).

### <a name="onboard-machines-using-powershell"></a>Zprovoznění počítačů pomocí PowerShellu

Pomocí rutiny [Register-AzAutomationDscNode](/powershell/module/az.automation/register-azautomationdscnode) v PowerShellu můžete připojit virtuální počítače do konfigurace stavu Azure Automation. 

> [!NOTE]
>Rutina je implementována v `Register-AzAutomationDscNode` současné době pouze pro počítače s Windows, protože aktivuje pouze rozšíření systému Windows.

### <a name="register-vms-across-azure-subscriptions"></a>Registrace virtuálních počítačů napříč předplatnými Azure

Nejlepším způsobem, jak registrovat virtuální počítače z jiných předplatných Azure, je použít rozšíření DSC v šabloně nasazení Azure Resource Manager. Příklady jsou k dispozici v [rozšíření konfigurace požadovaného stavu pomocí šablon Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/extensions/dsc-template).

Pokud chcete najít registrační klíč a registrační adresu URL, které se mají použít jako parametry v šabloně, podívejte se do části [zabezpečené používáním registrace](#onboarding-securely-using-registration) v tomto článku.

## <a name="onboarding-physicalvirtual-windows-machines"></a>Připojování fyzických a virtuálních počítačů s Windows

Můžete připojit servery Windows spuštěné místně nebo v jiných cloudových prostředích (včetně instancí AWS EC2), aby se konfigurace stavu Azure Automation. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Ujistěte se, že je na počítačích nainstalovaných nejnovější verze [WMF 5](https://aka.ms/wmf5latest) , aby bylo možné konfigurovat stav Azure Automation. Kromě toho musí být na počítači, který používáte pro operaci zprovoznění, nainstalovaná aplikace WMF 5.
1. Postupujte podle pokynů v části [generování DSC metaconfigurations](#generating-dsc-metaconfigurations) a vytvořte složku, která obsahuje požadované metaconfigurations DSC. 
1. Pomocí následující rutiny použijte vzdálenou aplikaci PowerShell DSC metaconfigurations na počítače, které chcete připojit. 

   ```powershell
   Set-DscLocalConfigurationManager -Path C:\Users\joe\Desktop\DscMetaConfigs -ComputerName MyServer1, MyServer2
   ```

1. Pokud nemůžete použít metaconfigurations prostředí PowerShell DSC vzdáleně, zkopírujte složku **metaconfigurations** na počítače, které se chystáte připojit. Pak přidejte kód pro volání [set-DscLocalConfigurationManager](https://docs.microsoft.com/powershell/module/psdesiredstateconfiguration/set-dsclocalconfigurationmanager?view=powershell-5.1) místně na počítačích.
1. Pomocí rutin Azure Portal a ověřte, že se počítače, které se mají připojit, zobrazují jako uzly Konfigurace stavu zaregistrované ve vašem účtu Azure Automation.

## <a name="onboarding-physicalvirtual-linux-machines"></a>Připojování fyzických a virtuálních počítačů se systémem Linux

Můžete připojit servery Linux spuštěné místně nebo v jiných cloudových prostředích a Azure Automation konfigurace stavu. Servery musí mít [odchozí přístup k Azure](automation-dsc-overview.md#network-planning).

1. Zajistěte, aby byla na počítačích, kde je nainstalovaná konfigurace stavu Azure Automation, nainstalovaná nejnovější verze [PowerShellu pro konfiguraci požadovaného stavu pro Linux](https://github.com/Microsoft/PowerShell-DSC-for-Linux) .
2. Pokud se [výchozí nastavení Configuration Manager PowerShellu pro DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) shodují s vaším případem použití, a chcete připojit počítače tak, aby využívaly i sestavy Azure Automation konfigurace stavu:

   - V každém počítači se systémem Linux, který se má připojit ke konfiguraci `Register.py` stavu Azure Automation, použijte příkaz k připojení pomocí výchozích nastavení Configuration Manager PowerShellu pro DSC.

     `/opt/microsoft/dsc/Scripts/Register.py <Automation account registration key> <Automation account registration URL>`

   - Pokud chcete najít registrační klíč a adresu URL pro registraci k vašemu účtu Automation, přečtěte si část věnované [bezpečné registraci pomocí registrace](#onboarding-securely-using-registration) v tomto článku.

3. Pokud se výchozí hodnoty prostředí PowerShell DSC Local Configuration Manager (LCM) neshodují s vaším případem použití, nebo chcete připojit počítače, které nastavují pouze Azure Automation konfigurace stavu, postupujte podle kroků 4-7. V opačném případě pokračujte přímo na krok 7.

4. Postupujte podle pokynů v části [generování DSC metaconfigurations](#generating-dsc-metaconfigurations) a vytvořte složku obsahující požadované metaconfigurations DSC.

5. Ujistěte se, že je v počítači, který se používá pro registraci, nainstalovaná nejnovější verze [WMF 5](https://aka.ms/wmf5latest) .

6. Následujícím způsobem přidejte kód pro použití prostředí PowerShell DSC metaconfigurations vzdáleně na počítače, které chcete připojit.

    ```powershell
    $SecurePass = ConvertTo-SecureString -String '<root password>' -AsPlainText -Force
    $Cred = New-Object System.Management.Automation.PSCredential 'root', $SecurePass
    $Opt = New-CimSessionOption -UseSsl -SkipCACheck -SkipCNCheck -SkipRevocationCheck

    # need a CimSession for each Linux machine to onboard
    $Session = New-CimSession -Credential $Cred -ComputerName <your Linux machine> -Port 5986 -Authentication basic -SessionOption $Opt

    Set-DscLocalConfigurationManager -CimSession $Session -Path C:\Users\joe\Desktop\DscMetaConfigs
    ```

7. Pokud nemůžete použít metaconfigurations prostředí PowerShell DSC vzdáleně, zkopírujte metaconfigurations odpovídající vzdáleným počítačům ze složky popsané v kroku 4 do počítačů se systémem Linux.

8. Přidejte kód pro volání `Set-DscLocalConfigurationManager.py` lokálně na každém počítači se systémem Linux, který se připojí k konfiguraci stavu Azure Automation.

   `/opt/microsoft/dsc/Scripts/SetDscLocalConfigurationManager.py -configurationmof <path to metaconfiguration file>`

9. Pomocí rutin Azure Portal nebo zajistěte, aby se počítače, které se mají připojit, nyní zobrazovaly jako uzly DSC zaregistrované ve vašem účtu Azure Automation.

## <a name="generating-dsc-metaconfigurations"></a>Generování DSC metaconfigurations

Pokud chcete připojit jakýkoli počítač ke konfiguraci stavu Azure Automation, můžete vygenerovat [Metaconfiguration DSC](/powershell/scripting/dsc/managing-nodes/metaConfig). Tato konfigurace oznamuje agentovi DSC, aby načetl zprávu z nebo do konfigurace stavu Azure Automation. Metaconfiguration DSC můžete vygenerovat pro konfiguraci stavu Azure Automation pomocí konfigurace PowerShellu DSC nebo rutin Azure Automation PowerShellu.

> [!NOTE]
> DSC metaconfigurations obsahují tajné klíče potřebné k připojení počítače k účtu Automation pro správu. Ujistěte se, že jste správně chránili všechny metaconfigurationsy DSC, které jste vytvořili, nebo je po použití odstraňte.

Podpora proxy serveru pro metaconfigurations je řízena pomocí LCM, což je modul Windows PowerShell DSC. LCM běží na všech cílových uzlech a zodpovídá za volání prostředků konfigurace, které jsou součástí skriptu DSC metaconfiguration. Do metaconfiguration můžete zahrnout podporu proxy serveru tak, že zahrnete definice adresy URL proxy serveru a přihlašovacích údajů proxy serveru `ConfigurationRepositoryWeb`podle `ResourceRepositoryWeb`potřeby v `ReportServerWeb` blocích, a. Viz [Konfigurace místního Configuration Manager](https://docs.microsoft.com/powershell/scripting/dsc/managing-nodes/metaconfig?view=powershell-7).

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

1. Zadejte registrační klíč a adresu URL pro váš účet Automation a názvy počítačů, které se mají připojit. Všechny ostatní parametry jsou volitelné. Pokud chcete najít registrační klíč a adresu URL pro registraci k vašemu účtu Automation, přečtěte si část věnované [bezpečné registraci pomocí registračního](#onboarding-securely-using-registration) oddílu.

1. Pokud chcete, aby počítače nahlásily informace o stavu DSC do konfigurace stavu Azure Automation, ale ne z konfigurace Pull nebo modulů prostředí `ReportOnly` PowerShell, nastavte parametr na hodnotu true.

1. Pokud `ReportOnly` není nastavené, počítače nahlásí informace o stavu DSC do konfigurace stavu Azure Automation a konfigurace pro vyžádání obsahu nebo moduly prostředí PowerShell. Nastavte parametry odpovídajícím způsobem `ConfigurationRepositoryWeb`v `ResourceRepositoryWeb`blocích, `ReportServerWeb` a.

1. Spusťte skript. Teď byste měli mít pracovní složku s názvem **DscMetaConfigs**, která obsahuje PowerShell DSC metaconfigurations pro počítače, které se mají připojit (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path ./DscMetaConfigs
    ```

### <a name="generate-dsc-metaconfigurations-using-azure-automation-cmdlets"></a>Generování DSC metaconfigurations pomocí rutin Azure Automation

Pokud se výchozí hodnoty prostředí PowerShell DSC shodují s vaším případem použití a chcete zařadit počítače do konfigurace Azure Automation stavů a nahlásit je, můžete vygenerovat potřebné DSC metaconfigurations pomocí rutin Azure Automation.

1. Otevřete konzolu PowerShellu nebo VSCode jako správce na počítači v místním prostředí.
2. Připojte se k Azure Resource Manager pomocí [Connect-AzAccount](https://docs.microsoft.com/powershell/module/Az.Accounts/Connect-AzAccount?view=azps-3.7.0).
3. Stáhněte si prostředí PowerShell DSC metaconfigurations pro počítače, které chcete připojit z účtu Automation, ve kterém nastavujete uzly.

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

1. Teď byste měli mít složku **DscMetaConfigs** , která obsahuje PowerShell DSC metaconfigurations pro počítače, které se mají připojit (jako správce).

    ```powershell
    Set-DscLocalConfigurationManager -Path $env:UserProfile\Desktop\DscMetaConfigs
    ```

## <a name="onboarding-securely-using-registration"></a>Zabezpečené připojení pomocí registrace

Počítače mohou bezpečně připojit Azure Automation k účtu pomocí registračního protokolu WMF 5 DSC. Tento protokol umožňuje, aby se uzel DSC ověřil na server pro vyžádání obsahu nebo na serveru sestav PowerShellu DSC, včetně konfigurace stavu Azure Automation. Uzel se registruje u serveru v registrační adrese URL a ověřuje se pomocí registračního klíče. V průběhu registrace vyjednávat uzel DSC a server pro vyžádání/sestavování DSC jedinečný certifikát, který má uzel použít k ověřování po registraci serveru. Tento proces znemožňuje uzlům připojit uzly od sebe navzájem, například pokud dojde k ohrožení bezpečnosti uzlu a pracuje se zlými úmysly. Po registraci se registrační klíč znovu nepoužívá pro ověřování a z uzlu se odstraní.

Informace požadované pro registrační protokol konfigurace stavu můžete získat z **klíčů** v části **nastavení účtu** v Azure Portal. 

![Klíče a adresa URL služby Azure Automation](./media/automation-dsc-onboarding/DSC_Onboarding_4.png)

- Adresa URL registrace je pole Adresa URL na stránce klíče.
- Registrační klíč je hodnota pole **Primární přístupový klíč** nebo **sekundární přístupový klíč** na stránce klíče. Lze použít buď klíč.

Pro zvýšení zabezpečení můžete na stránce klíče kdykoli znovu vygenerovat primární a sekundární přístupové klíče účtu Automation. Opětovné vygenerování klíče brání v budoucí registraci uzlu v použití předchozích klíčů.

## <a name="re-registering-a-node"></a>Opětovné registrace uzlu

Po registraci počítače jako uzlu DSC v konfiguraci stavu Azure Automation existuje několik důvodů, proč možná budete muset uzel v budoucnu znovu zaregistrovat.

- **Obnovení certifikátu.** U verzí Windows serveru před Windows serverem 2019 každý uzel automaticky vyjednává jedinečný certifikát pro ověřování, jehož platnost vyprší po jednom roce. Pokud certifikát vyprší bez obnovení, uzel nebude schopen komunikovat s Azure Automation a je označen `Unresponsive`. V současné době nemůže registrační protokol pro PowerShell DSC automaticky obnovovat certifikáty, když se blíží vypršení platnosti, a musíte uzly znovu zaregistrovat po roce. Před opakovaným registrací se ujistěte, že na každém uzlu je spuštěný WMF 5 RTM. 

    Opakovaná registrace provedla 90 dní nebo méně z času vypršení platnosti certifikátu nebo kdykoli po uplynutí doby vypršení platnosti certifikátu dojde k vygenerování a použití nového certifikátu. Řešení tohoto problému je součástí Windows serveru 2019 a novějších verzí.

- **Změny hodnot LCM v DSC.** Možná budete muset změnit [hodnoty prostředí POWERSHELL DSC](/powershell/scripting/dsc/managing-nodes/metaConfig4) , které nastavíte při počáteční registraci uzlu, například `ConfigurationMode`. V současné době můžete tyto hodnoty agenta DSC změnit pouze pomocí opětovné registrace. Jedinou výjimkou je hodnota konfigurace uzlu přiřazená uzlu. Tuto možnost můžete v Azure Automation DSC změnit přímo.

Uzel můžete znovu zaregistrovat stejným způsobem, jako jste předtím zaregistrovali uzel, pomocí kterékoli metody připojování popsané v tomto dokumentu. Před opětovnou registrací není nutné zrušit registraci uzlu v konfiguraci stavu Azure Automation.

## <a name="troubleshooting-azure-virtual-machine-onboarding"></a>Řešení potíží s připojováním virtuálních počítačů Azure

Konfigurace stavu Azure Automation umožňuje snadno připojit virtuální počítače Azure s Windows ke správě konfigurace. V digestoři se k registraci virtuálního počítače s konfigurací stavu Azure Automation používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure. Vzhledem k tomu, že se rozšíření konfigurace požadovaného stavu virtuálního počítače Azure spouští asynchronně, sledování průběhu a řešení potíží s jeho spuštěním může být důležité.

> [!NOTE]
> Jakákoli metoda připojení virtuálního počítače Azure s Windows k Azure Automation stavu, který používá rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, může trvat až hodinu, než se Azure Automation zobrazí jako zaregistrované. Tato prodleva je způsobená instalací WMF 5 na VIRTUÁLNÍm počítači pomocí rozšíření konfigurace požadovaného stavu virtuálního počítače Azure, které je nutné k připojení virtuálního počítače ke konfiguraci stavu Azure Automation.

Postup při odstraňování potíží nebo zobrazení stavu rozšíření konfigurace požadovaného stavu virtuálního počítače Azure:

1. V Azure Portal přejděte na virtuální počítač, který jste připojili.
2. V části **Nastavení**klikněte na **rozšíření** . 
3. V závislosti na vašem operačním systému teď vyberte **DSC** nebo **DSCForLinux**. 
4. Pro další podrobnosti můžete kliknout na **zobrazit podrobný stav**.

Další informace o řešení potíží najdete v tématu [řešení potíží s Azure Automation konfigurace požadovaného stavu (DSC)](./troubleshoot/desired-state-configuration.md).

## <a name="next-steps"></a>Další kroky

- Informace o tom, jak začít, najdete v tématu [Začínáme s konfigurací stavu Azure Automation](automation-dsc-getting-started.md).
- Další informace o kompilaci konfigurací DSC, abyste je mohli přiřadit cílovým uzlům, najdete v tématu [kompilace konfigurací v konfiguraci stavu Azure Automation](automation-dsc-compile.md).
- Referenční informace k rutinám PowerShellu najdete v tématu [AZ. Automation](https://docs.microsoft.com/powershell/module/az.automation/?view=azps-3.7.0#automation
).
- Informace o cenách najdete v tématu [Azure Automation ceny konfigurace stavu](https://azure.microsoft.com/pricing/details/automation/).
- Příklad použití konfigurace stavu Azure Automation v kanálu průběžného nasazování najdete v tématu [Příklad použití: průběžné nasazování do virtuálních počítačů pomocí konfigurace stavu Azure Automation a čokolády](automation-dsc-cd-chocolatey.md).
