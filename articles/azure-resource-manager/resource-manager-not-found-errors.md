---
title: Prostředek Azure nenalezení | Dokumentace Microsoftu
description: Popisuje, jak vyřešit chyby, když prostředek se nenašel.
services: azure-resource-manager
documentationcenter: ''
author: tfitzmac
manager: timlt
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 06/06/2018
ms.author: tomfitz
ms.openlocfilehash: 176de6f19274dfd8a6cf0335bb4cf16a8baa874b
ms.sourcegitcommit: 9819e9782be4a943534829d5b77cf60dea4290a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2018
ms.locfileid: "39525340"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Vyřešte chyby nenalezení pro prostředky Azure

Tento článek popisuje chyby můžete setkat, když se nenašel prostředek během nasazení.

## <a name="symptom"></a>Příznak

Vaše šablona obsahuje název prostředku, který nelze přeložit, obdržíte chybu podobnou této:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Pokud používáte [odkaz](resource-group-template-functions-resource.md#reference) nebo [klíče Listkey](resource-group-template-functions-resource.md#listkeys) funkce, s prostředkem, který nelze přeložit, zobrazí následující chybová zpráva:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Příčina

Resource Manager je potřeba načíst vlastnosti pro prostředek, ale nemůže identifikovat prostředků ve vašem předplatném.

## <a name="solution-1---set-dependencies"></a>Řešení 1 – nastavte závislosti

Pokud se snažíte nasadit chybí prostředek v šabloně, zkontrolujte, jestli je potřeba přidat závislost. Nasazení Resource Manageru optimalizuje tak, že vytvoříte prostředky paralelně, pokud je to možné. Pokud po jiný prostředek musí být nasazený jeden prostředek, budete muset použít **dependsOn** element ve vaší šabloně. Například při nasazení webové aplikace, musí existovat plán služby App Service. Pokud jste nezadali, webové aplikace závisí na plán služby App Service, správce prostředků vytvoří oba prostředky ve stejnou dobu. Dojde k chybě oznamující, že služby App Service se nenašel prostředek plán, protože nebude při pokusu o nastavení vlastnosti ve webové aplikaci ještě existovat. K této chybě zabráníte tak, že nastavíte závislost ve webové aplikaci.

```json
{
  "apiVersion": "2015-08-01",
  "type": "Microsoft.Web/sites",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Ale chcete se vyhnout nastavení závislostí, které nejsou potřebné. Až budete mít zbytečné závislosti, prodloužit během nasazení tím, že prostředky, které nejsou na sobě navzájem závislé z nasazení paralelně. Kromě toho můžete vytvořit cyklické závislosti, které blokují nasazení. [Odkaz](resource-group-template-functions-resource.md#reference) funkce a [seznamu *](resource-group-template-functions-resource.md#list) funkce vytvoří implicitní závislostí v odkazovaném prostředku při tohoto prostředku se nasadí do stejné šablony a odkazuje jeho název (není ID prostředku ). Proto je nutné další závislosti, než je určeno závislosti **dependsOn** vlastnost. [ResourceId](resource-group-template-functions-resource.md#resourceid) funkce není vytvoření implicitního závislost nebo ověřit, že prostředky existují. [Odkaz](resource-group-template-functions-resource.md#reference) funkce a [seznamu *](resource-group-template-functions-resource.md#list) funkce nevytvářejte implicitní závislostí, když na něj pomocí jeho ID prostředku. K vytvoření implicitního závislostí, předejte název prostředku, který je nasazený do stejné šablony.

Když se zobrazí problémy závislost, budete muset získat přehled o tom, pořadí nasazení prostředků. Chcete-li zobrazit pořadí, které operace nasazení:

1. Vyberte historii nasazení skupiny prostředků.

   ![Vyberte historie nasazení](./media/resource-manager-not-found-errors/select-deployment.png)

2. Vyberte z historie nasazení a vyberte **události**.

   ![Vyberte události nasazení](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Zkontrolujte pořadí událostí pro každý zdroj. Věnujte pozornost stav jednotlivých operací. Například následující obrázek ukazuje tři účty úložiště, které nasadí současně. Všimněte si, že 3 účty úložiště jsou spuštěné ve stejnou dobu.

   ![paralelní nasazení](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   Následující obrázek znázorňuje 3 účty úložiště, které nejsou nasazeni paralelně. Druhý účet úložiště závisí na první účet úložiště a třetí účtu úložiště závisí na druhého účtu úložiště. První účet úložiště je spuštění, přijmout a provést dřív, než se spustí na další.

   ![postupné nasazení](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Řešení 2 – získání prostředku v jiné skupině prostředků.

Pokud prostředek existuje v jiné skupině prostředků než ten, který se nasazuje do, použijte [funkce resourceId](resource-group-template-functions-resource.md#resourceid) získat plně kvalifikovaný název prostředku.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Řešení 3 - vrácení odkazu – funkce

Vyhledejte výraz, který zahrnuje [odkaz](resource-group-template-functions-resource.md#reference) funkce. Hodnoty, které poskytnete lišit v závislosti na tom, jestli prostředek je ve stejné šablony, skupinu prostředků a předplatném. Pečlivě zkontrolujte, že zadáváte hodnoty parametrů požadované pro váš scénář. Pokud prostředek je v jiné skupině prostředků, zadejte ID úplné prostředku. Například pokud chcete odkazovat na účet úložiště v jiné skupině prostředků, použijte:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```