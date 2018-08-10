---
title: Rozšíření virtuálních počítačů a funkce pro Windows Azure | Dokumentace Microsoftu
description: Zjistěte, jaká rozšíření jsou dostupná pro Azure virtual machines, seskupené podle co, zadejte nebo zvýšit.
services: virtual-machines-windows
documentationcenter: ''
author: zroiy
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
ms.author: roiyz
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e23d3c39c6fd4e69f03ab0f2c11c9b523307b788
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39715137"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozšíření virtuálních počítačů a funkce pro Windows

Rozšíření virtuálních počítačů (VM) Azure jsou malých aplikací, které poskytují konfiguraci a automatizaci úloh po nasazení na virtuálních počítačích Azure. Například, pokud virtuální počítač vyžaduje instalace softwaru, antivirové ochrany, nebo spusťte skript uvnitř této můžete použít rozšíření virtuálního počítače. Rozšíření virtuálního počítače Azure je možné spustit pomocí rozhraní příkazového řádku Azure, Powershellu, šablon Azure Resource Manageru a webu Azure portal. Rozšíření můžete dodávat s nasazením nového virtuálního počítače nebo spouštět všechny stávající systém.

Tento článek obsahuje přehled rozšíření virtuálních počítačů, požadavků na používání rozšíření virtuálního počítače Azure, a pokyny o tom, jak zjišťovat, spravovat a odebrání rozšíření virtuálních počítačů. Tento článek obsahuje obecné informace, protože mnoho rozšíření virtuálního počítače jsou k dispozici, každý s potenciálně jedinečnou konfiguraci. Podrobnosti o konkrétní rozšíření najdete v jednotlivých dokumentech specifických pro jednotlivá rozšíření.

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

Několik různých rozšíření virtuálního počítače Azure jsou k dispozici, každý s konkrétním případu použití. Možné příklady:

