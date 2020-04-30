---
title: Nasazení prostředků pomocí Azure CLI a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure CLI. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 04/20/2020
ms.openlocfilehash: 8ee15699a085178add05137be895fe6b660b715b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "81685690"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon ARM a Azure CLI

Tento článek vysvětluje, jak používat Azure CLI se šablonami Azure Resource Manager (ARM) k nasazení prostředků do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, přečtěte si téma [Přehled nasazení šablony](overview.md).

Příkazy nasazení změněné v Azure CLI verze 2.2.0. Příklady v tomto článku vyžadují Azure CLI verze 2.2.0 nebo novější.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Pokud nemáte nainstalované rozhraní příkazového řádku Azure CLI, můžete použít [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo tenanta. Ve většině případů budete cílit na nasazení do skupiny prostředků. Chcete-li použít zásady a přiřazení rolí v rámci většího rozsahu, použijte předplatné, skupinu pro správu nebo nasazení klientů. Při nasazování do předplatného můžete vytvořit skupinu prostředků a nasadit do ní prostředky.

V závislosti na rozsahu nasazení použijete jiné příkazy.

Pokud ho chcete nasadit do **skupiny prostředků**, použijte příkaz [AZ Deployment Group Create](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

K nasazení do **předplatného**použijte [AZ Deployment sub Create](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create):

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

Pokud ho chcete nasadit do **skupiny pro správu**, použijte příkaz [AZ Deployment mg Create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

K nasazení do **tenanta**použijte [AZ Deployment tenant Create](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

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
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters storageAccountType=Standard_GRS
```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva, která obsahuje výsledek:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Nasadit vzdálenou šablonu

Místo uložení šablon ARM na místní počítač můžete chtít ukládat je do externího umístění. Šablony můžete ukládat do úložiště správy zdrojového kódu (například GitHub). Nebo je můžete uložit do účtu služby Azure Storage pro sdílený přístup ve vaší organizaci.

K nasazení externí šablony použijte parametr **template-URI** . Pomocí identifikátoru URI v příkladu nasaďte ukázkovou šablonu z GitHubu.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
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
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametry

K předání hodnot parametrů můžete použít buď vložené parametry, nebo soubor parametrů.

### <a name="inline-parameters"></a>Vložené parametry

Chcete-li předat vložené parametry, zadejte hodnoty `parameters`v. Například pro předání řetězce a pole do šablony je bash shell, použijte:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Pokud používáte Azure CLI s příkazovým řádkem (CMD) nebo prostředím PowerShell pro Windows, předejte pole ve `exampleArray="['value1','value2']"`formátu:.

Obsah souboru můžete také získat a poskytnout ho jako vložený parametr.

```azurecli-interactive
az deployment group create \
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

Chcete-li předat místní soubor parametrů, `@` použijte k určení místního souboru s názvem Storage. Parameters. JSON.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Zpracovat rozšířený formát JSON

Pokud chcete nasadit šablonu s víceřádkovými řetězci nebo komentáři pomocí rozhraní příkazového řádku Azure s verzí 2.3.0 nebo starší, musíte `--handle-extended-json-format` použít přepínač.  Příklad:

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

K otestování svých šablon a hodnot parametrů bez skutečného nasazení prostředků použijte příkaz [AZ Deployment Group Validate](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Pokud nejsou zjištěny žádné chyby, příkaz vrátí informace o testovacím nasazení. Konkrétně si všimněte, že hodnota **chyby** je null.

```output
{
  "error": null,
  "properties": {
      ...
```

Pokud se zjistí chyba, vrátí příkaz chybovou zprávu. Například předáním nesprávné hodnoty SKU účtu úložiště vrátí následující chybu:

```output
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

```output
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
- Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
- Pokud chcete službu bezpečně zavést do více než jedné oblasti, přečtěte si [Azure Deployment Manager](deployment-manager-overview.md).
