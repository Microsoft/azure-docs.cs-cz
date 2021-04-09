---
title: Nasazení prostředků pomocí Azure CLI a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure CLI. Prostředky jsou definovány v Správce prostředků šabloně nebo v souboru bicep.
ms.topic: conceptual
ms.date: 03/25/2021
ms.openlocfilehash: b19dc6cc292306cc796f8c1d8f93b358a079d83b
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "105544430"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon ARM a Azure CLI

Tento článek vysvětluje, jak pomocí rozhraní příkazového řádku Azure s Azure Resource Manager šablon (šablon ARM) nebo souborů bicep nasadit vaše prostředky do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, přečtěte si téma [Přehled nasazení šablony](overview.md) nebo [bicep Overview](bicep-overview.md).

Příkazy nasazení změněné v Azure CLI verze 2.2.0. Příklady v tomto článku vyžadují Azure CLI verze 2.2.0 nebo novější. K nasazení souborů bicep potřebujete [Azure CLI verze 2.20.0 nebo novější](/cli/azure/install-azure-cli).

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Pokud nemáte nainstalované rozhraní příkazového řádku Azure CLI, můžete použít Azure Cloud Shell. Další informace najdete v tématu [Nasazení šablon ARM z Azure Cloud Shell](deploy-cloud-shell.md).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo tenanta. V závislosti na rozsahu nasazení použijete jiné příkazy.

