---
title: Přesun prostředků Azure do jiné skupiny prostředků | Dokumentace Microsoftu
description: Další informace o přesunutí prostředků Azure z jedné skupiny prostředků do jiné skupiny prostředků nebo z jednoho předplatného do jiného předplatného.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 12/19/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 0ae29146b1b44f3017d37b3cebf7ec4cf39115d0
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/21/2018
ms.locfileid: "53729710"
---
# <a name="tutorial-move-azure-resources-to-another-resource-group-or-subscription"></a>Kurz: Přesunutí prostředků Azure do jiné skupiny prostředků nebo předplatného

Další informace o přesunutí prostředků Azure z jedné skupiny prostředků do jiné skupiny prostředků. Prostředky Azure z jednoho předplatného Azure můžete také přesunout do jiného předplatného Azure. V tomto kurzu pomocí šablony resource Manageru k nasazení dvě skupiny prostředků a jeden účet úložiště. Potom přesuňte účet úložiště z jedné skupiny prostředků do jiné.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Připravte prostředky.
> * Ověřte, zda že lze přesunout prostředek.
> * Kontrolní seznam před přesunutím prostředku.
> * Ověření operace přesunu.
> * Přesun prostředku.
> * Vyčištění prostředků.

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prepare-the-resources"></a>Příprava prostředků

Byla vytvořena a umístěna do šablony [sdíleného účtu úložiště](https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json). Šablona definuje dvě skupiny prostředků a jeden účet úložiště. Při nasazení šablony, budete muset zadat název projektu. Název projektu slouží ke generování názvy prostředků jedinečné.  Následující kód JSON je extrahován ze šablony:

```json
"variables": {
  "resourceGroupSource": {
    "name": "[concat(parameters('projectName'), 'rg1')]",
    "location": "eastus"
  },
  "resourceGroupDestination": {
    "name": "[concat(parameters('projectName'), 'rg2')]",
    "location": "westus"      
  },
  "storageAccount": {
    "name": "[concat(parameters('projectName'), 'store')]",
    "location": "eastus"
  }
},
```

Všimněte si, že umístění definovaných v kódu json, skupiny dvou zdrojů, které jsou umístěné v USA – východ a USA – západ. Účet úložiště se nachází v oblasti východní USA. Když přesouváte prostředek do jiné skupiny prostředků s jinam, nezmění přesunutím umístění prostředku.

Vyberte **vyzkoušet** otevřete Cloud shell, a potom spusťte skript prostředí PowerShell ve službě Cloud shell:

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
New-AzureRmDeployment `
    -Name $projectname `
    -Location "centralus" `
    -TemplateUri "https://armtutorials.blob.core.windows.net/moveresources/azuredeploy.json" `
    -projectName $projectName
```

Počkejte, dokud úspěšném dokončení skriptu a potom ho otevřete [webu Azure portal](https://portal.azure.com)a ověřte, skupiny prostředků a účtu úložiště jsou nasazeny podle očekávání.

> [!NOTE]
> Vzhledem k tomu, že šablona definuje dvě skupiny prostředků, toto nasazení se považuje nasazení na úrovni předplatného. Nasazení šablony portálu nepodporuje nasazení na úrovni předplatného. V tomto kurzu se tedy používá prostředí Azure PowerShell. Azure CLI podporuje také nasazení na úrovni předplatného. Zobrazit [vytvoření skupiny prostředků a prostředků pro předplatné Azure](./deploy-to-subscription.md).

## <a name="verify-the-resource-can-be-moved"></a>Ověřte, zda že lze přesunout prostředek

Ne všechny prostředky je možné přesunout. V tomto kurzu používá prostředek je v účtu úložiště, který je možné přesunout. Pokud chcete ověřit, zda lze přesunout prostředek, naleznete v tématu [služby, které je možné přesunout](./resource-group-move-resources.md#services-that-can-be-moved).

## <a name="checklist-before-moving-resources"></a>Kontrolní seznam před přesunutím prostředků

Tento krok je volitelný pro účely tohoto kurzu, jak to dělá.

Existuje několik důležitých kroků provedete před přesunutím prostředku je. Zobrazit [kontrolní seznam před přesunutím prostředků](./resource-group-move-resources.md#checklist-before-moving-resources).

## <a name="validate-the-move"></a>Ověření přesunutí

Ověření přesunu je volitelný pro účely tohoto kurzu, jak to dělá.

Ověření operace přesunu umožňuje testovat váš scénář přesunu bez skutečně přesouvání prostředků. Pomocí této operace můžete zjistit, pokud bude úspěšné přesunutí. Další informace najdete v tématu [ověřit přesun](./resource-group-move-resources.md#validate-move).

## <a name="move-the-resource"></a>Přesunout prostředek

Účet úložiště se nachází uvnitř zdrojovou skupinu prostředků (rg1), spusťte následující příkaz Powershellu skript přesuňte do prostředku cílová skupina prostředků (rg2). Ujistěte se, že pro použití stejného názvu projektu jako jste použili při nasazení prostředků.

```azurepowershell-interactive
$projectName = Read-Host -prompt "Enter a project name"
$resourceGroupSource = $projectName + "rg1"
$resourceGroupDestination = $projectName + "rg2"
$storageAccountName = $projectName + "store"

$storageAccount = Get-AzureRmResource -ResourceGroupName $resourceGroupSource -ResourceName $storageAccountName
Move-AzureRmResource -DestinationResourceGroupName $resourceGroupDestination -ResourceId $storageAccount.ResourceId
```

Otevřít [webu Azure portal](https://portal.azure.com)ověřte účet úložiště se přesunul do jiné skupiny prostředků a také ověřte umístění účtu úložiště je stále USA – východ.

Při přesouvání prostředků, zdrojová skupina a cílová skupina jsou zamčené během operace. Zápis a odstranění operace jsou blokovány o skupinách prostředků, až do dokončení přechodu. Tento Zámek znamená, že nelze přidat, aktualizovat nebo odstranit prostředky ve skupinách prostředků, ale neznamená, že prostředky jsou zmražená. Například při přesunutí serveru SQL Server a jeho databázi do nové skupiny prostředků, aplikace, která používá databázi prostředí bez výpadků. Můžete nadále číst a zapisovat do databáze.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Do pole **Filtrovat podle názvu** zadejte název skupiny prostředků.
3. Vyberte název zdrojové skupiny prostředků.  
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.
5. Vyberte název cílové skupiny prostředků.  
6. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak přesunout do jiné skupiny prostředků účtu úložiště ze skupiny prostředků. Zatím jste byla zabýváte pomocí jednoho účtu úložiště nebo víc instancí účtu úložiště. V dalším kurzu se zaměříte na vývoj šablony s více prostředky a více typy prostředků. Některé prostředky mají závislé prostředky.

> [!div class="nextstepaction"]
> [Vytvořit závislé prostředky](./resource-manager-tutorial-create-templates-with-dependent-resources.md)
