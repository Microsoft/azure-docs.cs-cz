---
title: Nasazení prostředků pomocí Azure CLI a šablony
description: K nasazení prostředků do Azure použijte Azure Resource Manager a Azure CLI. Prostředky jsou definovány v šabloně Resource Manageru.
ms.topic: conceptual
ms.date: 09/08/2020
ms.openlocfilehash: 7e8ae7e8c568f5f0ebb85f434e33f142b5fe94e8
ms.sourcegitcommit: d0541eccc35549db6381fa762cd17bc8e72b3423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/09/2020
ms.locfileid: "89566156"
---
# <a name="deploy-resources-with-arm-templates-and-azure-cli"></a>Nasazení prostředků pomocí šablon ARM a Azure CLI

Tento článek vysvětluje, jak pomocí rozhraní příkazového řádku Azure s Azure Resource Manager šablon (šablon ARM) nasadit vaše prostředky do Azure. Pokud nejste obeznámeni s koncepty nasazení a správy řešení Azure, přečtěte si téma [Přehled nasazení šablony](overview.md).

Příkazy nasazení změněné v Azure CLI verze 2.2.0. Příklady v tomto článku vyžadují Azure CLI verze 2.2.0 nebo novější.

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

Pokud nemáte nainstalované rozhraní příkazového řádku Azure CLI, můžete použít [Cloud Shell](#deploy-template-from-cloud-shell).

## <a name="deployment-scope"></a>Rozsah nasazení

Nasazení můžete cílit na skupinu prostředků, předplatné, skupinu pro správu nebo tenanta. Ve většině případů budete cílit na nasazení do skupiny prostředků. Chcete-li použít zásady a přiřazení rolí v rámci většího rozsahu, použijte předplatné, skupinu pro správu nebo nasazení klientů. Při nasazování do předplatného můžete vytvořit skupinu prostředků a nasadit do ní prostředky.

V závislosti na rozsahu nasazení použijete jiné příkazy.

* Pokud ho chcete nasadit do **skupiny prostředků**, použijte příkaz [AZ Deployment Group Create](/cli/azure/deployment/group#az-deployment-group-create):

  ```azurecli-interactive
  az deployment group create --resource-group <resource-group-name> --template-file <path-to-template>
  ```

* K nasazení do **předplatného**použijte [AZ Deployment sub Create](/cli/azure/deployment/sub#az-deployment-sub-create):

  ```azurecli-interactive
  az deployment sub create --location <location> --template-file <path-to-template>
  ```

  Další informace o nasazeních na úrovni předplatného najdete v tématu [Vytvoření skupin prostředků a prostředků na úrovni předplatného](deploy-to-subscription.md).

* Pokud ho chcete nasadit do **skupiny pro správu**, použijte příkaz [AZ Deployment mg Create](/cli/azure/deployment/mg#az-deployment-mg-create):

  ```azurecli-interactive
  az deployment mg create --location <location> --template-file <path-to-template>
  ```

  Další informace o nasazení na úrovni skupiny pro správu najdete v tématu věnovaném [vytvoření prostředků na úrovni skupiny pro správu](deploy-to-management-group.md).

* K nasazení do **tenanta**použijte [AZ Deployment tenant Create](/cli/azure/deployment/tenant#az-deployment-tenant-create):

  ```azurecli-interactive
  az deployment tenant create --location <location> --template-file <path-to-template>
  ```

  Další informace o nasazeních na úrovni tenanta najdete v tématu [vytvoření prostředků na úrovni tenanta](deploy-to-tenant.md).

Příklady v tomto článku používají nasazení skupin prostředků.

## <a name="deploy-local-template"></a>Nasadit místní šablonu

Při nasazování prostředků do Azure:

1. Přihlášení k účtu Azure
2. Vytvořte skupinu prostředků, která slouží jako kontejner pro nasazené prostředky. Název skupiny prostředků může obsahovat jenom alfanumerické znaky, tečky, podtržítka, spojovníky a závorky. Může to být až 90 znaků. Nemůže končit tečkou.
3. Do skupiny prostředků nasaďte šablonu, která definuje prostředky, které se mají vytvořit.

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

## <a name="deployment-name"></a>Název nasazení

V předchozím příkladu jste najmenovali nasazení `ExampleDeployment` . Pokud název nasazení nezadáte, použije se název souboru šablony. Například pokud nasadíte šablonu s názvem `azuredeploy.json` a nezadáte název nasazení, nasazení se pojmenuje `azuredeploy` .

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

## <a name="deploy-template-spec"></a>Nasadit specifikaci šablony

Místo nasazení místní nebo vzdálené šablony můžete vytvořit [specifikaci šablony](template-specs.md). Specifikace šablony je prostředek ve vašem předplatném Azure, který obsahuje šablonu ARM. Usnadňuje bezpečné sdílení šablony s uživateli ve vaší organizaci. Řízení přístupu na základě role (RBAC) slouží k udělení přístupu ke specifikaci šablony. Tato funkce je aktuálně ve verzi Preview.

Následující příklady ukazují, jak vytvořit a nasadit specifikace šablony. Tyto příkazy jsou k dispozici pouze v případě, že jste se [zaregistrovali ve verzi Preview](https://aka.ms/templateSpecOnboarding).

Nejprve vytvoříte specifikaci šablony zadáním šablony ARM.

```azurecli
az ts create \
  --name storageSpec \
  --version "1.0" \
  --resource-group templateSpecRG \
  --location "westus2" \
  --template-file "./mainTemplate.json"
```

Potom získáte ID pro specifikaci šablony a nasadíte ji.

```azurecli
id = $(az ts show --name storageSpec --resource-group templateSpecRG --version "1.0" --query "id")

az deployment group create \
  --resource-group demoRG \
  --template-spec $id
```

Další informace najdete v tématu [Azure Resource Manager specifikace šablon (Preview)](template-specs.md).

## <a name="preview-changes"></a>Zobrazit náhled změn

Před nasazením šablony můžete zobrazit náhled změn, které šablona provede pro vaše prostředí. Pomocí [operace citlivosti](template-deploy-what-if.md) ověřte, že šablona provádí očekávané změny. Co když zároveň ověří chyby v šabloně.

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

Chcete-li předat vložené parametry, zadejte hodnoty v `parameters` . Například pro předání řetězce a pole do šablony je bash shell, použijte:

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString='inline string' exampleArray='("value1", "value2")'
```

Pokud používáte Azure CLI s příkazovým řádkem (CMD) nebo prostředím PowerShell pro Windows, předejte pole ve formátu: `exampleArray="['value1','value2']"` .

Obsah souboru můžete také získat a poskytnout ho jako vložený parametr.

```azurecli-interactive
az deployment group create \
  --resource-group testgroup \
  --template-file demotemplate.json \
  --parameters exampleString=@stringContent.txt exampleArray=@arrayContent.json
```

Získání hodnoty parametru ze souboru je užitečné v případě, že potřebujete zadat konfigurační hodnoty. Můžete například zadat [hodnoty Cloud-init pro virtuální počítač se systémem Linux](../../virtual-machines/linux/using-cloud-init.md).

arrayContent.jsve formátu:

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

Místo předání parametrů jako vložených hodnot do skriptu může být snazší použít soubor JSON, který obsahuje hodnoty parametrů. Soubor parametrů musí být místní soubor. Soubory externích parametrů se v Azure CLI nepodporují.

Další informace o souboru parametrů naleznete v tématu [Create správce prostředků Parameter File](parameter-files.md).

Chcete-li předat místní soubor parametrů, použijte `@` k určení místního souboru s názvem storage.parameters.jsv.

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

- Chcete-li se vrátit k úspěšnému nasazení, když se zobrazí chyba, přečtěte si téma [vrácení chyby při úspěšném nasazení](rollback-on-error.md).
- Pokud chcete určit, jak se mají zpracovávat prostředky, které existují ve skupině prostředků, ale nejsou definované v šabloně, přečtěte si téma [režimy nasazení Azure Resource Manager](deployment-modes.md).
- Informace o definování parametrů v šabloně najdete v tématu [pochopení struktury a syntaxe šablon ARM](template-syntax.md).
- Tipy k řešení běžných chyb nasazení najdete v tématu [řešení běžných chyb při nasazení Azure pomocí Azure Resource Manager](common-deployment-errors.md).
- Informace o nasazení šablony, která vyžaduje token SAS, najdete v tématu [nasazení privátní šablony s tokenem SAS](secure-template-with-sas-token.md).
