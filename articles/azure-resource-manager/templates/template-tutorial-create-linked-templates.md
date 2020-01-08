---
title: Vytvoření propojených šablon
description: Zjistěte, jak vytvořit propojené šablony Azure Resource Manageru pro vytvoření virtuálního počítače.
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 903193b48f41b2ba25bc35ac245e5afb482e61d2
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75472776"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Kurz: Vytvoření propojených šablon Azure Resource Manageru

Zjistěte, jak vytvořit propojené šablony Azure Resource Manageru. Použitím propojených šablon, může mít jednu šablonu vyvolávající jinou šablonu. Je to velmi vhodné pro modulační šablony. V tomto kurzu použijete stejné šabloně použité při [kurz: vytvoření Azure Resource Manageru šablony s závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md), vytváří virtuální počítač, virtuální sítě a dalších závislých prostředků včetně úložiště účet. Můžete oddělit vytvoření prostředků účtu úložiště na propojenou šablonu.

Volání propojené šablony je jako volání funkce.  Naučíte se také, jak předat hodnoty parametrů do propojené šablony a jak z propojené šablony získat návratové hodnoty.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vytvoření propojené šablony
> * Odeslání propojené šablony
> * Propojení propojené šablony
> * Konfigurace závislostí
> * Nasazení šablony
> * Další postupy

Další informace najdete v tématu [použití propojených a vnořených šablon při nasazování prostředků Azure](./linked-templates.md).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Code s rozšířením nástrojů Správce prostředků Tools. Pokud [chcete vytvořit Azure Resource Manager šablony](use-vs-code-to-create-template.md), přečtěte si téma použití Visual Studio Code.
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows). Toto je stejné šabloně použité při [kurz: vytvoření Azure Resource Manageru šablony s závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md). Uložte dvě kopie stejné šablony, které budou použity jako:

* **Hlavní šablona**: vytvoří se všechny prostředky s výjimkou účtu úložiště.
* **Propojená šablona**: vytvoří účet úložiště.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
1. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```

1. Výběrem **Open** (Otevřít) soubor otevřete.
1. Šablona definuje šest prostředků:

   * [`Microsoft.Storage/storageAccounts`](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts)
   * [`Microsoft.Network/publicIPAddresses`](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses)
   * [`Microsoft.Network/networkSecurityGroups`](https://docs.microsoft.com/azure/templates/microsoft.network/networksecuritygroups)
   * [`Microsoft.Network/virtualNetworks`](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks)
   * [`Microsoft.Network/networkInterfaces`](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces)
   * [`Microsoft.Compute/virtualMachines`](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines)

     Před přizpůsobením šablony je užitečné získat základní porozumění schématu šablony.
1. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.
1. Zvolte **Soubor**>**Uložit jako** a vytvořte jinou kopii souboru s názvem **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Vytvoření propojené šablony

Propojená šablona vytvoří účet úložiště. Odkazovaná šablona se dá použít jako samostatná šablona k vytvoření účtu úložiště. V tomto kurzu propojená šablona používá dva parametry a předává hodnotu zpět do hlavní šablony. Tato "návratová" hodnota je definována v elementu `outputs`.

1. Otevřete **linkedTemplate. JSON** v Visual Studio Code, pokud soubor není otevřený.
1. Proveďte následující změny:

    * Odeberte všechny parametry jiné než **umístění**.
    * Přidejte parametr s názvem **storageAccountName**.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      Název a umístění účtu úložiště se předávají z hlavní šablony do propojené šablony jako parametry.

    * Odeberte **proměnné** element a všechny definice proměnných.
    * Odeberte všechny prostředky kromě účtu úložiště. Odeberete celkem čtyři prostředky.
    * Aktualizujte hodnotu **název** element prostředku účtu úložiště:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```

    * Aktualizujte **výstupy** prvku, aby vypadaly takto:

        ```json
        "outputs": {
          "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
        }
        ```

       **storageUri** vyžaduje definici prostředků virtuálního počítače v hlavní šabloně.  Předejte hodnotu zpět na hlavní šablonu jako výstupní hodnotu.

        Až budete hotovi, šablony se vypadat takto:

        ```json
        {
          "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
          "contentVersion": "1.0.0.0",
          "parameters": {
            "storageAccountName": {
              "type": "string",
              "metadata": {
                "description": "Azure Storage account name."
              }
            },
            "location": {
              "type": "string",
              "defaultValue": "[resourceGroup().location]",
              "metadata": {
                "description": "Location for all resources."
              }
            }
          },
          "resources": [
            {
              "type": "Microsoft.Storage/storageAccounts",
              "name": "[parameters('storageAccountName')]",
              "location": "[parameters('location')]",
              "apiVersion": "2018-11-01",
              "sku": {
                "name": "Standard_LRS"
              },
              "kind": "Storage",
              "properties": {}
            }
          ],
          "outputs": {
            "storageUri": {
              "type": "string",
              "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
          }
        }
        ```

