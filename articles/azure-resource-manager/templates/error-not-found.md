---
title: Zdroj nebyl nalezen chyby
description: Popisuje, jak vyřešit chyby, když prostředek nelze najít při nasazování pomocí šablony Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76773249"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Řešení nezjištěných chyb pro prostředky Azure

Tento článek popisuje chyby, které se mohou zobrazit, když prostředek nelze najít během nasazení.

## <a name="symptom"></a>Příznak

Pokud šablona obsahuje název prostředku, který nelze přeložit, zobrazí se chyba podobná:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Pokud používáte [funkce reference](template-functions-resource.md#reference) nebo [listKeys](template-functions-resource.md#listkeys) s prostředkem, který nelze vyřešit, zobrazí se následující chyba:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Příčina

Správce prostředků potřebuje načíst vlastnosti prostředku, ale nemůže identifikovat prostředek ve vašem předplatném.

## <a name="solution-1---set-dependencies"></a>Řešení 1 - nastavit závislosti

Pokud se pokoušíte nasadit chybějící prostředek v šabloně, zkontrolujte, zda je třeba přidat závislost. Správce prostředků optimalizuje nasazení vytvářením prostředků paralelně, pokud je to možné. Pokud jeden prostředek musí být nasazeny po jiný prostředek, je třeba použít **dependsOn** prvek v šabloně. Například při nasazování webové aplikace musí existovat plán služby App Service. Pokud jste nezadali, že webová aplikace závisí na plánu služby App Service, Správce prostředků vytvoří oba prostředky současně. Zobrazí se chyba oznamující, že prostředek plánu služby App Service nelze najít, protože ještě neexistuje při pokusu o nastavení vlastnosti ve webové aplikaci. Této chybě zabráníte nastavením závislosti ve webové aplikaci.

```json
{
  "type": "Microsoft.Web/sites",
  "apiVersion": "2015-08-01",
  "dependsOn": [
    "[variables('hostingPlanName')]"
  ],
  ...
}
```

Ale chcete se vyhnout nastavení závislostí, které nejsou potřeba. Pokud máte zbytečné závislosti, prodloujíte dobu trvání nasazení tím, že zabráníte souběžnému nasazení prostředků, které na sobě nejsou závislé. Kromě toho můžete vytvořit cyklické závislosti, které blokují nasazení. [Funkce reference](template-functions-resource.md#reference) a [seznam*](template-functions-resource.md#list) vytvoří implicitní závislost na odkazovaném prostředku, když je tento prostředek nasazen ve stejné šabloně a je odkazován svým názvem (nikoli ID prostředku). Proto můžete mít více závislostí než závislosti zadané v **dependsOn** vlastnost. Funkce [resourceId](template-functions-resource.md#resourceid) nevytváří implicitní závislost ani neověřuje, zda prostředek existuje. [Funkce referenční](template-functions-resource.md#reference) funkce a [seznam*](template-functions-resource.md#list) nevytvářejí implicitní závislost, pokud je prostředek odkazován jeho ID prostředku. Chcete-li vytvořit implicitní závislost, předajte název prostředku, který je nasazen ve stejné šabloně.

Když se zobrazí problémy se závislostmi, je třeba získat přehled o pořadí nasazení prostředků. Chcete-li zobrazit pořadí operací nasazení:

1. Vyberte historii nasazení pro skupinu prostředků.

   ![vybrat historii nasazení](./media/error-not-found/select-deployment.png)

2. Vyberte nasazení z historie a vyberte **Události**.

   ![vybrat události nasazení](./media/error-not-found/select-deployment-events.png)

3. Prozkoumejte posloupnost událostí pro každý prostředek. Věnujte pozornost stavu každé operace. Například následující obrázek zobrazuje tři účty úložiště, které byly nasazeny paralelně. Všimněte si, že tři účty úložiště jsou spuštěny současně.

   ![paralelní nasazení](./media/error-not-found/deployment-events-parallel.png)

   Další obrázek zobrazuje tři účty úložiště, které nejsou nasazeny paralelně. Druhý účet úložiště závisí na prvním účtu úložiště a třetí účet úložiště závisí na druhém účtu úložiště. První účet úložiště je spuštěn, přijat a dokončen před dalším spuštěním.

   ![sekvenční nasazení](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Řešení 2 – získání prostředků z různých skupin prostředků

Pokud prostředek existuje v jiné skupině prostředků, než je ta, do které se nasazuje, použijte [funkci resourceId](template-functions-resource.md#resourceid) k získání plně kvalifikovaného názvu prostředku.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Řešení 3 - kontrola referenční funkce

Vyhledejte výraz, který obsahuje [referenční](template-functions-resource.md#reference) funkci. Hodnoty, které zadáte, se liší v závislosti na tom, zda je prostředek ve stejné šabloně, skupině prostředků a předplatném. Zkontrolujte, zda poskytujete požadované hodnoty parametrů pro váš scénář. Pokud je prostředek v jiné skupině prostředků, zadejte Úplné ID prostředku. Chcete-li například odkazovat na účet úložiště v jiné skupině prostředků, použijte:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Řešení 4 – získání spravované identity z prostředku

Pokud nasazujete prostředek, který implicitně vytvoří [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md), musíte počkat, až se tento prostředek nasadí, než nabude tecích hodnot na spravované identitě. Pokud předáte název spravované identity [referenční](template-functions-resource.md#reference) funkci, Resource Manager se pokusí přeložit odkaz před nasazením prostředku a identity. Místo toho předaj název prostředku, na který je identita použita. Tento přístup zajišťuje, že prostředek a spravovaná identita jsou nasazeny dříve, než Správce prostředků vyřeší referenční funkci.

V referenční funkci `Full` použijte k získání všech vlastností včetně spravované identity.

Chcete-li například získat ID klienta pro spravovanou identitu, která se použije pro škálovací sadu virtuálních strojů, použijte:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```