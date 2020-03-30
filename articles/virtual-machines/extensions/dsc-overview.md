---
title: Přehled konfigurace požadovaného stavu pro Azure
description: Přečtěte si, jak používat obslužnou rutinu rozšíření Microsoft Azure pro konfiguraci požadovaného stavu prostředí PowerShell (DSC). Článek obsahuje požadavky, architekturu a rutiny.
services: virtual-machines-windows
documentationcenter: ''
author: bobbytreed
manager: carmonm
editor: ''
tags: azure-resource-manager
keywords: Dsc
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 05/02/2018
ms.author: robreed
ms.openlocfilehash: dcb63031e6c033ce2372dc05e588b0f54cb1609f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294804"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Úvod k obslužné rutině rozšíření Azure Desired State Configuration

Agent virtuálních počítačích Azure a přidružená rozšíření jsou součástí infrastrukturních služeb Microsoft Azure. Rozšíření virtuálních počítače jsou softwarové součásti, které rozšiřují funkce virtuálních zařízení a zjednodušují různé operace správy virtuálních zařízení.

Primární případ použití pro rozšíření Azure Desired State Configuration (DSC) je zavést virtuální počítač do [služby Azure Automation State Configuration (DSC).](../../automation/automation-dsc-overview.md)
Služba poskytuje [výhody,](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) které zahrnují průběžnou správu konfigurace virtuálních počítačů a integraci s dalšími provozními nástroji, jako je například Monitorování Azure.
Použití rozšíření pro registraci virtuálních her do služby poskytuje flexibilní řešení, které funguje i napříč předplatnými Azure.

Rozšíření DSC můžete použít nezávisle na službě Automation DSC.
To však bude pouze vysunout konfiguraci do virtuálního počítače.
Žádné průběžné vytváření sestav je k dispozici, než místně ve virtuálním ms.

Tento článek obsahuje informace o obou scénářích: použití rozšíření DSC pro automatizaci onboarding a použití rozšíření DSC jako nástroj pro přiřazení konfigurace virtuálních počítačů pomocí Azure SDK.

## <a name="prerequisites"></a>Požadavky

