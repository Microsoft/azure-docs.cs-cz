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
ms.date: 07/20/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 7c78636a210ae90c5bfe1d0bfd35e4e05633f5cd
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188195"
---
# <a name="tutorial-create-an-azure-resource-manager-template-for-deploying-an-encrypted-storage-account"></a>Kurz: Vytvoření šablony Azure Resource Manageru pro nasazení šifrovaného účtu úložiště

Zjistěte, kde najít informace potřebné k vytvoření šablony Azure Resource Manageru.

V tomto kurzu vytvoříte účet služby Azure Storage pomocí základní šablony ze sady šablon rychlého startu Azure.  Pomocí referenční dokumentace šablon přizpůsobíte základní šablonu tak, abyste s její pomocí vytvořili šifrovaný účet úložiště.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Otevření šablony pro rychlý start
> * Vysvětlení formátu šablony
> * Použití parametrů v šabloně
> * Použití proměnných v šabloně
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
- **uniqueString()**: vytvoří deterministický řetězec hash na základě hodnot zadaných jako parametry. Každý účet Azure Storage musí mít na celé platformě Azure jedinečný název. Tato funkce poskytuje jedinečný řetězec. Další řetězcové funkce najdete v článku [Řetězcové funkce](./resource-group-template-functions-string.md).

Použití proměnné definované v šabloně:

```json
"name": "[variables('storageAccountName')]"
```

## <a name="edit-the-template"></a>Úprava šablony

K vyhledání konfigurace šifrování účtu úložiště můžete použít odkaz na šablonu účtu Azure Storage.

1. Přejděte na článek o [šablonách Azure](https://docs.microsoft.com/azure/templates/).
2. V obsahu vlevo vyberte **Reference (Odkazy)**->**Storage (Úložiště)**->**Storage Accounts (Účty úložiště)**. Na této stránce najdete informace o tom, jak definovat údaje účtu úložiště.
3. Projděte si informace týkající se šifrování.  
4. Do elementu properties v definici prostředku účtu úložiště přidejte následující kód JSON:

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

Element resources nakonec vypadá takto:

![Šablona Resource Manageru – element resources šifrovaného účtu úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-resources.png)

## <a name="deploy-the-template"></a>Nasazení šablony

Šablony můžete nasadit mnoha způsoby.  V tomto kurzu použijete Cloud Shell z webu Azure Portal. Cloud Shell podporuje jak Azure CLI, tak i Azure PowerShell. V pokynech v tomto článku se používá CLI.

1. Přihlaste se k portálu [Azure Portal](https://portal.azure.com).
2. V pravém horním rohu vyberte **Cloud Shell**, jak je znázorněno na tomto obrázku:

    ![Cloud Shell na portálu Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell.png)

3. Vyberte šipku dolů a potom zvolte **Bash**, pokud ještě tato možnost není vybraná. V tomto kurzu použijete Azure CLI.

    ![CLI v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-choose-cli.png)
4. Vyberte **Restartovat** a prostředí restartujte.
5. Vyberte **Nahrát nebo stáhnout soubory** a potom vyberte **Nahrát**.

    ![Nahrání souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-upload-file.png)
6. Vyberte soubor, který jste si v rámci tohoto kurzu uložili. Výchozí název je **azuredeploy.json**.
7. V Cloud Shellu spusťte příkaz **ls** a ověřte, že se soubor nahrál úspěšně. Obsah šablony můžete ověřit také pomocí příkazu **cat**.

    ![Zobrazení souboru v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-list-file.png)
8. V Cloud Shellu spusťte následující příkazy:

    ```cli
    az group create --name <ResourceGroupName> --location <AzureLocation>

    az group deployment create --name <DeploymentName> --resource-group <ResourceGroupName> --template-file azuredeploy.json
    ```
    Tady je snímek obrazovky s ukázkovým nasazením:

    ![Šablona nasazení v Cloud Shellu na portálu Azure Portal](./media/resource-manager-tutorial-create-encrypted-storage-accounts/azure-portal-cloud-shell-deploy-template.png)

    Na snímku obrazovky jsou použity tyto hodnoty:

    * **&lt;ResourceGroupName>**: myresourcegroup0719. Tento parametr je použit na dvou místech.  Nezapomeňte použít stejnou hodnotu.
    * **&lt;AzureLocation>**: eastus2
    * **&lt;DeployName>**: mydeployment0719
    * **&lt;TemplateFile>**: azuredeploy.json

    Ve výstupu na snímku obrazovky má účet úložiště název *fhqbfslikdqdsstandardsa*. 

9. Spuštěním následujícího powershellového příkazu zobrazíte nově vytvořený účet úložiště:

    ```cli
    az storage account show --resource-group <ResourceGroupName> --name <StorageAccountName>
    ```

    Zobrazí se výstup podobný následujícímu snímku obrazovky, který znamená, že se pro úložiště objektů blob povolilo šifrování.

    ![Šablona Resource Manageru – šifrovaný účet úložiště](./media/resource-manager-tutorial-create-encrypted-storage-accounts/resource-manager-template-encrypted-storage-account.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název skupiny prostředků.  Ve skupině prostředků uvidíte celkem šest prostředků.
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak přizpůsobit existující šablonu pomocí odkazu na šablonu. Šablona použitá v tomto kurzu obsahuje jenom jeden prostředek Azure.  V dalším kurzu se zaměříte na vývoj šablony s více prostředky.  Některé prostředky mají závislé prostředky.

> [!div class="nextstepaction"]
> [Vytvoření několika prostředků](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
