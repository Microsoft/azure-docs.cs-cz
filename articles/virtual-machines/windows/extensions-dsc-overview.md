---
title: Konfigurace stavu požadovaného pro přehled Azure | Microsoft Docs
description: Další informace o použití rozšíření obslužná rutina Microsoft Azure pro prostředí PowerShell požadovaného stavu konfigurace (DSC). Článek obsahuje požadavky, architektura a rutiny.
services: virtual-machines-windows
documentationcenter: ''
author: mgreenegit
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 02/02/2018
ms.author: migreene
ms.openlocfilehash: e23d0a70cdfcc1b37f02d86dd6418aa28c5bbf2c
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2018
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Úvod do rozšíření obslužné rutiny konfigurace požadovaného stavu Azure

[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Agent virtuálního počítače Azure a související rozšíření jsou součástí služby infrastruktury Microsoft Azure. Rozšíření virtuálního počítače jsou softwarové komponenty, které rozšiřovat funkce virtuálního počítače a zjednodušit různé operace správy virtuálních počítačů.

Primární případ použití pro rozšíření Azure požadovaného stavu konfigurace (DSC) je k navázání připojení virtuální počítač, abyste [služby Azure Automation DSC](../../automation/automation-dsc-overview.md). Zavádění virtuálního počítače poskytuje [výhody](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig#pull-service) , zahrnout průběžnou správu konfigurace virtuálního počítače a integrace s jinými provozní nástroji, jako jsou monitorování Azure.

Můžete použít rozšíření DSC nezávisle na službě Automation DSC. To zahrnuje však singulární akci, která nastane během nasazení. Žádné probíhající vytváření sestav, nebo Správa konfigurace je k dispozici, jinak než místně ve virtuálním počítači.

Tento článek obsahuje informace o oba scénáře: použití rozšíření DSC pro automatizaci registrace a pomocí rozšíření DSC jako nástroj pro přiřazení konfigurací k virtuálním počítačům pomocí sady Azure SDK.

## <a name="prerequisites"></a>Požadavky

- **Místní počítač**: pro interakci s rozšíření virtuálního počítače Azure, musíte použít portál Azure nebo Azure PowerShell SDK.
- **Agent hosta**: virtuálního počítače Azure, který je nakonfigurovaný pomocí konfigurace DSC musí být operační systém, který podporuje Windows Management Framework (WMF) 4.0 nebo novější. Úplný seznam podporovaných verzí operačního systému najdete v tématu [historie verzí rozšíření DSC](https://blogs.msdn.microsoft.com/powershell/2014/11/20/release-history-for-the-azure-dsc-extension/).

## <a name="terms-and-concepts"></a>Podmínky a koncepty

Tato příručka předpokládá znalost následující koncepty:

- **Konfigurace**: dokumentu konfigurace A DSC.
- **Uzel**: cíle pro konfigurace DSC. V tomto dokumentu *uzlu* vždy odkazuje na virtuální počítač Azure.
- **Konfigurační data**: .psd1 soubor, který má prostředí dat pro konfiguraci.

## <a name="architecture"></a>Architektura

Rozšíření Azure DSC používá rozhraní agenta virtuálního počítače Azure, aby doručily, uplatní a sestav o konfiguracích DSC běžící na virtuálních počítačích Azure. Rozšíření DSC přijme dokumentu konfigurace a sadu parametrů. Pokud je zadaný žádný soubor, [výchozí konfigurační skript](#default-configuration-script) vložené s příponou. Výchozí konfigurační skript slouží pouze k nastavení metadat v [správce místní konfigurace](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig).

Při prvním volání rozšíření, nainstaluje verzi WMF pomocí následujícího postupu:

* Je-li operačním systémem virtuálního počítače Azure Windows Server 2016, nebyla provedena žádná akce. Windows Server 2016 již nejnovější verzi prostředí PowerShell nainstalovaný.
* Pokud **wmfVersion** vlastnost určena, nainstalovat tuto verzi WMF, pokud tato verze je nekompatibilní s operačním systémem Virtuálního počítače.
* Pokud žádné **wmfVersion** je zadána vlastnost, je nainstalovaná nejnovější verze WMF, která je použít.

Instalaci WMF vyžaduje restartování. Po restartování počítače se rozšíření soubory ke stažení souboru .zip, který je uveden v **modulesUrl** vlastnost, pokud zadaná. Pokud toto umístění je v úložišti objektů Blob v Azure, můžete zadat tokenu SAS v **sasToken** vlastnost, která má přístup k souboru. Po stažení a vybaleno ZIP funkce konfigurace definované v **configurationFunction** používá pro generování souboru MOF. Rozšíření se pak spustí **Start DscConfiguration-Force** pomocí soubor MOF vygenerovaný soubor. Přípona zachytí výstup a zapíše ho do kanál Azure stav.

### <a name="default-configuration-script"></a>Výchozí konfigurační skript

Rozšíření Azure DSC obsahuje výchozí konfigurační skript, který má by měla být použít, když je využití virtuálního počítače ke službě Azure Automation DSC. Parametry skriptu jsou v souladu s konfigurovatelné vlastnosti [správce místní konfigurace](https://docs.microsoft.com/en-us/powershell/dsc/metaconfig). Parametry skriptu, najdete v části [výchozí konfigurační skript](extensions-dsc-template.md#default-configuration-script) v [rozšíření konfigurace požadovaného stavu s šablon Azure Resource Manageru](extensions-dsc-template.md). Úplné skriptu, najdete v článku [šablony Azure rychlý start v Githubu](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozšíření DSC v šablonách Resource Manageru

Ve většině scénářů šablony nasazení Resource Manager jsou očekávané způsob, jak pracovat s příponou DSC. Další informace a příklady, jak se zahrnuje rozšíření DSC v šablonách nasazení Resource Manager, najdete v části [rozšíření konfigurace požadovaného stavu s šablon Azure Resource Manageru](extensions-dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rozšíření DSC rutiny prostředí PowerShell

Rutiny prostředí PowerShell, které se používají ke správě rozšíření DSC jsou nejvhodnější scénáře shromažďování informací a interaktivní řešení potíží. Rutiny můžete použít k balíčku, publikovat a monitorování nasazení rozšíření DSC. Všimněte si, že rutiny pro rozšíření DSC nejsou ještě aktualizovat, aby fungoval s [výchozí konfigurační skript](#default-configuration-script).

**Publikovat AzureRMVMDscConfiguration** rutiny trvá v konfiguračním souboru, hledá závislé prostředky DSC a vytvoří soubor .zip. Soubor ZIP obsahuje konfiguraci a prostředků DSC, které jsou potřebné k uplatní konfigurace. Rutinu můžete také vytvořit balíček místně pomocí *- OutputArchivePath* parametr. Rutiny, jinak hodnota publikuje soubor .zip do úložiště objektů blob a následně zabezpečí k tokenu SAS.

.Ps1 konfigurační skript, který vytváří rutina je v souboru ZIP v kořenové složce archivu. Složku modulu je umístěn ve složce archivu v prostředky.

**Set-AzureRMVMDscExtension** rutiny vloží nastavení, které vyžaduje rozšíření DSC Powershellu do objekt konfigurace virtuálního počítače.

**Get-AzureRMVMDscExtension** rutina načte stav rozšíření DSC konkrétní virtuální počítač.

**Get-AzureRMVMDscExtensionStatus** rutina načte stav konfigurace DSC, který je vydaných obslužná rutina rozšíření DSC. Tuto akci lze provést na jeden virtuální počítač nebo na skupinu virtuálních počítačů.

**Odebrat AzureRMVMDscExtension** rutina odebere obslužná rutina rozšíření z konkrétní virtuální počítač. Tato rutina nemá *není* odebrat konfiguraci, WMF odinstalovat nebo změnit nastavení použité ve virtuálním počítači. Odebere pouze obslužná rutina rozšíření. 

Důležité informace o rutinách rozšíření DSC Resource Manager:

- Rutiny Azure Resource Manager jsou synchronní.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *verze*, a *umístění*jsou všechny požadované parametry.
- *ArchiveResourceGroupName* je volitelný parametr. Tento parametr můžete zadat, když váš účet úložiště patří do jiné skupině prostředků než ten, kde se má vytvořit virtuální počítač.
- Použití **automatických aktualizací** přepínač tak, aby automaticky aktualizovat obslužná rutina rozšíření na nejnovější verzi, pokud je k dispozici. Všimněte si, zda má tento parametr by mohly mít restartování virtuálního počítače při novou verzi WMF vydání.

### <a name="get-started-with-cmdlets"></a>Začínáme s rutinami

Rozšíření Azure DSC můžete použít dokumenty konfigurace DSC přímo konfiguraci virtuálních počítačích Azure během nasazení. Tento krok není zaregistrovat uzel automatizace. Uzel je *není* centrálně spravované.

Následující příklad ukazuje jednoduchý příklad konfigurace. Uložte konfiguraci místně jako IisInstall.ps1.

```powershell
configuration IISInstall
{
    node "localhost"
    {
        WindowsFeature IIS
        {
            Ensure = "Present"
            Name = "Web-Server"
        }
    }
}
```

Následující příkazy skriptu IisInstall.ps1 umístit na zadaný virtuální počítač. Příkazy také provést konfiguraci a zpětně hlásit stav.

```powershell
$resourceGroup = "dscVmDemo"
$location = "westus"
$vmName = "myVM"
$storageName = "demostorage"
#Publish the configuration script to user storage
Publish-AzureRmVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzureRmVmDscExtension -Version 2.72 -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName iisInstall.ps1.zip -AutoUpdate:$true -ConfigurationName "IISInstall"
```

## <a name="azure-portal-functionality"></a>Funkce Azure portálu

Nastavení DSC na portálu:

1. Přejděte k virtuálnímu počítači. 
2. V části **nastavení** > **Obecné**, vyberte **rozšíření**. 
3. V podokně nové, který je vytvořen, vyberte **přidat**a potom vyberte **DSC Powershellu**.

Na portálu vyžaduje následující vstup:

* **Konfigurace moduly nebo skriptu**: Toto pole je povinné (nebyla aktualizována formuláře [výchozí konfigurační skript](#default-configuration-script)). Konfigurace moduly a skripty vyžadují souboru .ps1, který má konfigurační skript nebo s .ps1 konfigurační skript v kořenovém adresáři souboru ZIP. Pokud používáte soubor .zip, všechny závislé prostředky musí být součástí modulu složek v ZIP. Můžete vytvořit soubor .zip pomocí **publikovat AzureVMDscConfiguration - OutputArchivePath** rutinu, která je součástí sady SDK Azure PowerShell. Soubor ZIP je nahrán do úložiště objektů blob uživatele a zabezpečené tokenu SAS.

* **Soubor konfiguračních dat PSD1**: Toto pole je nepovinné. Pokud vaše konfigurace vyžaduje datový soubor konfigurace v .psd1, použijte toto pole vyberte pole data a odešlete ji do úložiště objektů blob uživatele. Datový soubor konfigurace je zabezpečená službou tokenu SAS v úložišti objektů blob.

* **Modul kvalifikovaný název konfigurace**: víc funkcí konfigurace můžete zahrnout do souboru s příponou .ps1. Zadejte název skriptu konfigurace .ps1, za nímž následuje \\ a název funkce konfigurace. Například pokud váš skript .ps1 configuration.ps1 název a konfigurace je **IisInstall**, zadejte **configuration.ps1\IisInstall**.

* **Konfigurace argumentů**: Pokud konfigurace funkce přijímá argumenty, je zde zadat ve formátu **argumentName1 = value1, argumentName2 = value2**. Tento formát je jiný formát, ve kterém jsou přijímány konfigurace argumenty rutin prostředí PowerShell nebo šablony Resource Manageru.

## <a name="logs"></a>Logs
Místní protokolů v tomto umístění:

```powerShell
C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>
```

## <a name="next-steps"></a>Další postup
* Další informace o DSC Powershellu, přejděte na [centru dokumentace prostředí PowerShell](https://msdn.microsoft.com/powershell/dsc/overview). 
* Zkontrolujte [šablony Resource Manageru pro rozšíření DSC](extensions-dsc-template.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json). 
* Pro další funkce, které můžete spravovat pomocí DSC Powershellu a pro další prostředky DSC, přejděte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
* Podrobnosti o předávání citlivých parametrů do konfigurace najdete v tématu [spravovat pověření bezpečně s obslužná rutina rozšíření DSC](extensions-dsc-credentials.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).

