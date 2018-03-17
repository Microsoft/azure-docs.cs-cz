---
title: "Přidání položky marketplace zásobník Azure z místního zdroje | Microsoft Docs"
description: "Popisuje postup přidání bitové kopie místního operačního systému do Azure Marketplace zásobníku."
services: azure-stack
documentationcenter: 
author: jeffgilb
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 03/16/2018
ms.author: jeffgilb
ms.reviewer: misainat
ms.openlocfilehash: 296719ddd23fb9ee717455420906e9a634a71a8d
ms.sourcegitcommit: a36a1ae91968de3fd68ff2f0c1697effbb210ba8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/17/2018
---
# <a name="tutorial-add-an-azure-stack-marketplace-item-from-a-local-source"></a>Kurz: Přidání položky marketplace zásobník Azure z místního zdroje

Jako operátor zásobník Azure je první věcí, kterou je potřeba udělat, aby mohli uživatelé nasadit virtuální počítač (VM) přidat bitovou kopii virtuálního počítače do zásobníku Azure marketplace. Ve výchozím nastavení nic je publikována ve službě Azure zásobníku marketplace, ale můžete nahrát bitové kopie ISO virtuálního počítače tak, aby byly dostupné pro vaše uživatele. Tuto možnost použijte, pokud jste nasadili zásobník Azure ve scénáři odpojené nebo ve scénářích s omezené připojení.

