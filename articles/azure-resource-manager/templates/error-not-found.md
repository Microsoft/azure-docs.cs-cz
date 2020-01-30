---
title: Chyby nenalezených prostředků
description: Popisuje, jak vyřešit chyby, pokud se prostředek nenajde při nasazení pomocí šablony Azure Resource Manager.
ms.topic: troubleshooting
ms.date: 01/21/2020
ms.openlocfilehash: b6f433118092e46f734d4b65040dd97c2fcb58d9
ms.sourcegitcommit: 984c5b53851be35c7c3148dcd4dfd2a93cebe49f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2020
ms.locfileid: "76773249"
---
# <a name="resolve-not-found-errors-for-azure-resources"></a>Vyřešit chyby nenalezení pro prostředky Azure

Tento článek popisuje chyby, se kterými se můžete setkat, když se prostředek během nasazení nenajde.

## <a name="symptom"></a>Příznak

Pokud šablona obsahuje název prostředku, který nelze vyřešit, zobrazí se chybová zpráva podobná této:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Použijete-li funkci [reference](template-functions-resource.md#reference) nebo [klíče listkey](template-functions-resource.md#listkeys) s prostředkem, který nelze vyřešit, dojde k následující chybě:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Příčina

Správce prostředků potřebuje načíst vlastnosti pro prostředek, ale nedokáže identifikovat prostředek v předplatném.

## <a name="solution-1---set-dependencies"></a>Řešení 1 – Nastavení závislostí

Pokud se pokoušíte nasadit chybějící prostředek v šabloně, ověřte, zda potřebujete přidat závislost. Správce prostředků optimalizuje nasazení díky paralelnímu vytváření prostředků, pokud je to možné. Pokud musí být jeden prostředek nasazen po jiném prostředku, je nutné ve vaší šabloně použít element **dependsOn** . Například při nasazení webové aplikace musí existovat plán App Service. Pokud jste neurčili, že webová aplikace závisí na plánu App Service, Správce prostředků vytvoří oba prostředky současně. Zobrazí se chyba oznamující, že se prostředek App Serviceho plánu nepovedlo najít, protože ještě neexistuje při pokusu o nastavení vlastnosti ve webové aplikaci. Této chybě zabráníte nastavením závislosti ve webové aplikaci.

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

Ale chcete se vyhnout nastavení závislostí, které nepotřebujete. Pokud máte zbytečné závislosti, prodloužete dobu trvání nasazení tím, že zabráníte souběžnému nasazení prostředků, které na sobě nejsou závislé. Kromě toho můžete vytvořit cyklické závislosti, které zablokují nasazení. Funkce [reference](template-functions-resource.md#reference) a [list *](template-functions-resource.md#list) vytvoří implicitní závislost na odkazovaném prostředku, pokud je tento prostředek nasazený ve stejné šabloně a odkazuje na jeho název (nikoli ID prostředku). Proto je možné, že budete mít více závislostí než závislosti zadané ve vlastnosti **dependsOn** . Funkce [ResourceID](template-functions-resource.md#resourceid) nevytvoří implicitní závislost nebo ověří, zda prostředek existuje. Funkce [reference](template-functions-resource.md#reference) a [list *](template-functions-resource.md#list) nevytvoří implicitní závislost, pokud je prostředek odkazován podle jeho ID prostředku. Chcete-li vytvořit implicitní závislost, předejte název prostředku, který je nasazen ve stejné šabloně.

Když vidíte problémy se závislostmi, potřebujete získat přehled o pořadí nasazení prostředků. Zobrazení pořadí operací nasazení:

1. Vyberte historii nasazení pro skupinu prostředků.

   ![Vybrat historii nasazení](./media/error-not-found/select-deployment.png)

2. Vyberte nasazení z historie a vyberte **události**.

   ![vybrat události nasazení](./media/error-not-found/select-deployment-events.png)

3. Prostudujte si posloupnost událostí pro jednotlivé prostředky. Věnujte pozornost stavu každé operace. Například následující obrázek ukazuje tři účty úložiště, které jsou nasazeny paralelně. Všimněte si, že tři účty úložiště jsou spuštěné ve stejnou dobu.

   ![paralelní nasazení](./media/error-not-found/deployment-events-parallel.png)

   Následující obrázek ukazuje tři účty úložiště, které nejsou nasazeny paralelně. Druhý účet úložiště závisí na prvním účtu úložiště a třetí účet úložiště závisí na druhém účtu úložiště. První účet úložiště se spustí, přijme a dokončí před tím, než se spustí další.

   ![sekvenční nasazení](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-2---get-resource-from-different-resource-group"></a>Řešení 2 – získání prostředku z jiné skupiny prostředků

Pokud prostředek existuje v jiné skupině prostředků než ta, která je nasazena do, použijte [funkci ResourceID](template-functions-resource.md#resourceid) k získání plně kvalifikovaného názvu prostředku.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-3---check-reference-function"></a>Řešení 3 – Podívejte se na odkazovou funkci

Vyhledejte výraz, který obsahuje [odkazovou](template-functions-resource.md#reference) funkci. Hodnoty, které zadáte, se liší v závislosti na tom, jestli je prostředek ve stejné šabloně, skupině prostředků a předplatném. Dvakrát ověřte, že ve svém scénáři máte požadované hodnoty parametrů. Pokud se prostředek nachází v jiné skupině prostředků, zadejte úplné ID prostředku. Pokud například chcete odkazovat na účet úložiště v jiné skupině prostředků, použijte:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Řešení 4 – získání spravované identity z prostředku

Pokud nasazujete prostředek, který implicitně vytvoří [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md), musíte před načtením hodnot na spravované identitě počkat, až se tento prostředek nasadí. Pokud předáte název spravované identity k [referenční](template-functions-resource.md#reference) funkci, správce prostředků se pokusí vyřešit odkaz před nasazením prostředku a identity. Místo toho předejte název prostředku, na který je identita použita. Tento přístup zajišťuje nasazení prostředků a spravované identity před tím, než Správce prostředků vyřeší odkazovou funkci.

V referenční funkci použijte `Full` k získání všech vlastností včetně spravované identity.

Pokud například chcete získat ID tenanta pro spravovanou identitu, která se používá pro sadu škálování virtuálního počítače, použijte:

```json
"tenantId": "[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), variables('vmssApiVersion'), 'Full').Identity.tenantId]"
```