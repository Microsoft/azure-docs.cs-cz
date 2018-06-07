---
title: Azure rozšíření virtuálního počítače a funkce pro Windows | Microsoft Docs
description: Zjistěte, jaká rozšíření jsou k dispozici pro virtuální počítače Azure, seskupené podle co se poskytují nebo zvýšit.
services: virtual-machines-windows
documentationcenter: ''
author: danielsollondon
manager: jeconnoc
editor: ''
tags: azure-service-management,azure-resource-manager
ms.assetid: 999d63ee-890e-432e-9391-25b3fc6cde28
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: danis
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e9e147e2cbe5ff42562d6fcfab62460df48f3d65
ms.sourcegitcommit: 6cf20e87414dedd0d4f0ae644696151e728633b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/06/2018
ms.locfileid: "34809722"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozšíření virtuálního počítače a funkce pro Windows

Rozšíření virtuálních počítačů (VM) Azure jsou malých aplikacích, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Například, pokud virtuální počítač vyžaduje instalace softwaru, antivirové ochrany, nebo spusťte skript uvnitř této můžete použít rozšíření virtuálního počítače. Rozšíření virtuálního počítače Azure můžete spustit pomocí rozhraní příkazového řádku Azure, prostředí PowerShell, šablony Azure Resource Manager a portálu Azure. Rozšíření můžete dodávat s nové nasazení virtuálního počítače nebo spouštění všechny existující systém.

Tento článek obsahuje přehled rozšíření virtuálních počítačů, požadavků na používání rozšíření virtuálního počítače Azure, a pokyny o tom, jak zjistit, spravovat a odeberte rozšíření virtuálního počítače. Tento článek obsahuje zobecněný informace, protože mnoho rozšíření virtuálního počítače nejsou k dispozici, každý s konfigurací potenciálně jedinečný. Podrobnosti o konkrétní rozšíření najdete v každý dokument specifické pro jednotlivé rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

Jsou k dispozici několik různých rozšíření virtuálního počítače Azure, každý s konkrétní případ použití. Možné příklady:

- Použít PowerShell požadovaná stavu konfigurace pro virtuální počítač s příponou DSC pro systém Windows. Další informace najdete v tématu [stavu Azure požadovaná konfigurace rozšíření](dsc-overview.md).
- Nakonfigurujte monitorování virtuálního počítače pomocí rozšíření Microsoft Monitoring Agent virtuálního počítače. Další informace najdete v tématu [připojit virtuální počítače Azure k analýze protokolů](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurace virtuálního počítače Azure pomocí Chef. Další informace najdete v tématu [nasazení automatizaci virtuálního počítače Azure s Chef](../windows/chef-automation.md).
- Konfigurace sledování infrastruktury Azure s příponou Datadog. Další informace najdete v tématu [Datadog blog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Kromě rozšíření specifické pro proces rozšíření vlastních skriptů je k dispozici pro virtuální počítače Windows a Linux. Rozšíření vlastních skriptů pro systém Windows umožňuje všech skriptů prostředí PowerShell ke spuštění na virtuálním počítači. Vlastní skripty jsou užitečné pro návrh Azure nasazení, které vyžadují konfiguraci nad rámec jaké nativní Azure nástrojů může poskytnout. Další informace najdete v tématu [rozšíření skriptů vlastní virtuální počítač Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Požadavky

Pro zpracování rozšíření ve virtuálním počítači, potřebujete Azure Linux Agent nainstalovaný. Některé jednotlivých rozšíření mít požadavky, jako je přístup k prostředkům nebo závislosti.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuální počítač Azure a kontroleru prostředků infrastruktury Azure. Agent virtuálního počítače je zodpovědná za funkční aspekty nasazení a správa virtuálních počítačích Azure, včetně spuštění rozšíření virtuálního počítače. Agent virtuálního počítače Azure je předinstalován v Azure Marketplace bitové kopie a může být nainstalován ručně na podporovaných operačních systémů. Virtuální počítač agenta k Azure pro systém Windows se označuje jako agent hosta systému Windows.

Informace o podporovaných operačních systémů a pokyny k instalaci najdete v tématu [agent virtuálního počítače Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Verze podporovaného agenta

Chcete-li poskytovat nejlepší možný výkon, jsou minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operační systémy

Agent hosta Windows, který běží na více operační systémy, ale rozhraní rozšíření má omezení pro operační systémy tohoto rozšíření. Další informace najdete v tématu [v tomto článku] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Některá rozšíření nejsou podporovány v rámci všechny operační systémy a může vysílat *51 kódu chyby, nepodporovaný operační systém*. Zkontrolujte dokumentaci jednotlivých rozšíření možnosti podpory.

#### <a name="network-access"></a>Síťový přístup

Rozšiřující balíčky budou staženy z rozšíření úložiště Azure Storage a nahrávání stav rozšíření jsou odeslány do služby Azure Storage. Pokud používáte [podporované](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verze agentů, není nutné pro povolení přístupu k Azure Storage v oblasti virtuálních počítačů, jak můžete použít agenta pro přesměrování komunikace na kontroler prostředků infrastruktury Azure pro komunikaci agenta. Pokud jste v nepodporované verzi agenta, budete muset povolit odchozí přístup k úložišti Azure v této oblasti z virtuálního počítače.

> [!IMPORTANT]
> Pokud mají blokovaný přístup k *168.63.129.1* použití brány firewall hosta, potom rozšíření nezdaří bez ohledu na výše.

Agenti slouží pouze ke stažení rozšíření balíčky a vytváření sestav stavu. Například pokud instalaci rozšíření je nutné stáhnout skript z webu GitHub (vlastní skript) nebo potřebují přístup ke službě Azure Storage (Azure Backup), pak další brány firewall nebo síťových zabezpečení muset otevřít porty skupiny. Různá rozšíření mají různé požadavky, protože jsou aplikace samy. Pro rozšíření, které vyžadují přístup k úložišti Azure, můžete povolit přístup pomocí značek služby Azure skupina NSG pro [úložiště](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Agent hosta Windows nemá podporu pro vás k přesměrování agenta požadavky na provoz prostřednictvím proxy serveru.

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálního počítače

Mnoho různých rozšíření virtuálního počítače jsou k dispozici pro použití s virtuálními počítači Azure. Pokud chcete zobrazit úplný seznam, použijte [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage). Následující příklad uvádí všechny dostupné rozšíření v *WestUS* umístění:

```powershell
Get-AzureRmVmImagePublisher -Location "WestUS" | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálního počítače

Rozšíření virtuálního počítače Azure spustit na existující virtuální počítače, což je užitečné, když potřebujete udělat změny konfigurace nebo obnovit připojení již nasazené virtuálního počítače. Rozšíření virtuálního počítače můžete také dodávat s nasazení šablony Azure Resource Manager. Virtuální počítače Azure můžete pomocí rozšíření šablony Resource Manageru, nasazení a nakonfigurování bez zásahu po nasazení.

Tyto metody slouží ke spuštění rozšíření stávajícího virtuálního počítače.

### <a name="powershell"></a>PowerShell

Existuje několik příkazů prostředí PowerShell pro spouštění jednotlivých rozšíření. Chcete-li zobrazit seznam, použijte [Get-Command](/powershell/module/microsoft.powershell.core/get-command) a filtrujte *rozšíření*:

```powershell
Get-Command Set-AzureRM*Extension* -Module AzureRM.Compute
```

To poskytuje výstup podobný následujícímu:

```powershell
CommandType     Name                                               Version    Source
-----------     ----                                               -------    ------
Cmdlet          Set-AzureRmVMAccessExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMADDomainExtension                     4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMAEMExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBackupExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMBginfoExtension                       4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMChefExtension                         4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMCustomScriptExtension                 4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiagnosticsExtension                  4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDiskEncryptionExtension               4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMDscExtension                          4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMExtension                             4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVMSqlServerExtension                    4.5.0      AzureRM.Compute
Cmdlet          Set-AzureRmVmssDiskEncryptionExtension             4.5.0      AzureRM.Compute
```

Následující příklad používá rozšíření vlastních skriptů na stažení skriptu z úložiště Githubu do cílového virtuálního počítače a pak spusťte skript. Další informace o rozšíření vlastních skriptů najdete v tématu [přehled rozšíření vlastních skriptů](custom-script-windows.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

V následujícím příkladu se používá rozšíření pro virtuální počítač přístup k resetování hesla pro správu systému Windows virtuálního počítače do dočasné heslo. Další informace o rozšíření pro přístup virtuálních počítačů najdete v tématu [služby Vzdálená plocha resetovat ve virtuálním počítači Windows](../windows/reset-rdp.md). Když spustíte to byste měli obnovit heslo při prvním přihlášení:

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` Příkaz můžete použít ke spuštění všech rozšíření virtuálního počítače. Další informace najdete v tématu [odkaz na sadu AzureRmVMExtension](https://msdn.microsoft.com/library/mt603745.aspx).


### <a name="azure-portal"></a>Azure Portal

Rozšíření virtuálního počítače je použít pro existující virtuální počítač prostřednictvím portálu Azure. Vyberte virtuální počítač na portálu, zvolte **rozšíření**, pak vyberte **přidat**. Zvolte rozšíření, ze seznamu dostupných rozšíření a postupujte podle pokynů v průvodci.

Následující příklad ukazuje instalací rozšíření Microsoft Antimalware z portálu Azure:

![Nainstalujte rozšíření proti malwaru](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů můžete přidat do šablony Azure Resource Manager a provést při nasazení šablony. Když nasadíte rozšíření pomocí šablony, můžete vytvořit plně nakonfigurované Azure nasazení. Například se provede následujícím kódu JSON ze Správce prostředků šablony nasadí sadu virtuálních počítačů s vyrovnáváním zatížení a Azure SQL database a potom nainstaluje aplikace .NET Core na každý virtuální počítač. Rozšíření virtuálního počítače má na starosti instalace softwaru.

Další informace najdete v tématu [úplné šablony Resource Manageru](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Další informace o vytváření šablon Resource Manageru najdete v tématu [šablon pro tvorbu Azure Resource Manageru pomocí rozšíření virtuálního počítače Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečení dat rozšíření virtuálního počítače

Když spustíte rozšíření virtuálního počítače, může být nutné zahrnovat citlivé informace, jako je například přihlašovací údaje, názvy účtů úložiště a přístupových klíčů k účtu úložiště. Mnoho rozšíření virtuálního počítače zahrnují chráněné konfigurace, která data šifruje a dešifruje ji pouze v cílovém virtuálním počítači. Každé rozšíření má schéma konkrétní chráněné konfigurace a každý je podrobně popsaná v dokumentaci konkrétní rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro systém Windows. Příkaz k provedení zahrnuje sadu přihlašovacích údajů. V tomto příkladu není šifrován spuštění příkazu:

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

Přesun **příkaz k provedení** vlastnost, která má **chráněné** konfigurace zabezpečuje provádění řetězec, jak je znázorněno v následujícím příkladu:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak aktualizovat agenty a rozšíření?

Agenti a rozšíření sdílet stejný mechanismus aktualizace. Některé aktualizace se nevyžadují žádné další brány firewall pravidla.

Aktualizace je dostupná, pouze instalován na virtuálním počítači když dojde ke změně rozšíření a jiné změny modelu virtuálních počítačů, jako:

- Datové disky
- Rozšíření
- Spouštění diagnostiky kontejneru
- Tajné klíče hostovaného operačního systému
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupnění aktualizací pro oblasti v různou dobu, takže je možné, že může mít virtuální počítače v různých oblastech v různých verzích.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Výpis rozšíření nasadit do virtuálního počítače

```powershell
$vm = Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aktualizace agenta

Agent hosta Windows obsahuje pouze *zpracování rozšíření kódu*, *Windows zřizování kód* jsou oddělené. Můžete odinstalovat agenta hosta systému Windows. Nelze zakázat automatické aktualizace agenta hosta okno.

*Zpracování rozšíření kódu* zodpovídá za komunikaci s prostředky infrastruktury Azure a zpracování operace rozšíření virtuálního počítače, jako nainstaluje, zprávy o stavu, aktualizaci jednotlivých rozšíření a jejich odebrání. Aktualizace obsahují opravy zabezpečení, oprav chyb a vylepšení *zpracování rozšíření kódu*.

Chcete-li zkontrolovat, jaká verze, kterou používáte, přečtěte si téma [zjištění nainstalován Agent hosta Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Rozšíření aktualizací

Když je dostupná aktualizace rozšíření, Agent hosta Windows stáhne a upgraduje rozšíření. Aktualizace automatické rozšíření jsou buď *menší* nebo *opravu Hotfix*. Můžete vyjádřit výslovný souhlas nebo vyjádření výslovného nesouhlasu rozšíření *menší* aktualizace při zřizování rozšíření. Následující příklad ukazuje, jak lze automaticky upgradovat podverze v šabloně Resource Manager s *autoUpgradeMinorVersion ": true, se*:

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

Chcete-li získat nejnovější opravy chyb vedlejší verzi, důrazně doporučujeme vždy vyberte automatickou aktualizaci v nasazeních rozšíření. Aktualizace oprav hotfix, které provedení oprav chyb zabezpečení nebo klíč nemůže být zakázána.

### <a name="how-to-identify-extension-updates"></a>Zjištění aktualizací rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifikace, pokud je rozšíření s verzí autoUpgradeMinorVersion nastavená na virtuálním počítači

Uvidíte z modelu virtuálních počítačů, pokud rozšíření byla zřízena 'autoUpgradeMinorVersion'. Chcete-li zkontrolovat, použijte [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) a zadejte skupinu prostředků a virtuálních počítačů název následujícím způsobem:

```powerShell
 $vm = Get-AzureRmVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Zobrazuje následující příklad výstupu, který *autoUpgradeMinorVersion* je nastaven na *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifikace, když došlo autoUpgradeMinorVersion

Najdete při aktualizaci rozšíření došlo k chybě, projděte si agenta protokolů na virtuální počítač na *C:\WindowsAzure\Logs\WaAppAgent.log*

V následujícím příkladu je virtuální počítač měl *Microsoft.Compute.CustomScriptExtension 1.8* nainstalována. Opravu hotfix byla k dispozici na verzi *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Oprávnění agenta

K provádění úloh, je potřeba agenta spustit jako *místní systém*.

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálního počítače

Každé rozšíření virtuálního počítače může mít při řešení potíží konkrétní rozšíření. Například pokud použijete rozšíření vlastních skriptů, podrobnosti provádění skriptu najdete místně na virtuálním počítači, kde byla rozšíření spustit. Kroky řešení potíží konkrétní rozšíření jsou podrobně popsané v dokumentaci k konkrétní rozšíření.

Následující kroky řešení potíží použít na všechny přípony virtuálních počítačů.

1. Zkontrolujte protokol agenta hosta systému Windows, podívejte se na aktivity při rozšíření byla se zřídí v *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Další podrobnosti v protokolech o skutečné rozšíření *C:\WindowsAzure\Logs\Plugins\<Název_rozšíření >*

3. Zkontrolujte rozšíření dokumentace konkrétní části pro kódy chyb, známé problémy atd.

4. Podívejte se na protokol systému. Zkontrolujte, zda jiné operace, které může mít brání rozšíření, jako je třeba dlouhotrvající instalace jiné aplikace, která vyžaduje výhradní balíček manager přístup.

### <a name="common-reasons-for-extension-failures"></a>Z možných příčin selhání rozšíření

1. Rozšíření mít 20 minut ke spuštění (výjimky jsou rozšíření CustomScript, Chef a DSC, které mají 90 minut). Pokud nasazení překročí tuto dobu, je označena jako vypršení časového limitu. Příčinou může být z důvodu nedostatku prostředků virtuálních počítačů, dalších virtuálních počítačů konfigurace nebo po spuštění úlohy využívání vysoké objemy prostředků a zároveň rozšíření se pokouší o zřízení.

2. Nebyly splněny minimální předpoklady. Některá rozšíření jsou závislé na SKU virtuálních počítačů, například bitové kopie prostředí HPC. Rozšíření může vyžadovat určité síťové požadavky přístup, jako je například komunikaci s Azure Storage nebo veřejné služby. Další příklady může být přístup k balíčku úložišť dostatek místa na disku nebo omezení zabezpečení.

3. Balíček výhradní přístup správce. V některých případech se můžete setkat s dlouhotrvající konfigurace virtuálního počítače a instalace rozšíření konfliktní, kde se oba musí výhradní přístup k Správce balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače pro virtuální počítač, použijte [Get-AzureRmVM ](/powershell/module/azurerm.compute/get-azurermvm) vrátit stav rozšíření. *Dílčí stavy [0]* ukazuje, že rozšíření zřizování úspěšně, znamená to úspěšné nasazení do virtuálního počítače, ale spuštění rozšíření ve virtuálním počítači se nezdařilo, *dílčí stavy [1]*.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Výstup je podobné výstupu v následujícím příkladu:

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

Stav spuštění rozšíření možné také najít na portálu Azure. Chcete-li zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**, pak vyberte požadované rozšíření.

### <a name="rerun-vm-extensions"></a>Znovu spustit, rozšíření virtuálního počítače

Můžou nastat případy, ve kterých musí být znovu rozšíření virtuálního počítače. Rozšíření může znovu odebrat, a pak znovu spustit rozšíření s metodu provádění podle svého výběru. Chcete-li odebrat rozšíření, použijte [odebrat AzureRmVMExtension](/powershell/module/AzureRM.Compute/Remove-AzureRmVMExtension) následujícím způsobem:

```powershell
Remove-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Můžete také odebrat rozšíření na portálu Azure následujícím způsobem:

1. Vyberte virtuální počítač.
2. Zvolte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **odinstalovat**.

## <a name="common-vm-extensions-reference"></a>Běžné odkaz rozšíření virtuálního počítače
| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Windows |Spouštění skriptů na virtuálním počítači Azure |[Rozšíření vlastních skriptů pro Windows](custom-script-windows.md) |
| Rozšíření DSC pro Windows |Rozšíření prostředí PowerShell DSC (Desired State Configuration) |[Rozšíření DSC pro Windows](dsc-overview.md) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření diagnostiky Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření pro přístup virtuálních počítačů Azure |Spravovat uživatele a přihlašovací údaje |[Rozšíření pro přístup virtuálních počítačů pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další postup

Další informace o rozšíření virtuálního počítače najdete v tématu [virtuální počítač Azure rozšíření a funkce přehled](overview.md).