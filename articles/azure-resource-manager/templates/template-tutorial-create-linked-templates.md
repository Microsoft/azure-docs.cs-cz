---
title: Vytvoření propojených šablon
description: Zjistěte, jak vytvořit propojené šablony Azure Resource Manageru pro vytvoření virtuálního počítače.
author: mumian
ms.date: 12/03/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e1cce566fb7aab286c57f32d9348e51dd0a7c1ee
ms.sourcegitcommit: 253d4c7ab41e4eb11cd9995190cd5536fcec5a3c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2020
ms.locfileid: "80239320"
---
# <a name="tutorial-create-linked-arm-templates"></a>Kurz: Vytvoření propojených šablon ARM

Zjistěte, jak vytvořit propojené šablony Azure Resource Manager (ARM). Použitím propojených šablon, může mít jednu šablonu vyvolávající jinou šablonu. Je to velmi vhodné pro modulační šablony. V tomto kurzu použijete stejnou šablonu použitou v [kurzu: Vytvořte šablony ARM se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md), které vytvoří virtuální počítač, virtuální síť a další závislý prostředek včetně účtu úložiště. Vytvoření prostředku účtu úložiště oddělíte od propojené šablony.

Volání propojené šablony je jako volání funkce.  Dozvíte se také, jak předat hodnoty parametrů do propojené šablony a jak získat "vrácené hodnoty" z propojené šablony.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vytvoření propojené šablony
> * Odeslání propojené šablony
> * Propojení propojené šablony
> * Konfigurace závislostí
> * Nasazení šablony
> * Další postupy

Další informace najdete v tématu [Použití propojených a vnořených šablon při nasazování prostředků Azure](./linked-templates.md).

