---
title: Integrace azure devtest labs do vašich Azure kanálů
description: Zjistěte, jak integrovat Azure DevTest Labs do průběžné integrace a kanálu pro průběžné doručování Azure Pipelines
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: a26df85e-2a00-462b-aac1-dd3539532569
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 9604da5252254120ac7bd3fca3f0cc97324aef92
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76293211"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrace azure devtest labs do vašeho kanálu Azure Pipelines CI/CD potrubí

Rozšíření *Úlohy Azure DevTest Labs* můžete použít k integraci průběžné integrace Azure Pipelines a průběžného doručování (CI/CD) sestavení a vydání kanálů s Azure DevTest Labs. Rozšíření nainstaluje několik úloh, včetně: 

- Vytvořit virtuální počítač
- Vytvoření vlastní image z virtuálního počítače
- Odstranění virtuálního počítače 

Tyto úkoly usnadňují například rychlé nasazení virtuálního virtuálního *virtuálního virtuálního montovapro* konkrétní testovací úlohu a po dokončení testu virtuální ho odstranit.

Tento článek ukazuje, jak pomocí úloh Azure DevTest Labs k vytvoření a nasazení virtuálního počítače, vytvoření vlastní image a následném odstranění virtuálního počítače, to vše jako kanál pro jednu verzi. Obvykle byste provádět úkoly jednotlivě ve vlastním sestavení, testování a nasazení kanálů.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Požadavky