V tomto kurzu si stáhnout bitovou kopii virtuálního počítače Windows serveru 2016 z stránce hodnocení serveru Windows a nahrajte ho do zásobníku Azure marketplace.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání bitové kopie virtuálního počítače Windows serveru 2016
> * Ověřte, zda že je k dispozici bitovou kopii virtuálního počítače 
> * Testování image virtuálního počítače

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- Nainstalujte [modulů prostředí Azure PowerShell kompatibilní s Azure zásobníku](asdk-post-deploy.md#install-azure-stack-powershell)
- Stažení [nástroje Azure zásobníku](asdk-post-deploy.md#download-the-azure-stack-tools)
- Stažení [bitovou kopii ISO virtuálního počítače Windows serveru 2106](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2016) na stránce hodnocení serveru Windows

## <a name="add-a-windows-server-vm-image"></a>Přidání bitové kopie virtuálního počítače Windows serveru
Zásobník Azure marketplace můžete publikovat bitovou kopii systému Windows Server 2016 přidáním již dříve stažené bitovou kopii ISO pomocí prostředí PowerShell. 

Tuto možnost použijte, pokud jste nasadili zásobník Azure ve scénáři odpojené nebo ve scénářích s omezené připojení.

1. Import v Azure zásobníku `Connect` a `ComputeAdmin` moduly Powershellu, které jsou zahrnuty v adresáři nástroje zásobník Azure pomocí následujících příkazů:

   ```powershell
   Set-ExecutionPolicy RemoteSigned

   # Import the Connect and ComputeAdmin modules.   
   Import-Module .\Connect\AzureStack.Connect.psm1
   Import-Module .\ComputeAdmin\AzureStack.ComputeAdmin.psm1

   ```

2. Spusťte jeden z následujících skriptů na hostitelském počítači ASDK v závislosti na tom, jestli jste nasadili prostředí zásobníku Azure pomocí Azure Active Directory (Azure AD) nebo Active Directory Federation Services (AD FS):

  - Příkazy pro **nasazení služby Azure AD**: 

      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.windows.net/"
      
      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience

      $TenantID = Get-AzsDirectoryTenantId `
      # Replace the AADTenantName value to reflect your Azure AD tenant name.
        -AADTenantName "<myDirectoryTenantName>.onmicrosoft.com" `
        -EnvironmentName AzureStackAdmin

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```

  - Příkazy pro **nasazení služby AD FS**:
      
      ```PowerShell
      # To get this value for Azure Stack integrated systems, contact your service provider.
      $ArmEndpoint = "https://adminmanagement.local.azurestack.external"

      # To get this value for Azure Stack integrated systems, contact your service provider.
      $GraphAudience = "https://graph.local.azurestack.external/"

      # Create the Azure Stack operator's Azure Resource Manager environment by using the following cmdlet:
      Add-AzureRMEnvironment `
        -Name "AzureStackAdmin" `
        -ArmEndpoint $ArmEndpoint

      Set-AzureRmEnvironment `
        -Name "AzureStackAdmin" `
        -GraphAudience $GraphAudience `
        -EnableAdfsAuthentication:$true

      $TenantID = Get-AzsDirectoryTenantId `
      -ADFS `
      -EnvironmentName "AzureStackAdmin" 

      Login-AzureRmAccount `
        -EnvironmentName "AzureStackAdmin" `
        -TenantId $TenantID 
      ```
   
3. Přidejte bitovou kopii systému Windows Server 2016 do zásobníku Azure marketplace. (Nahraďte *fully_qualified_path_to_ISO* cestou k Windows serveru 2016 ISO, který jste si stáhli.)

    ```PowerShell
    $ISOPath = "<fully_qualified_path_to_ISO>"

    # Add a Windows Server 2016 Evaluation VM image.
    New-AzsServer2016VMImage `
      -ISOPath $ISOPath

    ```

## <a name="verify-the-marketplace-item-is-available"></a>Ověřte, zda že je k dispozici položku marketplace.
Tyto kroky použijte k ověření, že nová bitová kopie virtuálního počítače je k dispozici v zásobníku Azure marketplace.

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).

2. Vyberte **další služby** > **Marketplace správu**. 

3. Ověřte, že bitovou kopii virtuálního počítače Windows serveru 2016 datového centra byl úspěšně přidán.

   ![Stažený bitovou kopii z Azure](media/asdk-marketplace-item/azs-marketplace-ws2016.png)

## <a name="test-the-vm-image"></a>Testování image virtuálního počítače
Jako operátor zásobník Azure, můžete použít [portálu správce](https://adminportal.local.azurestack.external) vytvořit testovací virtuální počítač k ověření bitovou kopii byl zpřístupněn úspěšně. 

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).

2. Klikněte na tlačítko **nové** > **výpočetní** > **systému Windows Server 2016 Datacenter** > **vytvořit**.  
 ![Vytvoření virtuálního počítače image pořízené na marketplace](media/asdk-marketplace-item/new-compute.png)

3. V **Základy** okno, zadejte následující informace a potom klikněte na **OK**:
  - **Název**: test-vm-1
  - **Uživatelské jméno**: AdminTestUser
  - **Heslo**: AzS TestVM01
  - **Předplatné**: přijměte výchozí zprostředkovatel předplatné
  - **Skupina prostředků**: test-vm-rg
  - **Umístění**: místní

4. V **zvolte velikost** okně klikněte na tlačítko **A1 standardní**a potom klikněte na **vyberte**.  

5. V **nastavení** , přijměte výchozí hodnoty a klikněte na tlačítko **OK**

6. V okně **Shrnutí** klikněte na **OK** a vytvořte virtuální počítač.  
> [!NOTE]
> Nasazení virtuálního počítače trvá několik minut.

7. Chcete-li zobrazit na nový virtuální počítač, klikněte na tlačítko **virtuální počítače**, vyhledejte **test-vm-1** a klikněte na jeho název.
    ![Zobrazí první image virtuálního počítače testu](media/asdk-marketplace-item/first-test-vm.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků
Poté, co jste úspěšně přihlášení k virtuálnímu počítači a ověřit, že bitovou kopii test funguje správně, byste měli odstranit testovací skupina prostředků. Uvolní se omezené prostředky k dispozici pro jeden uzel ASDK instalace.

Pokud již nepotřebujete, odstraňte skupinu prostředků, virtuální počítač a všechny související prostředky pomocí následujících kroků:

1. Přihlaste se k [portál pro správu ASDK](https://adminportal.local.azurestack.external).
2. Klikněte na tlačítko **skupiny prostředků** > **test-vm-rg** > **odstranit skupinu prostředků**.
3. Typ **test-vm-rg** jako název skupiny prostředků a pak klikněte na tlačítko **odstranit**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přidání bitové kopie virtuálního počítače Windows serveru 2016
> * Ověřte, zda že je k dispozici bitovou kopii virtuálního počítače 
> * Testování image virtuálního počítače

Přechodu na v dalším kurzu se dozvíte, jak vytvoření nabídky Azure zásobníku a plán.

> [!div class="nextstepaction"]
> [Nabízejí služby Azure IaaS zásobníku](asdk-offer-services.md)