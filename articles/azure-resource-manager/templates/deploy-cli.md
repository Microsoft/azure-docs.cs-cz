---
title: Nasazení prostředků pomocí Azure CLI a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure CLI. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 10/09/2019
ms.openlocfilehash: 242b9f2a4bc39f8aa083d9c89d3dd7ed850b3489
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2020
ms.locfileid: "76154291"
---
# <a name="deploy-resources-with-resource-manager-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon Resource Manageru a Azure CLI

Tento článek vysvětluje, jak pomocí Azure CLI s Správce prostředků šablonami nasadit vaše prostředky do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, přečtěte si téma [Přehled nasazení šablony](overview.md).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Pokud nemáte nainstalované rozhraní příkazového řádku Azure CLI, můžete použít [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit buď na předplatné Azure, nebo na skupinu prostředků v rámci předplatného. Ve většině případů budete cílit na nasazení do skupiny prostředků. Nasazení předplatných použijte k uplatnění zásad a přiřazení rolí v rámci předplatného. K vytvoření skupiny prostředků a nasazení prostředků do ní taky použijete nasazení předplatného. V závislosti na rozsahu nasazení použijete jiné příkazy.

Pokud ho chcete nasadit do **skupiny prostředků**, použijte příkaz [AZ Group Deployment Create](/cli/azure/group/deployment?view=azure-cli-latest#az-group-deployment-create):

```azurecli
az group deployment create --resource-group <resource-group-name> --template-file <path-to-template>
```

K nasazení do **předplatného**použijte [AZ Deployment Create](/cli/azure/deployment?view=azure-cli-latest#az-deployment-create):

```azurecli
az deployment create --location <location> --template-file <path-to-template>
```

Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

V současné době se nasazení skupin pro správu podporují jenom prostřednictvím REST API. Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

Příklady v tomto článku používají nasazení skupin prostředků.

## <a name="deploy-local-template"></a>Nasadit místní šablonu

Při nasazování prostředků do Azure:

1. Přihlášení k účtu Azure
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazené prostředky. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Nemůže končit tečkou.
3. Nasaďte do skupiny prostředků šablonu, která definuje prostředky, které se mají vytvořit.

Šablona může obsahovat parametry, které umožňují přizpůsobení nasazení. Můžete například zadat hodnoty, které jsou upraveny pro konkrétní prostředí (například vývoj, testování a produkce). Ukázková šablona definuje parametr pro SKLADOVOU položku účtu úložiště.

Následující příklad vytvoří skupinu prostředků a nasadí šablonu z místního počítače:

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva, která obsahuje výsledek:

```azurecli
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Nasadit vzdálenou šablonu

Místo uložení šablon Správce prostředků na místním počítači můžete chtít ukládat je do externího umístění. Šablony můžete ukládat do úložiště správy zdrojového kódu (například GitHub). Nebo je můžete uložit do účtu služby Azure Storage pro sdílený přístup ve vaší organizaci.

K nasazení externí šablony použijte parametr **template-URI** . Pomocí identifikátoru URI v příkladu nasaďte ukázkovou šablonu z GitHubu.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Předchozí příklad vyžaduje pro šablonu veřejně přístupný identifikátor URI, který funguje ve většině scénářů, protože by šablona neměla obsahovat citlivá data. Pokud potřebujete zadat citlivá data (třeba heslo správce), předejte tuto hodnotu jako zabezpečený parametr. Pokud ale nechcete, aby byla šablona veřejně přístupná, můžete ji chránit uložením do privátního kontejneru úložiště. Informace o nasazení šablony, která vyžaduje token sdíleného přístupového podpisu (SAS), najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

V Cloud Shell použijte následující příkazy:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az group deployment create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametry

K předání hodnot parametrů můžete použít buď vložené parametry, nebo soubor parametrů.

### <a name="inline-parameters"></a>Vložené parametry

Chcete-li předat vložené parametry, zadejte hodnoty v `parameters`. Například pro předání řetězce a pole do šablony je bash shell, použijte:

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Pokud používáte Azure CLI s příkazovým řádkem (CMD) nebo PowerShellem pro Windows, předejte pole ve formátu: `exampleArray="['value1','value2']"`.

Obsah souboru můžete také získat a poskytnout ho jako vložený parametr.

```azurecli
az group deployment create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Získání hodnoty parametru ze souboru je užitečné v případě, že potřebujete zadat konfigurační hodnoty. Můžete například zadat [hodnoty Cloud-init pro virtuální počítač se systémem Linux](../../virtual-machines/linux/using-cloud-init.md).

Formát arrayContent. JSON je:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Soubory parametrů

Místo předání parametrů jako vložených hodnot do skriptu může být snazší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů musí být místní soubor. Soubory externích parametrů se v Azure CLI nepodporují.

Další informace o souboru parametrů naleznete v tématu [Create správce prostředků Parameter File](parameter-files.md).

Chcete-li předat místní soubor parametrů, použijte `@` k určení místního souboru s názvem Storage. Parameters. JSON.

```azurecli-interactive
az group deployment create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Zpracovat rozšířený formát JSON

Chcete-li nasadit šablonu s víceřádkovými řetězci nebo komentáři, je nutné použít přepínač `--handle-extended-json-format`.  Například:

```json
{
  "type": "Microsoft.Compute/virtualMachines",
  "apiVersion": "2018-10-01",
  "name": "[variables('vmName')]", // to customize name, change it in variables
  "location": "[
    parameters('location')
    ]", //defaults to resource group location
  /*
    storage account and network interface
    must be deployed first
  */
  "dependsOn": [
    "[resourceId('Microsoft.Storage/storageAccounts/', variables('storageAccountName'))]",
    "[resourceId('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
  ],
```

## <a name="test-a-template-deployment"></a>Testování nasazení šablony

K otestování svých šablon a hodnot parametrů bez skutečného nasazení prostředků použijte příkaz [AZ Group Deployment Validate](/cli/azure/group/deployment#az-group-deployment-validate).

```azurecli-interactive
az group deployment validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Pokud nejsou zjištěny žádné chyby, příkaz vrátí informace o testovacím nasazení. Konkrétně si všimněte, že hodnota **chyby** je null.

```azurecli
{
  "error": null,
  "properties": {
      ...
```

Pokud se zjistí chyba, vrátí příkaz chybovou zprávu. Například předáním nesprávné hodnoty SKU účtu úložiště vrátí následující chybu:

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

Pokud má vaše šablona syntaktickou chybu, příkaz vrátí chybu oznamující, že nešlo analyzovat šablonu. Zpráva indikuje číslo řádku a umístění chyby analýzy.

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

## <a name="next-steps"></a>Další kroky

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Chcete-li pochopit, jak definovat parametry v šabloně, přečtěte si téma [pochopení struktury a syntaxe šablon Azure Resource Manager](template-syntax.md).
- Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
- Pokud chcete službu bezpečně zavést do více než jedné oblasti, přečtěte si [Azure Deployment Manager](deployment-manager-overview.md).
