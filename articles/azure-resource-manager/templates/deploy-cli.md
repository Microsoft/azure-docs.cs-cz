---
title: Nasazení prostředků pomocí azure cli a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure CLI. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 241b84bc7b8c0b213e74cd7ee5f3d7668fe0d808
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282643"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon ARM a azure cli

Tento článek vysvětluje, jak používat Azure CLI se šablonami Azure Resource Manager (ARM) k nasazení prostředků do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, podívejte se na [přehled nasazení šablony](overview.md).

Příkazy nasazení se změnily v azure CLI verze 2.2.0. Příklady v tomto článku vyžadují Azure CLI verze 2.2.0 nebo novější.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Pokud nemáte nainstalovaný Azure CLI, můžete použít [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo klienta. Ve většině případů se zaměříte na nasazení do skupiny prostředků. Chcete-li použít zásady a přiřazení rolí napříč větším oborem, použijte předplatné, skupinu pro správu nebo nasazení tenanta. Při nasazování do předplatného můžete vytvořit skupinu prostředků a nasadit do ní prostředky.

V závislosti na rozsahu nasazení můžete použít různé příkazy.

Chcete-li nasadit do **skupiny prostředků**, použijte [vytvoření skupiny nasazení az](/cli/azure/deployment/group?view=azure-cli-latest#az-deployment-group-create):

```azurecli-interactive
az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
```

Chcete-li nasadit do **předplatného**, použijte [dílčí vytvoření nasazení az](/cli/azure/deployment/sub?view=azure-cli-latest#az-deployment-sub-create):

```azurecli-interactive
az deployment sub create --location <location> --template-file <path-to-template>
```

Další informace o nasazení na úrovni předplatného naleznete v [tématu Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

Chcete-li se nasadit do **skupiny pro správu**, použijte [nasazení az mg create](/cli/azure/deployment/mg?view=azure-cli-latest#az-deployment-mg-create):

```azurecli-interactive
az deployment mg create --location <location> --template-file <path-to-template>
```

Další informace o nasazení na úrovni skupiny pro správu naleznete [v tématu Vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

Chcete-li nasadit do **klienta**, použijte [vytvoření klienta nasazení AZ](/cli/azure/deployment/tenant?view=azure-cli-latest#az-deployment-tenant-create):

```azurecli-interactive
az deployment tenant create --location <location> --template-file <path-to-template>
```

Další informace o nasazení na úrovni klienta najdete v tématu [Vytváření prostředků na úrovni klienta](deploy-to-tenant.md).

Příklady v tomto článku používají nasazení skupiny prostředků.

## <a name="deploy-local-template"></a>Nasazení místní šablony

Při nasazování prostředků do Azure:

1. Přihlášení k účtu Azure
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazené prostředky. Název skupiny prostředků může obsahovat pouze alfanumerické znaky, tečky, podtržítka, pomlčky a závorky. Může to být až 90 znaků. Nemůže to skončit v určité době.
3. Nasazení do skupiny prostředků šablona, která definuje prostředky k vytvoření

Šablona může obsahovat parametry, které umožňují přizpůsobit nasazení. Můžete například zadat hodnoty, které jsou přizpůsobeny pro určité prostředí (například dev, test a výroba). Ukázková šablona definuje parametr pro skladovou položku účtu úložiště.

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

## <a name="deploy-remote-template"></a>Nasazení vzdálené šablony

Místo ukládání arm šablony na místním počítači, můžete raději uložit do externího umístění. Šablony můžete ukládat do úložiště správy zdrojového kódu (například GitHub). Nebo je můžete uložit do účtu úložiště Azure pro sdílený přístup ve vaší organizaci.

Chcete-li nasadit externí šablonu, použijte parametr **template-uri.** Použití IDENTIFIKÁTORU URI v příkladu k nasazení ukázkové šablony z GitHubu.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Předchozí příklad vyžaduje veřejně přístupný identifikátor URI pro šablonu, který funguje pro většinu scénářů, protože šablona by neměla obsahovat citlivá data. Pokud potřebujete zadat citlivá data (například heslo správce), předajte tuto hodnotu jako zabezpečený parametr. Pokud však nechcete, aby byla šablona veřejně přístupná, můžete ji chránit uložením do kontejneru soukromého úložiště. Informace o nasazení šablony, která vyžaduje token sdíleného přístupového podpisu (SAS), naleznete [v tématu Nasazení soukromé šablony pomocí tokenu SAS](secure-template-with-sas-token.md).

[!INCLUDE [resource-manager-cloud-shell-deploy.md](../../../includes/resource-manager-cloud-shell-deploy.md)]

V prostředí Cloud Shell použijte následující příkazy:

```azurecli-interactive
az group create --name examplegroup --location "South Central US"
az deployment group create --resource-group examplegroup \
  --template-uri <copied URL> \
  --parameters storageAccountType=Standard_GRS
```

## <a name="parameters"></a>Parametry

Chcete-li předat hodnoty parametrů, můžete použít buď vložky, nebo soubor parametrů.

### <a name="inline-parameters"></a>Vsazené parametry

Chcete-li předat vřadné parametry, zadejte hodnoty v . `parameters` Chcete-li například předat řetězec a pole šabloně, je prostředí Bash, použijte:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Pokud používáte Azure CLI s příkazovým příkazovým příkazem systému Windows (CMD) nebo PowerShellem, předajpole ve formátu: `exampleArray="['value1','value2']"`.

Můžete také získat obsah souboru a poskytnout tento obsah jako vsazený parametr.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Získání hodnoty parametru ze souboru je užitečné, když potřebujete zadat hodnoty konfigurace. Můžete například zadat [hodnoty cloud-init pro virtuální počítač S IP](../../virtual-machines/linux/using-cloud-init.md).

Formát arrayContent.json je:

```json
[
    "value1",
    "value2"
]
```

### <a name="parameter-files"></a>Soubory parametrů

Spíše než předávání parametrů jako vložkové hodnoty ve skriptu, může být jednodušší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů musí být místní soubor. Externí parametrsoubory nejsou podporovány s Azure CLI.

Další informace o souboru parametrů naleznete v tématu [Vytvoření souboru parametrů Správce prostředků](parameter-files.md).

Chcete-li předat soubor `@` místních parametrů, použijte k určení místního souboru s názvem storage.parameters.json.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Zpracování rozšířeného formátu JSON

Chcete-li nasadit šablonu s víceřádkovými `--handle-extended-json-format` řetězci nebo komentáři, musíte použít přepínač.  Například:

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

Chcete-li otestovat hodnoty šablony a parametrů bez skutečného nasazení prostředků, použijte [ověření skupiny nasazení az](/cli/azure/group/deployment).

```azurecli-interactive
az deployment group validate \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

Pokud nejsou zjištěny žádné chyby, příkaz vrátí informace o testovacím nasazení. Všimněte si zejména, že hodnota **chyby** je null.

```output
{
  "error": null,
  "properties": {
      ...
```

Pokud je zjištěna chyba, příkaz vrátí chybovou zprávu. Například předání nesprávné hodnoty pro skladovou položku účtu úložiště vrátí následující chybu:

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

Pokud má šablona chybu syntaxe, příkaz vrátí chybu označující, že nemohla šablonu analyzovat. Zpráva označuje číslo řádku a umístění chyby analýzy.

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

- Pokud chcete vrátit se k úspěšnému nasazení, když se zobrazí chyba, přečtěte si informace [o chybě vrácení zpět k úspěšnému nasazení](rollback-on-error.md).
- Pokud chcete určit, jak zpracovat prostředky, které existují ve skupině prostředků, ale nejsou definovány v šabloně, najdete v [tématu režimy nasazení Správce prostředků Azure](deployment-modes.md).
- Informace o tom, jak definovat parametry v šabloně, naleznete [v tématu Principy struktury a syntaxe šablon ARM](template-syntax.md).
- Tipy k řešení běžných chyb nasazení najdete [v tématu Řešení běžných chyb nasazení Azure ve Správci prostředků Azure](common-deployment-errors.md).
- Informace o nasazení šablony, která vyžaduje token SAS, naleznete v [tématu Nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
- Pokud chcete bezpečně zavést službu do více než jedné oblasti, přečtěte si informace [o Tom, jak najít Správce nasazení Azure](deployment-manager-overview.md).
