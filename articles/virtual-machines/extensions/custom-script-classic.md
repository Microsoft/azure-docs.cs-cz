---
title: Rozšíření vlastních skriptů na virtuálním počítači s Windows | Dokumentace Microsoftu
description: Automatizaci úloh konfigurace virtuálního počítače Azure s využitím rozšíření vlastních skriptů ke spouštění skriptů prostředí PowerShell na vzdáleném virtuálním počítači s Windows
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: ebb7340a-8f61-4d3c-a290-d7bf8de2d0bd
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 01/17/2017
ms.author: roiyz
ms.openlocfilehash: f7a1cb0ff1e530f15db7d56421fcc67555ac8ada
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55978557"
---
# <a name="custom-script-extension-for-windows-using-the-classic-deployment-model"></a>Vlastní skript rozšíření pro Windows pomocí modelu nasazení classic

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Zjistěte, jak [provést tento postup pomocí modelu Resource Manageru](custom-script-windows.md).
> [!INCLUDE [virtual-machines-common-classic-createportal](../../../includes/virtual-machines-classic-portal.md)]

Rozšíření vlastních skriptů stahuje a spouští skripty na virtuálních počítačích Azure. Toto rozšíření je užitečné pro konfiguraci po nasazení, instalaci softwaru nebo jakékoli jiné úlohy konfigurace nebo správy. Skripty si můžete stáhnout z Azure Storage nebo z GitHubu, případně je za běhu rozšíření najdete na portálu Azure Portal. Rozšíření vlastních skriptů integruje šablony Azure Resource Manageru a je možné ho spustit také pomocí rozhraní příkazového řádku Azure, PowerShellu, portálu Azure Portal nebo REST API pro virtuální počítač Azure.

Tento dokument podrobně popisuje, jak použít rozšíření vlastních skriptů pomocí modulu Azure PowerShell, šablon Azure Resource Manageru a podrobnosti o řešení potíží v systémech Windows.

## <a name="prerequisites"></a>Požadavky

### <a name="operating-system"></a>Operační systém

Rozšíření vlastních skriptů pro Windows můžete spustit na Windows Server 2008 R2, 2012, 2012 R2 a 2016 uvolní.

### <a name="script-location"></a>Umístění skriptu

Skript musí být uložené ve službě Azure storage nebo jakékoli jiné umístění, které jsou přístupné prostřednictvím platnou adresu URL.

### <a name="internet-connectivity"></a>Připojení k Internetu

Vlastní skript rozšíření pro Windows vyžaduje, aby cílový virtuální počítač je připojený k Internetu. 

## <a name="extension-schema"></a>Schéma rozšíření

Následující kód JSON ukazuje schématu pro rozšíření vlastních skriptů. Rozšíření vyžaduje umístění skriptu (Azure Storage nebo jiného umístění s platnou adresu URL) a příkaz ke spuštění. Při použití služby Azure Storage jako zdroj skriptu, Azure storage účtu název a klíč účtu je povinný. Tyto položky by měly považovat za citlivá data a zadaný v konfiguraci chráněných nastavení rozšíření. Data Azure nastavení rozšíření chráněný virtuální počítač je zašifrovaný a dešifrovat jenom na cílovém virtuálním počítači.

```json
{
    "name": "config-app",
    "type": "Microsoft.ClassicCompute/virtualMachines/extensions",
    "location": "[resourceGroup().location]",
    "apiVersion": "2015-06-01",
    "properties": {
        "publisher": "Microsoft.Compute",
        "extension": "CustomScriptExtension",
        "version": "1.8",
        "parameters": {
            "public": {
                "fileUris": "[myScriptLocation]"
            },
            "private": {
                "commandToExecute": "[myExecutionString]"
            }
        }
    }
}
```

### <a name="property-values"></a>Hodnoty vlastností

| Název | Hodnota / příklad |
| ---- | ---- |
| apiVersion | 2015-06-15 |
| vydavatele | Microsoft.Compute |
| Rozšíření | CustomScriptExtension |
| typeHandlerVersion | 1.8 |
| fileUris (např.) | https://raw.githubusercontent.com/Microsoft/dotnet-core-sample-templates/master/dotnet-core-music-windows/scripts/configure-music-app.ps1 |
| commandToExecute (např.) | prostředí PowerShell - ExecutionPolicy Unrestricted - soubor konfigurace app.ps1 Hudba |

## <a name="template-deployment"></a>Nasazení šablon

Rozšíření virtuálního počítače Azure je možné nasadit s využitím šablon Azure Resource Manageru. Schéma JSON, které jsou podrobně popsané v předchozí části lze použít v šabloně Azure Resource Manageru pro spuštění pomocí rozšíření vlastních skriptů při nasazení šablony Azure Resource Manageru. Ukázková šablona, která zahrnuje rozšíření vlastních skriptů můžete najít zde, [Githubu](https://github.com/Microsoft/dotnet-core-sample-templates/tree/master/dotnet-core-music-windows).

## <a name="powershell-deployment"></a>Nasazení pomocí Powershellu

`Set-AzureVMCustomScriptExtension` Příkaz je možné přidat do existujícího virtuálního počítače rozšíření vlastních skriptů. Další informace najdete v tématu [Set-AzVMCustomScriptExtension ](https://docs.microsoft.com/powershell/module/az.compute/set-azvmcustomscriptextension).

```powershell
# create vm object
$vm = Get-AzureVM -Name 2016clas -ServiceName 2016clas1313

# set extension
Set-AzureVMCustomScriptExtension -VM $vm -FileUri myFileUri -Run 'create-file.ps1'

# update vm
$vm | Update-AzureVM
```

## <a name="troubleshoot-and-support"></a>Řešení potíží a podpora

### <a name="troubleshoot"></a>Řešení potíží

Data o stavu nasazení rozšíření se dají načíst z portálu Azure portal a pomocí modulu Azure PowerShell. Pokud chcete zobrazit stav nasazení rozšíření pro daný virtuální počítač, spusťte následující příkaz.

```powershell
Get-AzureVMExtension -ResourceGroupName myResourceGroup -VMName myVM -Name myExtensionName
```

Rozšíření spuštění výstupní nám zaznamenána soubory nalezené v následujícím adresáři na cílovém virtuálním počítači.

```cmd
C:\WindowsAzure\Logs\Plugins\Microsoft.Compute.CustomScriptExtension
```

Samotný skript se stáhne do následující složky v cílovém virtuálním počítači.

```cmd
C:\Packages\Plugins\Microsoft.Compute.CustomScriptExtension\1.*\Downloads
```

### <a name="support"></a>Podpora

Pokud potřebujete další nápovědu v libovolném bodě v tomto článku, můžete se obrátit odborníků na Azure na [fóra MSDN Azure a Stack Overflow](https://azure.microsoft.com/support/forums/). Alternativně můžete soubor incidentu podpory Azure. Přejděte [web podpory Azure](https://azure.microsoft.com/support/options/) a vyberte získat podporu. Informace o používání podpory Azure najdete v článku [nejčastější dotazy k podpoře Microsoft Azure](https://azure.microsoft.com/support/faq/).
