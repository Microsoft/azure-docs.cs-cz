---
title: Integrace Azure DevTest Labs do Azure Pipelines
description: Naučte se integrovat Azure DevTest Labs do Azure Pipelines nepřetržité integrace a kanálu pro doručování.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 96f99d41d0a7ea07bf3854292f9c3bd6245414b3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87288921"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-pipelines-cicd-pipeline"></a>Integrace Azure DevTest Labs do kanálu CI/CD Azure Pipelines

Pomocí rozšíření *Azure DevTest Labs úlohy* můžete integrovat Azure Pipelines průběžná integrace a průběžné doručování (CI/CD) a kanály pro sestavování a vydávání s Azure DevTest Labs. Rozšíření nainstaluje několik úloh, včetně: 

- Vytvořit virtuální počítač
- Vytvoření vlastní image z virtuálního počítače
- Odstranění virtuálního počítače 

Tyto úlohy usnadňují, například rychle nasadit virtuální počítač *zlaté image* pro konkrétní testovací úkol a pak odstranit virtuální počítač po dokončení testu.

Tento článek popisuje, jak pomocí úloh Azure DevTest Labs vytvořit a nasadit virtuální počítač, vytvořit vlastní image a pak tento virtuální počítač odstranit, a to vše jako jeden kanál verze. Obvykle byste úlohy prováděli jednotlivě ve vlastním vytváření, testování a nasazování kanálů.

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="prerequisites"></a>Požadavky

