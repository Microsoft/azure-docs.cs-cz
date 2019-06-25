---
title: Nasazení šablony Azure s tokenem SAS a rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Pomocí Azure Resource Manageru a Azure CLI k nasazení prostředků do Azure ze šablony, který je chráněný pomocí tokenu SAS.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: a71698831859e92300706ade8c2284cbfc7ee241
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67205414"
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Nasazení privátní šablony Resource Manageru s tokenem SAS a rozhraní příkazového řádku Azure

Pokud vaše šablona se nachází v účtu úložiště, můžete omezit přístup k šabloně a během nasazení zadat token sdíleného přístupového podpisu (SAS). Toto téma vysvětluje, jak pomocí Azure Powershellu pomocí šablon Resource Manageru během nasazení zadat SAS token. 

## <a name="add-private-template-to-storage-account"></a>Přidejte privátní šablony do účtu úložiště

Šablony můžete přidat do účtu úložiště a propojit je během nasazení s tokenem SAS.

> [!IMPORTANT]
> Pomocí následujících kroků, objektů blob, který obsahuje šablonu je přístupná pouze vlastník účtu. Ale při vytváření tokenu SAS pro objekt blob, objekt blob je přístupný pro každý, kdo má tento identifikátor URI. Pokud jiný uživatel zachycuje identifikátor URI, je tomuto uživateli přístup k šabloně. Pomocí tokenu SAS je vhodný způsob omezení přístupu ke své šablony, ale by neměly obsahovat citlivá data, jako jsou hesla přímo v šabloně.
> 
> 

Následující příklad nastaví kontejner účtu úložiště. privátní a nahrávání šablony:
   
```azurecli
az group create --name "ManageGroup" --location "South Central US"
az storage account create \
    --resource-group ManageGroup \
    --location "South Central US" \
    --sku Standard_LRS \
    --kind Storage \
    --name {your-unique-name}
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
az storage container create \
    --name templates \
    --public-access Off \
    --connection-string $connection
az storage blob upload \
    --container-name templates \
    --file vmlinux.json \
    --name vmlinux.json \
    --connection-string $connection
```

### <a name="provide-sas-token-during-deployment"></a>Během nasazení zadat SAS token
Nasazení privátní šablony v účtu úložiště, vygenerujte SAS token a zahrnout do identifikátoru URI pro šablonu. Nastavte čas vypršení platnosti vyhradit dostatek času k dokončení nasazení.
   
```azurecli
expiretime=$(date -u -d '30 minutes' +%Y-%m-%dT%H:%MZ)
connection=$(az storage account show-connection-string \
    --resource-group ManageGroup \
    --name {your-unique-name} \
    --query connectionString)
token=$(az storage blob generate-sas \
    --container-name templates \
    --name vmlinux.json \
    --expiry $expiretime \
    --permissions r \
    --output tsv \
    --connection-string $connection)
url=$(az storage blob url \
    --container-name templates \
    --name vmlinux.json \
    --output tsv \
    --connection-string $connection)
az group deployment create --resource-group ExampleGroup --template-uri $url?$token
```

Příklad pomocí tokenu SAS s propojenými šablonami najdete v tématu [použití propojených šablon s Azure Resource Managerem](resource-group-linked-templates.md).

## <a name="next-steps"></a>Další postup
* Úvod do nasazení šablon najdete v tématu [nasazení prostředků pomocí šablon Resource Manageru a prostředí Azure PowerShell](resource-group-template-deploy-cli.md).
* Úplný ukázkový skript, který se nasazuje šablony najdete v tématu [skript šablony nasazení Resource Manageru](resource-manager-samples-cli-deploy.md)
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
