---
title: Azure DevTest Labs integrovat do Azure kanály průběžné integrace a doručování kanálu | Dokumentace Microsoftu
description: Zjistěte, jak Azure DevTest Labs integrovat do Azure kanály průběžné integrace a doručování kanálu
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
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 459b06df954d9cc913b6d1503c9f876f93b494e9
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53082947"
---
# <a name="integrate-azure-devtest-labs-into-your-azure-devops-continuous-integration-and-delivery-pipeline"></a>Azure DevTest Labs integrovat do Azure DevOps, průběžná integrace a doručování kanálu
Můžete použít *úloh Azure DevTest Labs* rozšíření, které je nainstalovaný Azure DevOps na snadno integrovat vaše sestavení a vydání kanálu CI/CD s Azure DevTest Labs. Rozšíření nainstaluje tři úkoly: 
* Vytvoření virtuálního počítače
* Vytvoření vlastní image z virtuálního počítače
* Odstranění virtuálního počítače 

Proces zjednodušuje, například rychle nasadit "zlaté image" pro konkrétní testovací úlohu a po dokončení testu ji odstranit.

Tento článek ukazuje, jak vytvořit a nasadit virtuální počítač, vytvořit vlastní image a odstraňte virtuální počítač, všechny jako jeden kompletní kanál. Každý úkol by obvykle provádět jednotlivě ve vašem vlastním vlastní sestavení, testování a nasazení kanálu.