* Pokud ho chcete nasadit do **skupiny prostředků**, použijte příkaz [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template-or-bicep>
  ```

* K nasazení do **předplatného** použijte [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

* Pokud ho chcete nasadit do **skupiny pro správu**, použijte příkaz [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

* K nasazení do **tenanta** použijte [AZ Deployment tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template-or-bicep>
  ```

  Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

Pro každý obor musí mít uživatel, který šablonu nasazuje, nebo soubor bicep potřebná oprávnění k vytváření prostředků.

## <a name="deploy-local-template-or-bicep-file"></a>Nasadit místní šablonu nebo soubor bicep

Šablonu můžete nasadit z místního počítače nebo z nějakého, který je uložen externě. Tato část popisuje nasazení místní šablony.

Pokud provádíte nasazení do skupiny prostředků, která neexistuje, vytvořte skupinu prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Název nemůže končit tečkou.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

K nasazení místní šablony nebo souboru bicep použijte `--template-file` parametr v příkazu nasazení. Následující příklad také ukazuje, jak nastavit hodnotu parametru.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file <path-to-template-or-bicep> \
  --parameters storageAccountType=Standard_GRS
```

Dokončení nasazení může trvat několik minut. Po dokončení se zobrazí zpráva, která obsahuje výsledek:

```output
"provisioningState": "Succeeded",
```

## <a name="deploy-remote-template"></a>Nasadit vzdálenou šablonu

> [!NOTE]
> V současné době Azure CLI nepodporuje nasazování vzdálených souborů bicep. Pomocí [BICEP CLI](./bicep-install.md#development-environment) zkompilujte soubor bicep do šablony JSON a potom soubor JSON načtěte do vzdáleného umístění.

Místo uložení šablon ARM na místní počítač můžete chtít ukládat je do externího umístění. Šablony můžete uložit do úložiště pro správu zdrojového kódu (jako je GitHub). Nebo je můžete uložit do účtu úložiště v Azure, abyste k nim mohli v organizaci sdílet přístup.

[!INCLUDE [Deploy templates in private GitHub repo](../../../includes/resource-manager-private-github-repo-templates.md)]

Pokud provádíte nasazení do skupiny prostředků, která neexistuje, vytvořte skupinu prostředků. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Název nemůže končit tečkou.

```azurecli-interactive
az group create --name ExampleGroup --location "Central US"
```

K nasazení externí šablony použijte parametr `template-uri`.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

Předchozí příklad vyžaduje pro šablonu veřejně přístupný identifikátor URI, který funguje ve většině scénářů, protože by šablona neměla obsahovat citlivá data. Pokud potřebujete zadat citlivá data (třeba heslo správce), předejte tuto hodnotu jako zabezpečený parametr. Pokud ale chcete spravovat přístup k šabloně, zvažte použití [specifikací šablon](#deploy-template-spec).

Pokud chcete nasadit vzdálené propojené šablony s relativní cestou uloženou v účtu úložiště, použijte `query-string` k zadání tokenu SAS:

```azurecli-interactive
az deployment group create \
  --name linkedTemplateWithRelativePath \
  --resource-group myResourceGroup \
  --template-uri "https://stage20210126.blob.core.windows.net/template-staging/mainTemplate.json" \
  --query-string $sasToken
```

Další informace najdete v tématu [použití relativní cesty pro propojené šablony](./linked-templates.md#linked-template).

## <a name="deployment-name"></a>Název nasazení

Při nasazování šablony ARM můžete dát nasazení název. Tento název vám může pomáhat s načtením nasazení z historie nasazení. Pokud název nasazení nezadáte, použije se název souboru šablony. Například pokud nasadíte šablonu s názvem `azuredeploy.json` a nezadáte název nasazení, nasazení se pojmenuje `azuredeploy` .

Pokaždé, když spustíte nasazení, do historie nasazení skupiny prostředků se přidá záznam s názvem nasazení. Pokud spustíte jiné nasazení a přiřadíte mu stejný název, bude předchozí položka nahrazena aktuálním nasazením. Pokud chcete zachovat jedinečné položky v historii nasazení, udělte každému nasazení jedinečný název.

Chcete-li vytvořit jedinečný název, můžete přiřadit náhodné číslo.

```azurecli-interactive
deploymentName='ExampleDeployment'$RANDOM
```

Nebo přidejte hodnotu data.

```azurecli-interactive
deploymentName='ExampleDeployment'$(date +"%d-%b-%Y")
```

Pokud spustíte souběžná nasazení do stejné skupiny prostředků se stejným názvem nasazení, bude dokončeno pouze poslední nasazení. Všechna nasazení se stejným názvem, která nebyla dokončena, budou nahrazena posledním nasazením. Pokud například spustíte nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage1` a zároveň spustíte jiné nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage2` , nasadíte jenom jeden účet úložiště. Výsledný účet úložiště je pojmenován `storage2` .

Pokud ale spustíte nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage1` , a hned po jeho dokončení spustíte jiné nasazení s názvem `newStorage` , které nasadí účet úložiště s názvem `storage2` , budete mít dva účty úložiště. Jedna má název `storage1` a druhá má název `storage2` . Ale v historii nasazení máte jenom jednu položku.

Pokud pro každé nasazení zadáte jedinečný název, můžete je spustit souběžně bez konfliktu. Pokud spustíte nasazení s názvem, `newStorage1` které nasadí účet úložiště s názvem `storage1` a zároveň spustíte jiné nasazení s názvem, `newStorage2` které nasadí účet úložiště s názvem `storage2` , budete mít dva účty úložiště a dvě položky v historii nasazení.

Aby nedocházelo ke konfliktům s souběžnými nasazeními a zajistili v historii nasazení jedinečné položky, udělte každé nasazení jedinečný název.

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

> [!NOTE]
> V současné době Azure CLI nepodporuje vytváření specifikací šablon poskytnutím souborů bicep. Můžete však vytvořit soubor bicep pomocí prostředku [Microsoft. Resources/templateSpecs](/azure/templates/microsoft.resources/templatespecs) pro nasazení specifikace šablony. Tady je [příklad](https://github.com/Azure/azure-docs-json-samples/blob/master/create-template-spec-using-template/azuredeploy.bicep).

Místo nasazení místní nebo vzdálené šablony můžete vytvořit [specifikaci šablony](template-specs.md). Specifikace šablony je prostředek ve vašem předplatném Azure, který obsahuje šablonu ARM. Usnadňuje bezpečné sdílení šablony s uživateli ve vaší organizaci. K udělení přístupu ke specifikaci šablony použijte řízení přístupu na základě role Azure (Azure RBAC). Tato funkce je aktuálně ve verzi Preview.

Následující příklady ukazují, jak vytvořit a nasadit specifikace šablony.

Nejdřív vytvořte specifikaci šablony tím, že poskytnete šablonu ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Pak Získejte ID pro specifikaci šablony a nasaďte ji.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Další informace najdete v tématu [Azure Resource Manager specifikace šablon (Preview)](template-specs.md).

## <a name="preview-changes"></a>Zobrazit náhled změn

Před nasazením šablony můžete zobrazit náhled změn, které šablona provede pro vaše prostředí. Pomocí [operace citlivosti](template-deploy-what-if.md) ověřte, že šablona provádí očekávané změny. Co když zároveň ověří chyby v šabloně.

## <a name="parameters"></a>Parametry

K předání hodnot parametrů můžete použít buď vložené parametry, nebo soubor parametrů.

### <a name="inline-parameters"></a>Vložené parametry

Chcete-li předat vložené parametry, zadejte hodnoty v `parameters` . Například pro předání řetězce a pole do šablony v prostředí bash použijte:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Pokud používáte Azure CLI s příkazovým řádkem (CMD) nebo prostředím PowerShell pro Windows, předejte pole ve formátu: `exampleArray="['value1','value2']"` .

Obsah souboru můžete také získat a poskytnout ho jako vložený parametr.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file <path-to-template-or-bicep> \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Získání hodnoty parametru ze souboru je užitečné v případě, že potřebujete zadat konfigurační hodnoty. Můžete například zadat [hodnoty Cloud-init pro virtuální počítač se systémem Linux](../../virtual-machines/linux/using-cloud-init.md).

_arrayContent.jsve_ formátu:

```json
[
    "value1",
    "value2"
]
```

Chcete-li předat objekt, například pro nastavení značek, použijte JSON. Vaše šablona může například obsahovat parametr, jako je tento:

```json
    "resourceTags": {
      "type": "object",
      "defaultValue": {
        "Cost Center": "IT Department"
      }
    }
```

V tomto případě můžete předat řetězec JSON pro nastavení parametru, jak je znázorněno v následujícím skriptu bash:

```bash
tags='{"Owner":"Contoso","Cost Center":"2345-324"}'
az deployment group create --name addstorage  --resource-group myResourceGroup \
--template-file $templateFile \
--parameters resourceName=abcdef4556 resourceTags="$tags"
```

Použijte dvojité uvozovky kolem formátu JSON, který chcete předat objektu.

### <a name="parameter-files"></a>Soubory parametrů

Místo předávání parametrů v podobě hodnot vložených do skriptu pro vás možná bude jednodušší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů musí být místní soubor. Soubory externích parametrů se v Azure CLI nepodporují. Šablona ARM i soubor bicep používají soubory parametrů JSON.

Další informace o souboru parametrů najdete v tématu [Vytvoření souboru parametrů Resource Manageru](parameter-files.md).

Chcete-li předat místní soubor parametrů, použijte `@` k určení místního souboru s názvem _storage.parameters.jsv_.

```azurecli-interactive
az deployment group create \
  --name ExampleDeployment \
  --resource-group ExampleGroup \
  --template-file storage.json \
  --parameters @storage.parameters.json
```

## <a name="handle-extended-json-format"></a>Zpracovat rozšířený formát JSON

Pokud chcete nasadit šablonu s víceřádkovými řetězci nebo komentáři pomocí rozhraní příkazového řádku Azure s verzí 2.3.0 nebo starší, musíte použít `--handle-extended-json-format` přepínač.  Například:

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

## <a name="next-steps"></a>Další kroky

* Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
* Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
* Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
* Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
