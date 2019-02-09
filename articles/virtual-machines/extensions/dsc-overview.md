---
title: Desired State Configuration pro Azure – přehled
description: Další informace o použití obslužné rutiny rozšíření Microsoft Azure pro prostředí PowerShell Desired State Configuration (DSC). Článek obsahuje požadavky, architektury a rutiny.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: e5e134fa7dd08bad4220866dd4f5bd9b788e624e
ms.sourcegitcommit: 943af92555ba640288464c11d84e01da948db5c0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2019
ms.locfileid: "55980597"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Úvod do obslužné rutiny rozšíření Azure Desired State Configuration

Agent virtuálního počítače Azure a související rozšíření jsou součástí infrastrukturní služby Microsoft Azure. Rozšíření virtuálních počítačů jsou softwarové komponenty, které rozšiřují funkce virtuálního počítače a zjednodušit různé operace správy virtuálního počítače.

Primární případ použití pro rozšíření Azure Desired State Configuration (DSC), je spustit virtuální počítač, který [služby Azure Automation DSC](../../automation/automation-dsc-overview.md). Spuštění virtuálního počítače obsahuje [výhody](/powershell/dsc/metaconfig#pull-service) , které zahrnují průběžnou správu konfigurace virtuálního počítače a integrace s dalšími provozní nástroji, jako je monitorování Azure.

Můžete použít rozšíření DSC nezávisle na službě Automation DSC. To však zahrnuje jednotného akcí, ke které dojde během nasazení. Žádné probíhající vytváření sestav nebo Správa konfigurace je k dispozici, jinak než místně ve virtuálním počítači.

Tento článek obsahuje informace o oba scénáře: použití rozšíření DSC pro připojení služby Automation a pomocí rozšíření DSC jako nástroj pro přiřazení konfigurací k virtuálním počítačům s využitím sady Azure SDK.

## <a name="prerequisites"></a>Požadavky

- **Místní počítač**: K interakci s rozšíření virtuálního počítače Azure, musíte použít na webu Azure portal nebo Azure PowerShell SDK.
- **Agent hosta**: Virtuální počítač Azure, který je nakonfigurovaný v konfiguraci DSC, musí být operační systém, který podporuje Windows Management Frameworku (WMF) 4.0 nebo novější. Úplný seznam podporovaných verzí operačního systému, najdete v článku [historie verzí rozšíření DSC](/powershell/dsc/azuredscexthistory).

## <a name="terms-and-concepts"></a>Terminologie a koncepty

Tento průvodce to předpokládá znalost následujících konceptů:

- **Konfigurace**: Dokument konfigurace DSC.
- **Uzel**: Cíl pro konfiguraci DSC. V tomto dokumentu *uzel* vždy odkazuje na Virtuálním počítači Azure.
- **Konfigurační data**: .Psd1 soubor obsahující údaje o životním prostředí pro konfiguraci.

## <a name="architecture"></a>Architektura

Rozšíření DSC Azure používá rozhraní agenta virtuálního počítače Azure k doručování, přijmout a generování sestav o konfiguracích DSC běžící na virtuálních počítačích Azure. Rozšíření DSC přijímá konfigurační dokument a sadu parametrů. Pokud je k dispozici žádný soubor [výchozí konfigurační skript](#default-configuration-script) vložených s příponou. Výchozí konfigurační skript slouží pouze k nastavení metadat [Local Configuration Manageru](/powershell/dsc/metaconfig).

Při prvním volání rozšíření, nainstaluje verzi WMF pomocí následujícího postupu:

- Pokud operační systém virtuálního počítače Azure je Windows Server 2016, nedojde k žádné akci. Windows Server 2016 už má nejnovější verzi prostředí PowerShell nainstalovaný.
- Pokud **wmfVersion** je zadána vlastnost, je nainstalovat tuto verzi WMF, pokud tato verze je nekompatibilní s operačním systémem Virtuálního počítače.
- Pokud ne **wmfVersion** je zadána vlastnost, je nainstalovaná nejnovější příslušné verze WMF.

Instalaci WMF vyžaduje restartování. Po restartování počítače, rozšíření, soubory ke stažení souboru .zip, který je zadán v **modulesUrl** vlastnost, je-li k dispozici. Pokud je toto umístění v úložišti objektů Blob v Azure, můžete zadat token SAS v **sasToken** vlastnosti pro přístup k souboru. Po stažení a rozbalení nachází ZIP konfigurace funkce definované v **configurationFunction** spuštění k vygenerování souboru MOF. Rozšíření pak spustí `Start-DscConfiguration -Force` pomocí souboru generovaného MOF. Rozšíření zaznamenává výstup a zapisuje je do kanálu stav služby Azure.

### <a name="default-configuration-script"></a>Výchozí konfigurační skript

Rozšíření DSC Azure obsahuje výchozí konfigurační skript, který má být určen nepoužívá, pokud připojíte virtuální počítač ke službě Azure Automation DSC. Parametry skriptu jsou v souladu s konfigurovatelné vlastnosti [Local Configuration Manageru](/powershell/dsc/metaconfig). Parametry skriptu, naleznete v tématu [výchozí konfigurační skript](dsc-template.md#default-configuration-script) v [Desired State Configuration rozšíření pomocí šablon Azure Resource Manageru](dsc-template.md). Úplná skript, najdete v článku [šablony Azure quickstart na Githubu](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozšíření DSC v šablonách Resource Manageru

Ve většině scénářů nasazení šablony Resource Manageru jsou očekávané způsob práce s rozšíření DSC. Další informace a příklady, jak zahrnout rozšíření DSC v šablonách nasazení Resource Manageru najdete v tématu [Desired State Configuration rozšíření pomocí šablon Azure Resource Manageru](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rozšíření DSC rutin prostředí PowerShell

Rutiny Powershellu, které se používají ke správě rozšíření DSC se uplatňují nejlépe v interaktivní řešení potíží a získávání informací o scénáře. Rutiny slouží k zabalení, publikování a monitorování nasazení rozšíření DSC. Rutiny pro rozšíření DSC nejsou ještě neaktualizovaly pracovat [výchozí konfigurační skript](#default-configuration-script).

**Publikovat AzVMDscConfiguration** rutiny trvá v konfiguračním souboru, hledá závislé prostředky DSC a vytvoří soubor ZIP. Soubor .zip obsahuje soubor konfigurace a prostředky DSC, je nutná k vydává konfigurace. Rutinu můžete také vytvořit balíček místně pomocí *- OutputArchivePath* parametru. V opačném případě rutina publikuje soubor ZIP do úložiště objektů blob a pak zabezpečuje s tokenem SAS.

.Ps1 konfigurační skript, který vytváří rutina je v souboru ZIP v kořenové složce archivu. Složku modulu je umístěn ve složce Archiv v prostředcích.

**Set-AzVMDscExtension** rutiny vkládá nastavení, která vyžaduje rozšíření PowerShell DSC do objektu konfigurace virtuálního počítače.

**Get-AzVMDscExtension** rutina načte stav rozšíření DSC konkrétního virtuálního počítače.

**Get-AzVMDscExtensionStatus** rutina načte stav konfiguraci DSC, která budou přijaty ve obslužné rutiny rozšíření DSC. Tuto akci může provést na jeden virtuální počítač nebo skupinu virtuálních počítačů.

**Odebrat AzVMDscExtension** rutina odebere obslužné rutiny rozšíření z konkrétního virtuálního počítače. Tato rutina provede *není* odebrat konfiguraci, WMF odinstalovat nebo změnit nastavení použitá na virtuálním počítači. Odebere jenom obslužné rutiny rozšíření. 

Důležité informace o rutiny rozšíření DSC Resource Manageru:

- Rutiny Azure Resource Manageru jsou synchronní.
- *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *verze*, a *umístění*jsou všechny požadované parametry.
- *ArchiveResourceGroupName* je volitelný parametr. Tento parametr můžete zadat, když svůj účet úložiště patří do jiné skupině prostředků než ten, kde se vytvoří virtuální počítač.
- Použití **automatických aktualizací** přepínač tak, aby automaticky aktualizovat obslužné rutiny rozšíření na nejnovější verzi, pokud je k dispozici. Tento parametr má potenciál způsobit restartování na virtuálním počítači po vydání nové verze WMF.

### <a name="get-started-with-cmdlets"></a>Začínáme s rutinami

Rozšíření DSC Azure můžete dokumenty konfigurace DSC přímo nakonfigurovat během nasazení virtuálních počítačů Azure. Tento krok není registrace uzlu automatizace. Uzel je *není* centrálně spravované.

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
$resourceGroup = 'dscVmDemo'
$location = 'westus'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate $true -ConfigurationName 'IISInstall'
```

## <a name="azure-portal-functionality"></a>Funkce Azure portal

Nastavení DSC na portálu:

1. Přejděte k virtuálnímu počítači.
2. V části **Nastavení** vyberte **Rozšíření**.
3. Nová stránka, která je vytvořena, vyberte **+ přidat**a pak vyberte **PowerShell Desired State Configuration**.
4. Klikněte na tlačítko **vytvořit** v dolní části na stránce informace o rozšíření.

Na portálu shromažďuje následující vstup:

- **Konfigurace moduly nebo skript**: Toto pole je povinné (se neaktualizoval formulář [výchozí konfigurační skript](#default-configuration-script)). Konfigurace moduly a skripty vyžadují soubor .ps1, který má konfigurace skriptu nebo soubor .zip s .ps1 konfigurační skript v kořenovém adresáři. Pokud používáte soubor ZIP, všechny závislé prostředky musí být součástí modulu složky ZIP. Můžete vytvořit soubor .zip s použitím **publikovat AzureVMDscConfiguration - OutputArchivePath** rutinu, která je součástí sady SDK Azure Powershellu. Soubor ZIP je nahráli do úložiště objektů blob uživatele a zabezpečené pomocí tokenu SAS.

- **Název konfigurace s kvalifikací modulu**: Může obsahovat více funkcí konfigurace v souboru s příponou .ps1. Zadejte název skriptu .ps1 konfigurace, za nímž následuje \\ a název konfigurace funkce. Například pokud má vaše skriptu .ps1 configuration.ps1 název a konfigurace je **IisInstall**, zadejte **configuration.ps1\IisInstall**.

- **Konfigurace argumenty**: Pokud funkce konfigurace má argumenty, je zde zadat ve formátu **argumentName1 = value1, argumentName2 = hodnota2**. Tento formát je v jiném formátu, ve kterém jsou přijímány konfigurační argumenty rutin Powershellu nebo šablony Resource Manageru.

- **Soubor konfiguračních dat PSD1**: Toto pole je nepovinné. Pokud vaše konfigurace vyžaduje soubor konfiguračních dat v .psd1, použijte toto pole vybrat pole dat a nahrajte ho do úložiště objektů blob uživatele. Soubor konfiguračních dat je zabezpečena pomocí tokenu SAS ve službě blob storage.

- **Verze WMF**: Určuje verzi Windows Management Frameworku (WMF), který musí být nainstalován na váš virtuální počítač. Nastavení této vlastnosti na nejnovější nainstaluje nejnovější verzi WMF. V současné době pouze možné hodnoty této vlastnosti jsou 4.0, 5.0, 5.1 a nejnovější. Tyto možné hodnoty jsou v souladu s aktualizací. Výchozí hodnota je **nejnovější**.

- **Shromažďování dat**: Určuje, pokud rozšíření shromažďovat telemetrii. Další informace najdete v tématu [shromažďování dat rozšíření DSC Azure](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Verze**: Určuje verzi rozšíření DSC k instalaci. Informace o verzích, najdete v části [historie verzí rozšíření DSC](/powershell/dsc/azuredscexthistory).

- **Automatický Upgrade podverze**: Toto pole se mapuje na **automatických aktualizací** přepnout v rutinách a povolí rozšíření automaticky aktualizovat na nejnovější verzi během instalace. **Ano** bude dáte pokyn, aby obslužná rutina rozšíření má použít na nejnovější dostupnou verzi a **ne** vynutí **verze** zadaný k instalaci. Výběr ani **Ano** ani **ne** je stejný jako výběr **ne**.

## <a name="logs"></a>Logs

Protokoly pro rozšíření se ukládají v následujícím umístění: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Další postup

- Další informace o prostředí PowerShell DSC, přejděte [centrum dokumentace k Powershellu](/powershell/dsc/overview).
- Zkontrolujte [šablony Resource Manageru pro rozšíření DSC](dsc-template.md).
- Další funkce, které můžete spravovat pomocí prostředí PowerShell DSC a další prostředky DSC, přejděte [Galerie prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Podrobnosti o předání parametrů citlivé na konfigurace najdete v tématu [Správa přihlašovacích údajů, bezpečně se obslužné rutiny rozšíření DSC](dsc-credentials.md).