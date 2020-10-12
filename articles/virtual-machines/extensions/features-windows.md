---
title: Rozšíření a funkce virtuálních počítačů Azure pro Windows
description: Zjistěte, jaká rozšíření jsou k dispozici pro virtuální počítače Azure seskupená podle toho, co poskytují nebo zlepšují.
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
ms.openlocfilehash: 611edb06762b96ded7671b70ec0f5d4f07f51848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87829080"
---
# <a name="virtual-machine-extensions-and-features-for-windows"></a>Rozšíření a funkce virtuálních počítačů pro Windows

Rozšíření virtuálních počítačů Azure jsou malé aplikace, které na virtuálních počítačích Azure umožňují provádět úlohy konfigurace a automatizace po nasazení. Pokud virtuální počítač vyžaduje například instalaci softwaru, antivirovou ochranu nebo spuštění interního skriptu, je možné pro tento účel použít rozšíření virtuálního počítače. Rozšíření virtuálních počítačů Azure je možné spustit přes Azure CLI, PowerShell, šablony Azure Resource Manageru a Azure Portal. Rozšíření lze spojit s novým nasazením virtuálního počítače, případně spustit v jakémkoli existujícím systému.

Tento článek poskytuje přehled rozšíření virtuálních počítačů, předpoklady pro používání rozšíření virtuálních počítačů Azure a pokyny k tomu, jak detekovat, spravovat a odebírat rozšíření virtuálních počítačů. Tento článek poskytuje generalizované informace, protože je k dispozici mnoho rozšíření virtuálních počítačů, z nichž každá má potenciálně jedinečnou konfiguraci. Podrobnosti o rozšíření se dají najít v každém dokumentu specifickém pro jednotlivé rozšíření.

 

## <a name="use-cases-and-samples"></a>Případy použití a ukázky

K dispozici je několik různých rozšíření virtuálních počítačů Azure, z nichž každý má konkrétní případ použití. Možné příklady:

- Použijte konfiguraci požadovaného stavu PowerShellu na virtuální počítač s rozšířením DSC pro Windows. Další informace najdete v tématu [rozšíření konfigurace požadovaného stavu Azure](dsc-overview.md).
- Nakonfigurujte monitorování virtuálního počítače pomocí rozšíření virtuálního počítače agenta Log Analytics. Další informace najdete v tématu [připojení virtuálních počítačů Azure k Azure monitor protokolů](../../azure-monitor/learn/quick-collect-azurevm.md).
- Nakonfigurujte virtuální počítač Azure pomocí systému pro použití. Další informace najdete v tématu [Automatizace nasazení virtuálních počítačů Azure pomocí nástroje pro instalaci](/azure/developer/chef/windows-vm-configure).
- Nakonfigurujte monitorování infrastruktury Azure pomocí rozšíření služby Datadog. Další informace najdete na [blogu služby Datadog](https://www.datadoghq.com/blog/introducing-azure-monitoring-with-one-click-datadog-deployment/).


Kromě rozšíření specifických pro procesy je k dispozici rozšíření vlastních skriptů pro virtuální počítače s Windows i Linux. Rozšíření vlastních skriptů pro Windows umožňuje spuštění libovolného skriptu PowerShellu na virtuálním počítači. Vlastní skripty jsou užitečné pro navrhování nasazení Azure, která vyžadují konfiguraci, a to nad rámec toho, co můžou využít nativní nástroje Azure. Další informace najdete v tématu [rozšíření vlastních skriptů pro virtuální počítače s Windows](custom-script-windows.md).

## <a name="prerequisites"></a>Požadavky

Pro zpracování rozšíření na virtuálním počítači potřebujete nainstalovaného agenta Azure Windows. Některá jednotlivá rozšíření mají požadavky, jako je například přístup k prostředkům nebo závislostem.

### <a name="azure-vm-agent"></a>Agent virtuálního počítače Azure

Agent virtuálního počítače Azure spravuje interakce mezi virtuálním počítačem Azure a řadičem prostředků infrastruktury Azure. Agent virtuálního počítače zodpovídá za mnoho funkčních aspektů nasazení a správy virtuálních počítačů Azure, včetně spuštění rozšíření virtuálních počítačů. Agent virtuálního počítače Azure je předinstalovaný na Azure Marketplace imagí a dá se nainstalovat ručně v podporovaných operačních systémech. Agent virtuálního počítače Azure pro Windows je známý jako Agent hosta systému Windows.

Informace o podporovaných operačních systémech a pokyny k instalaci najdete v tématu [Agent virtuálního počítače Azure](agent-windows.md).

#### <a name="supported-agent-versions"></a>Podporované verze agentů

Aby se zajistilo nejlepší možné prostředí, existují minimální verze agenta. Další informace najdete v [tomto článku](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support).

#### <a name="supported-oses"></a>Podporované operačních systémech

Agent hosta systému Windows běží na více operačních systémech, ale rozhraní rozšíření má omezení pro operačních systémech rozšíření. Další informace najdete v [tomto článku](https://support.microsoft.com/help/4078134/azure-extension-supported-operating-systems
).

Některá rozšíření nejsou v rámci všech operačních systémech podporovaná a můžou generovat *kód chyby 51, nepodporovaný operační systém*. Projděte si dokumentaci k jednotlivým příponám, abyste mohli podporu.

#### <a name="network-access"></a>Síťový přístup

Balíčky rozšíření se stáhnou z úložiště rozšíření Azure Storage a nahrávání stavu rozšíření se publikují do Azure Storage. Pokud používáte [podporovanou](https://support.microsoft.com/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support) verzi agentů, nemusíte v oblasti virtuálního počítače povolit přístup k Azure Storage, protože agent může pomocí agenta přesměrovat komunikaci do řadiče Azure Fabric pro komunikaci s agenty (funkce HostGAPlugin prostřednictvím privilegovaného kanálu na privátní IP [168.63.129.16](../../virtual-network/what-is-ip-address-168-63-129-16.md)). Pokud jste v nepodporované verzi agenta, musíte z virtuálního počítače v této oblasti povolený odchozí přístup ke službě Azure Storage.

> [!IMPORTANT]
> Pokud jste zablokovali přístup k *168.63.129.16* pomocí brány firewall hosta nebo proxy serveru, rozšíření selžou bez ohledu na výše uvedené. Vyžadují se porty 80, 443 a 32526.

Agenty lze použít pouze ke stažení balíčků rozšíření a stavu hlášení. Pokud třeba instalace rozšíření potřebuje stáhnout skript z GitHubu (vlastní skript) nebo potřebuje přístup k Azure Storage (Azure Backup), musí se otevřít další porty skupiny zabezpečení brány firewall/sítě. Různá rozšíření mají různé požadavky, protože se jedná o aplikace v pravém. Pro rozšíření, která vyžadují přístup k Azure Storage nebo Azure Active Directory, můžete přístup pomocí [značek služby Azure NSG](../../virtual-network/security-overview.md#service-tags) zpřístupnit pro úložiště nebo azureactivedirectory selhala.

Agent hosta systému Windows nemá proxy server podporu pro přesměrování požadavků na přenos agenta prostřednictvím, což znamená, že Agent hosta systému Windows bude spoléhat na váš vlastní proxy server (Pokud máte jeden) k přístupu k prostředkům na internetu nebo na hostiteli prostřednictvím protokolu IP 168.63.129.16.

## <a name="discover-vm-extensions"></a>Zjistit rozšíření virtuálních počítačů

Pro použití s virtuálními počítači Azure je k dispozici řada různých rozšíření virtuálních počítačů. Chcete-li zobrazit úplný seznam, použijte [příkaz Get-AzVMExtensionImage](/powershell/module/az.compute/get-azvmextensionimage). V následujícím příkladu jsou uvedena všechna dostupná rozšíření v umístění *WestUS* :

```powershell
Get-AzVmImagePublisher -Location "WestUS" | `
Get-AzVMExtensionImageType | `
Get-AzVMExtensionImage | Select Type, Version
```

## <a name="run-vm-extensions"></a>Spustit rozšíření virtuálních počítačů

Rozšíření virtuálních počítačů Azure běží na stávajících virtuálních počítačích, což je užitečné v případě, že potřebujete udělat změny konfigurace nebo obnovit připojení na už nasazeném virtuálním počítači. Rozšíření virtuálních počítačů je také možné seskupit pomocí Azure Resource Managerch nasazení šablon. Pomocí rozšíření se šablonami Správce prostředků můžou být virtuální počítače Azure nasazené a nakonfigurované bez zásahu po nasazení.

Pomocí následujících metod lze spustit rozšíření pro existující virtuální počítač.

### <a name="powershell"></a>PowerShell

Pro spuštění jednotlivých rozšíření existuje několik příkazů PowerShellu. Chcete-li zobrazit seznam, použijte [příkaz Get-Command](/powershell/module/microsoft.powershell.core/get-command) a filtrujte podle *rozšíření*:

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

Následující příklad používá rozšíření vlastních skriptů ke stažení skriptu z úložiště GitHub na cílový virtuální počítač a následné spuštění skriptu. Další informace o rozšíření vlastních skriptů najdete v tématu [Přehled rozšíření vlastních skriptů](custom-script-windows.md).

```powershell
Set-AzVMCustomScriptExtension -ResourceGroupName "myResourceGroup" `
    -VMName "myVM" -Name "myCustomScript" `
    -FileUri "https://raw.githubusercontent.com/neilpeterson/nepeters-azure-templates/master/windows-custom-script-simple/support-scripts/Create-File.ps1" `
    -Run "Create-File.ps1" -Location "West US"
```

V následujícím příkladu se rozšíření přístupu virtuálních počítačů používá k resetování hesla pro správu virtuálního počítače s Windows na dočasné heslo. Další informace o rozšíření přístupu virtuálních počítačů najdete v tématu [resetování služby Vzdálená plocha na virtuálním počítači s Windows](../troubleshooting/reset-rdp.md). Po spuštění tohoto nastavení byste měli resetovat heslo při prvním přihlášení:

```powershell
$cred=Get-Credential

Set-AzVMAccessExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myVMAccess" `
    -Location WestUS -UserName $cred.GetNetworkCredential().Username `
    -Password $cred.GetNetworkCredential().Password -typeHandlerVersion "2.0"
```

Pomocí `Set-AzVMExtension` příkazu můžete spustit libovolné rozšíření virtuálního počítače. Další informace najdete v referenčních informacích k [set-AzVMExtension](/powershell/module/az.compute/set-azvmextension).


### <a name="azure-portal"></a>portál Azure

Rozšíření virtuálních počítačů je možné použít pro existující virtuální počítač prostřednictvím Azure Portal. Vyberte virtuální počítač na portálu, klikněte na **rozšíření**a pak vyberte **Přidat**. V seznamu dostupných rozšíření vyberte požadované rozšíření a postupujte podle pokynů v průvodci.

Následující příklad ukazuje instalaci rozšíření Microsoft Antimalware z Azure Portal:

![Instalace antimalwarového rozšíření](./media/features-windows/installantimalwareextension.png)

### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Rozšíření virtuálních počítačů lze přidat do šablony Azure Resource Manager a spustit s nasazením šablony. Když nasadíte rozšíření s šablonou, můžete vytvořit plně nakonfigurovaná nasazení Azure. Například následující kód JSON je pořízen z šablony Správce prostředků nasadí sadu virtuálních počítačů s vyrovnáváním zatížení a Azure SQL Database a potom do každého virtuálního počítače nainstaluje aplikaci .NET Core. Rozšíření virtuálního počítače má za starosti instalaci softwaru.

Další informace najdete v tématu [úplná správce prostředků šablona](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

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

Další informace o vytváření šablon Správce prostředků najdete v tématu věnovaném [tvorbě Azure Resource Manager šablon s rozšířeními virtuálních počítačů s Windows](../windows/template-description.md#extensions).

## <a name="secure-vm-extension-data"></a>Data rozšíření zabezpečeného virtuálního počítače

Když spustíte rozšíření virtuálního počítače, může být nutné zahrnout citlivé informace, jako jsou přihlašovací údaje, názvy účtů úložiště a přístupové klíče účtu úložiště. Mnoho rozšíření virtuálních počítačů zahrnuje chráněnou konfiguraci, která šifruje data a jenom dešifruje v cílovém virtuálním počítači. Každé rozšíření má specifické schéma chráněné konfigurace a každá je podrobně popsána v dokumentaci ke konkrétnímu rozšíření.

Následující příklad ukazuje instanci rozšíření vlastních skriptů pro Windows. Příkaz, který se má provést, zahrnuje sadu přihlašovacích údajů. V tomto příkladu není příkaz ke spuštění zašifrovaný:

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

Přesunutí **příkazu k provedení** vlastnosti do **chráněné** konfigurace zabezpečuje řetězec spuštění, jak je znázorněno v následujícím příkladu:

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

Na virtuálním počítači Azure s IaaS, který používá rozšíření, se můžou v konzole certifikáty zobrazovat certifikáty, které mají předmět **_generátoru certifikátů Windows Azure CRP_**. Na klasickém virtuálním počítači RDFE mají tyto certifikáty **_pro rozšíření název subjektu Windows Azure Service Management_**.

Tyto certifikáty zabezpečují komunikaci mezi virtuálním počítačem a jeho hostitelem během přenosu chráněných nastavení (hesla, jiné přihlašovací údaje) používaných rozšířeními. Certifikáty jsou sestaveny kontrolerem prostředků infrastruktury Azure a předány agentovi virtuálního počítače. Pokud každý den virtuální počítač zastavíte a spustíte, může kontroler prostředků infrastruktury vytvořit nový certifikát. Certifikát je uložený v úložišti osobních certifikátů počítače. Tyto certifikáty je možné odstranit. Agent virtuálního počítače znovu vytvoří certifikáty v případě potřeby.

### <a name="how-do-agents-and-extensions-get-updated"></a>Jak se aktualizují agenti a rozšíření?

Agenti a rozšíření mají stejný mechanismus aktualizace. Některé aktualizace nevyžadují další pravidla brány firewall.

Pokud je k dispozici aktualizace, nainstaluje se jenom na virtuální počítač, pokud dojde ke změně rozšíření a dalším změnám modelu virtuálního počítače, jako třeba:

- Datové disky
- Rozšíření
- Kontejner diagnostiky spouštění
- Tajné kódy operačního systému hosta
- Velikost virtuálního počítače
- Profil sítě

Vydavatelé zpřístupňují aktualizace oblastí v různých časech, takže je možné mít virtuální počítače v různých oblastech v různých verzích.

#### <a name="listing-extensions-deployed-to-a-vm"></a>Rozšíření pro výpisy nasazené do virtuálního počítače

```powershell
$vm = Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM"
$vm.Extensions | select Publisher, VirtualMachineExtensionType, TypeHandlerVersion
```

```powershell
Publisher             VirtualMachineExtensionType          TypeHandlerVersion
---------             ---------------------------          ------------------
Microsoft.Compute     CustomScriptExtension                1.9
```

#### <a name="agent-updates"></a>Aktualizace agenta

Agent hosta systému Windows obsahuje *kód pro zpracování přípony*, *kód zřizování systému Windows* je oddělený. Můžete odinstalovat agenta hosta systému Windows. Nelze zakázat automatickou aktualizaci agenta hosta okna.

*Kód pro manipulaci s rozšířeními* zodpovídá za komunikaci s prostředky infrastruktury Azure a zpracovává operace rozšíření virtuálních počítačů, jako jsou instalace, stav generování sestav, aktualizace jednotlivých rozšíření a jejich odebrání. Aktualizace obsahují opravy zabezpečení, opravy chyb a vylepšení *kódu pro zpracování rozšíření*.

Pokud chcete zjistit verzi, kterou používáte, přečtěte si téma [Zjištění nainstalovaného agenta hosta systému Windows](agent-windows.md#detect-the-vm-agent).

#### <a name="extension-updates"></a>Aktualizace rozšíření

Pokud je k dispozici aktualizace rozšíření, Agent hosta systému Windows stáhne a upgraduje rozšíření. Automatické aktualizace rozšíření jsou buď *drobné* , nebo *hotfix*. Při zřizování rozšíření můžete povolit nebo odhlásit rozšíření *dílčí* aktualizace. Následující příklad ukazuje, jak automaticky upgradovat dílčí verze v Správce prostředků šabloně pomocí *autoUpgradeMinorVersion ": true,"*:

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

Chcete-li získat nejnovější drobné opravy chyb vydaných verzí, důrazně doporučujeme, abyste vždy vybrali možnost automaticky aktualizovat v nasazeních rozšíření. Aktualizace oprav hotfix, které obsahují opravy zabezpečení nebo chyby klíčů, nelze odhlásit.

### <a name="how-to-identify-extension-updates"></a>Jak identifikovat aktualizace rozšíření

#### <a name="identifying-if-the-extension-is-set-with-autoupgrademinorversion-on-a-vm"></a>Určení, jestli se rozšíření nastavilo pomocí autoUpgradeMinorVersion na virtuálním počítači

V případě, že se rozšíření zřídilo pomocí ' autoUpgradeMinorVersion ', můžete vidět z modelu virtuálního počítače. K ověření použijte [Get-AzVm](/powershell/module/az.compute/get-azvm) a zadejte skupinu prostředků a název virtuálního počítače následujícím způsobem:

```powerShell
 $vm = Get-AzVm -ResourceGroupName "myResourceGroup" -VMName "myVM"
 $vm.Extensions
```

Následující příklad výstupu ukazuje, že *autoUpgradeMinorVersion* je nastavená na *hodnotu true*:

```powershell
ForceUpdateTag              :
Publisher                   : Microsoft.Compute
VirtualMachineExtensionType : CustomScriptExtension
TypeHandlerVersion          : 1.9
AutoUpgradeMinorVersion     : True
```

#### <a name="identifying-when-an-autoupgrademinorversion-occurred"></a>Identifikace, kdy došlo k autoUpgradeMinorVersion

Pokud chcete zjistit, kdy došlo k aktualizaci rozšíření, zkontrolujte protokoly agenta na virtuálním počítači na adrese *C:\WindowsAzure\Logs\WaAppAgent.log* .

V následujícím příkladu měl virtuální počítač nainstalovaný *Microsoft. Compute. CustomScriptExtension 1,8* . Pro verzi *1,9*byla k dispozici oprava hotfix:

```powershell
[INFO]  Getting plugin locations for plugin 'Microsoft.Compute.CustomScriptExtension'. Current Version: '1.8', Requested Version: '1.9'
[INFO]  Auto-Upgrade mode. Highest public version for plugin 'Microsoft.Compute.CustomScriptExtension' with requested version: '1.9', is: '1.9'
```

## <a name="agent-permissions"></a>Oprávnění agenta

Aby bylo možné provádět jeho úkoly, musí být agent spuštěn jako *místní systém*.

## <a name="troubleshoot-vm-extensions"></a>Řešení potíží s rozšířeními virtuálních počítačů

Každé rozšíření virtuálního počítače může mít postup řešení potíží specifický pro toto rozšíření. Pokud například použijete rozšíření vlastních skriptů, podrobnosti spuštění skriptu můžete najít místně na virtuálním počítači, kde bylo rozšíření spuštěno. Jakékoli kroky při odstraňování potíží specifických pro rozšíření jsou podrobně popsané v dokumentaci ke konkrétnímu rozšíření.

Následující kroky pro řešení potíží se vztahují na všechna rozšíření virtuálních počítačů.

1. Pokud chcete zkontrolovat protokol agenta hosta systému Windows, podívejte se na aktivitu při zřizování rozšíření v *C:\WindowsAzure\Logs\WaAppAgent.log* .

2. Další podrobnosti najdete v protokolech aktuálních rozšíření *C:\WindowsAzure\Logs\Plugins \<extensionName> * .

3. Podívejte se na oddíly řešení potíží v dokumentaci ke konkrétnímu rozšíření pro kódy chyb, známé problémy atd.

4. Podívejte se na systémové protokoly. Vyhledejte další operace, které mohly být v konfliktu s rozšířením, například dlouhodobě běžící instalace jiné aplikace, která vyžadovala výhradní přístup správce balíčků.

### <a name="common-reasons-for-extension-failures"></a>Běžné důvody selhání rozšíření

1. Spuštění rozšíření má 20 minut (výjimky jsou rozšířeními CustomScript, a DSC a DSC, která mají 90 minut). Pokud nasazení překročí tuto dobu, bude označeno jako časový limit. Příčinou může být to, že virtuální počítače s nízkou spotřebou, další konfigurace virtuálních počítačů nebo úlohy spouštějí úlohy s vysokými objemy prostředků, zatímco se rozšíření pokouší zřídit.

2. Minimální požadavky nejsou splněné. Některá rozšíření mají závislosti na SKU virtuálních počítačů, jako jsou například Image HPC. Rozšíření můžou vyžadovat určité požadavky na přístup k síti, jako je například komunikace s Azure Storage nebo veřejnými službami. Dalším příkladem může být přístup k úložištím balíčků, nedostatek místa na disku nebo omezení zabezpečení.

3. Výhradní přístup správce balíčků. V některých případech se může vyskytnout dlouho běžící konfigurace virtuálního počítače a instalace rozšíření, kde obě potřebují exkluzivní přístup ke Správci balíčků.

### <a name="view-extension-status"></a>Zobrazit stav rozšíření

Po spuštění rozšíření virtuálního počítače na virtuálním počítači můžete vrátit stav rozšíření pomocí rutiny [Get-AzVM](/powershell/module/az.compute/get-azvm) . *Dílčí stavy [0]* ukazují, že zřizování rozšíření bylo úspěšné, což znamená, že se úspěšně nasadilo na virtuální počítač, ale spuštění rozšíření uvnitř virtuálního počítače selhalo, *dílčí stavy [1]*.

```powershell
Get-AzVM -ResourceGroupName "myResourceGroup" -VMName "myVM" -Status
```

Výstup se podobá následujícímu příkladu výstupu:

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

Stav spuštění rozšíření lze také najít v Azure Portal. Pokud chcete zobrazit stav rozšíření, vyberte virtuální počítač, zvolte **rozšíření**a pak vyberte požadované rozšíření.

### <a name="rerun-vm-extensions"></a>Znovu spustit rozšíření virtuálních počítačů

Můžou nastat případy, kdy je potřeba znovu spustit rozšíření virtuálního počítače. Rozšíření můžete znovu spustit tak, že ho odeberete a pak znovu spustíte rozšíření s vámi zvolenou metodou spuštění. Chcete-li odebrat rozšíření, použijte [příkaz Remove-AzVMExtension](/powershell/module/az.compute/remove-azvmextension) následujícím způsobem:

```powershell
Remove-AzVMExtension -ResourceGroupName "myResourceGroup" -VMName "myVM" -Name "myExtensionName"
```

Můžete také odebrat rozšíření v Azure Portal následujícím způsobem:

1. Vyberte virtuální počítač.
2. Vyberte **rozšíření**.
3. Vyberte požadované rozšíření.
4. Vyberte možnost **odinstalovat**.

## <a name="common-vm-extensions-reference"></a>Referenční informace o běžných rozšířeních virtuálních počítačů
| Název rozšíření | Description | Další informace |
| --- | --- | --- |
| Rozšíření vlastních skriptů pro virtuální počítače |Spouštění skriptů na virtuálním počítači Azure |[Rozšíření vlastních skriptů pro virtuální počítače](custom-script-windows.md) |
| Rozšíření DSC pro Windows |Rozšíření PowerShell DSC (Konfigurace požadovaného stavu) |[Rozšíření DSC pro Windows](dsc-overview.md) |
| Rozšíření Azure Diagnostics |Správa Azure Diagnostics |[Rozšíření Azure Diagnostics](https://azure.microsoft.com/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) |
| Rozšíření přístupu k virtuálnímu počítači Azure |Správa uživatelů a přihlašovacích údajů |[Rozšíření přístupu virtuálních počítačů pro Linux](https://azure.microsoft.com/blog/using-vmaccess-extension-to-reset-login-credentials-for-linux-vm/) |

## <a name="next-steps"></a>Další kroky

Další informace o rozšíření virtuálních počítačů najdete v tématu [Přehled rozšíření a funkcí virtuálních počítačů Azure](overview.md).