---
title: Nasazení prostředků pomocí Azure CLI a šablon | Dokumentace Microsoftu
description: Nasazení prostředků do Azure pomocí Azure Resource Manageru a Azure CLI. Prostředky jsou definovány v šabloně Resource Manageru.
services: azure-resource-manager
documentationcenter: na
author: tfitzmac
ms.assetid: 493b7932-8d1e-4499-912c-26098282ec95
ms.service: azure-resource-manager
ms.devlang: azurecli
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/14/2019
ms.author: tomfitz
ms.openlocfilehash: 0c298ba2074a57bd182b23e5fd9bc6b68ee496ad
ms.sourcegitcommit: f863ed1ba25ef3ec32bd188c28153044124cacbc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56300445"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI

Tento článek vysvětluje, jak nasadit prostředky do Azure pomocí rozhraní příkazového řádku Azure pomocí šablon Resource Manageru. Pokud nejsou dobře známé koncepty nasazení a správou řešení Azure, najdete v článku [přehled Azure Resource Manageru](resource-group-overview.md).  

Šablony Resource Manageru, který nasazujete, může být místní soubor na počítači, nebo externí soubor, který se nachází v úložišti, jako je GitHub. Šablona nasazení v tomto článku je k dispozici jako [Šablona účtu úložiště v Githubu](https://github.com/Azure/azure-quickstart-templates/blob/master/101-storage-account-create/azuredeploy.json).

[!INCLUDE [sample-cli-install](../../includes/sample-cli-install.md)]

Pokud nemáte nainstalované rozhraní příkazového řádku Azure, můžete použít [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deploy-local-template"></a>Nasazení místního šablony

Při nasazování prostředků do Azure, můžete:

1. Přihlášení k účtu Azure
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazených prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může být až 90 znaků. Nesmí končit tečkou.
3. Nasazení do skupiny prostředků, který definuje prostředky k vytvoření šablony

Šablona může obsahovat parametry, které vám umožní přizpůsobit nasazení. Například můžete zadat hodnoty, které jsou přizpůsobené pro konkrétní prostředí (jako je vývoj, testování a produkce). Ukázková šablona definuje parametr pro SKU účtu úložiště. 

Následující příklad vytvoří skupinu prostředků a nasadí šablony z místního počítače:

```azurecli-interactive
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
   
```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

V předchozím příkladu vyžaduje veřejně přístupné identifikátor URI pro šablony, které lze použít pro většinu scénářů, protože šablony by neměl obsahovat citlivá data. Pokud je třeba zadat citlivá data (jako je zadání hesla správce), předáte tuto hodnotu jako zabezpečený parametr. Ale pokud nechcete, aby vaše šablona veřejně přístupný, budete ho chránit ukládáním do privátního úložiště kontejnerů. Informace o nasazení šablony, která se vyžaduje token sdíleného přístupového podpisu (SAS), najdete v části [nasazení privátní šablony s tokenem SAS](resource-manager-cli-sas-token.md).

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

## <a name="redeploy-when-deployment-fails"></a>Opětovné nasazení při nasazení se nezdaří

Pokud se nasazení nezdaří, můžete automaticky znovu nasadit starší a úspěšné nasazení z historie nasazení. Pokud chcete nastavit opětovné nasazení, použijte `--rollback-on-error` parametr v příkazu pro nasazení.

Tato možnost dala použít, vaše nasazení musí mít jedinečné názvy, tak je možné identifikovat v historii. Pokud nemáte jedinečné názvy, aktuální selhání nasazení může přepsat předchozí úspěšné nasazení v historii. Tuto možnost můžete použít pouze u kořenové úrovně nasazení. Nasazení z vnořené šablony nejsou k dispozici pro nové nasazení.

K opětovnému nasazení poslední úspěšné nasazení, přidejte `--rollback-on-error` parametr jako příznak.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error
```

Chcete-li znovu nasadit konkrétní nasazení, použijte `--rollback-on-error` parametr a zadejte název nasazení.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment02 \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS \
  --rollback-on-error ExampleDeployment01
```

Zadané nasazení musí mít bylo úspěšné.

## <a name="parameters"></a>Parametry

Účelem předání hodnot parametrů, můžete použít parametry vložené nebo soubor parametru. Předchozí příklady v tomto článku ukazují vložených parametrů.

### <a name="inline-parameters"></a>Vložené parametry

Pro předání parametrů vložený, zadejte hodnoty v `parameters`. Například k předání řetězce a pole do šablony je dostupné prostředí Bash, použijte:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Můžete také získat obsah souboru a poskytují tento obsah jako parametr vložené.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Získávání hodnotu parametru ze souboru je užitečné, když je potřeba zadat hodnoty konfigurace. Například můžete zadat [cloud-init hodnoty pro virtuální počítač s Linuxem](../virtual-machines/linux/using-cloud-init.md).

ArrayContent.json formát je:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Soubory parametrů

Místo předání parametrů jako hodnoty vložená ve skriptu, možná bude snadněji používá soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů musí být místní soubor. Soubory externí parametrů nejsou podporovány pomocí Azure CLI.

Soubor parametrů musí být v následujícím formátu:

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

Všimněte si, že sekci parametrů obsahuje název parametru, který odpovídá parametru definovaného v šabloně (storageAccountType). Soubor parametrů obsahuje hodnotu pro parametr. Tato hodnota je automaticky předávaných do šablony během nasazení. Můžete vytvořit více než jeden soubor s parametry a poté předejte soubor odpovídající parametr pro scénář. 

Předchozí příklad zkopírujte a uložte ho jako soubor s názvem `storage.parameters.json`.

Chcete-li předat parametr místní soubor, použijte `@` zadat místní soubor s názvem storage.parameters.json.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

### <a name="parameter-precedence"></a>Parametr prioritu

Můžete použít parametry vložené a místní parametr souboru v rámci jedné operace nasazení. Můžete například zadat některé hodnoty v souboru parametrů místní a přidat další hodnoty vložený během nasazení. Pokud zadáte hodnoty pro parametr v parametru místního souboru a vložené, hodnota vložené přednost.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters @demotemplate.parameters.json \
  --parameters exampleArray=@arrtest.json
```


## <a name="test-a-template-deployment"></a>Test šablony nasazení

Chcete-li otestovat šablonu a parametry hodnoty bez skutečného nasazení všechny prostředky, použijte [ověřit nasazení skupiny pro az](/cli/azure/group/deployment#az-group-deployment-validate). 

```azurecli-interactive
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

Pokud dojde k chybě, příkaz vrátí chybovou zprávu. Nesprávná hodnota pro účet úložiště SKU, například vrátí následující chybu:

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

## <a name="next-steps"></a>Další postup
* V příkladech v tomto článku nasazení prostředků do skupiny prostředků ve vašem výchozím předplatném. Pokud chcete použít jiné předplatné, naleznete v tématu [Správa několika předplatných Azure](/cli/azure/manage-azure-subscriptions-azure-cli).
* Chcete-li určit způsob zpracování prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manageru](deployment-modes.md).
* Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [Princip struktury a syntaxe šablon Azure Resource Manageru](resource-group-authoring-templates.md).
* Tipy pro řešení běžných chyb při nasazení, najdete v části [řešit běžné chyby nasazení v Azure pomocí Azure Resource Manageru](resource-manager-common-deployment-errors.md).
* Informace o nasazení šablony, která se vyžaduje SAS token najdete v tématu [nasazení privátní šablony s tokenem SAS](resource-manager-cli-sas-token.md).
* Bezpečně zavést vaše služba pro více než jedné oblasti, naleznete v tématu [Azure Deployment Manager](deployment-manager-overview.md).