- Zaregistrujte se nebo se přihlaste do organizace [Azure DevOps](https://dev.azure.com) a [vytvořte projekt](/vsts/organizations/projects/create-project) v organizaci. 
  
- Nainstalujte rozšíření Úlohy azure devtest labs z webu Visual Studio Marketplace.
  
  1. Přejděte na [azure devtest labs úkoly](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Vyberte **Získat zdarma**.
  1. V rozevíracím souboru vyberte organizaci Azure DevOps a vyberte **Instalovat**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Vytvoření šablony pro vytvoření virtuálního počítače Azure 

Tato část popisuje, jak vytvořit šablonu Azure Resource Manager, kterou používáte k vytvoření virtuálního počítače Azure na vyžádání.

1. Chcete-li v předplatném vytvořit šablonu Správce prostředků, postupujte podle pokynů v části [Použít šablonu Správce prostředků](devtest-lab-use-resource-manager-template.md).
   
1. Před generováním šablony Správce prostředků přidejte [artefakt WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) jako součást vytváření virtuálního počítače. Úlohy nasazení, jako je *Azure File Copy* a *PowerShell na cílových počítačích,* potřebují přístup ke službě WinRM. Artefakt WinRM vyžaduje jako parametr název hostitele, což by měl být plně kvalifikovaný název domény (Plně kvalifikovaný název virtuálního_kva) virtuálního soudu. 
   
   > [!NOTE]
   > Při použití služby WinRM se sdílenou IP adresou je nutné přidat pravidlo NAT, které namapuje externí port na port WinRM. Nepotřebujete pravidlo NAT, pokud vytvoříte virtuální hosto v s veřejnou IP adresou.
   
   
1. Uložte šablonu do počítače jako soubor s názvem *CreateVMTemplate.json*.
   
1. Vrácení šablony se změnami v systému správy zdrojového kódu.

## <a name="create-a-script-to-get-vm-properties"></a>Vytvoření skriptu pro získání vlastností virtuálního počítače

Když spustíte kroky úloh, jako je *Azure File Copy* nebo *PowerShell v cílových počítačích* v kanálu vydání, následující skript shromažďuje hodnoty, které potřebujete k nasazení aplikace do virtuálního počítače. Obvykle byste tyto úkoly použili k nasazení aplikace do virtuálního počítače Azure. Úkoly vyžadují hodnoty, jako je název skupiny prostředků virtuálního počítačů, IP adresa a hlavní název názvu názvu služby.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Vytvoření souboru skriptu:

1. Otevřete textový editor a vložte do něj následující skript.
   
   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Uložte soubor s názvem jako *GetLabVMParams.ps1*a vrátit se změnami do systému správy zdrojového kódu. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Vytvoření kanálu verze v Azure Pipelines

Vytvoření nového kanálu vydání:

1. Na stránce projektu Azure DevOps vyberte **uvolnění kanálů** > **z** levé navigace.
1. Vyberte **Nový kanál**.
1. V části **Vybrat šablonu**přejděte dolů a vyberte **Prázdná úloha**a pak vyberte **Použít**.

### <a name="add-and-set-variables"></a>Přidání a nastavení proměnných

Úkoly kanálu používají hodnoty, které jste přiřadili k virtuálnímu počítači při vytváření šablony Správce prostředků na webu Azure Portal. 

Přidání proměnných pro hodnoty: 

1. Na stránce kanálu vyberte kartu **Proměnné.**
   
1. Pro každou proměnnou vyberte **Přidat** a zadejte název a hodnotu:
   
   |Name (Název)|Hodnota|
   |---|---|
   |*vmNázev*|Název virtuálního počítače, který jste přiřadili v šabloně Správce prostředků|
   |*Username*|Uživatelské jméno pro přístup k virtuálnímu virtuálnímu mísu|
   |*heslo*|Heslo pro uživatelské jméno. Vyberte ikonu zámku, chcete-li skrýt a zabezpečit heslo.

### <a name="create-a-devtest-labs-vm"></a>Vytvoření virtuálního virtuálního provozu devTest Labs

Dalším krokem je vytvoření virtuálního počítače se zlatou image, který se použije pro budoucí nasazení. Virtuální počítač vytvoříte v rámci instance Azure DevTest Labs pomocí *úlohy Azure DevTest Labs Create VM.*

1. Na kartě **Kanál** kanálu vydání vyberte text s hypertextovým odkazem ve **fázi 1** a **zobrazte úkoly fáze**a pak vyberte znaménko **+** plus vedle **úlohy Agent**. 
   
1. V části **Přidat úkoly**vyberte **Azure DevTest Labs Create VM**a vyberte **Přidat**. 
   
1. V levém podokně vyberte **Vytvořit virtuální počítač Azure DevTest Labs.** 

1. Následujícím způsobem vyplňte formulář v pravém podokně:
   
   |Pole|Hodnota|
   |---|---|
   |**Předplatné Azure RM**|V rozevíracím programu vyberte připojení nebo předplatné služby z **dostupných připojení služby Azure** nebo **předplatných Azure** a v případě potřeby vyberte **Autorizovat.**<br /><br />**Poznámka:** Informace o vytvoření omezenějšíoprávnění připojení k předplatnému Azure, najdete v článku [Koncový bod služby Azure Resource Manager](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Název laboratoře**|Vyberte název existujícího testovacího prostředí, ve kterém bude vytvořen virtuální virtuální virtuální soud testovacího prostředí.|
   |**Název šablony**|Zadejte úplnou cestu a název souboru šablony, který jste uložili do úložiště zdrojového kódu. Předdefinované vlastnosti můžete použít ke zjednodušení cesty, například:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parametry šablony**|Zadejte parametry pro proměnné, které jste definovali dříve:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**ID** > **virtuálního virtuálního zařízení virtuálního zařízení laboratoře** pro výstupní proměnné|Zadejte proměnnou pro vytvořené ID virtuálního zařízení testovacího prostředí. Pokud používáte výchozí **labVMId**, můžete odkazovat na proměnnou v následujících úkolech jako *$(labVMId)*.<br /><br />Můžete vytvořit jiný než výchozí název, ale nezapomeňte použít správný název v následujících úkolech. ID virtuálního virtuálního montovavky testovacího prostředí můžete napsat v následujícím formuláři:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Shromažďování podrobností o virtuálním provozu DevTest Labs

Spusťte skript, který jste vytvořili dříve shromažďovat podrobnosti o virtuálním účtu DevTest Labs. 

1. Na kartě **Kanál** kanálu vydání vyberte text s hypertextovým odkazem ve **fázi 1** a **zobrazte úkoly fáze**a pak vyberte znaménko **+** plus vedle **úlohy Agent**. 
   
1. V části **Přidat úkoly**vyberte **Azure PowerShell**a vyberte **Přidat**. 
   
1. V levém podokně vyberte **skript Azure PowerShell: FilePath.** 
   
1. Následujícím způsobem vyplňte formulář v pravém podokně:
   
   |Pole|Hodnota|
   |---|---|
   |**Typ připojení Azure**|Vyberte **Správce prostředků Azure**.|
   |**Předplatné Azure**|Vyberte připojení nebo předplatné služby.| 
   |**Typ skriptu**|Vyberte **Možnost Cesta k souboru skriptu**.|
   |**Cesta skriptu**|Zadejte úplnou cestu a název skriptu PowerShellu, který jste uložili do úložiště zdrojového kódu. Předdefinované vlastnosti můžete použít ke zjednodušení cesty, například:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumenty skriptu**|Zadejte název proměnné *labVmId,* která byla naplněna předchozím úkolem, například:<br /><br />`-labVmId '$(labVMId)'`|

Skript shromažďuje požadované hodnoty a ukládá je do proměnných prostředí v kanálu vydání, takže na ně můžete snadno odkazovat v následujících krocích.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Vytvoření image virtuálního virtuálního soudu z virtuálního virtuálního ms DevTest Labs

Dalším úkolem je vytvořit bitovou kopii nově nasazeného virtuálního počítače v instanci Azure DevTest Labs. Potom můžete použít image k vytvoření kopií virtuálního počítače na vyžádání vždy, když chcete provést úlohu dev nebo spustit některé testy. 

1. Na kartě **Kanál** kanálu vydání vyberte text s hypertextovým odkazem ve **fázi 1** a **zobrazte úkoly fáze**a pak vyberte znaménko **+** plus vedle **úlohy Agent**. 
   
1. V části **Přidat úkoly**vyberte **Azure DevTest Labs Create Custom Image**a vyberte **Přidat**. 
   
1. Nakonfigurujte úlohu následujícím způsobem:
   
   |Pole|Hodnota|
   |---|---|
   |**Předplatné Azure RM**|Vyberte připojení nebo předplatné služby.|
   |**Název laboratoře**|Vyberte název existujícího testovacího prostředí, ve kterém bude obraz vytvořen.|
   |**Název vlastní bitové kopie**|Zadejte název vlastního obrázku.|
   |**Popis** (nepovinné)|Zadejte popis, který umožní pozdější výběr správného obrázku.|
   |**Source Lab VM** > **ID virtuálního virtuálního virtuálního prostředí zdrojového virtuálního prostředí virtuálního prostředí** zdroje|Pokud jste změnili výchozí název proměnné LabVMId, zadejte ji sem. Výchozí hodnota je **$(labVMId)**.|
   |**ID** > **vlastního obrázku výstupních** proměnných|V případě potřeby můžete upravit výchozí název proměnné.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Nasazení aplikace do virtuálního počítače DevTest Labs (volitelné)

Můžete přidat úkoly pro nasazení aplikace do nového virtuálního počítače DevTest Labs. Úkoly, které obvykle používáte k nasazení aplikace jsou *Azure File Copy* a *PowerShell na cílových počítačích*.

Informace o virtuálním počítače, které potřebujete pro parametry těchto úloh, jsou uloženy ve třech konfiguračních proměnných s názvem **labVmRgName**, **labVMIpAddress**a **labVMFqdn** v kanálu vydání. Pokud chcete experimentovat jenom s vytvořením virtuálního účtu DevTest Labs a vlastní image, bez nasazení aplikace na něj, můžete tento krok přeskočit.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Posledním úkolem je odstranit virtuální počítač, který jste nasadili v instanci Azure DevTest Labs. Obvykle byste odstranit virtuální počítač po spuštění dev úlohy nebo spustit testy, které potřebujete na nasazený virtuální počítač. 

1. Na kartě **Kanál** kanálu vydání vyberte text s hypertextovým odkazem ve **fázi 1** a **zobrazte úkoly fáze**a pak vyberte znaménko **+** plus vedle **úlohy Agent**. 
   
1. V části **Přidat úkoly**vyberte **Azure DevTest Labs Delete VM**a vyberte **Přidat**. 
   
1. Nakonfigurujte úlohu následujícím způsobem:
   
   - V části **Předplatné Azure RM**vyberte připojení nebo předplatné služby. 
   - Pokud **Lab VM ID**jste změnili výchozí název proměnné LabVMId, zadejte ho sem. Výchozí hodnota je **$(labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Uložení kanálu vydání

Uložení nového kanálu vydání:

1. Vyberte název **Kanál nové verze** na stránce kanálu vydání a zadejte nový název kanálu. 
   
1. Vpravém horním bodě vyberte ikonu **Uložit.** 

## <a name="create-and-run-a-release"></a>Vytvoření a spuštění vydání

Vytvoření a spuštění verze pomocí nového kanálu:

1. Na stránce kanálu vydání vyberte **Vytvořit vydání** vpravo nahoře. 
   
1. V části **Artefakty**vyberte nejnovější sestavení a pak vyberte **Vytvořit**.
   
1. V každé fázi vydání aktualizujte zobrazení instance DevTest Labs na webu Azure Portal a zobrazte vytvoření virtuálního počítače, vytvoření image a odstranění virtuálního počítače.

Vlastní image můžete použít k vytvoření virtuálních virtuálních měn, kdykoli je budete potřebovat.

## <a name="next-steps"></a>Další kroky
- Přečtěte [si, jak vytvořit prostředí s více virtuálními počítačemi pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md).
- Prozkoumejte další šablony správce prostředků pro rychlý start pro automatizaci DevTest Labs z [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
- V případě potřeby najdete na stránce [řešení potíží s Azure DevOps.](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting)
 
