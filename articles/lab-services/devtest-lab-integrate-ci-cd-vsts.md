---
title: Do vaší služby VSTS průběžnou integraci a doručení kanálu integrovat Azure DevTest Labs | Microsoft Docs
description: Zjistěte, jak integrovat Azure DevTest Labs do vaší služby VSTS průběžnou integraci a doručení kanálu
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
ms.openlocfilehash: 1af195e644fe93e0c59f5e4402dd8942f5fe1aba
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="integrate-azure-devtest-labs-into-your-vsts-continuous-integration-and-delivery-pipeline"></a>Integrovat Azure DevTest Labs do vaší služby VSTS průběžnou integraci a doručení kanálu
Můžete použít *Azure DevTest Labs úlohy* rozšíření, který je nainstalován ve Visual Studio Team Services (služby VSTS) na snadno integrovat Azure DevTest Labs svůj kanál CI/CD sestavení a verze. Instalaci rozšíření je tři úkoly: 
* Vytvoření virtuálního počítače
* Vytvořit vlastní image z virtuálního počítače
* Odstranění virtuálního počítače 

Proces usnadňuje, například rychle nasadit "zlaté image" pro konkrétní testovací úlohy a pak odstraňte až po dokončení testu.

Tento článek ukazuje, jak vytvořit a nasadit virtuální počítač, vytvořit vlastní image a pak odstraňte virtuální počítač, všechny jako jeden kanál dokončení. Každý úkol by normálně provádějí jednotlivě v vlastní vlastní sestavení testovací nasazení kanálu.

## <a name="before-you-begin"></a>Než začnete
Před svůj kanál CI/CD může integrovat Azure DevTest Labs, musíte nainstalovat rozšíření Visual Studio Marketplace.
1. Přejděte na [úlohy Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).
1. Vyberte **nainstalovat**.
1. Dokončete průvodce.

## <a name="create-a-resource-manager-template"></a>Vytvoření šablony Resource Manageru
Tato část popisuje postup vytvoření šablony Azure Resource Manager, který použijete k vytvoření virtuálního počítače Azure na vyžádání.

1. Chcete-li vytvořit šablonu Resource Manager ve vašem předplatném, proveďte postup v [pomocí šablony Resource Manageru](devtest-lab-use-resource-manager-template.md).
1. Před vytvořením šablony Resource Manageru, přidejte [WinRM artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-winrm) při vytváření virtuálního počítače.

   WinRM přístup je nutný k pomocí úloh nasazení, jako například *Azure File Copy* a *prostředí PowerShell na cílových počítačích*.

   > [!NOTE]
   > Pokud používáte WinRM se sdílenou IP adresu, musíte přidat pravidlo NAT mapovat externí port na port vzdálené správy systému Windows. Tento krok se nevyžaduje, pokud vytvoříte virtuální počítač s veřejnou IP adresu.
   >
   >

1. Uložte šablonu jako soubor ve vašem počítači. Název souboru **CreateVMTemplate.json**.
1. Zkontrolujte šablonu, systému správy zdrojů.
1. Otevřete textový editor a vložte následující skript do ní.

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

1. Zkontrolujte skript systému správy zdrojů. Název na něco podobného jako **GetLabVMParams.ps1**.

   Při spuštění tohoto skriptu v agentovi jako součást definice vydání, a pokud používáte kroky úloh, jako *Azure File Copy* nebo *prostředí PowerShell na cílových počítačích*, skript shromažďuje hodnoty, které potřebujete nasazení aplikace do virtuálního počítače. Tyto úlohy by normálně používat pro nasazování aplikací na virtuálním počítači Azure. Úkoly vyžadují hodnoty, jako je název skupiny prostředků virtuálního počítače, IP adresu a plně kvalifikovaný název domény (FDQN).

## <a name="create-a-release-definition-in-release-management"></a>Vytvoření definice verze v Release Management
K vytvoření definice vydání, postupujte takto:

1. Na **verze** kartě **sestavení a verze** rozbočovače, vyberte tlačítko znaménko plus (+).
2. V **vytvořit verze definice** vyberte **prázdný** šablony a potom vyberte **Další**.
3. Vyberte **zvolte později**a potom vyberte **vytvořit** k vytvoření nové definice vydání s jedno prostředí a žádné propojené artefaktů.
4. Otevřete místní nabídky, v nové verzi definice, vyberte se třemi tečkami (...) vedle názvu prostředí a pak vyberte **nakonfigurovat proměnné**. 
5. V **konfigurovat - prostředí** okna pro proměnné, které můžete použít v definici úlohy verze, zadejte následující hodnoty:

   a. Pro **vmName**, zadejte název, který jste přiřadili k virtuálnímu počítači při vytváření šablony Resource Manageru na portálu Azure.

   b. Pro **uživatelské jméno**, zadejte uživatelské jméno, který jste přiřadili k virtuálnímu počítači při vytváření šablony Resource Manageru na portálu Azure.

   c. Pro **heslo**, zadejte heslo, které jste přiřadili k virtuálnímu počítači při vytváření šablony Resource Manageru na portálu Azure. Použijte na ikonu "zámku" Skrýt a zabezpečené heslo.