Pokud nemáte předplatné Azure, [vytvořte si bezplatný účet,](https://azure.microsoft.com/free/) než začnete.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Visual Studio Kód s rozšířením Nástroje Správce prostředků. Viz [Použití kódu Visual Studia k vytvoření šablon ARM](use-vs-code-to-create-template.md).
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```console
    openssl rand -base64 32
    ```

    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace naleznete [v tématu: Integrace trezoru klíčů Azure v nasazení šablony ARM](./template-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Azure QuickStart Templates je úložiště pro šablony ARM. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows). Jedná se o stejnou šablonu použitou v [kurzu: Vytvoření šablon ARM se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md). Uložte dvě kopie stejné šablony, které budou použity jako:

* **Hlavní šablona**: vytvoří se všechny prostředky s výjimkou účtu úložiště.
* **Propojená šablona**: vytvoří účet úložiště.

1. V kódu sady Visual Studio vyberte **Soubor**>**otevřít soubor**.
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

     Je užitečné získat některé základní znalosti schématu šablony před přizpůsobením šablony.
1. Vyberte **Soubor**>**Uložit jako,** chcete-li uložit kopii souboru do místního počítače s názvem **azuredeploy.json**.
1. Vyberte **Soubor**>**Uložit jako,** chcete-li vytvořit další kopii souboru s názvem **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Vytvoření propojené šablony

Propojená šablona vytvoří účet úložiště. Propojenou šablonu lze použít jako samostatnou šablonu k vytvoření účtu úložiště. V tomto kurzu propojená šablona přebírá dva parametry a předá hodnotu zpět do hlavní šablony. Tato hodnota "return" je `outputs` definována v prvku.

1. Pokud soubor není otevřen, otevřete soubor **LinkedTemplate.json** v kódu sady Visual Studio.
1. Proveďte následující změny:

    * Odeberte všechny jiné parametry než **umístění**.
    * Přidejte parametr s názvem **storageAccountName**.

      ```json
      "storageAccountName":{
        "type": "string",
        "metadata": {
            "description": "Azure Storage account name."
        }
      },
      ```

      Název a umístění účtu úložiště jsou předány z hlavní šablony do propojené šablony jako parametry.

    * Odeberte element **proměnných** a všechny definice proměnných.
    * Odeberte všechny prostředky kromě účtu úložiště. Odeberete celkem čtyři prostředky.
    * Aktualizujte hodnotu prvku **name** prostředku účtu úložiště na:

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
              "apiVersion": "2018-11-01",
              "name": "[parameters('storageAccountName')]",
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

1. Uložte změny.

## <a name="upload-the-linked-template"></a>Odeslání propojené šablony

Hlavní šablona a propojená šablona musí být přístupné z místa, kde spuštění nasazení. V tomto kurzu použijete metodu nasazení prostředí Cloud, jak jste použili v [kurzu: Vytvoření arm šablony s závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md). Hlavní šablona (azuredeploy.json) je odeslána do prostředí. Propojená šablona (linkedTemplate.json) musí být sdílena někde bezpečně. Následující skript Prostředí PowerShell vytvoří účet Azure Storage, nahraje šablonu do účtu úložiště a pak vygeneruje token SAS, který udělí omezený přístup k souboru šablony. Pro zjednodušení kurzu skript stáhne dokončenou propojenou šablonu z úložiště GitHub. Pokud chcete použít propojenou šablonu, kterou jste vytvořili, můžete použít [prostředí Cloud](https://shell.azure.com) k nahrání propojené šablony a pak upravit skript tak, aby používal vlastní propojenou šablonu.

> [!NOTE]
> Skript omezuje token SAS, který má být použit do osmi hodin. Pokud potřebujete více času k dokončení tohoto kurzu, zvyšte dobu vypršení platnosti.

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

1. Kliknutím zeleného tlačítka **Try It** otevřete podokno prostředí cloudazure.
2. Vyberte **Kopírovat,** chcete-li zkopírovat skript PowerShellu.
3. Klepněte pravým tlačítkem myši na libovolné místo v podokně skořepiny (tmavě modrá část) a pak vyberte **Vložit**.
4. Poznamenejte si dvě hodnoty (název skupiny prostředků a identifikátor URI propojené šablony) na konci podokna prostředí. Tyto hodnoty budete potřebovat v pozdější části kurzu.
5. Vyberte **Ukončit zaostřovací režim,** chcete-li podokno prostředí zavřít.

V praxi vygenerujete token SAS při nasazení hlavní šablony a přidělit vypršení platnosti tokenu SAS menší okno, aby bylo bezpečnější. Další informace naleznete [v tématu Provide SAS token během nasazení](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="call-the-linked-template"></a>Vyvolání propojené šablony

Hlavní šablona se nazývá azuredeploy.json.

1. Otevřete **azuredeploy.json** v kódu Visual Studio, pokud není otevřen.
1. Nahraďte definici prostředku účtu úložiště následujícím fragmentem json:

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
    * `templateLink/uri` obsahuje propojenou šablonu identifikátoru URI. Aktualizujte hodnotu identifikátoru URI, který získáte při nahrávání propojené šablony (té s tokenem SAS).
    * K předání hodnot z hlavní šablony do propojené šablony použijte `parameters`.
1. Ujistěte se, že jste `uri` aktualizovali hodnotu prvku na hodnotu, kterou jste získali při nahrávání propojené šablony (té s tokenem SAS). V praxi chcete zadat identifikátor URI s parametrem.
1. Uložení revidované šablony

## <a name="configure-dependency"></a>Konfigurace závislostí

Odvolání z [kurzu: Vytvořte šablony ARM se závislými prostředky](./template-tutorial-create-templates-with-dependent-resources.md), prostředek virtuálního počítače závisí na účtu úložiště:

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
3. Aktualizace **vlastností/diagnostikyProfil/bootDiagnostics/storageUri,** jak je znázorněno na předchozím snímku obrazovky.
4. Uložte revidovanou šablonu.

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./template-tutorial-create-templates-with-dependent-resources.md#deploy-the-template). Pro ukládání propojené šablony použijte stejný název skupiny prostředků jako účet úložiště. Usnadňuje vyčištění prostředků v další části. Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure vyberte **skupinu prostředků** z levé nabídky.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků.**

## <a name="additional-practice"></a>Další praxe

Chcete-li projekt vylepšit, proveďte následující další změny dokončeného projektu:

1. Upravte hlavní šablonu (azuredeploy.json) tak, aby přebírá hodnotu URI propojené šablony prostřednictvím parametru.
2. Namísto generování tokenu SAS při nahrávání propojené šablony vygenerujte token při nasazení hlavní šablony. Další informace naleznete [v tématu Provide SAS token během nasazení](./secure-template-with-sas-token.md#provide-sas-token-during-deployment).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste modularizovali šablonu do hlavní šablony a propojené šablony. Informace o tom, jak používat rozšíření virtuálních strojů k provádění úloh po nasazení, najdete v tématu:

> [!div class="nextstepaction"]
> [Nasazování rozšíření virtuálních počítačů](./template-tutorial-deploy-vm-extensions.md)