- Zaregistrujte se nebo se přihlaste do své organizace [Azure DevOps](https://dev.azure.com) a [vytvořte projekt](/vsts/organizations/projects/create-project) v organizaci. 
  
- Nainstalujte rozšíření Azure DevTest Labs úlohy z Visual Studio Marketplace.
  
  1. Přejít na [Azure DevTest Labs úlohy](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
  1. Vyberte **získat zdarma**.
  1. Z rozevíracího seznamu vyberte vaši organizaci Azure DevOps a vyberte **nainstalovat**. 
  
## <a name="create-the-template-to-build-an-azure-vm"></a>Vytvoření šablony pro sestavení virtuálního počítače Azure 

Tato část popisuje, jak vytvořit šablonu Azure Resource Manager, kterou použijete k vytvoření virtuálního počítače Azure na vyžádání.

1. Pokud chcete vytvořit šablonu Správce prostředků v předplatném, postupujte podle pokynů v části [použití Správce prostředků šablony](devtest-lab-use-resource-manager-template.md).
   
1. Než vygenerujete šablonu Správce prostředků, přidejte [artefakt WinRM](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) jako součást vytváření virtuálního počítače. Úlohy nasazení, jako je *Azure File Copy* a *PowerShell na cílových počítačích* , potřebují přístup ke službě WinRM. Artefakt WinRM vyžaduje jako parametr název hostitele, který by měl být plně kvalifikovaný název domény (FQDN) virtuálního počítače. 
   
   > [!NOTE]
   > Pokud používáte WinRM se sdílenou IP adresou, musíte přidat pravidlo překladu adres (NAT), abyste namapovali externí port na port WinRM. Pokud vytváříte virtuální počítač s veřejnou IP adresou, nepotřebujete pravidlo překladu adres (NAT).
   
   
1. Uložte šablonu do počítače jako soubor s názvem *CreateVMTemplate.jsv*.
   
1. Vraťte šablonu se změnami do systému správy zdrojového kódu.

## <a name="create-a-script-to-get-vm-properties"></a>Vytvoření skriptu pro získání vlastností virtuálního počítače

Při provádění kroků úkolu, jako je *Azure File Copy* nebo *PowerShell na cílových počítačích* v kanálu vydávání verzí, shromažďuje tento skript hodnoty, které potřebujete k nasazení aplikace do virtuálního počítače. Tyto úlohy byste obvykle použili k nasazení aplikace na virtuální počítač Azure. Úkoly vyžadují hodnoty, jako je název skupiny prostředků virtuálního počítače, IP adresa a plně kvalifikovaný název domény.

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

1. Uložte soubor s názvem, například *GetLabVMParams.ps1*, a vraťte se do systému správy zdrojového kódu. 

## <a name="create-a-release-pipeline-in-azure-pipelines"></a>Vytvoření kanálu verze v Azure Pipelines

Vytvoření nového kanálu vydaných verzí:

1. Na stránce projektu Azure DevOps v levém navigačním panelu vyberte **kanály**  >  **vydaných verzí** .
1. Vyberte **Nový kanál**.
1. V části **Vybrat šablonu**přejděte dolů a vyberte **prázdná úloha**a pak vyberte **použít**.

### <a name="add-and-set-variables"></a>Přidání a nastavení proměnných

Úlohy kanálu používají hodnoty, které jste přiřadili k virtuálnímu počítači při vytváření šablony Správce prostředků v Azure Portal. 

Přidání proměnných pro hodnoty: 

1. Na stránce kanál vyberte kartu **proměnné** .
   
1. Pro každou proměnnou vyberte **Přidat** a zadejte název a hodnotu:
   
   |Name|Hodnota|
   |---|---|
   |*vmName*|Název virtuálního počítače, který jste přiřadili v šabloně Správce prostředků|
   |*Jmen*|Uživatelské jméno pro přístup k virtuálnímu počítači|
   |*heslo*|Heslo pro uživatelské jméno. Vyberte ikonu zámku pro skrytí a zabezpečení hesla.

### <a name="create-a-devtest-labs-vm"></a>Vytvoření virtuálního počítače s DevTest Labs

Dalším krokem je vytvoření virtuálního počítače zlaté image pro použití při budoucích nasazeních. Virtuální počítač vytvoříte v rámci instance Azure DevTest Labs pomocí úlohy *Azure DevTest Labs vytvořit virtuální počítač* .

1. Na kartě **kanál** kanálu vydaných verzí vyberte v části **fáze 1** text s hypertextovými odkazy, aby se **zobrazily úlohy fáze**, a pak vyberte symbol plus **+** vedle **úlohy agenta**. 
   
1. V části **Přidat úlohy**vyberte **Azure DevTest Labs vytvořit virtuální počítač**a vyberte **Přidat**. 
   
1. V levém podokně vyberte **vytvořit Azure DevTest Labs virtuální počítač** . 

1. Následujícím způsobem vyplňte formulář v pravém podokně:
   
   |Pole|Hodnota|
   |---|---|
   |**Předplatné Azure RM**|V rozevíracím seznamu vyberte připojení služby nebo předplatné z **dostupných připojení služby Azure** nebo **dostupná předplatná Azure** a v případě potřeby vyberte **autorizovat** .<br /><br />**Poznámka:** Informace o vytvoření připojení s více omezenými oprávněními k předplatnému Azure najdete v tématu [Azure Resource Manager koncový bod služby](/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).|
   |**Název testovacího prostředí**|Vyberte název existujícího testovacího prostředí, ve kterém se vytvoří virtuální počítač testovacího prostředí.|
   |**Název šablony**|Zadejte úplnou cestu a název souboru šablony, který jste uložili do úložiště zdrojového kódu. Pomocí integrovaných vlastností můžete zjednodušit cestu, například:<br /><br />`$(System.DefaultWorkingDirectory)/Templates/CreateVMTemplate.json`|
   |**Parametry šablony**|Zadejte parametry pro proměnné, které jste definovali dříve:<br /><br />`-newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)`|
   |**Výstupní proměnné**  >  **ID virtuálního počítače testovacího prostředí**|Zadejte proměnnou pro vytvořené ID virtuálního počítače testovacího prostředí. Pokud použijete výchozí **labVMId**, můžete odkazovat na proměnnou v následujících úlohách jako *$ (labVMId)*.<br /><br />Můžete vytvořit jiný název než výchozí, ale nezapomeňte použít správný název v následujících úlohách. ID virtuálního počítače testovacího prostředí můžete napsat v následujícím tvaru:<br /><br />`/subscriptions/{subscription Id}/resourceGroups/{resource group Name}/providers/Microsoft.DevTestLab/labs/{lab name}/virtualMachines/{vmName}`|

### <a name="collect-the-details-of-the-devtest-labs-vm"></a>Shromažďování podrobností o virtuálním počítači DevTest Labs

Spusťte skript, který jste vytvořili dříve, abyste shromáždili podrobnosti o virtuálním počítači DevTest Labs. 

1. Na kartě **kanál** kanálu vydaných verzí vyberte v části **fáze 1** text s hypertextovými odkazy, aby se **zobrazily úlohy fáze**, a pak vyberte symbol plus **+** vedle **úlohy agenta**. 
   
1. V části **Přidat úlohy**vyberte možnost **Azure PowerShell**a vyberte možnost **Přidat**. 
   
1. V levém podokně vyberte **Azure PowerShell skript: FilePath** . 
   
1. Následujícím způsobem vyplňte formulář v pravém podokně:
   
   |Pole|Hodnota|
   |---|---|
   |**Typ připojení Azure**|Vyberte **Azure Resource Manager**.|
   |**Předplatné Azure**|Vyberte připojení služby nebo předplatné.| 
   |**Typ skriptu**|Vyberte **cestu k souboru skriptu**.|
   |**Cesta ke skriptu**|Zadejte úplnou cestu a název skriptu prostředí PowerShell, který jste uložili do úložiště zdrojového kódu. Pomocí integrovaných vlastností můžete zjednodušit cestu, například:<br /><br />`$(System.DefaultWorkingDirectory/Scripts/GetLabVMParams.ps1`|
   |**Argumenty skriptu**|Zadejte název proměnné *labVmId* , která byla naplněna předchozí úlohou, například:<br /><br />`-labVmId '$(labVMId)'`|

Skript shromažďuje požadované hodnoty a ukládá je do proměnných prostředí v rámci kanálu vydaných verzí, takže je můžete snadno odkazovat v následujících krocích.

### <a name="create-a-vm-image-from-the-devtest-labs-vm"></a>Vytvoření image virtuálního počítače z virtuálního počítače DevTest Labs

Dalším úkolem je vytvořit image nově nasazeného virtuálního počítače ve vaší instanci Azure DevTest Labs. Bitovou kopii pak můžete použít k vytvoření kopií virtuálního počítače na vyžádání vždy, když chcete spustit úlohu vývoje nebo spustit některé testy. 

1. Na kartě **kanál** kanálu vydaných verzí vyberte v části **fáze 1** text s hypertextovými odkazy, aby se **zobrazily úlohy fáze**, a pak vyberte symbol plus **+** vedle **úlohy agenta**. 
   
1. V části **Přidat úlohy**vyberte **Azure DevTest Labs vytvořit vlastní image**a vyberte **Přidat**. 
   
1. Nakonfigurujte úlohu následujícím způsobem:
   
   |Pole|Hodnota|
   |---|---|
   |**Předplatné Azure RM**|Vyberte připojení služby nebo předplatné.|
   |**Název testovacího prostředí**|Vyberte název existujícího testovacího prostředí, ve kterém se vytvoří obrázek.|
   |**Název vlastní image**|Zadejte název vlastní image.|
   |**Popis** (volitelné)|Zadejte popis, který usnadňuje výběr správné image později.|
   |**Zdrojový virtuální počítač**  >  testovacího prostředí **ID virtuálního počítače zdrojového testovacího prostředí**|Pokud jste změnili výchozí název proměnné LabVMId, zadejte ji sem. Výchozí hodnota je **$ (labVMId)**.|
   |**Výstupní proměnné**  >  **ID vlastní image**|V případě potřeby můžete upravit výchozí název proměnné.|
   
### <a name="deploy-your-app-to-the-devtest-labs-vm-optional"></a>Nasazení aplikace do virtuálního počítače DevTest Labs (volitelné)

Můžete přidat úlohy, které nasadí vaši aplikaci do nového virtuálního počítače DevTest Labs. Úkoly, které obvykle používáte k nasazení aplikace, jsou *kopírování souborů Azure* a *PowerShellu na cílových počítačích*.

Informace o virtuálním počítači, které potřebujete pro parametry těchto úloh, se ukládají ve třech konfiguračních proměnných s názvem **labVmRgName**, **labVMIpAddress**a **labVMFqdn** v kanálu pro vydávání verzí. Pokud chcete experimentovat jenom s vytvořením virtuálního počítače s DevTest Labs a vlastní image bez nasazení aplikace, můžete tento krok přeskočit.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Posledním úkolem je odstranit virtuální počítač, který jste nasadili v instanci Azure DevTest Labs. Virtuální počítač byste obvykle odstranili po spuštění úloh vývoje nebo spuštění testů, které potřebujete na nasazeném virtuálním počítači. 

1. Na kartě **kanál** kanálu vydaných verzí vyberte v části **fáze 1** text s hypertextovými odkazy, aby se **zobrazily úlohy fáze**, a pak vyberte symbol plus **+** vedle **úlohy agenta**. 
   
1. V části **Přidat úlohy**vyberte **Azure DevTest Labs odstranit virtuální počítač**a vyberte **Přidat**. 
   
1. Nakonfigurujte úlohu následujícím způsobem:
   
   - V části **předplatné Azure RM**vyberte připojení nebo předplatné služby. 
   - Pokud pro **ID virtuálního počítače testovacího prostředí**jste změnili výchozí název proměnné LabVMId, zadejte ji sem. Výchozí hodnota je **$ (labVMId)**.
   
### <a name="save-the-release-pipeline"></a>Uložení kanálu pro vydávání verzí

Postup uložení nového kanálu pro vydávání verzí:

1. Vyberte možnost název **nového kanálu vydání** na stránce kanál vydání a zadejte nový název kanálu. 
   
1. V pravém horním rohu vyberte ikonu **Uložit** . 

## <a name="create-and-run-a-release"></a>Vytvoření a spuštění vydané verze

Vytvoření a spuštění vydaných verzí pomocí nového kanálu:

1. V pravém horním rohu na stránce kanál verze vyberte **vytvořit vydání** . 
   
1. V části **artefakty**vyberte nejnovější sestavení a pak vyberte **vytvořit**.
   
1. V každé fázi vydaných verzí aktualizujte zobrazení instance DevTest Labs v Azure Portal tak, aby se zobrazilo vytváření virtuálních počítačů, vytváření imagí a odstraňování virtuálních počítačů.

Vlastní image můžete použít k vytvoření virtuálních počítačů, kdykoli je budete potřebovat.

## <a name="next-steps"></a>Další kroky
- Naučte [se vytvářet prostředí s několika virtuálními počítači pomocí šablon Správce prostředků](devtest-lab-create-environment-from-arm.md).
- Prozkoumejte další šablony Správce prostředků pro rychlý Start pro automatizaci DevTest Labs z [úložiště GitHub veřejné DevTest Labs](https://github.com/Azure/azure-quickstart-templates).
- V případě potřeby si přečtěte stránku [řešení potíží s Azure DevOps](/azure/devops/pipelines/troubleshooting) .
 