### <a name="create-a-vm"></a>Vytvoření virtuálního počítače

Další fáze nasazení je pro vytvoření virtuálního počítače, který chcete použít jako "zlatá image" pro následné nasazení. Vytvoříte virtuální počítač v rámci vaší instanci Azure DevTest Lab pomocí úloha, která je vytvořených speciálně pro tento účel. 

1. V definici verze vyberte **přidání úkolů**.
2. Na **nasadit** přidejte *Azure DevTest Labs vytvoření virtuálního počítače* úloh. Úloha nakonfigurujte následujícím způsobem:

   > [!NOTE]
   > Chcete-li vytvořit virtuální počítač, který chcete použít pro další nasazení, přečtěte si téma [úlohy Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pro **předplatné Azure RM**, vyberte připojení v **dostupné připojení služby Azure** seznamu, nebo vytvořit více omezená oprávnění připojení k předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Pro **název testovacího prostředí**, vyberte název instance, kterou jste vytvořili dříve.

   c. Pro **název šablony**, zadejte úplnou cestu a název souboru šablony, který jste uložili do vašeho úložiště zdrojového kódu. Můžete použít předdefinované vlastnosti správy verzí pro zjednodušení cestu, například:

   ```
   $(System.DefaultWorkingDirectory)/Contoso/ARMTemplates/CreateVMTemplate.json
   ```

   d. Pro **parametry šablony**, zadejte parametry pro proměnné, které jsou definovány v šabloně. Použijte názvy proměnných, které jste definovali v prostředí, například:

   ```
   -newVMName '$(vmName)' -userName '$(userName)' -password (ConvertTo-SecureString -String '$(password)' -AsPlainText -Force)
   ```

   e. Pro **výstup proměnné – ID virtuálního počítače testovacím**, je třeba ID nově vytvořený virtuální počítač pro následné kroky. Nastavit výchozí název proměnné prostředí, která je automaticky vyplněno s tímto ID v **výstup proměnné** části. Můžete upravit proměnnou v případě potřeby, ale musíte použít správný název v dalších úlohách. ID virtuálního počítače testovacího prostředí je napsána v následující podobě:

   ```
   /subscriptions/{subId}/resourceGroups/{rgName}/providers/Microsoft.DevTestLab/labs/{labName}/virtualMachines/{vmName}
   ```

3. Spusťte skript, který jste vytvořili dříve shromažďovat podrobnosti virtuálního počítače DevTest Labs. 
4. V definici verze vyberte **přidání úkolů** a pak klikněte na **nasadit** přidejte *prostředí Azure PowerShell* úloh. Úloha nakonfigurujte následujícím způsobem:

   > [!NOTE]
   > Shromažďování podrobností o virtuální počítač DevTest Labs, najdete v části [nasazení: Azure PowerShell](https://github.com/Microsoft/vsts-tasks/tree/master/Tasks/AzurePowerShell) a spusťte tento skript.

   a. Pro **typ připojení Azure**, vyberte **Azure Resource Manager**.

   b. Pro **předplatné Azure RM**, vyberte připojení ze seznamu v části **dostupné připojení služby Azure**, nebo vytvořit více omezená oprávnění připojení k předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   c. Pro **typ skriptu**, vyberte **soubor skriptu**.
 
   d. Pro **cestu ke skriptu**, zadejte úplnou cestu a název skriptu, který jste uložili do vašeho úložiště zdrojového kódu. Můžete použít předdefinované vlastnosti správy verzí pro zjednodušení cestu, například:
      ```
      $(System.DefaultWorkingDirectory/Contoso/Scripts/GetLabVMParams.ps1
      ```
   e. Pro **argumenty skriptu**, zadejte název proměnné prostředí, která byla automaticky vyplněna ID v prostředí virtuálního počítače předchozí úlohou, například: 
      ```
      -labVmId '$(labVMId)'
      ```
    Skript shromažďuje požadované hodnoty a ukládá je do proměnné prostředí v rámci definice vydání, tak, aby snadno najdete je v postupných krocích.

5. Nasazení aplikace do nového virtuálního počítače DevTest Labs. Úlohy, kterým obvykle použijete k nasazení aplikace se *Azure File Copy* a *prostředí PowerShell na cílových počítačích*.
   Informace o virtuálním počítači, musíte pro parametry tyto úlohy jsou uloženy v tři konfigurační proměnné s názvem **labVmRgName**, **labVMIpAddress**, a **labVMFqdn**v rámci definice verzí. Pokud chcete experimentovat s vytváření virtuálního počítače DevTest Labs a vlastní image, bez nasazení aplikace do něj, můžete tento krok přeskočit.

### <a name="create-an-image"></a>Vytvoření image

Další fáze je vytvoření bitové kopie nově nasazené virtuální počítač v Azure DevTest Labs instanci. Potom můžete image pro vytvoření kopie virtuálního počítače na vyžádání, vždy, když chcete spustit úlohu dev nebo některé testy. 

1. V definici verze vyberte **přidání úkolů**.
2. Na **nasadit** přidejte **Azure DevTest Labs vytvořit vlastní Image** úloh. Nakonfigurujte následujícím způsobem:

   > [!NOTE]
   > Pokud chcete vytvořit bitovou kopii, najdete v části [úlohy Azure DevTest Labs](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pro **předplatné Azure RM**v **dostupné připojení služby Azure** seznamu, vyberte připojení ze seznamu nebo vytvořte připojení více omezená oprávnění k předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).

   b. Pro **název testovacího prostředí**, vyberte název instance, které jste vytvořili dříve.

   c. Pro **vlastní název bitové kopie**, zadejte název pro vlastní image.

   d. (Volitelné) Pro **popis**, zadejte popis, který usnadnit výběr správné bitové kopie později.

   e. Pro **zdrojového virtuálního počítače testovacího prostředí – ID virtuálního počítače zdrojového prostředí**, pokud jste změnili výchozí název proměnné prostředí, která se vyplní automaticky s ID v prostředí virtuálního počítače starší úlohou sem ho upravit. Výchozí hodnota je **$(labVMId)**.

   f. Pro **výstup proměnné - ID bitové kopie vlastní**, je třeba ID nově vytvořené bitové kopie, pokud chcete spravovat nebo odstranit. Výchozí název proměnné prostředí, která je automaticky vyplněno s tímto ID je nastavena v **výstup proměnné** části. V případě potřeby můžete upravit proměnnou.

### <a name="delete-the-vm"></a>Odstranění virtuálního počítače

Závěrečná fáze je odstranit virtuální počítač, který jste nasadili ve vaší instanci Azure DevTest Labs. Po spuštění úlohy dev nebo spustit testy, které potřebujete pro nasazený virtuální počítač by normálně odstranění virtuálního počítače. 

1. V definici verze vyberte **přidání úkolů** a pak klikněte na **nasadit** přidejte *Azure DevTest Labs odstranit počítač* úloh. Nakonfigurujte následujícím způsobem:

      > [!NOTE]
      > Chcete-li odstranit virtuální počítač, přečtěte si téma [Azure DevTest Labs úlohy](https://marketplace.visualstudio.com/items?itemName=ms-azuredevtestlabs.tasks).

   a. Pro **předplatné Azure RM**, vyberte připojení v **dostupné připojení služby Azure** seznamu, nebo vytvořit více omezená oprávnění připojení k předplatnému Azure. Další informace najdete v tématu [koncový bod služby Azure Resource Manager](https://docs.microsoft.com/vsts/build-release/concepts/library/service-endpoints#sep-azure-rm).
 
   b. Pro **ID virtuálního počítače testovacím**, pokud jste změnili výchozí název proměnné prostředí, která se vyplní automaticky s ID v prostředí virtuálního počítače starší úlohou sem ho upravit. Výchozí hodnota je **$(labVMId)**.

2. Zadejte název pro definici verze a pak ho uložte.
3. Vytvořit novou verzi, vyberte na nejnovější verzi a nasadíte ho do jednoho prostředí v definici.

V každé fázi aktualizujte zobrazení instance DevTest Labs na portálu Azure k zobrazení virtuálních počítačů a bitovou kopii, která jsou vytvářena a virtuální počítač, který se odstraňuje znovu.

Teď můžete vlastní bitovou kopii k vytvoření virtuálních počítačů, když jsou požadované.


[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [vytvoření prostředí více virtuálních počítačů pomocí šablony Resource Manageru](devtest-lab-create-environment-from-arm.md).
* Prozkoumejte další šablony Resource Manageru rychlý start pro automatizaci DevTest Labs z [veřejného úložiště DevTest Labs GitHub](https://github.com/Azure/azure-quickstart-templates).
* V případě potřeby, najdete v článku [řešení potíží s služby VSTS](https://docs.microsoft.com/vsts/build-release/actions/troubleshooting) stránky.
 
