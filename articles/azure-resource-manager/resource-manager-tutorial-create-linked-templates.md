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
ms.date: 11/13/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: e72a7b967ef314dcdcfb2773627ce3d020e2fdcb
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51614221"
---
# <a name="tutorial-create-linked-azure-resource-manager-templates"></a>Kurz: Vytvoření propojených šablon Azure Resource Manageru

Zjistěte, jak vytvořit propojené šablony Azure Resource Manageru. Použitím propojených šablon, může mít jednu šablonu vyvolávající jinou šablonu. Je to velmi vhodné pro modulační šablony. V tomto kurzu použijete stejnou šablonu použitou při [kurzu: Vytvoření více instancí prostředků pomocí šablon Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md), které vytváří virtuální počítač, virtuální síť a další závislé prostředky, včetně účtu úložiště. Na propojenou šablonu můžete oddělit prostředek účtu úložiště.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vytvoření propojené šablony
> * Odeslání propojené šablony
> * Propojení propojené šablony
> * Konfigurace závislostí
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/) s [rozšířením Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites)
* Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Tady ukázka generování hesla:

    ```azurecli-interactive
    openssl rand -base64 32
    ```
    Služba Azure Key Vault je určená k ochraně kryptografických klíčů a dalších tajných klíčů. Další informace najdete v [kurzu integrace služby Azure Key Vault v nasazení šablony Resource Manageru](./resource-manager-tutorial-use-key-vault.md). Zároveň doporučujeme heslo každé tři měsíce aktualizovat.

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablony pro rychlý start Azure slouží jako úložiště šablon Resource Manageru. Místo vytvoření šablony úplně od začátku si můžete najít ukázkovou šablonu a přizpůsobit ji. Šablona používaná v tomto kurzu má název [Deploy a simple Windows VM](https://azure.microsoft.com/resources/templates/101-vm-simple-windows/) (Nasazení jednoduchého virtuálního počítače s Windows). Toto je stejná šablona používaná v [kurzu: Vytvoření více instancí prostředků pomocí šablon Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md). Uložte dvě kopie stejné šablony, které budou použity jako:

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

    Než začnete šablonu přizpůsobovat, je vhodné se s ní nejprve trochu seznámit.
5. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte na místní počítač pod názvem **azuredeploy.json**.
6. Zvolte **Soubor**>**Uložit jako** a vytvořte jinou kopii souboru s názvem **linkedTemplate.json**.

## <a name="create-the-linked-template"></a>Vytvoření propojené šablony

Propojená šablona vytvoří účet úložiště. Propojené šablony je téměř stejná jako samostatná šablona, která vytvoří účet úložiště. V tomto kurzu musí propojená šablona předat hodnotu zpět hlavní šabloně. Tato hodnota je definována v prvku `outputs`.

1. Ve Visual Studio Code otevřete soubor linkedTemplate.json, pokud ještě není otevřený.
2. Proveďte následující změny:

    * Odstraňte všechny prostředky s výjimkou účtu úložiště. Odeberete celkem čtyři prostředky.
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
    * Odeberte parametry, které se nikdy nepoužívají. Tyto parametry mají pod sebou zelené vlnkování. Můžete mít pouze jeden parametr vlevo s názvem **umístění**.
    * Odeberte **proměnný** prvek. Nejsou v tomto kurzu potřeba.
    * Přidejte parametr s názvem **storageAccountName**. Název účtu úložiště je jako parametr předán z hlavní šablony do propojené šablony.

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

Šablony musí být přístupné z místa, odkud spouštíte nasazení. Toto umístění může být účet úložiště Azure, Github nebo Dropbox. Pokud vaše šablony obsahují citlivé informace, ujistěte se, že je přístup k nim chráněn. V tomto kurzu použijete metodu nasazení prostředí cloudu, jako jste ho použili v [kurzu: Vytvoření více instancí prostředků pomocí šablon Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md). Hlavní šablona (azuredeploy.json) je odeslána do prostředí. Propojená šablona (linkedTemplate.json) musí být někde sdílena.  Ke snížení úloh v tomto kurzu byly propojené šablony definované v předchozí části nahrány do [účtu úložiště Azure](https://armtutorials.blob.core.windows.net/linkedtemplates/linkedStorageAccount.json).

## <a name="call-the-linked-template"></a>Vyvolání propojené šablony

Hlavní šablona se nazývá azuredeploy.json.

1. Ve Visual Studio Code otevřete soubor azuredeploy.json, pokud ještě není otevřený.
2. Ze šablony odstraňte definici prostředků účtu úložiště.
3. Přidejte následující fragment kódu json na místo, kde jste měli definici účtu úložiště:

    ```json
    {
      "apiVersion": "2017-05-10",
      "name": "linkedTemplate",
      "type": "Microsoft.Resources/deployments",
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
    * `templateLink/uri` obsahuje propojenou šablonu identifikátoru URI. Propojená šablona byla odeslána do účtu sdíleného úložiště. Identifikátor URI můžete aktualizovat, pokud odešlete šablonu jiného umístění na internetu.
    * K předání hodnot z hlavní šablony do propojené šablony použijte `parameters`.
4. Uložte změny.

## <a name="configure-dependency"></a>Konfigurace závislostí

Připomenutí z [kurzu: vytvoření více instancí prostředku pomocí šablony Resource Manageru](./resource-manager-tutorial-create-multiple-instances.md), prostředek virtuálního počítače závisí na účtu úložiště:

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
3. Aktualizujte **properties/diagnosticsProfile/bootDiagnostics/storageUri** jak je znázorněno na předchozím snímku obrazovky.

Další informace najdete v tématu [Použití propojené a vnořené šablony při nasazování prostředků Azure.](./resource-group-linked-templates.md)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-tutorial-create-multiple-instances.md#deploy-the-template). Pro zlepšení zabezpečení použijte pro účet správce virtuálního počítače vygenerované heslo. Viz [Požadavky](#prerequisites).

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste vytvořili a nasadili propojenou šablonu. Informace o použití rozšíření virtuálních počítačů k provádění úloh po nasazení najdete tady:

> [!div class="nextstepaction"]
> [Nasazování rozšíření virtuálních počítačů](./deployment-manager-tutorial.md)
