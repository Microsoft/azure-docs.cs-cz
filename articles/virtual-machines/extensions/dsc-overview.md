---
title: Konfigurace požadovaného stavu pro Azure – přehled
description: Naučte se používat obslužnou rutinu rozšíření Microsoft Azure pro konfiguraci požadovaného stavu prostředí PowerShell (DSC). Tento článek obsahuje požadavky, architekturu a rutiny.
services: virtual-machines
documentationcenter: ''
author: mgoedtel
manager: evansma
editor: ''
tags: azure-resource-manager
keywords: DSC
ms.assetid: bbacbc93-1e7b-4611-a3ec-e3320641f9ba
ms.service: virtual-machines
ms.subservice: extensions
ms.collection: windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: na
ms.date: 07/13/2020
ms.author: magoedte
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: dcdc325633aff5ab828cb1c82f4bb2d8becee967
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102560034"
---
# <a name="introduction-to-the-azure-desired-state-configuration-extension-handler"></a>Úvod k obslužné rutině rozšíření Azure Desired State Configuration

Agent virtuálního počítače Azure a přidružená rozšíření jsou součástí služby infrastruktury Microsoft Azure. Rozšíření virtuálních počítačů jsou softwarové komponenty, které zvyšují funkčnost virtuálních počítačů a zjednodušují různé operace správy virtuálních počítačů.