1. Uložte změny.

## <a name="upload-the-linked-template"></a>Odeslání propojené šablony

Hlavní šablony a propojené šablony musí být přístupné ze kterého spouštíte nasazení. V tomto kurzu použijete metody nasazení Cloud shell jako jste použili v [kurz: vytvoření Azure Resource Manageru šablony s závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md). Hlavní šablona (azuredeploy.json) je odeslána do prostředí. Propojená šablona (linkedTemplate.json) musí být někde bezpečně sdílet. Následující skript PowerShell vytvoří účet Azure Storage, nahraje šablonu do účtu úložiště a pak vygeneruje token SAS pro udělení omezeného přístupu k souboru šablony. Pro zjednodušení tohoto kurzu skript stáhne dokončenou propojenou šablonu z úložiště GitHub. Pokud chcete použít propojené šablony vytvoříte, můžete použít [Cloud shell](https://shell.azure.com) odeslání propojené šablony, a následně upravit skript, který chcete použít vlastní propojenou šablonu.

> [!NOTE]
> Skript omezuje token SAS pro použití v rámci osm hodin. Pokud potřebujete více času k dokončení tohoto kurzu, zvýšit čas vypršení platnosti.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/tutorial-linked-templates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
$fileName = "linkedStorageAccount.json" # A file name used for downloading and uploading the linked template.

# Download the tutorial linked template
Invoke-WebRequest -Uri $linkedTemplateURL -OutFile "$home/$fileName"

# Create a resource group
New-AzResourceGroup -Name $resourceGroupName -Location $location

# Create a storage account
$storageAccount = New-AzStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $storageAccountName `
    -Location $location `
    -SkuName "Standard_LRS"

$context = $storageAccount.Context

# Create a container
New-AzStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

Write-Host "You need the following values later in the tutorial:"
Write-Host "Resource Group Name: $resourceGroupName"
Write-Host "Linked template URI with SAS token: $templateURI"
Write-Host "Press [ENTER] to continue ..."
```

1. Vyberte **vyzkoušet** zeleného tlačítka a otevřete tak podokno Azure cloud shell.
2. Vyberte **kopírování** zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na libovolné místo v podokně prostředí (části navy modrá) a pak vyberte **vložit**.
4. Poznamenejte si tyto dvě hodnoty (název skupiny prostředků a propojenou šablonu identifikátoru URI) na konci podokně prostředí. Tyto hodnoty budete potřebovat v pozdější části kurzu.
5. Vyberte **ukončit detailní režim** zavřete podokno prostředí.

V praxi vygenerování tokenu SAS při nasazování hlavní šablonu a poskytnout menší okno kvůli většímu zabezpečení vypršení platnosti tokenu SAS. Další informace najdete v tématu [token SAS zadat během nasazování](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Vyvolání propojené šablony

Hlavní šablona se nazývá azuredeploy.json.

1. Otevřete **azuredeploy. JSON** v Visual Studio Code, pokud není otevřený.
1. Nahraďte definici prostředků účtu úložiště následujícím fragmentem kódu JSON:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":""
          },
          "parameters": {
              "storageAccountName":{"value": "[variables('storageAccountName')]"},
              "location":{"value": "[parameters('location')]"}
          }
      }
    },
    ```

    Věnujte pozornost těmto podrobnostem:

    * Prostředek `Microsoft.Resources/deployments` v hlavní šabloně je použit k propojení s ostatními šablonami.
    * Prostředek `deployments` se nazývá `linkedTemplate`. Tento název se používá pro [konfiguraci závislostí](#configure-dependency).
    * Můžete použít pouze [přírůstkový](./deployment-modes.md) režim nasazení při vyvolání propojených šablon.
    * `templateLink/uri` obsahuje propojenou šablonu identifikátoru URI. Aktualizujte hodnotu k identifikátoru URI při odeslání propojené šablony (jeden s tokenem SAS).
    * K předání hodnot z hlavní šablony do propojené šablony použijte `parameters`.
1. Ujistěte se, že jste aktualizovali hodnotu `uri` prvku na hodnotu se zobrazila při odeslání propojené šablony (jeden s tokenem SAS). V praxi budete chtít zadat identifikátor URI s parametrem.
1. Uložte upravený šablonu

## <a name="configure-dependency"></a>Konfigurace závislostí

Pamatujete z [kurz: vytvoření Azure Resource Manageru šablony s závislé prostředky](./template-tutorial-create-templates-with-dependent-resources.md), prostředku virtuálního počítače závisí na účet úložiště:

![Diagram závislostí šablon Azure Resource Manageru](./media/template-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Protože účet úložiště je teď definovaný v propojené šabloně, je nutné aktualizovat následující dva prvky prostředku `Microsoft.Compute/virtualMachines`.

* Změňte konfiguraci prvku `dependsOn`. Definice účtu úložiště je přesunuta na propojenou šablonu.
* Změňte konfiguraci prvku `properties/diagnosticsProfile/bootDiagnostics/storageUri`. V části [Vytvoření propojené šablony](#create-the-linked-template), jste přidali výstupní hodnotu:

    ```json
    "outputs": {
        "storageUri": {
            "type": "string",
            "value": "[reference(parameters('storageAccountName')).primaryEndpoints.blob]"
            }
    }
    ```

    Tato hodnota je požadovaná hlavní šablonou.

1. Ve Visual Studio Code otevřete soubor azuredeploy.json, pokud ještě není otevřený.
2. Rozbalte definice prostředků virtuálního počítače, aktualizujte **dependsOn**, jak je znázorněno na následujícím snímku obrazovky:

    ![Konfigurace závislostí propojených šablon Azure Resource Manageru](./media/template-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* je název prostředku nasazení.
3. Aktualizovat **vlastnosti/diagnosticsProfile/bootDiagnostics/storageUri** jak je znázorněno na předchozím snímku obrazovky.
4. Revidovaná šablonu uložte.

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Použijte stejný název skupiny prostředků jako účet úložiště pro ukládání propojené šablony. To usnadňuje vyčištění prostředků v další části. Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="additional-practice"></a>Další postup

Ke zlepšení projekt, proveďte následující další změny do dokončení projektu:

1. Hlavní šablony (azuredeploy.json) upravte, aby přebírá hodnotu identifikátoru URI propojenou šablonu prostřednictvím parametru.
2. Místo aby generovala tokenu SAS, když nahrajete propojené šablony, vygenerujte token při nasazení hlavní šablony. Další informace najdete v tématu [token SAS zadat během nasazování](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste modulární šablonu do hlavní šablony a propojenou šablonu. Další informace o použití rozšíření virtuálních počítačů provést úkoly po nasazení, najdete v tématech:

> [!div class="nextstepaction"]
> [Nasazování rozšíření virtuálních počítačů](./template-tutorial-deploy-vm-extensions.md)
