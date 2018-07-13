---
title: Nasazení prostředků pomocí Azure CLI a šablon | Dokumentace Microsoftu
description: Nasazení prostředků do Azure pomocí Azure Resource Manageru a Azure CLI. Prostředky jsou definovány v šabloně Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/31/2017
ms.author: tomfitz
ms.openlocfilehash: 5a6b227cee3765593adbda430d8c47312f996c18
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38723837"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI

Tento článek vysvětluje, jak nasadit prostředky do Azure pomocí rozhraní příkazového řádku Azure pomocí šablon Resource Manageru. Pokud nejsou dobře známé koncepty nasazení a správou řešení Azure, najdete v článku [přehled Azure Resource Manageru](resource-group-overview.md).  

Šablony Resource Manageru, který nasazujete, může být místní soubor na počítači, nebo externí soubor, který se nachází v úložišti, jako je GitHub. Šablona nasazení v tomto článku je k dispozici v [Ukázková šablona](#sample-template) části nebo [Šablona účtu úložiště v Githubu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Pokud nemáte nainstalované rozhraní příkazového řádku Azure, můžete použít [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Nasazení místního šablony

Při nasazování prostředků do Azure, můžete:

1. Přihlaste se ke svému účtu Azure.
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazených prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může být až 90 znaků. Nesmí končit tečkou.
3. Nasazení do skupiny prostředků, který definuje prostředky k vytvoření šablony

Šablona může obsahovat parametry, které vám umožní přizpůsobit nasazení. Například můžete zadat hodnoty, které jsou přizpůsobené pro konkrétní prostředí (jako je vývoj, testování a produkce). Ukázková šablona definuje parametr pro SKU účtu úložiště. 

Následující příklad vytvoří skupinu prostředků a nasadí šablony z místního počítače:

```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zprávu, která obsahuje výsledek:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-external-template"></a>Nasazení externí šablony

Místo uložení šablony Resource Manageru na místním počítači, můžete chtít uložit je do externího umístění. Šablony můžete uložit úložiště správy zdrojového kódu (např. GitHub). Nebo můžete je ukládat v účtu úložiště Azure pro zajištění sdíleného přístupu ve vaší organizaci.

Chcete-li nasadit externí šablony, použijte **šablona identifikátoru uri** parametru. Použijte identifikátor URI v příkladu nasazení ukázkové šablony z Githubu.
   
```azurecli
az login

az group create --name ExampleGroup --location "Central US"
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
    --parameters storageAccountType=Standard_GRS
```

V předchozím příkladu vyžaduje veřejně přístupné identifikátor URI pro šablony, které lze použít pro většinu scénářů, protože šablony by neměl obsahovat citlivá data. Pokud je třeba zadat citlivá data (jako je zadání hesla správce), předáte tuto hodnotu jako zabezpečený parametr. Ale pokud nechcete, aby se šablony pro veřejně přístupný, budete ho chránit ukládáním do privátního úložiště kontejnerů. Informace o nasazení šablony, která se vyžaduje token sdíleného přístupového podpisu (SAS), najdete v části [nasazení privátní šablony s tokenem SAS](resource-manager-cli-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../includes/resource-manager-cloud-shell-deploy.md)]

Ve službě Cloud Shell použijte následující příkazy:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="deploy-to-more-than-one-resource-group-or-subscription"></a>Nasazení do více než jedné skupiny prostředků nebo předplatného

Zpravidla nasazujete, všechny prostředky ve vaší šabloně do jedné skupiny prostředků. Existují ale scénáře, ve které chcete nasadit sadu prostředků společně, ale je umístit do různých skupin prostředků nebo předplatných. Můžete nasadit do pouze pět skupin prostředků v jednom nasazení. Další informace najdete v tématu [Azure nasadit prostředky do více než jedno předplatné nebo skupinu prostředků](resource-manager-cross-resource-group-deployment.md).

## <a name="parameter-files"></a>Soubory parametrů

Místo předání parametrů jako hodnoty vložená ve skriptu, možná bude snadněji používá soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů musí být v následujícím formátu:

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
     "storageAccountType": {
         "value": "Standard_GRS"
     }
  }
}
```

Všimněte si, že sekci parametrů obsahuje název parametru, který odpovídá parametru definovaného v šabloně (storageAccountType). Soubor parametrů obsahuje hodnotu pro parametr. Tato hodnota je automaticky předávaných do šablony během nasazení. Můžete vytvořit více soubory parametrů pro odlišné scénáře nasazení a poté předejte soubor odpovídající parametr. 

Předchozí příklad zkopírujte a uložte ho jako soubor s názvem `storage.parameters.json`.

Chcete-li předat parametr místní soubor, použijte `@` zadat místní soubor s názvem storage.parameters.json.

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

## <a name="test-a-template-deployment"></a>Test šablony nasazení

Chcete-li otestovat šablonu a parametry hodnoty bez skutečného nasazení všechny prostředky, použijte [ověřit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_validate). 

```azurecli
az group deployment validate \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters @storage.parameters.json
```

Pokud nejsou zjištěny žádné chyby, příkaz vrátí informace o nasazení testu. Zejména, Všimněte si, že **chyba** hodnotu null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Pokud dojde k chybě, příkaz vrátí chybovou zprávu. Pokus o předání nesprávnou hodnotu pro účet úložiště skladovou Položku, například vrátí následující chybu:

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template validation failed: 'The provided value 'badSKU' for the template parameter 
      'storageAccountType' at line '13' and column '20' is not valid. The parameter value is not part of the allowed 
      value(s): 'Standard_LRS,Standard_ZRS,Standard_GRS,Standard_RAGRS,Premium_LRS'.'.",
    "target": null
  },
  "properties": null
}
```

Pokud vaše šablona obsahuje chybu syntaxe, příkaz vrátí chybu s informacemi, že ho nebylo možné rozložit šablony. Zpráva číslo řádku a pozice chybu analýzy.

```azurecli
{
  "error": {
    "code": "InvalidTemplate",
    "details": null,
    "message": "Deployment template parse failed: 'After parsing a value an unexpected character was encountered:
      \". Path 'variables', line 31, position 3.'.",
    "target": null
  },
  "properties": null
}
```

[!INCLUDE [resource-manager-deployments](../../includes/resource-manager-deployments.md)]

Chcete-li použít úplný režim, použijte `mode` parametr:

```azurecli
az group deployment create \
    --name ExampleDeployment \
    --mode Complete \
    --resource-group ExampleGroup \
    --template-file storage.json \
    --parameters storageAccountType=Standard_GRS
