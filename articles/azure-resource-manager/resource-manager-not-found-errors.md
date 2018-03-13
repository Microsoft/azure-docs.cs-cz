---
title: "Chyby nenalezení prostředků Azure | Microsoft Docs"
description: "Popisuje, jak vyřešit chyby, když prostředek nebyl nalezen."
services: azure-resource-manager,azure-portal
documentationcenter: 
author: tfitzmac
manager: timlt
editor: 
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: support-article
ms.date: 03/08/2018
ms.author: tomfitz
ms.openlocfilehash: 6844c1c2938873b0a74fe66e846dc733a4bd6ff7
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Vyřešte chyby nenalezení pro prostředky Azure

Tento článek popisuje chyb, které může dojít, pokud prostředek nelze nalézt během nasazení.

## <a name="symptom"></a>Příznaky

Pokud vaše šablona zahrnuje název prostředku, které nelze vyřešit, obdržíte chybu podobně jako:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Pokud pokus o použití [odkaz](resource-group-template-functions-resource.md#reference) nebo [listKeys](resource-group-template-functions-resource.md#listkeys) funkce s prostředky, které nelze vyřešit, zobrazí se následující chyba:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Příčina

Správce prostředků je potřeba načíst vlastnosti pro prostředek, ale nemůže identifikovat prostředků ve vašem předplatném.

## <a name="solution-1---set-dependencies"></a>Řešení 1 – nastavte závislosti

Pokud se pokoušíte nasadit chybějící prostředek v šabloně, zkontrolujte, jestli je potřeba přidat závislost. Správce prostředků optimalizuje nasazení tak, že vytvoříte prostředky paralelně, pokud je to možné. Pokud po jiný prostředek musí být nasazený jeden prostředek, budete muset použít **dependsOn** element ve vaší šabloně. Například Pokud nasazujete webovou aplikaci, musí existovat plán služby App Service. Pokud jste nezadali, že webová aplikace závisí na plán služby App Service, vytvoří Resource Manager i prostředky ve stejnou dobu. Dojde k chybě oznamující, že App Service plán prostředek nelze nalézt, protože neexistuje ještě při pokusu o nastavení vlastnosti ve webové aplikaci. Tuto chybu můžete zabránit nastavením závislost ve webové aplikaci.

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

Ale budete chtít vyhnout nastavení závislosti, které nejsou potřebné. Až budete mít nepotřebné závislosti, můžete prodloužit doba trvání nasazení tím, že prostředky, které nejsou na sobě navzájem závislé z nasazuje paralelně. Kromě toho můžete vytvořit cyklické závislosti, které blokují nasazení. [Odkaz](resource-group-template-functions-resource.md#reference) funkce vytvoří implicitní závislostí v odkazovaném prostředku, když tento prostředek je nasazen do stejné šablony. Proto můžete mít další závislosti, než je určeno závislosti **dependsOn** vlastnost. [ResourceId](resource-group-template-functions-resource.md#resourceid) funkce vytvořit implicitní závislostí nebo ověřit, zda prostředek existuje.

Pokud narazíte na potíže závislostí, budete muset získat přehled o pořadí nasazení prostředků. Chcete-li zobrazit pořadí operace nasazení:

1. Vyberte historii nasazení skupiny prostředků.

   ![Vyberte historii nasazení](./media/resource-manager-not-found-errors/select-deployment.png)

2. Vyberte nasazení z historie a vyberte **události**.

   ![Vybrat nasazení události](./media/resource-manager-not-found-errors/select-deployment-events.png)

3. Zkontrolujte posloupnost událostí pro každý zdroj. Věnujte pozornost stav jednotlivých operací. Například následující obrázek ukazuje tři účty úložiště, které nasadí paralelně. Všimněte si, že tři účty úložiště jsou spuštěné ve stejnou dobu.

   ![Paralelní nasazení](./media/resource-manager-not-found-errors/deployment-events-parallel.png)

   Další obrázek ukazuje tři účty úložiště, které nejsou nasazené paralelně. Druhý účtu úložiště závisí na první účet úložiště, a třetí účet úložiště na druhý účtu úložiště. První účet úložiště je spuštěna, přijmout a dokončit před zahájením na další.

   ![sekvenční nasazení](./media/resource-manager-not-found-errors/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Řešení 2 - získat prostředek z jiné skupině prostředků.

Pokud existuje v jiné skupině prostředků než ten, který provádí jeho nasazení na prostředek, použijte [resourceId funkce](resource-group-template-functions-resource.md#resourceid) získat plně kvalifikovaný název prostředku.

```json
"properties": {
    "name": "[parameters('siteName')]",
    "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Řešení 3 – odkaz funkce kontroly

Vyhledat výraz, který zahrnuje [odkaz](resource-group-template-functions-resource.md#reference) funkce. Hodnoty, které zadáte lišit v závislosti na tom, zda prostředek je ve stejné šablony, skupinu prostředků a předplatného. Zkontrolujte, že poskytnete požadovaný parametr hodnoty pro váš scénář. Pokud je v jiné skupině prostředků, zadejte ID úplné prostředku. Chcete-li účet úložiště v jiné skupině prostředků, například použijte:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```