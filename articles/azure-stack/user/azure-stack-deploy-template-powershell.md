---
title: Nasazení šablon pomocí prostředí PowerShell v zásobníku Azure | Microsoft Docs
description: Nasazení šablony do protokolů Azure pomocí prostředí PowerShell.
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: 12fe32d7-0a1a-4c02-835d-7b97f151ed0f
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/25/2017
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: 4af82deef029120aa2699e7c69c501ae61a1e8bd
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
ms.locfileid: "34359811"
---
# <a name="deploy-a-template-to-azure-stack-using-powershell"></a>Nasazení šablony do protokolů Azure pomocí prostředí PowerShell

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Prostředí PowerShell můžete použít k nasazení šablony Azure Resource Manageru do Azure zásobníku. Tento článek ukazuje, jak pomocí prostředí PowerShell pro nasazení šablony.

## <a name="run-azurerm-powershell-cmdlets"></a>Spusťte rutiny prostředí AzureRM PowerShell

Tento příklad používá AzureRM PowerShell a šablonu uložené na Githubu. Šablona vytvoří virtuální počítač Windows serveru 2012 R2 Datacenter.

>[!NOTE]
>Než se pokusíte tento příklad, ujistěte se, že jste [nakonfigurované prostředí PowerShell](azure-stack-powershell-configure-user.md) pro uživatele Azure zásobníku.

1. Přejděte na <http://aka.ms/AzureStackGitHub> a najděte **101jednoduché windows-vm** šablony. Uložit šablonu do tohoto umístění: C:\\šablony\\azuredeploy-101jednoduché windows-vm.json.
2. Otevřete příkazový řádek se zvýšenými oprávněními prostředí PowerShell.
3. Nahraďte *uživatelské jméno* a *heslo* v následující skript s vaše uživatelské jméno a heslo a pak spusťte skript.

   ```PowerShell
       # Set Deployment Variables
       $myNum = "001" #Modify this per deployment
       $RGName = "myRG$myNum"
       $myLocation = "local"
   
       # Create Resource Group for Template Deployment
       New-AzureRmResourceGroup -Name $RGName -Location $myLocation
   
       # Deploy Simple IaaS Template
       New-AzureRmResourceGroupDeployment `
           -Name myDeployment$myNum `
           -ResourceGroupName $RGName `
           -TemplateFile c:\templates\azuredeploy-101-simple-windows-vm.json `
           -NewStorageAccountName mystorage$myNum `
           -DnsNameForPublicIP mydns$myNum `
           -AdminUsername <username> `
           -AdminPassword ("<password>" | ConvertTo-SecureString -AsPlainText -Force) `
           -VmName myVM$myNum `
           -WindowsOSVersion 2012-R2-Datacenter
   ```

   >[!IMPORTANT]
   >Při spuštění tohoto skriptu zvýšit hodnotu parametru "$myNum", abyste zabránili přepsání vaše nasazení.

4. Otevřete portál, vyberte zásobník Azure **Procházet**a potom vyberte **virtuální počítače** najít nového virtuálního počítače (*myDeployment001*).

## <a name="next-steps"></a>Další postup

[Nasazení šablon pomocí sady Visual Studio](azure-stack-deploy-template-visual-studio.md)