- **Místní počítač:** Chcete-li pracovat s rozšířením virtuálního počítače Azure, musíte použít buď portál Azure Nebo Azure PowerShell SDK.
- **Agent hosta**: Virtuální počítač Azure, který je nakonfigurovaný konfigurací DSC, musí být operační systém, který podporuje rozhraní WMF (Windows Management Framework) 4.0 nebo novější. Úplný seznam podporovaných verzí operačního systému naleznete v [historii verzí rozšíření DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

## <a name="terms-and-concepts"></a>Pojmy a pojmy

Tato příručka předpokládá znalost následujících konceptů:

- **Konfigurace**: Konfigurační dokument DSC.
- **Uzel**: Cíl pro konfiguraci DSC. V tomto dokumentu *uzel* vždy odkazuje na virtuální počítač Azure.
- **Konfigurační data**: Soubor PSD1, který obsahuje environmentální data pro konfiguraci.

## <a name="architecture"></a>Architektura

Rozšíření Azure DSC používá rozhraní agenta virtuálních počítačů Azure k doručování, uzákonění a sestavování konfigurací DSC spuštěných na virtuálních počítačích Azure. Rozšíření DSC přijímá konfigurační dokument a sadu parametrů. Pokud není k dispozici žádný soubor, je s příponou vložen [výchozí konfigurační skript.](#default-configuration-script) Výchozí konfigurační skript se používá pouze k nastavení metadat v [místním konfiguračním správci](/powershell/scripting/dsc/managing-nodes/metaConfig).

Při prvním volání rozšíření nainstaluje verzi wmf pomocí následující logiky:

- Pokud je operační systém Azure VM Windows Server 2016, nepodnikne se žádná akce. Windows Server 2016 už má nainstalovanou nejnovější verzi PowerShellu.
- Pokud je zadána vlastnost **wmfVersion,** je nainstalována tato verze WMF, pokud tato verze není nekompatibilní s os virtuálního zařízení.
- Pokud není zadána žádná vlastnost **wmfVersion,** je nainstalována nejnovější příslušná verze wmf.

Instalace wmf vyžaduje restartování. Po restartování rozšíření stáhne soubor ZIP, který je zadán ve **vlastnosti modulesUrl,** pokud je k dispozici. Pokud je toto umístění v úložišti objektů Blob Azure, můžete zadat token SAS ve vlastnosti **sasToken** pro přístup k souboru. Po stažení a rozbalení .zip se spustí konfigurační funkce definovaná v **configurationFunction,** aby se vygeneroval soubor .mof([Managed Object Format).](https://docs.microsoft.com/windows/win32/wmisdk/managed-object-format--mof-) Přípona `Start-DscConfiguration -Force` se pak spustí pomocí generovaného souboru MOF. Rozšíření zachytí výstup a zapíše jej do kanálu stavu Azure.

### <a name="default-configuration-script"></a>Výchozí konfigurační skript

Rozšíření Azure DSC obsahuje výchozí konfigurační skript, který je určen pro použití při napalubě virtuálního počítače do služby Azure Automation DSC. Parametry skriptu jsou zarovnány s konfigurovatelnými vlastnostmi [nástroje Local Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig). Parametry skriptu najdete [v tématu Výchozí konfigurační skript](dsc-template.md#default-configuration-script) v [rozšíření Konfigurace požadovaného stavu se šablonami Azure Resource Manageru](dsc-template.md). Úplný skript najdete [v šabloně azure quickstart na GitHubu](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informace pro registraci ve službě Azure Automation State Configuration (DSC)

Při použití rozšíření DSC zaregistrovat uzel se službou konfigurace stavu, bude nutné zadat tři hodnoty.

- RegistrationUrl – adresa https účtu Azure Automation
- RegistrationKey - sdílený tajný klíč používaný k registraci uzlů u služby
- NodeConfigurationName - název konfigurace uzlu (MOF) pro vyžádat ze služby ke konfiguraci role serveru

Tyto informace se můžou vidět na [webu Azure Portal](../../automation/automation-dsc-onboarding.md#onboard-a-vm-using-azure-portal) nebo můžete použít PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

Pro název konfigurace uzlu se ujistěte, že konfigurace uzlu existuje v konfiguraci stavu Azure.  Pokud tomu tak není, nasazení rozšíření vrátí selhání.  Také se ujistěte, že používáte název *konfigurace uzlu* a nikoli konfigurace.
Konfigurace je definována ve skriptu, který se používá [ke kompilaci konfigurace uzlu (soubor MOF).](https://docs.microsoft.com/azure/automation/automation-dsc-compile)
Název bude vždy konfigurace následovaná `.` tečkou `localhost` a buď nebo určitým názvem počítače.

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozšíření DSC v šablonách Správce prostředků

Ve většině scénářů jsou šablony nasazení Správce prostředků očekávaným způsobem práce s rozšířením DSC. Další informace a příklady, jak zahrnout rozšíření DSC v šablonách nasazení Správce prostředků, najdete v [tématu rozšíření Konfigurace požadovaného stavu se šablonami Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rutiny powershellu rozšíření DSC

Rutiny prostředí PowerShell, které se používají ke správě rozšíření DSC, se nejlépe používají v interaktivních scénářích řešení potíží a shromažďování informací. Rutiny můžete použít k balení, publikování a monitorování nasazení rozšíření DSC. Rutiny pro rozšíření DSC ještě nejsou aktualizovány pro práci s [výchozím konfiguračním skriptem](#default-configuration-script).

Rutina **Publish-AzVMDscConfiguration** pořídí konfigurační soubor, prohledá jej pro závislé prostředky DSC a potom vytvoří soubor ZIP. Soubor ZIP obsahuje konfigurace a DSC prostředky, které jsou potřebné k provedení konfigurace. Rutina můžete také vytvořit balíček místně pomocí *parametru -OutputArchivePath.* V opačném případě rutina publikuje soubor ZIP do úložiště objektů blob a pak jej zabezpečí tokenem SAS.

Konfigurační skript PS1, který rutina vytvoří, je v souboru ZIP v kořenovém adresáři složky archivu. Složka modulu je umístěna do složky archivu ve zdrojích.

Rutina **Set-AzVMDscExtension** vloží nastavení, která vyžaduje rozšíření PowerShell DSC, do objektu konfigurace virtuálního počítače.

Rutina **Get-AzVMDscExtension** načte stav rozšíření DSC konkrétního virtuálního soudu.

Rutina **Get-AzVMDscExtensionStatus** načte stav konfigurace DSC, která je uzákoněna obslužnou rutinou rozšíření DSC. Tuto akci lze provést na jednom virtuálním počítači nebo na skupině virtuálních počítačích.

Rutina **Remove-AzVMDscExtension** odebere obslužnou rutinu rozšíření z určitého virtuálního virtuálního soudu. Tato rutina *neodebere* konfiguraci, odinstaluje WMF ani nezmění použitá nastavení na virtuálním počítači. Odebere pouze obslužnou rutinu rozšíření. 

Důležité informace o rutinách rozšíření Resource Manager DSC:

- Rutiny Azure Resource Manager jsou synchronní.
- Jsou vyžadovány parametry *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version*a *Location.*
- *ArchiveResourceGroupName* je volitelný parametr. Tento parametr můžete zadat, když váš účet úložiště patří do jiné skupiny prostředků, než ve které je virtuální počítač vytvořen.
- Pomocí **přepínače Automatické aktualizace** můžete automaticky aktualizovat obslužnou rutinu rozšíření na nejnovější verzi, jakmile bude k dispozici. Tento parametr má potenciál způsobit restartování na virtuálním počítači při vydání nové verze WMF.

### <a name="get-started-with-cmdlets"></a>Začínáme s rutinami

Rozšíření Azure DSC můžete použít dokumenty konfigurace DSC přímo nakonfigurovat virtuální počítače Azure během nasazení. Tento krok nezaregistruje uzel automatizace. Uzel *není* centrálně spravován.

Následující příklad ukazuje jednoduchý příklad konfigurace. Uložte konfiguraci místně jako iisInstall.ps1.

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

Následující příkazy umístí skript iisInstall.ps1 na určený virtuální počítač. Příkazy také provést konfiguraci a potom zprávu zpět o stavu.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Nasazení azure cli

Azure CLI slouží k nasazení rozšíření DSC do existujícího virtuálního počítače.

Pro virtuální počítač se systémem Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Pro virtuální stroj se systémem Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funkce portálu Azure

Nastavení DSC na portálu:

1. Přejděte na virtuální hod.
2. V části **Nastavení** vyberte **Rozšíření**.
3. Na nové stránce, která je vytvořena, vyberte **+ Přidat**a pak vyberte **Konfigurace požadovaného stavu prostředí PowerShell**.
4. V dolní části stránky s informacemi o rozšíření klikněte na **Vytvořit.**

Portál shromažďuje následující vstupy:

- **Konfigurační moduly nebo skript**: Toto pole je povinné (formulář nebyl aktualizován pro [výchozí konfigurační skript](#default-configuration-script)). Konfigurační moduly a skripty vyžadují soubor PS1, který má konfigurační skript nebo soubor ZIP s konfiguračním skriptem PS1 v kořenovém adresáři. Pokud používáte soubor ZIP, musí být všechny závislé prostředky zahrnuty do složek modulu v souboru ZIP. Soubor ZIP můžete vytvořit pomocí **rutiny Publish-AzureVMDscConfiguration -OutputArchivePath,** která je součástí sady Azure PowerShell SDK. Soubor ZIP se nahraje do úložiště objektů blob uživatele a bude zabezpečen tokenem SAS.

- **Název konfigurace s kvalifikací modulu**: Do souboru PS1 můžete zahrnout více konfiguračních funkcí. Zadejte název konfiguračního skriptu PS1 následovaný \\ a název konfigurační funkce. Pokud má například skript PS1 název configuration.ps1 a konfigurace je **IisInstall**, zadejte **configuration.ps1\IisInstall**.

- **Argumenty konfigurace**: Pokud konfigurační funkce přebírá argumenty, zadejte je zde do formátu **argumentName1=value1,argumentName2=value2**. Tento formát je jiný formát, ve kterém jsou argumenty konfigurace přijímány v rutinách prostředí PowerShell nebo šablonách Správce prostředků.

- **Konfigurační data PSD1 Soubor**: Toto pole je volitelné. Pokud vaše konfigurace vyžaduje konfigurační datový soubor v .psd1, použijte toto pole k výběru datového pole a jeho nahrání do úložiště objektů blob uživatele. Konfigurační datový soubor je zabezpečený tokenem SAS v úložišti objektů blob.

- **Verze WMF**: Určuje verzi rozhraní WMF (Windows Management Framework), která by měla být nainstalována na vašem virtuálním počítači. Nastavení této vlastnosti na nejnovější nainstaluje nejnovější verzi WMF. V současné době pouze možné hodnoty pro tuto vlastnost jsou 4.0, 5.0, 5.1 a nejnovější. Tyto možné hodnoty podléhají aktualizacím. Výchozí hodnota je **nejnovější**.

- **Shromažďování dat**: Určuje, zda rozšíření bude shromažďovat telemetrii. Další informace naleznete v [tématu Azure DSC rozšíření kolekce dat](https://blogs.msdn.microsoft.com/powershell/2016/02/02/azure-dsc-extension-data-collection-2/).

- **Verze**: Určuje verzi rozšíření DSC k instalaci. Informace o verzích naleznete v [tématu Historie verzí rozšíření DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Automatická inovace dílčí verze**: Toto pole se mapuje na přepínač **automatické aktualizace** v rutinách a umožňuje rozšíření automaticky aktualizovat na nejnovější verzi během instalace. **Ano** dá pokyn obslužné rutině rozšíření, aby používala nejnovější dostupnou verzi, a **ne** vynutí instalaci zadané **verze.** Výběr možnosti **Ano** ani **Ne** je stejný jako výběr **možnosti Ne**.

## <a name="logs"></a>Protokoly

Protokoly pro rozšíření jsou uloženy v následujícím umístění:`C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Další kroky

- Další informace o prostředí PowerShell DSC naleznete v [dokumentačním centru prostředí PowerShell](/powershell/scripting/dsc/overview/overview).
- Zkontrolujte [šablonu Správce prostředků pro rozšíření DSC](dsc-template.md).
- Další funkce, které můžete spravovat pomocí prostředí PowerShell DSC, a další prostředky DSC naleznete v [galerii prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Podrobnosti o předávání citlivých parametrů do konfigurací naleznete v [tématu Správa pověření bezpečně pomocí obslužné rutiny rozšíření DSC](dsc-credentials.md).
