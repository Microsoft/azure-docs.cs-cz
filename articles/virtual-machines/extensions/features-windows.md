---
title: Rozšíření a funkce virtuálních počítačů Azure pro Windows
description: Zjistěte, jaká rozšíření jsou dostupná pro virtuální počítače Azure seskupená podle toho, co poskytují nebo vylepšují.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: akjosh
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: cd6439bf1b1f52b8e63819e8e519fc4971d1bc2a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80066842"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozšíření a funkce virtuálních strojů pro Windows

Rozšíření virtuálních počítačů Azure jsou malé aplikace, které na virtuálních počítačích Azure umožňují provádět úlohy konfigurace a automatizace po nasazení. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače. Rozšíření virtuálních počítačů Azure je možné spustit přes Azure CLI, PowerShell, šablony Azure Resource Manageru a Azure Portal. Rozšíření lze spojit s novým nasazením virtuálního počítače, případně spustit v jakémkoli existujícím systému.

Tento článek obsahuje přehled rozšíření virtuálních počítačů, předpoklady pro použití rozšíření virtuálních zařízení Azure a pokyny, jak zjistit, spravovat a odebírat rozšíření virtuálních počítačových společností. Tento článek poskytuje zobecněné informace, protože mnoho rozšíření virtuálních počítačů jsou k dispozici, každý s potenciálně jedinečnou konfiguraci. Podrobnosti specifické pro rozšíření lze nalézt v každém dokumentu specifické pro jednotlivé rozšíření.

 

## <a name="use-cases-and-samples"></a>Případy použití a vzorky

K dispozici je několik různých rozšíření virtuálních počítačů Azure, z nichž každé má konkrétní případ použití. Možné příklady:

- Použijte konfigurace požadovaného stavu prostředí PowerShell u virtuálního počítače s rozšířením DSC pro Windows. Další informace naleznete v [tématu Rozšíření konfigurace Požadované ho stavu Azure](dsc-overview.md).
- Konfigurace monitorování virtuálního počítače s rozšířením virtuálního počítače agenta analýzy protokolů. Další informace najdete [v tématu Připojení virtuálních počítačích Azure k protokolům Azure Monitor .](../../log-analytics/log-analytics-azure-vm-extension.md)
- Nakonfigurujte virtuální počítač Azure pomocí chef. Další informace najdete [v tématu Automatizace nasazení virtuálních počítačích Azure s chef](../../chef/chef-automation.md).
- Nakonfigurujte monitorování infrastruktury Azure pomocí rozšíření Datadog. Další informace naleznete v [blogu Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Kromě rozšíření specifických pro proces je k dispozici rozšíření Custom Script pro virtuální počítače s Windows i Linuxem. Rozšíření Vlastní skript pro Windows umožňuje spuštění libovolného skriptu prostředí PowerShell na virtuálním počítači. Vlastní skripty jsou užitečné pro navrhování nasazení Azure, které vyžadují konfiguraci nad rámec toho, co mohou poskytovat nativní nástroje Azure. Další informace naleznete v [tématu rozšíření Vlastní skript virtuálního aplikace windows](custom-script-windows.md).

## <a name="prerequisites"></a>Požadavky

Chcete-li zpracovat rozšíření na virtuálním počítači, budete potřebovat nainstalovaný Azure Windows Agent. Některá jednotlivá rozšíření mají požadavky, například přístup k prostředkům nebo závislostem.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuálním počítačem Azure a řadičem infrastruktury Azure. Agent virtuálního počítače je zodpovědný za mnoho funkčních aspektů nasazení a správy virtuálních počítačích Azure, včetně spuštění rozšíření virtuálních počítačů. Agent virtuálního počítače Azure je předinstalovaný na ibi Azure Marketplace a dá se nainstalovat ručně do podporovaných operačních systémů. Agent virtuálního počítače Azure pro Windows se označuje jako agent hosta Windows.

Informace o podporovaných operačních systémech a pokynech k instalaci najdete v [tématu Azure virtual machine agent](agent-windows.md).

#### <a name="supported-agent-versions"></a>Podporované verze agentů

Chcete-li poskytnout nejlepší možné zkušenosti, existují minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operační systémy

Agent hosta systému Windows běží na více operačních systémech, ale rozšíření rozhraní má limit pro operační systémy, které rozšíření. Další informace najdete v [tomto článku](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Některá rozšíření nejsou podporována ve všech operačních systémech a mohou vyzařovat *kód chyby 51, "Nepodporovaný operační systém"*. Zkontrolujte, zda je v dokumentaci k jednotlivým rozšířením k podpoře.

#### <a name="network-access"></a>Síťový přístup

Balíčky rozšíření se stáhnou z úložiště rozšíření Azure Storage a odeslání stavu rozšíření se zaúčtují do Služby Azure Storage. Pokud používáte [podporovanou](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzi agentů, nemusíte povolit přístup k Azure Storage v oblasti virtuálních počítačů, stejně jako agent k přesměrování komunikace do řadiče infrastruktury Azure pro komunikaci agenta (HostGAPlugin funkce prostřednictvím privilegovaného kanálu na privátní IP [168.63.129.16).](https://docs.microsoft.com/azure/virtual-network/what-is-ip-address-168-63-129-16) Pokud jste na nepodporovanou verzi agenta, musíte povolit odchozí přístup k úložišti Azure v této oblasti z virtuálního počítače.

> [!IMPORTANT]
> Pokud jste zablokovali přístup k *168.63.129.16* pomocí brány firewall hosta nebo s proxy serverem, rozšíření se nezdaří bez ohledu na výše uvedené. Porty 80, 443 a 32526 jsou povinné.

Agenti lze použít pouze ke stažení balíčků rozšíření a stavu vykazování. Pokud například instalace rozšíření potřebuje stáhnout skript z GitHubu (vlastní skript) nebo potřebuje přístup k Úložišti Azure Storage (Azure Backup), je třeba otevřít další porty firewallu/skupiny zabezpečení sítě. Různá rozšíření mají různé požadavky, protože se jedná o aplikace samy o sobě. U rozšíření, která vyžadují přístup k Azure Storage nebo Azure Active Directory, můžete povolit přístup pomocí [značek služby Azure NSG](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags) do úložiště nebo AzureActiveDirectory.

Agent hosta systému Windows nemá podporu proxy serveru pro přesměrování požadavků na přenos agentů prostřednictvím, což znamená, že agent hosta systému Windows bude spoléhat na váš vlastní proxy server (pokud ho máte) pro přístup k prostředkům na internetu nebo na hostiteli prostřednictvím protokolu IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Objevte rozšíření virtuálních míchačů

Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. Chcete-li zobrazit úplný seznam, použijte [Get-AzVMExtensionImage](https://docs.microsoft.com/powershell/module/az.compute/get-azvmextensionimage). V následujícím příkladu jsou uvedena všechna dostupná rozšíření v umístění *WestUS:*

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálních míchačů

Rozšíření virtuálních počítačů Azure běží na stávajících virtuálních počítačích, což je užitečné, když potřebujete provést změny konfigurace nebo obnovit připojení na již nasazený virtuální počítač. Rozšíření virtuálních aplikací můžou být taky svázaná s nasazeními šablon Azure Resource Manageru. Pomocí rozšíření se šablonami Správce prostředků lze virtuální počítače Azure nasadit a nakonfigurovat bez zásahu po nasazení.

Následující metody lze použít ke spuštění rozšíření proti existující virtuální hod.

### <a name="powershell"></a>PowerShell

Pro spouštění jednotlivých rozšíření existuje několik příkazů prostředí PowerShell. Chcete-li zobrazit seznam, použijte [příkaz Get-Command](https://docs.microsoft.com/powershell/module/microsoft.powershell.core/get-command) a filtr v *rozšíření*:

```powershell
Get-Command Set-Az*Extension* -Module Az.Compute
```

To poskytuje výstup podobný následujícímu:

```powershell
CommandType     Name                                          Version    Source
-----------     ----                                          -------    ------
Cmdlet          Set-AzVMAccessExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMADDomainExtension                     4.5.0      Az.Compute
Cmdlet          Set-AzVMAEMExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMBackupExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMBginfoExtension                       4.5.0      Az.Compute
Cmdlet          Set-AzVMChefExtension                         4.5.0      Az.Compute
Cmdlet          Set-AzVMCustomScriptExtension                 4.5.0      Az.Compute
Cmdlet          Set-AzVMDiagnosticsExtension                  4.5.0      Az.Compute
Cmdlet          Set-AzVMDiskEncryptionExtension               4.5.0      Az.Compute
Cmdlet          Set-AzVMDscExtension                          4.5.0      Az.Compute
Cmdlet          Set-AzVMExtension                             4.5.0      Az.Compute
Cmdlet          Set-AzVMSqlServerExtension                    4.5.0      Az.Compute
Cmdlet          Set-AzVmssDiskEncryptionExtension             4.5.0      Az.Compute
```

Následující příklad používá rozšíření Vlastní skript ke stažení skriptu z úložiště GitHub do cílového virtuálního počítače a pak skript spustit. Další informace o rozšíření Vlastní skript naleznete v [tématu Přehled rozšíření Vlastní skript](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

V následujícím příkladu se rozšíření Přístup k virtuálnímu počítače používá k resetování hesla pro správu virtuálního počítače se systémem Windows na dočasné heslo. Další informace o rozšíření přístupu k virtuálním počítačům najdete v tématu [Obnovení služby Vzdálená plocha ve virtuálním počítači se systémem Windows](../windows/reset-rdp.md). Po spuštění byste měli heslo resetovat při prvním přihlášení:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Příkaz `Set-AzVMExtension` lze použít ke spuštění libovolnérozšíření virtuálního mísy. Další informace naleznete [v odkazu Set-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>portál Azure

Rozšíření virtuálních počítače se dá použít na existující virtuální počítač prostřednictvím portálu Azure. Vyberte virtuální virtuální ms na portálu, zvolte **Rozšíření**a pak vyberte **Přidat**. Ze seznamu dostupných rozšíření vyberte požadované rozšíření a postupujte podle pokynů průvodce.

Následující příklad ukazuje instalaci rozšíření Microsoft Antimalware z webu Azure Portal:

![Instalace antimalwarového rozšíření](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítače lze přidat do šablony Azure Resource Manager a spustit s nasazením šablony. Když nasadíte rozšíření se šablonou, můžete vytvořit plně nakonfigurovaná nasazení Azure. Například následující JSON se odebírá ze šablony Správce prostředků nasadí sadu virtuálních počítačích s vyrovnáváním zatížení a databázi Azure SQL a pak nainstaluje aplikaci .NET Core na každý virtuální počítač. Rozšíření virtuálního počítače se postará o instalaci softwaru.

Další informace naleznete v [úplné šabloně Správce prostředků](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Další informace o vytváření šablon Správce prostředků najdete v [tématu Vytváření šablon Azure Resource Manageru s rozšířeními virtuálních aplikací pro Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečená data rozšíření virtuálního aplikace

Při spuštění rozšíření virtuálního počítačů může být nutné zahrnout citlivé informace, jako jsou přihlašovací údaje, názvy účtů úložiště a přístupové klíče účtu úložiště. Mnoho rozšíření virtuálních počítačů obsahuje chráněnou konfiguraci, která šifruje data a dešifruje je pouze uvnitř cílového virtuálního počítače. Každé rozšíření má specifické chráněné schéma konfigurace a každý je podrobně popsán v dokumentaci specifické pro rozšíření.

Následující příklad ukazuje instanci rozšíření Vlastní skript pro Windows. Příkaz, který má být proveden, obsahuje sadu pověření. V tomto příkladu není příkaz, který má být proveden, šifrován:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ],
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Přesunutím **příkazu ke spuštění** vlastnosti do **chráněné** konfigurace je zabezpečen řetězec spuštění, jak je znázorněno v následujícím příkladu:

```json
{
    "apiVersion": "2015-06-15",
    "type": "extensions",
    "name": "config-app",
    "location": "[resourceGroup().location]",
    "dependsOn": [
    "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'),copyindex())]",
    "[variables('musicstoresqlName')]"
    ],
    "tags": {
    "displayName": "config-app"
    },
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
    "protectedSettings": {
        "commandToExecute": "[concat('powershell -ExecutionPolicy Unrestricted -File configure-music-app.ps1 -user ',parameters('adminUsername'),' -password ',parameters('adminPassword'),' -sqlserver ',variables('musicstoresqlName'),'.database.windows.net')]"
    }
    }
}
```

Na virtuálním počítači Azure IaaS, který používá rozšíření, se v konzole certifikátů mohou zobrazit certifikáty, které mají předmět **_Windows Azure CRP Certificate Generator_**. Na klasickém virtuálním počítači RDFE mají tyto certifikáty název předmětu **_Windows Azure Service Management for Extensions_**.

Tyto certifikáty zabezpečují komunikaci mezi virtuálním počítačem a jeho hostitelem během přenosu chráněných nastavení (heslo, další pověření) používané rozšířeními. Certifikáty jsou vytvořené řadičem infrastruktury Azure a předány agentovi virtuálního počítače. Pokud zastavíte a spustíte virtuální ho svitek každý den, může být nový certifikát vytvořen řadičem infrastruktury. Certifikát je uložen v úložišti osobních certifikátů počítače. Tyto certifikáty lze odstranit. Agent virtuálního mísa v případě potřeby znovu vytvoří certifikáty.

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak se agenti a rozšíření aktualizují?

Agenti a rozšíření sdílejí stejný mechanismus aktualizace. Některé aktualizace nevyžadují další pravidla brány firewall.

Když je k dispozici aktualizace, je nainstalovaný na virtuálním počítači, když dojde ke změně rozšíření a další změny modelu virtuálního počítače, jako jsou:

- Datové disky
- Rozšíření
- Kontejner diagnostiky spouštění
- Tajné kódy hostovaného operačního es
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupňují aktualizace do oblastí v různých časech, takže je možné, že virtuální počítači můžete mít v různých oblastech v různých verzích.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Výpis rozšíření nasazených k virtuálnímu virtuálnímu ms

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aktualizace agentů

Agent hosta systému Windows obsahuje pouze *kód zpracování rozšíření*, kód *zřizování systému Windows* je samostatný. Agenta hosta systému Windows můžete odinstalovat. Automatickou aktualizaci agenta hosta okna nelze zakázat.

*Kód zpracování rozšíření* je zodpovědný za komunikaci s prostředků infrastruktury Azure a zpracování operací rozšíření virtuálních počítače, jako jsou instalace, stav vykazování, aktualizace jednotlivých rozšíření a jejich odebrání. Aktualizace obsahují opravy zabezpečení, opravy chyb a vylepšení *kódu zpracování rozšíření*.

Informace o tom, jakou verzi používáte, naleznete [v tématu Zjištění nainstalovaného agenta hosta systému Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizace rozšíření

Pokud je k dispozici aktualizace rozšíření, agent hosta systému Windows stáhne a inovuje rozšíření. Automatické aktualizace rozšíření jsou *menší* nebo *oprava hotfix*. Při zřizování rozšíření se můžete odhlásit nebo odhlásit z *rozšíření.* Následující příklad ukazuje, jak automaticky upgradovat dílčí verze v šabloně Správce prostředků s *autoUpgradeMinorVersion": true,'*:

```json
    "properties": {
    "publisher": "Microsoft.Compute",
    "type": "CustomScriptExtension",
    "typeHandlerVersion": "1.9",
    "autoUpgradeMinorVersion": true,
    "settings": {
        "fileUris": [
        "https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1"
        ]
    },
```

Chcete-li získat nejnovější drobné opravy chyb vydání, důrazně doporučujeme vždy vybrat automatickou aktualizaci v nasazení rozšíření. Aktualizace opravy hotfix, které provádějí opravy zabezpečení nebo opravy chyb klíčů, nelze odhlášení.

### <a name="how-to-identify-extension-updates"></a>Jak identifikovat aktualizace rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifikace, pokud je rozšíření nastaveno pomocí automatického upgraduMinorVersion na virtuálním počítači

Můžete vidět z modelu virtuálního soudu, pokud rozšíření bylo zřízeno s "autoUpgradeMinorVersion". Chcete-li zkontrolovat, použijte [Get-AzVm](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) a zadejte název skupiny prostředků a virtuálního soudu takto:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Následující příklad výstupu ukazuje, že *autoUpgradeMinorVersion* je nastavena na *hodnotu true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifikace, kdy došlo k automatickému upgraduMinorVersion

Chcete-li zjistit, kdy došlo k aktualizaci rozšíření, zkontrolujte protokoly agenta na virtuálním počítači na adrese *C:\WindowsAzure\Logs\WaAppAgent.log*

V následujícím příkladu měl virtuální počítač nainstalovanou *aplikaci Microsoft.Compute.CustomScriptExtension 1.8.* Oprava hotfix byla k dispozici pro verzi *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Oprávnění agenta

Chcete-li provádět své úkoly, musí být agent spuštěn jako *místní systém*.

## <a name="troubleshoot-vm-extensions"></a>Poradce při potížích s rozšířeními virtuálních míchačů

Každé rozšíření virtuálního virtuálního virtuálního montovny může mít kroky řešení potíží specifické pro rozšíření. Například při použití rozšíření vlastní skript, podrobnosti spuštění skriptu lze nalézt místně na virtuálním počítači, kde byla spuštěna rozšíření. Všechny kroky řešení potíží specifické pro rozšíření jsou podrobně popsány v dokumentaci specifické pro rozšíření.

Následující kroky řešení potíží platí pro všechna rozšíření virtuálních mit.

1. Chcete-li zkontrolovat protokol agenta hosta systému Windows, podívejte se na aktivitu při zřizování rozšíření v *c:\WindowsAzure\Logs\WaAppAgent.log*

2. Další podrobnosti naleznete v protokolech skutečného rozšíření v *aplikaci\<C:\WindowsAzure\Logs\Plugins extensionName>*

3. Zkontrolujte, zda se v částech s podrobnostmi o chybách, známých problémech atd.

4. Podívejte se na systémové protokoly. Zkontrolujte další operace, které mohly narušit rozšíření, jako je například dlouhotrvající instalace jiné aplikace, která vyžadovala výhradní přístup správce balíčků.

### <a name="common-reasons-for-extension-failures"></a>Běžné důvody selhání rozšíření

1. Rozšíření mají 20 minut ke spuštění (výjimkou jsou rozšíření CustomScript, Chef a DSC, které mají 90 minut). Pokud vaše nasazení překročí tuto dobu, je označen jako časový limit. Příčinou může být nedostatek prostředků virtuálních počítačů, jiné konfigurace virtuálních počítačů/spuštění úlohy spotřebovávají vysoké množství prostředků, zatímco rozšíření se pokouší zřídit.

2. Minimální předpoklady nejsou splněny. Některá rozšíření mají závislosti na virtuálních aplikacích SKU, jako jsou například image HPC. Rozšíření mohou vyžadovat určité požadavky na přístup k síti, jako je například komunikace s Azure Storage nebo veřejnými službami. Dalšími příklady mohou být přístup k úložištím balíčků, nedostatek místa na disku nebo omezení zabezpečení.

3. Exkluzivní přístup správce balíčků. V některých případech může dojít k dlouho běžící konfiguraci virtuálního počítače a rozšíření instalace konfliktní, kde oba potřebují výhradní přístup ke správci balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního virtuálního provozu proti virtuálnímu virtuálnímu virtuálnímu provozu, použijte [Get-AzVM](https://docs.microsoft.com/powershell/module/az.compute/get-azvm) vrátit stav rozšíření. *Substatuses[0]* ukazuje, že rozšíření zřizování proběhlo úspěšně, což znamená, že bylo úspěšné nasazení na virtuální ms, ale spuštění rozšíření uvnitř virtuálního virtuálního byl neúspěšný, *Substatuses[1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Výstup je podobný následujícímu příkladu výstupu:

```powershell
Extensions[0]           :
  Name                  : CustomScriptExtension
  Type                  : Microsoft.Compute.CustomScriptExtension
  TypeHandlerVersion    : 1.9
  Substatuses[0]        :
    Code                : ComponentStatus/StdOut/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : Windows PowerShell \nCopyright (C) Microsoft Corporation. All rights reserved.\n
  Substatuses[1]        :
    Code                : ComponentStatus/StdErr/succeeded
    Level               : Info
    DisplayStatus       : Provisioning succeeded
    Message             : The argument 'cseTest%20Scriptparam1.ps1' to the -File parameter does not exist. Provide the path to an existing '.ps1' file as an argument to the

-File parameter.
  Statuses[0]           :
    Code                : ProvisioningState/failed/-196608
    Level               : Error
    DisplayStatus       : Provisioning failed
    Message             : Finished executing command
```

Stav spuštění rozšíření se také nachází na webu Azure Portal. Pokud chcete zobrazit stav rozšíření, vyberte virtuální honový virtuální ms, zvolte **Rozšíření**, pak vyberte požadované rozšíření.

### <a name="rerun-vm-extensions"></a>Opětovné spuštění rozšíření virtuálních virtuálních míchačů

Mohou existovat případy, ve kterých je třeba znovu spustit rozšíření virtuálního provozu. Rozšíření můžete znovu spustit odebráním a opětovným spuštěním rozšíření s metodou spuštění podle vašeho výběru. Chcete-li rozšíření odebrat, použijte [funkci Remove-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/Remove-AzVMExtension) následujícím způsobem:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Rozšíření na webu Azure Portal můžete taky odebrat takto:

1. Vyberte virtuální hod.
2. Zvolte **Rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **Odinstalovat**.

## <a name="common-vm-extensions-reference"></a>Odkaz na rozšíření běžných virtuálních mís
| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro virtuální počítače |Spouštění skriptů proti virtuálnímu počítači Azure |[Rozšíření vlastních skriptů pro virtuální počítače](custom-script-windows.md) |
| Rozšíření DSC pro Windows |Rozšíření PowerShell DSC (požadovaná konfigurace stavu) |[Rozšíření DSC pro Windows](dsc-overview.md) |
| Rozšíření Azure Diagnostics |Správa diagnostiky Azure |[Rozšíření diagnostiky Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístupu k virtuálním montovně Azure |Správa uživatelů a přihlašovacích údajů |[Rozšíření přístupu k virtuálním montovně pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další kroky

Další informace o rozšířeních virtuálních virtuálních zařízení najdete v [tématu Rozšíření virtuálních zařízení Azure a přehled funkcí](overview.md).