```

## <a name="sample-template"></a>Ukázková šablona

Následující šablony se používá pro příklady v tomto článku. Zkopírujte a uložte ho jako soubor s názvem storage.json. Postup vytvoření této šablony najdete v tématu [vytvoření první šablony Azure Resource Manageru](resource-manager-create-first-template.md).  

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountType": {
      "type": "string",
      "defaultValue": "Standard_LRS",
      "allowedValues": [
        "Standard_LRS",
        "Standard_GRS",
        "Standard_ZRS",
        "Premium_LRS"
      ],
      "metadata": {
        "description": "Storage Account type"
      }
    }
  },
  "variables": {
    "storageAccountName": "[concat(uniquestring(resourceGroup().id), 'standardsa')]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "apiVersion": "2016-01-01",
      "location": "[resourceGroup().location]",
      "sku": {
          "name": "[parameters('storageAccountType')]"
      },
      "kind": "Storage", 
      "properties": {
      }
    }
  ],
  "outputs": {
      "storageAccountName": {
          "type": "string",
          "value": "[variables('storageAccountName')]"
      }
  }
}
```

## <a name="next-steps"></a>Další postup
* V příkladech v tomto článku nasazení prostředků do skupiny prostředků ve vašem výchozím předplatném. Pokud chcete použít jiné předplatné, naleznete v tématu [Správa několika předplatných Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Úplný ukázkový skript, který se nasazuje šablony najdete v tématu [skript nasazení šablony Resource Manageru](resource-manager-samples-cli-deploy.md).
* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Tipy pro řešení běžných chyb při nasazení, najdete v části [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která se vyžaduje SAS token najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-cli-sas-token.md).
* Pokyny k tomu, jak můžou podniky používat Resource Manager k efektivní správě předplatných, najdete v části [Základní kostra Azure Enterprise – zásady správného řízení pro předplatná](/azure/architecture/cloud-adoption-guide/subscription-governance).