- Použijte PowerShell Desired State konfigurace virtuálního počítače pomocí rozšíření DSC pro Windows. Další informace najdete v tématu [rozšíření Azure Desired State configuration](dsc-overview.md).
- Konfigurace monitorování virtuálního počítače pomocí rozšíření Microsoft Monitoring Agent virtuálního počítače. Další informace najdete v tématu [připojit virtuální počítače Azure do Log Analytics](../../log-analytics/log-analytics-azure-vm-extension.md).
- Konfigurace virtuálního počítače Azure pomocí Chefu. Další informace najdete v tématu [nasazení automatizace virtuálních počítačů Azure pomocí Chefu](../windows/chef-automation.md).
- Konfigurace sledování infrastruktury Azure pomocí služby Datadog rozšíření. Další informace najdete v tématu [blog služby Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Kromě rozšíření specifické pro proces rozšíření vlastních skriptů je k dispozici pro virtuální počítače s Windows a Linux. Rozšíření vlastních skriptů pro Windows umožňuje jakýkoli skript prostředí PowerShell spustit na virtuálním počítači. Vlastní skripty jsou užitečné pro návrh nasazení Azure, které vyžadují konfiguraci nad rámec může poskytnout co nativních nástrojů Azure. Další informace najdete v tématu [rozšíření vlastních skriptů virtuálního počítače Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Požadavky

Pro zpracování rozšíření na virtuálním počítači, musíte nainstalovat agenta Azure Linux. Některé konkrétní rozšíření jsou splněné požadavky, jako je například přístup k prostředkům nebo závislosti.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure slouží ke správě interakcí mezi Virtuálním počítači Azure a kontroler prostředků infrastruktury Azure. Agent virtuálního počítače je zodpovědná za funkční aspekty nasazení a správa virtuálních počítačů Azure, včetně spouštění rozšíření virtuálních počítačů. Agent virtuálního počítače Azure je jako předinstalované na imagích Azure Marketplace a můžete nainstalovat ručně do podporovaných operačních systémů. Azure VM Agent pro Windows se označuje jako agent hosta Windows.

Informace o podporovaných operačních systémů a pokyny k instalaci najdete v tématu [agent virtuálního počítače Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Podporovaný agent verze

K zajištění nejlepšího možného prostředí, jsou minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operační systémy

Agent hosta Windows běží na více operačních systémů, ale rozhraní rozšíření má limit pro operačních systémech tohoto rozšíření. Další informace najdete v tématu [tohoto článku] (https://support.microsoft.com/en-us/help/4078134/azure-extension-supported-operating-systems ).

Některá rozšíření nejsou podporovány ve všech operačních systémů a může vysílat *51 kód chyby, nepodporovaný operační systém*. Možnosti podpory v dokumentaci jednotlivých rozšíření.

#### <a name="network-access"></a>Síťový přístup

Balíčky rozšíření se stáhnou z rozšíření úložiště Azure Storage a stav nahrávání rozšíření jsou odeslány do služby Azure Storage. Pokud používáte [podporované](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verze agentů, není nutné pro povolení přístupu ke službě Azure Storage v oblasti virtuálních počítačů, můžete k používání agenta pro přesměrování komunikace na kontroler prostředků infrastruktury Azure pro komunikaci agenta. Pokud jste v nepodporované verzi agenta, budete muset povolit odchozí přístup k Azure storage v dané oblasti z virtuálního počítače.

> [!IMPORTANT]
> Pokud budete mít zablokovaný přístup k *168.63.129.1* pomocí brány firewall hosta, pak rozšíření nezdaří bez ohledu na to, výše uvedené.

Agenti jde použít jenom chcete stáhnout balíčky rozšíření a vykazování stavu. Například, pokud instalaci rozšíření je potřeba stáhnout skript z Githubu (Custom Script) nebo je nutné získat přístup ke službě Azure Storage (Azure Backup), pak další brány firewall nebo sítě zabezpečení musejí být otevřeny porty skupiny. Různá rozšíření mají různé požadavky, protože jsou aplikace samy. Pro rozšíření, které vyžadují přístup ke službě Azure Storage, můžete povolit přístup pomocí značky služeb Azure NSG pro [úložiště](https://docs.microsoft.com/azure/virtual-network/security-overview#service-tags).

Agent hosta Windows nemá podporu můžete přesměrovat žádosti o přenos agenta pomocí proxy serveru.

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálních počítačů

Mnoho různých rozšíření virtuálního počítače jsou k dispozici pro použití s virtuálními počítači Azure. Pokud chcete zobrazit úplný seznam, použijte [Get-AzureRmVMExtensionImage](/powershell/module/azurerm.compute/get-azurermvmextensionimage). Následující příklad zobrazí seznam všech dostupných rozšíření v *WestUS* umístění:

```powershell
Get-AzureRmVmImagePublisher -Location "WestUS" | `
Get-AzureRmVMExtensionImageType | `
Get-AzureRmVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Spuštění rozšíření virtuálních počítačů

Rozšíření Azure VM spustit na stávajících virtuálních počítačů, což je užitečné, když budete chtít provést změny konfigurace nebo obnovit připojení na již nasazených virtuálních počítačů. Rozšíření virtuálních počítačů může být součástí nasazení šablon Azure Resource Manageru. Pomocí rozšíření pomocí šablon Resource Manageru, je možné nasadit a nakonfigurovat bez zásahu po nasazení virtuálních počítačů Azure.

Následující metody můžete použít ke spuštění rozšíření existujícího virtuálního počítače.

### <a name="powershell"></a>PowerShell

Pro spouštění jednotlivých rozšíření existovat několik příkazů Powershellu. Chcete-li zobrazit seznam, použijte [Get-Command](/powershell/module/microsoft.powershell.core/get-command) a filtrujte *rozšíření*:

```powershell
Get-Command Set-AzureRM*Extension* -Module AzureRM.Compute
```

Získáte výstup podobný následujícímu:

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

Následující příklad používá rozšíření vlastních skriptů stáhněte si skript z úložiště GitHub do cílového virtuálního počítače a pak spusťte skript. Další informace o rozšíření vlastních skriptů, najdete v části [přehled rozšíření vlastních skriptů](custom-script-windows.md).

```powershell
Set-AzureRmVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

V následujícím příkladu se používá rozšíření přístupu virtuálních počítačů se resetovat heslo správce virtuálního počítače s Windows do dočasné heslo. Další informace o rozšíření přístupu virtuálních počítačů najdete v tématu [služby Vzdálená plocha obnovení ve virtuálním počítači Windows](../windows/reset-rdp.md). Po spuštění to byste měli resetovat heslo při prvním přihlášení:

```powershell
$cred=Get-Credential

Set-AzureRmVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

`Set-AzureRmVMExtension` Příkaz je možné spustit libovolné rozšíření virtuálního počítače. Další informace najdete v tématu [Set-AzureRmVMExtension odkaz](https://docs.microsoft.com/powershell/module/azurerm.compute/set-azurermvmextension).


### <a name="azure-portal"></a>portál Azure

Rozšíření virtuálních počítačů můžete použít pro existující virtuální počítač na webu Azure portal. Vyberte virtuální počítač na portálu, zvolte **rozšíření**a pak vyberte **přidat**. Vyberte rozšíření ze seznamu dostupných rozšíření a postupujte podle pokynů v průvodci.

Následující příklad ukazuje instalaci rozšíření Microsoft Antimalware z webu Azure portal:

![Nainstalujte antimalwarové rozšíření](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů lze přidat do šablony Azure Resource Manageru a spustit při nasazení šablony. Když nasadíte rozšíření pomocí šablony, můžete vytvořit plně nakonfigurovaného nasazení Azure. Například následující kód JSON je převzata z Resource Manageru šablony nasadí sadu virtuálních počítačů s vyrovnáváním zatížení a databázi Azure SQL a pak nainstaluje aplikaci .NET Core na každém virtuálním počítači. Rozšíření virtuálního počítače se postará o instalaci softwaru.

Další informace najdete v tématu [kompletní šablonu Resource Manageru](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Další informace o vytváření šablon Resource Manageru najdete v tématu [šablon pro vytváření Azure Resource Manageru pomocí rozšíření virtuálních počítačů Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Zabezpečení dat rozšíření virtuálního počítače

Při spuštění rozšíření virtuálního počítače může být nutné zahrnout citlivé informace, jako je například přihlašovací údaje, názvy účtů úložiště a přístupové klíče účtu úložiště. Mnoho rozšíření virtuálního počítače zahrnují chráněné konfigurace, která šifruje data a dešifruje ji pouze v cílovém virtuálním počítači. Každé rozšíření má specifickou konfiguraci chráněných schéma a každý je podrobně popsán v dokumentaci pro konkrétní rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro Windows. Příkaz provedený zahrnuje sadu přihlašovacích údajů. V tomto příkladu není šifrovaná příkaz pro spuštění:

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

Přechod **příkaz k provedení** vlastnost **chráněné** zabezpečuje konfigurace spuštění řetězce, jak je znázorněno v následujícím příkladu:

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

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak aktualizaci agentů a rozšíření?

Agenti a rozšíření sdílet stejný mechanismus aktualizace. Některé aktualizace se nevyžadují žádné další pravidla brány firewall.

Pokud je k dispozici aktualizace, je pouze nainstalovaná na virtuálním počítači při dojde ke změně rozšíření a jiné změny modelu virtuálního počítače, jako:

- Datové disky
- Rozšíření
- Spouštění diagnostiky kontejnerů
- Tajné kódy hostovaného operačního systému
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupnění aktualizací pro regiony v různých časech, takže je možné, že může mít virtuální počítače v různých oblastech na různé verze.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Seznam rozšíření, které jsou nasazené do virtuálního počítače

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

Agent hosta Windows obsahuje pouze *zpracování rozšíření kódu*, *zřizování Windows kód* neběží. Můžete odinstalovat agenta hosta Windows. Nelze zakázat automatickou aktualizaci agenta hosta okna.

*Zpracování rozšíření kódu* zodpovídá za komunikaci s prostředky infrastruktury Azure a zpracování operace rozšíření virtuálních počítačů, jako nainstaluje, hlásí stav jednotlivých rozšíření a aktualizace je odebrali. Aktualizace obsahují opravy zabezpečení, opravy chyb a vylepšení *zpracování rozšíření kódu*.

Jaké verze jsou spuštěny, najdete v části [zjištění nenainstaloval Agent hosta Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizace rozšíření

Při aktualizaci rozšíření je k dispozici, Agent hosta Windows soubory ke stažení a upgraduje rozšíření. Aktualizace automatické rozšíření jsou buď *menší* nebo *opravu Hotfix*. Můžete vyjádřit výslovný souhlas nebo vyjádřit výslovný nesouhlas rozšíření *menší* aktualizuje při zřizování rozšíření. Následující příklad ukazuje, jak automaticky aktualizovat dílčí verze v šabloně Resource Manageru s *autoUpgradeMinorVersion ": true,"*:

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

Pokud chcete získat nejnovější podverzi opravy, důrazně doporučujeme vždy vybrat automatické aktualizace v nasazení vašeho rozšíření. Aktualizace hotfix, které zajišťují opravy zabezpečení nebo klíč nemůže být zakázána.

### <a name="how-to-identify-extension-updates"></a>Zjištění aktualizace rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Identifikace, pokud je rozšíření nastavená možnost autoupgrademinorversion na virtuálním počítači

Je vidět z modelu virtuálního počítače, pokud rozšíření a byla opatřena "verzi autoUpgradeMinorVersion". Chcete-li zkontrolovat, použijte [Get-AzureRmVm](/powershell/module/azurerm.compute/get-azurermvm) a zadejte skupinu prostředků a virtuální počítač pojmenujte následujícím způsobem:

```powerShell
 $vm = Get-AzureRmVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Následující příklad výstupu ukazuje, že *autoUpgradeMinorVersion* je nastavena na *true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Určení, kdy došlo k chybě verzí autoUpgradeMinorVersion

Chcete-li zobrazit, kdy došlo k aktualizaci rozšíření, zkontrolujte agenta protokoly na virtuálním počítači na *C:\WindowsAzure\Logs\WaAppAgent.log*

V následujícím příkladu virtuální počítač měl *Microsoft.Compute.CustomScriptExtension 1.8* nainstalované. Oprava hotfix byla k dispozici na verzi *1.9*:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Oprávnění agenta

Umožní provádět úkoly, je potřeba agenta spustit jako *místní systém*.

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálních počítačů

Každé rozšíření virtuálního počítače může mít řešení potíží s konkrétní kroky pro rozšíření. Například při použití rozšíření vlastních skriptů podrobnosti spuštění skriptu můžete najít místně na virtuálním počítači, kde byla spuštěna rozšíření. Všechny kroky pro řešení problémů s konkrétní rozšíření jsou podrobně popsány v rozšíření příslušnou část dokumentace.

Následující postup platí pro všechna rozšíření virtuálních počítačů.

1. Zkontrolujte protokol agenta hosta Windows, podívejte se na aktivity při rozšíření se zřídí *C:\WindowsAzure\Logs\WaAppAgent.txt*

2. Pro další informace naleznete v protokolech skutečné rozšíření *C:\WindowsAzure\Logs\Plugins\<extensionName >*

3. Zkontrolujte rozšíření příslušnou část dokumentace části řešení potíží pro kódy chyb, známé problémy atd.

4. Podívejte se na protokoly systému. Kontrola pro jiné operace, které mohou mít brání navigací příponu, třeba dlouho probíhající instalace jiné aplikace, která vyžaduje výhradní balíček správce přístup.

### <a name="common-reasons-for-extension-failures"></a>Běžné důvody selhání rozšíření

1. Máte 20 minut. Chcete-li spustit rozšíření (výjimky jsou rozšíření CustomScript, Chef a DSC, které mají 90 minut). Pokud nasazení překročí tuto dobu, je označena jako vypršení časového limitu. Příčin této chyby může být z důvodu nedostatku prostředků virtuálních počítačů a dalších virtuálních počítačů konfigurace/úloh při spuštění spotřebovává vysoké množství prostředků, zatímco rozšíření se pokouší o zřízení.

2. Nebyly splněny minimální předpoklady. Některá rozšíření jsou závislé na skladové položky virtuálních počítačů, jako jsou obrázky prostředí HPC. Rozšíření můžou vyžadovat některé síťové požadavky na přístup, jako je například komunikace a veřejné služby Azure Storage. Další příklady může být přístup k úložištím balíčku, nemá dostatek místa na disku nebo omezení zabezpečení.

3. Exkluzivní balíček přístup správce. V některých případech se můžete setkat dlouho probíhající konfigurace virtuálního počítače a instalace rozšíření konfliktní, kde obě potřebovat výhradní přístup k Správce balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače na virtuálním počítači použít [Get-AzureRmVM ](/powershell/module/azurerm.compute/get-azurermvm) vrátit stav rozšíření. *Dílčí stavy [0]* odhalí zřizování rozšíření bylo úspěšné, což znamená, že se úspěšně nasadit do virtuálních počítačů, ale provádění rozšíření ve virtuálním počítači se nezdařilo, *dílčí stavy [1]*.

```powershell
Get-AzureRmVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Výstup se podobá následující příklad výstupu:

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

Stav spuštění rozšíření najdete také na webu Azure Portal. Chcete-li zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**, pak vyberte požadovaného rozšíření.

### <a name="rerun-vm-extensions"></a>Znovu spusťte rozšíření virtuálních počítačů

Můžou nastat případy, ve kterých je rozšíření virtuálního počítače potřeba znovu spustit. Rozšíření můžete znovu spustit ho odebrat, a potom znovu spustit rozšíření s metodou spuštění podle vašeho výběru. Chcete-li odebrat rozšíření, použijte [Remove-AzureRmVMExtension](/powershell/module/AzureRM.Compute/Remove-AzureRmVMExtension) následujícím způsobem:

```powershell
Remove-AzureRmVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Můžete také odebrat rozšíření na webu Azure Portal následujícím způsobem:

1. Vyberte virtuální počítač.
2. Zvolte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Zvolte **odinstalovat**.

## <a name="common-vm-extensions-reference"></a>Běžný termín rozšíření virtuálního počítače
| Název rozšíření | Popis | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro Windows |Spouštění skriptů pro virtuální počítač Azure |[Rozšíření vlastních skriptů pro Windows](custom-script-windows.md) |
| Rozšíření DSC pro Windows |Rozšíření PowerShell DSC (Desired State Configuration) |[Rozšíření DSC pro Windows](dsc-overview.md) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření diagnostiky Azure](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístupu virtuálních počítačů Azure |Správa uživatelů a přihlašovacích údajů |[Rozšíření přístupu virtuálních počítačů pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další postup

Další informace o rozšíření virtuálních počítačů najdete v tématu [přehled funkcí a rozšíření virtuálních počítačů Azure](overview.md).