## <a name="before-you-begin"></a>Před zahájením
Než svůj kanál CI/CD můžete integrovat s Azure DevTest Labs, musíte nainstalovat rozšíření z Visual Studio Marketplace.
1. Přejděte na [úloh Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Vyberte **Install** (Nainstalovat).
1. Dokončete průvodce.

## <a name="create-a-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Tato část popisuje postup vytvoření šablony Azure Resource Manageru, který použijete k vytvoření virtuálního počítače Azure na vyžádání.

1. Chcete-li vytvořit šablonu Resource Manageru ve vašem předplatném, proveďte postup uvedený v [pomocí šablony Resource Manageru](devtest-lab-use-resource-manager-template.md).
1. Před vytvořením šablony Resource Manageru, přidejte [WinRM artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) jako součást vytváření virtuálního počítače.

   Použít úkoly nasazení, jako je nutné mít přístup WinRM *Azure File Copy* a *prostředí PowerShell na cílových počítačích*.

   > [!NOTE]
   > Při použití WinRM se sdílenou IP adresu, je nutné přidat pravidlo NAT pro mapování externí port na portu WinRM. Tento krok není povinný, pokud vytvoříte virtuální počítač s veřejnou IP adresu.
   >
   >

1. Uložení šablony do souboru ve vašem počítači. Název souboru **CreateVMTemplate.json**.
1. Šablona vrácení se změnami do systému správy zdrojů.
1. Otevřete textový editor a vložte do něj následující skript.

   ```powershell
   Param( [string] $labVmId)

   $labVmComputeId = (Get-AzureRmResource -Id $labVmId).Properties.ComputeId

   # Get lab VM resource group name
   $labVmRgName = (Get-AzureRmResource -Id $labVmComputeId).ResourceGroupName

   # Get the lab VM Name
   $labVmName = (Get-AzureRmResource -Id $labVmId).Name

   # Get lab VM public IP address
   $labVMIpAddress = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
                   -Name $labVmName).IpAddress

   # Get lab VM FQDN
   $labVMFqdn = (Get-AzureRmPublicIpAddress -ResourceGroupName $labVmRgName
              -Name $labVmName).DnsSettings.Fqdn

   # Set a variable labVmRgName to store the lab VM resource group name
   Write-Host "##vso[task.setvariable variable=labVmRgName;]$labVmRgName"

   # Set a variable labVMIpAddress to store the lab VM Ip address
   Write-Host "##vso[task.setvariable variable=labVMIpAddress;]$labVMIpAddress"

   # Set a variable labVMFqdn to store the lab VM FQDN name
   Write-Host "##vso[task.setvariable variable=labVMFqdn;]$labVMFqdn"
   ```

1. Skript vrácení se změnami do systému správy zdrojů. Název na něco jako **GetLabVMParams.ps1**.

   Při spuštění tohoto skriptu na agentovi v rámci procesu vydávání verzí, a pokud použijete kroky úloh, jako *Azure File Copy* nebo *prostředí PowerShell na cílových počítačích*, skript shromažďuje hodnoty, které potřebujete nasazení aplikace do virtuálního počítače. Tyto úlohy obvykle použijete k nasazení aplikace na Virtuálním počítači Azure. Úkoly vyžadují hodnoty, jako je název skupiny prostředků virtuálního počítače, IP adresy a plně kvalifikovaný název domény (plně kvalifikovaného názvu domény).

## <a name="create-a-release-pipeline-in-release-management"></a>Vytvořte vydávání v produktu Release Management
Pokud chcete vytvořit kanál pro vydávání verzí, postupujte takto:

1. Na **verze** karty **sestavení a vydání** rozbočovače, vyberte tlačítko plus (+).
1. V **definice vydané verze vytvořit** okna, vyberte **prázdný** šablonu a pak vyberte **Další**.
1. Vyberte **zvolte později**a pak vyberte **vytvořit** vytvořit nový kanál pro vydávání verzí s jeden výchozí prostředí a bez propojených artefaktů.
1. Otevřete místní nabídku v nový kanál pro vydávání verzí, vyberte tři tečky (...) vedle názvu prostředí a pak vyberte **nakonfigurovat proměnné**. 
1. V **konfigurovat - prostředí** okna proměnných, které používáte ve verzi kanálu úkoly, zadejte následující hodnoty:

   a. Pro **vmName**, zadejte název, který jste přiřadili k virtuálnímu počítači při vytváření šablony Resource Manageru na webu Azure Portal.

   b. Pro **uživatelské jméno**, zadejte uživatelské jméno, který jste přiřadili k virtuálnímu počítači při vytváření šablony Resource Manageru na webu Azure Portal.

   c. Pro **heslo**, zadejte heslo, které jste přiřadili k virtuálnímu počítači při vytváření šablony Resource Manageru na webu Azure Portal. Pomocí ikony "visacího zámku nezobrazuje" Skrýt a zabezpečené heslo.

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Další fáze nasazení je vytvoření virtuálního počítače, který chcete použít jako "zlatá image" pro další nasazení. Vytvoříte virtuální počítač v rámci vaší instanci Azure DevTest Labs pomocí úloh, která je vytvořena speciálně pro tento účel. 

1. Kanál pro vydávání verzí, vyberte **přidat úkoly**.
1. Na **nasadit** kartu, přidejte *Azure DevTest Labs vytvořit virtuální počítač* úloh. Úkol nakonfigurujte následujícím způsobem:

   > [!NOTE]
   > Vytvoření virtuálního počítače používat pro následné nasazení najdete v tématu [úloh Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pro **předplatné Azure RM**, vyberte připojení **dostupných připojení služby Azure** seznamu, nebo vytvořte omezenější oprávnění připojení ke svému předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manageru](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Pro **název testovacího prostředí**, vyberte název instance, kterou jste vytvořili dříve.

   c. Pro **název šablony**, zadejte úplnou cestu a název souboru šablony, který jste uložili do vašeho úložiště zdrojového kódu. Předdefinované vlastnosti produktu Release Management můžete použít ke zjednodušení cestu, například:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Pro **parametry šablony**, zadejte parametry pro proměnné, které jsou definovány v šabloně. Použijte název proměnné, které jste definovali v prostředí, například:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Pro **výstupní proměnné – ID virtuálního počítače testovacího prostředí**, budete potřebovat ID nově vytvořenému virtuálnímu počítači pro následné kroky. Nastavit výchozí název, který se automaticky vyplní toto ID v proměnné prostředí **výstupní proměnné** oddílu. Upravit proměnnou v případě potřeby, ale nezapomeňte použít správný název v následných úloh. ID testovacího prostředí virtuálního počítače je zapsán v následujícím tvaru:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

1. Spusťte skript, který jste vytvořili dříve, získat podrobnosti o virtuální počítač DevTest Labs. 
1. Kanál pro vydávání verzí, vyberte **přidat úkoly** a pak klikněte na **nasadit** kartu, přidejte *prostředí Azure PowerShell* úloh. Úkol nakonfigurujte následujícím způsobem:

   > [!NOTE]
   > Shromažďování podrobností virtuálního počítače DevTest Labs, najdete v článku [nasazení: Azure PowerShell](https://github.com/Microsoft/azure-pipelines-tasks/tree/master/Tasks/AzurePowerShellV3) a spusťte tento skript.

   a. Pro **typ připojení Azure**vyberte **Azure Resource Manageru**.

   b. Pro **předplatné Azure RM**, vyberte ze seznamu v části připojení **dostupných připojení služby Azure**, nebo vytvořte omezenější oprávnění připojení ke svému předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manageru](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   c. Pro **typ skriptu**vyberte **soubor skriptu**.
 
   d. Pro **cesta ke skriptu**, zadejte úplnou cestu a název skriptu, který jste uložili do vašeho úložiště zdrojového kódu. Předdefinované vlastnosti produktu Release Management můžete použít ke zjednodušení cestu, například:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Pro **argumenty skriptu**, zadejte název proměnné prostředí, které se automaticky vyplní ID testovací virtuální počítač podle předchozího úkolu, například: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skript shromažďuje požadované hodnoty a ukládá je do proměnné prostředí v rámci kanál pro vydávání verzí tak, aby snadno najdete je v dalších krocích.

1. Nasazení aplikace do nového virtuálního počítače DevTest Labs. Úkoly obvykle použijete k nasazení aplikace jsou *Azure File Copy* a *prostředí PowerShell na cílových počítačích*.
   Informace o virtuálním počítači je třeba pro parametry tyto úlohy jsou uloženy v tři konfigurační proměnné s názvem **labVmRgName**, **labVMIpAddress**, a **labVMFqdn**v rámci kanál pro vydávání verzí. Pokud chcete experimentovat s vytvářením virtuálního počítače s DevTest Labs a vlastní image, bez nutnosti nasazovat aplikaci do něj, můžete tento krok přeskočit.

### <a name="create-an-image"></a>Vytvoření image

Další fází je pro vytvoření image nově nasazeného virtuálního počítače ve vaší instanci Azure DevTest Labs. Potom můžete image k vytvoření kopie virtuálního počítače na vyžádání, kdykoli budete chtít spustit úlohu vývoj nebo spustit některé testy. 

1. Kanál pro vydávání verzí, vyberte **přidat úkoly**.
1. Na **nasadit** kartu, přidejte **Azure DevTest Labs vytvořit vlastní Image** úloh. Nakonfigurujte následujícím způsobem:

   > [!NOTE]
   > Pokud chcete vytvořit image, najdete v článku [úloh Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pro **předplatné Azure RM**v **dostupných připojení služby Azure** seznamu, vyberte připojení ze seznamu nebo vytvořte omezenější oprávnění připojení ke svému předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manageru](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

   b. Pro **název testovacího prostředí**, vyberte název instance, který jste vytvořili dříve.

   c. Pro **název vlastní Image**, zadejte název vlastní image.

   d. (Volitelné) Pro **popis**, zadejte popis, který usnadňuje výběr správné bitové kopie později.

   e. Pro **zdrojového virtuálního počítače testovacího prostředí – ID virtuálního počítače zdrojového testovacího prostředí**, pokud jste změnili výchozí název proměnné prostředí, které se automaticky vyplní s ID testovací virtuální počítač v předchozím úkolu, upravit ho tady. Výchozí hodnota je **$(labVMId)**.

   f. Pro **výstupní proměnné – ID vlastní Image**, budete potřebovat ID nově vytvořené bitové kopie, pokud chcete spravovat nebo odstranit. Výchozí název proměnné prostředí, který je automaticky vyplněný s tímto ID je nastavena v **výstupní proměnné** oddílu. V případě potřeby můžete upravit proměnné.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Závěrečná fáze je odstranit virtuální počítač, který jste nasadili ve vaší instanci Azure DevTest Labs. Obvykle by odstranění virtuálního počítače po spuštění úlohy vývoj nebo testy, které je třeba na nasazených virtuálních počítačů. 

1. Kanál pro vydávání verzí, vyberte **přidat úkoly** a pak klikněte na **nasadit** kartu, přidejte *Azure DevTest Labs odstranit VM* úloh. Nakonfigurujte následujícím způsobem:

      > [!NOTE]
      > Pokud chcete odstranit virtuální počítač, přečtěte si téma [úloh Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pro **předplatné Azure RM**, vyberte připojení **dostupných připojení služby Azure** seznamu, nebo vytvořte omezenější oprávnění připojení ke svému předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manageru](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).
 
   b. Pro **ID virtuálního počítače testovacího prostředí**, pokud jste změnili výchozí název proměnné prostředí, které se automaticky vyplní s ID testovací virtuální počítač v předchozím úkolu, upravit ho tady. Výchozí hodnota je **$(labVMId)**.

1. Zadejte název pro kanál pro vydávání verzí a pak ho uložte.
1. Vytvořit novou verzi, vyberte nejnovější verzi a nasaďte ho do jednotné prostředí v kanálu.

V každé fázi aktualizujte zobrazení instance DevTest Labs na webu Azure Portal do zobrazení virtuálního počítače a bitovou kopii, která jsou vytvářeny a virtuální počítač, který je odstraňován znovu.

Teď můžete vlastní image k vytvoření virtuálních počítačů v případě, že se vyžaduje.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvoření prostředí více virtuálních počítačů pomocí šablon Resource Manageru](devtest-lab-create-environment-from-arm.md).
* Prozkoumejte další šablony Resource Manageru rychlý start pro DevTest Labs automatizace z [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
* V případě potřeby, najdete v článku [řešení potíží s Azure DevOps](https://docs.microsoft.com/azure/devops/pipelines/troubleshooting) stránky.
 
