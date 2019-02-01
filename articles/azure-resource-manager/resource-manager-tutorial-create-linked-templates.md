---
title: Vytvoření propojených šablon Azure Resource Manageru | Microsoft Docs
description: Zjistěte, jak vytvořit propojené šablony Azure Resource Manageru pro vytvoření virtuálního počítače.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 01/16/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5c193ce7966cdc303004c4b75871877074bf4711
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55494027"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Kurz: Vytvoření propojených šablon Azure Resource Manageru

Zjistěte, jak vytvořit propojené šablony Azure Resource Manageru. Použitím propojených šablon, může mít jednu šablonu vyvolávající jinou šablonu. Je to velmi vhodné pro modulační šablony. V tomto kurzu použijete stejné šabloně použité při [kurzu: Vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md), která vytvoří virtuální počítač, virtuální sítě a dalších závislých prostředků včetně účtu úložiště. Můžete oddělit vytvoření prostředků účtu úložiště na propojenou šablonu.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vytvoření propojené šablony
> * Odeslání propojené šablony
> * Propojení propojené šablony
> * Konfigurace závislostí
> * Nasazení šablony
> * Další postupy

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v tématu [kurzu: Integrace Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows). Toto je stejné šabloně použité při [kurzu: Vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Uložte dvě kopie stejné šablony, které budou použity jako:

* **Hlavní šablona**: vytvoří se všechny prostředky s výjimkou účtu úložiště.
* **Propojená šablona**: vytvoří účet úložiště.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-vm-simple-windows/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Šablona definuje pět prostředků:

    * `Microsoft.Storage/storageAccounts`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/Microsoft.Storage/storageAccounts). 
    * `Microsoft.Network/publicIPAddresses`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/publicipaddresses). 
    * `Microsoft.Network/virtualNetworks`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/virtualnetworks). 
    * `Microsoft.Network/networkInterfaces`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.network/networkinterfaces). 
    * `Microsoft.Compute/virtualMachines`. Viz [referenční informace k šablonám](https://docs.microsoft.com/azure/templates/microsoft.compute/virtualmachines).

    Je užitečné, chcete-li získat některé základní znalosti o šabloně před přizpůsobení šablony.
5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.
6. Zvolte **Soubor**>**Uložit jako** a vytvořte jinou kopii souboru s názvem **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Vytvoření propojené šablony

Propojená šablona vytvoří účet úložiště. Propojené šablony je téměř stejná jako samostatná šablona, která vytvoří účet úložiště. V tomto kurzu musí propojená šablona předat hodnotu zpět hlavní šabloně. Tato hodnota je definována v prvku `outputs`.

1. Pokud soubor není otevřen, otevřete linkedTemplate.json ve Visual Studio Code.
2. Proveďte následující změny:

    * Odstraňte všechny prostředky s výjimkou účtu úložiště. Odeberete celkem čtyři prostředky.
    * Aktualizujte hodnotu **název** element prostředku účtu úložiště:

        ```json
          "name": "[parameters('storageAccountName')]",
        ```
    * Odeberte **proměnné** element a všechny definice proměnných.
    * Odebrat všechny parametry s výjimkou **umístění**.
    * Přidejte parametr s názvem **storageAccountName**. Název účtu úložiště je jako parametr předán z hlavní šablony do propojené šablony.

        ```json
        "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
        },
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
          "storageAccountName":{
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
            "apiVersion": "2016-01-01",
            "location": "[parameters('location')]",
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
3. Uložte změny.

## <a name="upload-the-linked-template"></a>Odeslání propojené šablony

Hlavní šablony a propojené šablony musí být přístupné ze kterého spouštíte nasazení. V tomto kurzu použijete metody nasazení Cloud shell jako jste použili v [kurzu: Vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md). Hlavní šablona (azuredeploy.json) je odeslána do prostředí. Propojená šablona (linkedTemplate.json) musí být někde bezpečně sdílet. Následující skript Powershellu vytvoří účet služby Azure Storage, nahraje šablona do účtu úložiště a poté vygeneruje token SAS udělit omezený přístup k souboru šablony. Pro zjednodušení tento kurz, stáhne skript dokončené propojené šablony ze sdíleného umístění. Pokud chcete použít propojené šablony vytvoříte, můžete použít [Cloud shell](https://shell.azure.com) odeslání propojené šablony, a následně upravit skript, který chcete použít vlastní propojenou šablonu.

> [!NOTE]
> Skript omezuje token SAS pro použití v rámci osm hodin. Pokud potřebujete více času k dokončení tohoto kurzu, zvýšit čas vypršení platnosti.

```azurepowershell-interactive
$projectNamePrefix = Read-Host -Prompt "Enter a project name:"   # This name is used to generate names for Azure resources, such as storage account name.
$location = Read-Host -Prompt "Enter a location (i.e. centralus)"

$resourceGroupName = $projectNamePrefix + "rg"
$storageAccountName = $projectNamePrefix + "store"
$containerName = "linkedtemplates" # The name of the Blob container to be created.

$linkedTemplateURL = "https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json" # A completed linked template used in this tutorial.
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
New-AzureStorageContainer -Name $containerName -Context $context

# Upload the linked template
Set-AzureStorageBlobContent `
    -Container $containerName `
    -File "$home/$fileName" `
    -Blob $fileName `
    -Context $context

# Generate a SAS token
$templateURI = New-AzureStorageBlobSASToken `
    -Context $context `
    -Container $containerName `
    -Blob $fileName `
    -Permission r `
    -ExpiryTime (Get-Date).AddHours(8.0) `
    -FullUri

echo "You need the following values later in the tutorial:"
echo "Resource Group Name: $resourceGroupName"
echo "Linked template URI with SAS token: $templateURI"
```

1. Vyberte **vyzkoušet** zeleného tlačítka a otevřete tak podokno Azure cloud shell.
2. Vyberte **kopírování** zkopírujte skript prostředí PowerShell.
3. Klikněte pravým tlačítkem na libovolné místo v podokně prostředí (části navy modrá) a pak vyberte **vložit**.
4. Poznamenejte si tyto dvě hodnoty (název skupiny prostředků a propojenou šablonu identifikátoru URI) na konci podokně prostředí. Tyto hodnoty budete potřebovat v pozdější části kurzu.
5. Vyberte **ukončit detailní režim** zavřete podokno prostředí.

V praxi vygenerování tokenu SAS při nasazování hlavní šablonu a poskytnout menší okno kvůli většímu zabezpečení vypršení platnosti tokenu SAS. Další informace najdete v tématu [token SAS zadat během nasazování](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Vyvolání propojené šablony

Hlavní šablona se nazývá azuredeploy.json.

1. Ve Visual Studio Code otevřete soubor azuredeploy.json, pokud ještě není otevřený.
2. Odstraňte definici prostředků účtu úložiště ze šablony:

    ```json
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[parameters('location')]",
      "apiVersion": "2018-07-01",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "Storage",
      "properties": {}
    },
    ```
3. Přidejte následující fragment kódu json na místo, kde jste měli definici účtu úložiště:

    ```json
    {
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2018-05-01",
      "properties": {
          "mode": "Incremental",
          "templateLink": {
              "uri":"https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json"
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
4. Ujistěte se, že jste aktualizovali hodnotu `uri` prvku na hodnotu se zobrazila při odeslání propojené šablony (jeden s tokenem SAS). V praxi budete chtít zadat identifikátor URI s parametrem.
5. Uložte upravený šablonu

## <a name="configure-dependency"></a>Konfigurace závislostí

Pamatujete z [kurzu: Vytváření šablon Azure Resource Manageru s závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md), prostředku virtuálního počítače závisí na účet úložiště:

![Diagram závislostí šablon Azure Resource Manageru](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-visual-studio-code-dependency-diagram.png)

Protože účet úložiště je teď definovaný v propojené šabloně, je nutné aktualizovat následující dva prvky prostředku `Microsoft.Compute/virtualMachines`.

* Změňte konfiguraci prvku `dependOn`. Definice účtu úložiště je přesunuta na propojenou šablonu.
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

    ![Konfigurace závislostí propojených šablon Azure Resource Manageru ](./media/resource-manager-tutorial-create-linked-templates/resource-manager-template-linked-templates-configure-dependency.png)

    *linkedTemplate* je název prostředku nasazení.  
3. Aktualizovat **vlastnosti/diagnosticsProfile/bootDiagnostics/storageUri** jak je znázorněno na předchozím snímku obrazovky.
4. Revidovaná šablonu uložte.

Další informace najdete v tématu [Použití propojené a vnořené šablony při nasazování prostředků Azure.](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Použijte stejný název skupiny prostředků jako účet úložiště pro ukládání propojené šablony. To usnadňuje vyčištění prostředků v další části. Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="additional-practice"></a>Další postup

Ke zlepšení projekt, proveďte následující další změny do dokončení projektu:

1. Hlavní šablony (azuredeploy.json) upravte, aby přebírá hodnotu identifikátoru URI propojenou šablonu prostřednictvím parametru.
2. Místo aby generovala tokenu SAS, když nahrajete propojené šablony, vygenerujte token při nasazení hlavní šablony. Další informace najdete v tématu [token SAS zadat během nasazování](./resource-manager-powershell-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Další postup

V tomto kurzu jste modulární šablonu do hlavní šablony a propojenou šablonu. Další informace o použití rozšíření virtuálních počítačů provést úkoly po nasazení, najdete v tématech:

> [!div class="nextstepaction"]
> [Nasazování rozšíření virtuálních počítačů](./deployment-manager-tutorial.md)
