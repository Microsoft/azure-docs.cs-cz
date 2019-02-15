---
title: Nasazení šablony Azure s tokenem SAS a prostředí PowerShell | Dokumentace Microsoftu
description: Pomocí Azure Resource Manageru a Azure Powershellu k nasazení prostředků do Azure ze šablony, který je chráněný pomocí tokenu SAS.
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
ms.openlocfilehash: 818aa63ced56d7cf382536f10bb6150199ab74e9
ms.sourcegitcommit: f715dcc29873aeae40110a1803294a122dfb4c6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2019
ms.locfileid: "56268936"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-powershell"></a>Nasazení privátní šablony Resource Manageru s tokenem SAS a Azure Powershellu

Pokud vaše šablona se nachází v účtu úložiště, můžete omezit přístup k šabloně a během nasazení zadat token sdíleného přístupového podpisu (SAS). Toto téma vysvětluje, jak pomocí Azure Powershellu pomocí šablon Resource Manageru během nasazení zadat SAS token. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="add-private-template-to-storage-account"></a>Přidejte privátní šablony do účtu úložiště

Šablony můžete přidat do účtu úložiště a propojit je během nasazení s tokenem SAS.

> [!IMPORTANT]
> Pomocí následujících kroků, objektů blob, který obsahuje šablonu je přístupná pouze vlastník účtu. Ale při vytváření tokenu SAS pro objekt blob, objekt blob je přístupný pro každý, kdo má tento identifikátor URI. Pokud jiný uživatel zachycuje identifikátor URI, je tomuto uživateli přístup k šabloně. Pomocí tokenu SAS je vhodný způsob omezení přístupu ke své šablony, ale by neměly obsahovat citlivá data, jako jsou hesla přímo v šabloně.
> 
> 

Následující příklad nastaví kontejner účtu úložiště. privátní a nahrávání šablony:
   
```powershell
# create a storage account for templates
New-AzResourceGroup -Name ManageGroup -Location "South Central US"
New-AzStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name} -Type Standard_LRS -Location "West US"
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# create a container and upload template
New-AzStorageContainer -Name templates -Permission Off
Set-AzStorageBlobContent -Container templates -File c:\MyTemplates\storage.json
```

## <a name="provide-sas-token-during-deployment"></a>Během nasazení zadat SAS token
Nasazení privátní šablony v účtu úložiště, vygenerujte SAS token a zahrnout do identifikátoru URI pro šablonu. Nastavte čas vypršení platnosti vyhradit dostatek času k dokončení nasazení.
   
```powershell
Set-AzCurrentStorageAccount -ResourceGroupName ManageGroup -Name {your-unique-name}

# get the URI with the SAS token
$templateuri = New-AzStorageBlobSASToken -Container templates -Blob storage.json -Permission r `
  -ExpiryTime (Get-Date).AddHours(2.0) -FullUri

# provide URI with SAS token during deployment
New-AzResourceGroup -Name ExampleGroup -Location "South Central US"
New-AzResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateUri $templateuri
```

Příklad pomocí tokenu SAS s propojenými šablonami najdete v tématu [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).


## <a name="next-steps"></a>Další postup
* Úvod do nasazení šablon najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](resource-group-template-deploy.md).
* Úplný ukázkový skript, který se nasazuje šablony najdete v tématu [skript šablony nasazení Resource Manageru](resource-manager-samples-powershell-deploy.md)
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
