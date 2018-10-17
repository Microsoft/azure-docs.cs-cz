---
title: Vytvoření šablony Azure Resource Manageru pro nasazení šifrovaného účtu úložiště | Microsoft Docs
description: Vytvořte šablonu pro nasazení šifrovaného účtu úložiště pomocí nástroje Visual Studio Code.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 09/07/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: d48374d7919be3d141ea199e8238a220dbfe0332
ms.sourcegitcommit: 3150596c9d4a53d3650cc9254c107871ae0aab88
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2018
ms.locfileid: "47419521"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Kurz: Vytvoření šablony Azure Resource Manageru pro nasazení šifrovaného účtu úložiště

Zjistěte, kde najít informace potřebné k vytvoření šablony Azure Resource Manageru.

V tomto kurzu vytvoříte účet služby Azure Storage pomocí základní šablony ze sady šablon rychlého startu Azure.  Pomocí referenční dokumentace šablon přizpůsobíte základní šablonu tak, abyste s její pomocí vytvořili šifrovaný účet úložiště.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vysvětlení šablony
> * Úprava šablony
> * Nasazení šablony

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* [Visual Studio Code](https://code.visualstudio.com/).
* Rozšíření Nástroje Resource Manageru. Informace o instalaci najdete v článku [Instalace rozšíření Nástroje Resource Manageru](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="open-a-quickstart-template"></a>Otevření šablony pro rychlý start

Šablona, kterou jsme použili v tomto rychlém startu, se nazývá [Create a standard storage account](https://azure.microsoft.com/resources/templates/101-storage-account-create/) (Vytvoření standardního účtu úložiště). Šablona definuje prostředek účtu služby Azure Storage.

1. V nástroji Visual Studio Code vyberte **File** (Soubor) >**Open File** (Otevřít soubor).
2. Do pole **File name** (Název souboru) vložte následující adresu URL:

    ```url
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```
3. Výběrem **Open** (Otevřít) soubor otevřete.
4. Vyberte **File** (Soubor) >**Save As** (Uložit jako) a soubor uložte jako **azuredeploy.json** na místní počítač.

## <a name="understand-the-format"></a>Vysvětlení formátu

V nástroji VS Code sbalte šablony na kořenovou úroveň. Vidíte nejjednodušší strukturu s následující elementy:

![Nejjednodušší struktura šablony Resource Manageru](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-simplest-structure.png)

* **$schema**: zadejte umístění souboru schématu JSON, který popisuje verzi jazyka šablony.
* **contentVersion**: pro tento element můžete zvolit libovolnou hodnotu, která dokumentuje významné změny v šabloně.
* **parameters**: přizpůsobte nasazení prostředků zadáním hodnot, které se použijí při provádění nasazení.
* **variables**: určete hodnoty sloužící v šabloně jako fragmenty formátu JSON, aby se zjednodušily výrazy jazyka šablony.
* **resources**: určete typy prostředků nasazovaných nebo aktualizovaných ve skupině prostředků.
* **outputs**: uvádí hodnoty vrácené po nasazení.

## <a name="use-parameters-in-template"></a>Použití parametrů v šabloně

Parametry umožňují přizpůsobit nasazení tím, že zadáte hodnoty vhodné pro konkrétní prostředí. Parametry definované v šabloně se používají při nastavování hodnot účtu úložiště.

![Parametr šablony Resource Manageru](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-parameters.png)

V této šabloně se definují dva parametry. Všimněte si, že u položky location.defaultValue se používá funkce šablony:

```json
"defaultValue": "[resourceGroup().location]",
```

Funkce ResourceGroup() vrátí objekt reprezentující aktuální skupinu prostředků. Seznam funkcí šablon najdete v tématu [Funkce šablon Azure Resource Manageru](./resource-group-template-functions.md).

Použití parametrů definovaných v šabloně:

```json
"location": "[parameters('location')]",
"name": "[parameters('storageAccountType')]"
```

## <a name="use-variables-in-template"></a>Použití proměnných v šabloně

Proměnné umožňují vytvářet hodnoty, které se dají používat v celé šabloně. Proměnné pomáhají ke zjednodušení šablon.

![Proměnné šablon Resource Manageru](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-variables.png)

Tato šablona definuje jednu proměnnou *storageAccountName*. V definici se používají dvě funkce šablon:

- **concat()**: provádí zřetězení řetězců. Další informace najdete v popisu funkce [concat](./resource-group-template-functions-string.md#concat).
- **uniqueString()**: vytvoří deterministický řetězec hash na základě hodnot zadaných jako parametry. Každý účet úložiště Azure musí mít na celé platformě Azure jedinečný název. Tato funkce poskytuje jedinečný řetězec. Další řetězcové funkce najdete v článku [Řetězcové funkce](./resource-group-template-functions-string.md).

Použití proměnné definované v šabloně:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Úprava šablony

Cílem tohoto kurzu je definovat šablonu pro vytvoření šifrovaného účtu úložiště.  Ukázková šablona vytvoří pouze základní nešifrovaný účet úložiště. K vyhledání konfigurace šifrování můžete použít odkaz na šablonu účtu Azure Storage.

1. Přejděte na článek o [šablonách Azure](https://docs.microsoft.com/azure/templates/).
2. V nabídce **Filtrovat podle názvu**, zadejte **Účty úložiště**.
3. Vyberte **odkaz / šablonu odkazu / úložiště / úložiště účtů**, jak je znázorněno na následujícím snímku obrazovky:

    ![Odkaz šablony Azure Resource Manageru na účet úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-resources-reference-storage-accounts.png)

    resource-manager-template-resources-reference-storage-accounts
1. Projděte si informace týkající se šifrování.  
1. Do elementu properties v definici prostředku účtu úložiště přidejte následující kód JSON:

    ```json
    "encryption": {
        "keySource": "Microsoft.Storage",
        "services": {
            "blob": {
                "enabled": true
            }
        }
    }
    ```
    Tato část povolí šifrovací funkci služby Blob Storage.

Ve Visual Studio Code upravte šablonu tak, aby výsledný element resources vypadal následovně:

![Šablona Resource Manageru – element resources šifrovaného účtu úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Nasazení šablony

Informace o procesu nasazení najdete v části [Nasazení šablony](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#deploy-the-template) v rychlém startu pro Visual Studio Code.

Následující snímek obrazovky ukazuje příkaz rozhraní příkazového řádku pro zobrazení nově vytvořeného účtu úložiště, který značí povolení šifrování pro úložiště objektů blob.

![Azure Resource Manager – šifrovaný účet úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak přizpůsobit existující šablonu pomocí odkazu na šablonu. Naučte se, jak vytvořit několik instancí účtu úložiště, viz:

> [!div class="nextstepaction"]
> [Vytvoření víc instancí](./resource-manager-tutorial-create-multiple-instances.md)
