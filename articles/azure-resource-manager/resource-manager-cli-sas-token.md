---
title: Nasazení šablony Azure s tokenu SAS a rozhraní příkazového řádku Azure | Microsoft Docs
description: Pomocí Azure Resource Manager a rozhraní příkazového řádku Azure můžete nasadit prostředky do Azure ze šablony, která je chráněná pomocí tokenu SAS.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/31/2017
ms.author: tomfitz
ms.openlocfilehash: b97fdea36eab914926ff91e1f27bdab0fb8a3930
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="deploy-private-resource-manager-template-with-sas-token-and-azure-cli"></a>Nasazení privátní šablony Resource Manageru pomocí tokenu SAS a rozhraní příkazového řádku Azure

Pokud vaše šablona se nachází v účtu úložiště, můžete omezit přístup k šabloně a během nasazení zadat token sdílený přístupový podpis (SAS). Toto téma vysvětluje, jak pomocí prostředí Azure PowerShell s Resource Manager šablony během nasazení zadat SAS token. 

## <a name="add-private-template-to-storage-account"></a>Přidání šablony privátní účet úložiště.

Šablony můžete přidat k účtu úložiště a odkaz na jejich během nasazení s tokenem SAS.

> [!IMPORTANT]
> Pomocí následujících kroků, je přístupné pouze majiteli účtu objekt blob obsahující šablony. Objekt blob je přístupný pro každý, kdo má tento identifikátor URI, ale při vytváření tokenu SAS pro tento objekt blob. Pokud jiný uživatel zabrání identifikátor URI, tento uživatel má přístup k šabloně. Použití SAS token je vhodný způsob omezení přístupu ke šablony, ale nesmí obsahovat citlivá data, jako jsou hesla přímo v šabloně.
> 
> 

Následující příklad nastaví kontejner privátní úložiště účet a odesílá šablonu:
   
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

### <a name="provide-sas-token-during-deployment"></a>Během nasazení zadat tokenu SAS
Pokud chcete nasadit šablonu privátní v účtu úložiště, vygenerování tokenu SAS a její zahrnutí do identifikátor URI pro šablonu. Nastavte čas vypršení platnosti vyhradit dostatek času k dokončení nasazení.
   
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

Příklad použití tokenu SAS s propojených šablon naleznete v části [použití propojených šablon s Azure Resource Manager](resource-group-linked-templates.md).

## <a name="next-steps"></a>Další postup
* Úvod do nasazení šablony, najdete v části [nasazení prostředků pomocí šablony Resource Manageru a prostředí Azure PowerShell](resource-group-template-deploy-cli.md).
* Pro dokončení ukázkový skript, který nasadí šablonu, najdete v části [skript šablony nasazení Resource Manager](resource-manager-samples-cli-deploy.md)
* Chcete-li definovat parametry v šabloně, přečtěte si téma [vytváření šablon](resource-group-authoring-templates.md#parameters).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](resource-manager-subscription-governance.md).
