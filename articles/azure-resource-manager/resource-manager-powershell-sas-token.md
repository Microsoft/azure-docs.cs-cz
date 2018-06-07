---
title: Nasazení šablony Azure s tokenu SAS a prostředí PowerShell | Microsoft Docs
description: Pomocí Azure Resource Manageru a prostředí Azure PowerShell k nasazení prostředků do Azure ze šablony, která je chráněná pomocí tokenu SAS.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/19/2017
ms.author: tomfitz
ms.openlocfilehash: f138cceb88cb9a43efdd3f11b24203378a288286
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34602975"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Nasazení privátní šablony Resource Manageru pomocí tokenu SAS a prostředí Azure PowerShell

Pokud vaše šablona se nachází v účtu úložiště, můžete omezit přístup k šabloně a během nasazení zadat token sdílený přístupový podpis (SAS). Toto téma vysvětluje, jak pomocí prostředí Azure PowerShell s Resource Manager šablony během nasazení zadat SAS token. 

## <a name="add-private-template-to-storage-account"></a>Přidání šablony privátní účet úložiště.

Šablony můžete přidat k účtu úložiště a odkaz na jejich během nasazení s tokenem SAS.

> [!IMPORTANT]
> Pomocí následujících kroků, je přístupné pouze majiteli účtu objekt blob obsahující šablony. Objekt blob je přístupný pro každý, kdo má tento identifikátor URI, ale při vytváření tokenu SAS pro tento objekt blob. Pokud jiný uživatel zabrání identifikátor URI, tento uživatel má přístup k šabloně. Použití SAS token je vhodný způsob omezení přístupu ke šablony, ale nesmí obsahovat citlivá data, jako jsou hesla přímo v šabloně.
> 
> 

Následující příklad nastaví kontejner privátní úložiště účet a odesílá šablonu:
   
```powershell
# create a storage account for templates
New-AzureRmResourceGroup -Name ManageGroup -Location "South Central US"
New-AzureRmStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzureStorageContainer -Name templates -Permission Off
Set-AzureStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Během nasazení zadat tokenu SAS
Pokud chcete nasadit šablonu privátní v účtu úložiště, vygenerování tokenu SAS a její zahrnutí do identifikátor URI pro šablonu. Nastavte čas vypršení platnosti vyhradit dostatek času k dokončení nasazení.
   
```powershell
Set-AzureRmCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzureStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzureRmResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Příklad použití tokenu SAS s propojených šablon naleznete v části [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).


## <a name="next-steps"></a>Další postup
* Úvod do nasazení šablony, najdete v části [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](resource-group-template-deploy.md).
* Pro dokončení ukázkový skript, který nasadí šablonu, najdete v části [skript šablony nasazení Resource Manager](resource-manager-samples-powershell-deploy.md)
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