Primárním případem použití rozšíření DSC (Konfigurace požadovaného stavu) Azure je zavedení virtuálního počítače do [služby konfigurace stavu Azure Automation (DSC)](../../automation/automation-dsc-overview.md).
Služba poskytuje [výhody](/powershell/scripting/dsc/managing-nodes/metaConfig#pull-service) , které zahrnují průběžnou správu konfigurace virtuálních počítačů a integraci s jinými operačními nástroji, jako je monitorování Azure.
Použití rozšíření k registraci virtuálního počítače do služby poskytuje flexibilní řešení, které funguje i v rámci předplatných Azure.

Rozšíření DSC můžete použít nezávisle na službě Automatizace DSC.
Tím se ale do virtuálního počítače zasune jenom konfigurace.
Ve virtuálním počítači není k dispozici žádné Průběžné vytváření sestav, jiné než místně.

Tento článek poskytuje informace o obou scénářích: použití rozšíření DSC pro službu Automation pro automatizaci a používání rozšíření DSC jako nástroje pro přiřazení konfigurací k virtuálním počítačům pomocí sady Azure SDK.

## <a name="prerequisites"></a>Předpoklady

- **Místní počítač**: Pokud chcete pracovat s rozšířením virtuálního počítače Azure, musíte použít Azure Portal nebo sadu Azure PowerShell SDK.
- **Agent hosta**: virtuální počítač Azure NAKONFIGUROVANÝ konfigurací DSC musí být operační systém, který podporuje Windows Management Framework (WMF) 4,0 nebo novější. Úplný seznam podporovaných verzí operačních systémů najdete v části [Historie verzí rozšíření DSC](../../automation/automation-dsc-extension-history.md).

## <a name="terms-and-concepts"></a>Pojmy a koncepty

V této příručce se předpokládá znalost s následujícími koncepty:

- **Konfigurace**: dokument konfigurace DSC.
- **Uzel**: cíl pro konfiguraci DSC. V tomto dokumentu se *uzel* vždy odkazuje na virtuální počítač Azure.
- **Konfigurační data**: soubor. psd1, který obsahuje data o životním prostředí pro konfiguraci.

## <a name="architecture"></a>Architektura

Rozšíření Azure DSC využívá architekturu agenta virtuálního počítače Azure k doručování, přijetí a vytváření sestav o konfiguracích DSC spuštěných na virtuálních počítačích Azure. Rozšíření DSC přijímá konfigurační dokument a sadu parametrů. Pokud není zadán žádný soubor, je do rozšíření vložen [výchozí konfigurační skript](#default-configuration-script) . Výchozí konfigurační skript se používá pouze k nastavení metadat v [místních Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig).

Při prvním volání rozšíření se nainstaluje verze WMF pomocí následující logiky:

- Pokud je operačním systémem virtuálního počítače Azure Windows Server 2016, není provedena žádná akce. V systému Windows Server 2016 již je nainstalována nejnovější verze prostředí PowerShell.
- Pokud je zadána vlastnost **wmfVersion** , je nainstalována verze WMF, pokud není tato verze kompatibilní s operačním systémem virtuálního počítače.
- Pokud není zadána žádná vlastnost **wmfVersion** , je nainstalována nejnovější příslušná verze WMF.

Instalace WMF vyžaduje restart. Po restartování bude rozšíření stahovat soubor zip, který je zadán ve vlastnosti **modulesUrl** , pokud je k dispozici. Pokud je toto umístění v úložišti objektů BLOB v Azure, můžete pro přístup k souboru zadat token SAS ve vlastnosti **sasToken** . Po stažení a rozbalení souboru. zip se funkce konfigurace definovaná v **configurationFunction** spustí a vygeneruje soubor. mof ([Formát MOF (Managed Object Format)](/windows/win32/wmisdk/managed-object-format--mof-)). Rozšíření se pak spustí `Start-DscConfiguration -Force` pomocí generovaného souboru. mof. Rozšíření zachytí výstup a zapíše ho do kanálu stavu Azure.

### <a name="default-configuration-script"></a>Výchozí konfigurační skript

Rozšíření Azure DSC obsahuje výchozí konfigurační skript, který se má použít při připojování virtuálního počítače ke službě Azure Automation DSC. Parametry skriptu jsou zarovnané s konfigurovatelnými vlastnostmi [místních Configuration Manager](/powershell/scripting/dsc/managing-nodes/metaConfig). Parametry skriptu najdete v tématu [výchozí konfigurační skript](dsc-template.md#default-configuration-script) v [požadovaném rozšíření konfigurace stavu pomocí šablon Azure Resource Manager](dsc-template.md). Úplný skript najdete v tématu šablona pro [rychlý Start Azure v GitHubu](https://github.com/Azure/azure-quickstart-templates/blob/master/dsc-extension-azure-automation-pullserver/UpdateLCMforAAPull.zip?raw=true).

## <a name="information-for-registering-with-azure-automation-state-configuration-dsc-service"></a>Informace k registraci ve službě Azure Automation State Configuration (DSC)

Při použití rozšíření DSC k registraci uzlu se službou konfigurace stavu se musí zadat tři hodnoty.

- RegistrationUrl – adresa https účtu Azure Automation
- RegistrationKey – sdílený tajný klíč, který se používá k registraci uzlů u služby
- NodeConfigurationName – název konfigurace uzlu (MOF), který se má vyžádat od služby za účelem konfigurace role serveru.

Tyto informace se dají zobrazit v Azure Portal nebo můžete použít PowerShell.

```powershell
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).Endpoint
(Get-AzAutomationRegistrationInfo -ResourceGroupName <resourcegroupname> -AutomationAccountName <accountname>).PrimaryKey
```

V případě názvu konfigurace uzlu se ujistěte, že konfigurace uzlu existuje v konfiguraci stavu Azure.  Pokud tomu tak není, nasazení rozšíření vrátí chybu.  Také se ujistěte, že používáte název *konfigurace uzlu* , a ne konfiguraci.
Konfigurace je definována ve skriptu, který se používá [ke kompilaci konfigurace uzlu (soubor MOF)](../../automation/automation-dsc-compile.md).
Název bude vždycky konfigurace následovaný tečkou `.` a buď `localhost` nebo určitým názvem počítače.

## <a name="dsc-extension-in-resource-manager-templates"></a>Rozšíření DSC v šablonách Správce prostředků

Ve většině scénářů Správce prostředků šablony nasazení očekávaný způsob, jak pracovat s rozšířením DSC. Další informace a příklady, jak zahrnout rozšíření DSC do šablon nasazení Správce prostředků naleznete v tématu [rozšíření konfigurace požadovaného stavu pomocí šablon Azure Resource Manager](dsc-template.md).

## <a name="dsc-extension-powershell-cmdlets"></a>Rutiny PowerShellu rozšíření DSC

Rutiny PowerShellu, které se používají ke správě rozšíření DSC, se nejlépe použijí při řešení potíží s interaktivním řešením a shromažďování informací. Rutiny můžete použít k balení, publikování a monitorování nasazení rozšíření DSC. Rutiny pro rozšíření DSC se ještě neaktualizovaly, aby fungovaly s [výchozím konfiguračním skriptem](#default-configuration-script).

Rutina **Publish-AzVMDscConfiguration** přebírá v konfiguračním souboru, hledá závislé prostředky DSC a pak vytvoří soubor. zip. Soubor. zip obsahuje konfiguraci a prostředky DSC, které jsou potřeba k tomu, aby se konfigurace přijala. Rutina může balíček také vytvořit místně pomocí parametru *-OutputArchivePath* . V opačném případě rutina publikuje soubor. zip do úložiště objektů BLOB a pak ho zabezpečí pomocí tokenu SAS.

Konfigurační skript. ps1, který rutina vytvoří, je v souboru. zip v kořenovém adresáři složky archivu. Složka modulu je umístěná ve složce archivu v prostředcích.

Rutina **set-AzVMDscExtension** vloží nastavení, které vyžaduje rozšíření PowerShell DSC, do objektu konfigurace virtuálního počítače.

Rutina **Get-AzVMDscExtension** načte stav rozšíření DSC pro konkrétní virtuální počítač.

Rutina **Get-AzVMDscExtensionStatus** načte stav konfigurace DSC vydanou obslužnou rutinou rozšíření DSC. Tuto akci je možné provést na jednom virtuálním počítači nebo ve skupině virtuálních počítačů.

Rutina **Remove-AzVMDscExtension** odebere obslužnou rutinu rozšíření z konkrétního virtuálního počítače. Tato rutina *neodebere konfiguraci* , odinstaluje WMF ani nemění použitá nastavení virtuálního počítače. Pouze odebere obslužnou rutinu rozšíření.

Důležité informace o rutinách rozšíření DSC Správce prostředků:

- Rutiny Azure Resource Manager jsou synchronní.
- Parametry *ResourceGroupName*, *VMName*, *ArchiveStorageAccountName*, *Version* a *Location* jsou všechny povinné.
- *ArchiveResourceGroupName* je nepovinný parametr. Tento parametr můžete zadat, pokud váš účet úložiště patří do jiné skupiny prostředků, než v jaké je virtuální počítač vytvořený.
- Pokud je k dispozici, použijte přepínač **AutoUpdate** k automatické aktualizaci obslužné rutiny rozšíření na nejnovější verzi. Tento parametr může při vydání nové verze WMF způsobit restart virtuálního počítače.

### <a name="get-started-with-cmdlets"></a>Začínáme s rutinami

Rozšíření Azure DSC může použít konfigurační dokumenty DSC k přímé konfiguraci virtuálních počítačů Azure během nasazování. Tento krok neregistruje uzel do automatizace. Uzel *není centrálně* spravovaný.

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

Následující příkazy umístí skript iisInstall.ps1 na zadaný virtuální počítač. Příkazy také spustí konfiguraci a následně nahlásí stav zpět na.

```powershell
$resourceGroup = 'dscVmDemo'
$vmName = 'myVM'
$storageName = 'demostorage'
#Publish the configuration script to user storage
Publish-AzVMDscConfiguration -ConfigurationPath .\iisInstall.ps1 -ResourceGroupName $resourceGroup -StorageAccountName $storageName -force
#Set the VM to run the DSC configuration
Set-AzVMDscExtension -Version '2.76' -ResourceGroupName $resourceGroup -VMName $vmName -ArchiveStorageAccountName $storageName -ArchiveBlobName 'iisInstall.ps1.zip' -AutoUpdate -ConfigurationName 'IISInstall'
```

## <a name="azure-cli-deployment"></a>Nasazení Azure CLI

Rozhraní příkazového řádku Azure můžete použít k nasazení rozšíření DSC do existujícího virtuálního počítače.

Pro virtuální počítač s Windows:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name Microsoft.Powershell.DSC \
  --publisher Microsoft.Powershell \
  --version 2.77 --protected-settings '{}' \
  --settings '{}'
```

Pro virtuální počítač se systémem Linux:

```azurecli
az vm extension set \
  --resource-group myResourceGroup \
  --vm-name myVM \
  --name DSCForLinux \
  --publisher Microsoft.OSTCExtensions \
  --version 2.7 --protected-settings '{}' \
  --settings '{}'
```

## <a name="azure-portal-functionality"></a>Funkce Azure Portal

Nastavení DSC na portálu:

1. Přejít na virtuální počítač.
2. V části **Nastavení** vyberte **Rozšíření**.
3. Na nově vytvořené stránce vyberte **+ Přidat** a pak vyberte **Konfigurace požadovaného stavu prostředí PowerShell**.
4. V dolní části stránky s informacemi o rozšíření klikněte na **vytvořit** .

Portál shromažďuje následující vstup:

- **Moduly nebo skripty konfigurace**: Toto pole je povinné (formulář nebyl aktualizován pro [výchozí konfigurační skript](#default-configuration-script)). Konfigurační moduly a skripty vyžadují soubor. ps1, který má konfigurační skript nebo soubor. zip, který obsahuje konfigurační skript. ps1 v kořenovém adresáři. Použijete-li soubor. zip, musí být všechny závislé prostředky zahrnuty do složek modulů v souboru. zip. Soubor. zip můžete vytvořit pomocí rutiny **Publish-AzureVMDscConfiguration-OutputArchivePath** , která je součástí sady Azure PowerShell SDK. Soubor. zip se nahraje do úložiště objektů BLOB uživatele a zabezpečený tokenem SAS.

- **Modul – kvalifikovaný název konfigurace**: v souboru. ps1 můžete zahrnout více funkcí konfigurace. Zadejte název skriptu Configuration. ps1 následovaný \\ a názvem konfigurační funkce. Například pokud má váš skript. ps1 název configuration.ps1 a konfigurace je **IisInstall**, zadejte **configuration.ps1 \iisinstall**.

- **Argumenty konfigurace**: Pokud funkce konfigurace přebírá argumenty, zadejte je tady ve formátu **argumentName1 = Hodnota1, argumentName2 = hodnota2**. Tento formát je jiný formát, ve kterém jsou konfigurační argumenty přijaty v rutinách PowerShellu nebo šablonách Správce prostředků.

- **Soubor psd1 konfiguračních dat**: Pokud vaše konfigurace vyžaduje soubor dat konfigurace v `.psd1` , pomocí tohoto pole vyberte datový soubor a nahrajte ho do úložiště objektů BLOB uživatele. Soubor konfiguračních dat je zabezpečený tokenem SAS v úložišti objektů BLOB.

- **Verze WMF**: Určuje verzi rozhraní Windows Management Framework (WMF), která má být na vašem virtuálním počítači nainstalovaná. Nastavení této vlastnosti na nejnovější nainstaluje nejnovější verzi WMF. V současné době jsou jedinou možnou hodnotou této vlastnosti 4,0, 5,0, 5,1 a nejnovější. Tyto možné hodnoty se vztahují na aktualizace. Výchozí hodnota je **nejnovější**.

- **Shromažďování dat**: Určuje, zda bude rozšíření shromažďovat telemetrii. Další informace najdete v tématu [shromažďování dat rozšíření Azure DSC](https://devblogs.microsoft.com/powershell/azure-dsc-extension-data-collection-2/).

- **Version (verze**): Určuje verzi rozšíření DSC, která se má nainstalovat. Informace o verzích najdete v tématu [Historie verzí rozšíření DSC](/powershell/scripting/dsc/getting-started/azuredscexthistory).

- **Dílčí verze automatického upgradu**: Toto pole se mapuje na přepínač **AutoUpdate** v rutinách a umožňuje rozšíření při instalaci automaticky aktualizovat na nejnovější verzi. Hodnota **Ano** vydá pokyn obslužné rutině rozšíření k použití nejnovější dostupné verze a hodnota **ne** vynutí instalaci **verze** určeného k instalaci. Výběr možnosti **ne** **není stejný, ale** **ne** .

## <a name="logs"></a>Protokoly

Protokoly pro rozšíření jsou uložené v následujícím umístění: `C:\WindowsAzure\Logs\Plugins\Microsoft.Powershell.DSC\<version number>`

## <a name="next-steps"></a>Další kroky

- Další informace o prostředí PowerShell DSC najdete v [centru dokumentace PowerShellu](/powershell/scripting/dsc/overview/overview).
- Projděte si [šablonu správce prostředků pro rozšíření DSC](dsc-template.md).
- Další funkce, které můžete spravovat pomocí prostředí PowerShell DSC a další prostředky DSC, najdete v [galerii prostředí PowerShell](https://www.powershellgallery.com/packages?q=DscResource&x=0&y=0).
- Podrobnosti o předávání citlivých parametrů do konfigurací najdete v tématu [Zabezpečená Správa přihlašovacích údajů s obslužným rutinou rozšíření DSC](dsc-credentials.md).
