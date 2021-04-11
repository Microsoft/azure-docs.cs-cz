---
title: Chyby nenalezených prostředků
description: Popisuje, jak vyřešit chyby při nalezení prostředku. K této chybě může dojít při nasazování šablony Azure Resource Manager nebo při přijetí akcí správy.
ms.topic: troubleshooting
ms.date: 03/23/2021
ms.openlocfilehash: b80c32683190167d5c0d6e0a7f75acce8bbdb833
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "104950871"
---
# <a name="resolve-resource-not-found-errors"></a>Chyby při řešení nenalezených prostředků

Tento článek popisuje chybu, která se zobrazí, když se prostředek během operace nenajde. Tato chyba se obvykle zobrazí při nasazování prostředků. Tato chyba se zobrazí také při provádění úloh správy a Azure Resource Manager nemůže najít požadovaný prostředek. Pokud se například pokusíte přidat značky k prostředku, který neexistuje, zobrazí se tato chyba.

## <a name="symptom"></a>Příznak

Existují dva chybové kódy, které naznačují, že prostředek nebyl nalezen. Chyba **NotFound** vrátí výsledek podobný tomuto:

```
Code=NotFound;
Message=Cannot find ServerFarm with name exampleplan.
```

Chyba **ResourceNotFound** vrátí výsledek podobný tomuto:

```
Code=ResourceNotFound;
Message=The Resource 'Microsoft.Storage/storageAccounts/{storage name}' under resource
group {resource group name} was not found.
```

## <a name="cause"></a>Příčina

Správce prostředků potřebuje načíst vlastnosti pro prostředek, ale nenašel prostředek ve vašich předplatných.

## <a name="solution-1---check-resource-properties"></a>Řešení 1 – Projděte si vlastnosti prostředku

Pokud se zobrazí tato chyba při provádění úlohy správy, ověřte hodnoty, které zadáte pro daný prostředek. Tyto tři hodnoty mají být zkontrolovány:

* Název prostředku
* Název skupiny prostředků
* Předplatné

Pokud používáte prostředí PowerShell nebo rozhraní příkazového řádku Azure CLI, ověřte, zda je spuštěn příkaz v předplatném, které prostředek obsahuje. Předplatné můžete změnit pomocí [set-AzContext](/powershell/module/Az.Accounts/Set-AzContext) nebo [AZ Account set](/cli/azure/account#az-account-set). Mnoho příkazů také poskytuje parametr předplatného, který umožňuje zadat jiné předplatné, než je aktuální kontext.

Pokud máte potíže s ověřováním vlastností, přihlaste se k [portálu](https://portal.azure.com). Vyhledejte prostředek, který se pokoušíte použít, a Projděte si název prostředku, skupinu prostředků a předplatné.

## <a name="solution-2---set-dependencies"></a>Řešení 2 – Nastavení závislostí

Pokud se tato chyba zobrazí při nasazování šablony, možná budete muset přidat závislost. Správce prostředků optimalizuje nasazení díky paralelnímu vytváření prostředků, pokud je to možné. Pokud musí být jeden prostředek nasazen po jiném prostředku, je nutné ve vaší šabloně použít element **dependsOn** . Například při nasazení webové aplikace musí existovat plán App Service. Pokud jste neurčili, že webová aplikace závisí na plánu App Service, Správce prostředků vytvoří oba prostředky současně. Zobrazí se chyba oznamující, že se prostředek App Serviceho plánu nepovedlo najít, protože ještě neexistuje při pokusu o nastavení vlastnosti ve webové aplikaci. Této chybě zabráníte nastavením závislosti ve webové aplikaci.

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

   ![vybrat historii nasazení](./media/error-not-found/select-deployment.png)

2. Vyberte nasazení z historie a vyberte **události**.

   ![vybrat události nasazení](./media/error-not-found/select-deployment-events.png)

3. Prostudujte si posloupnost událostí pro jednotlivé prostředky. Věnujte pozornost stavu každé operace. Například následující obrázek ukazuje tři účty úložiště, které jsou nasazeny paralelně. Všimněte si, že tři účty úložiště jsou spuštěné ve stejnou dobu.

   ![paralelní nasazení](./media/error-not-found/deployment-events-parallel.png)

   Následující obrázek ukazuje tři účty úložiště, které nejsou nasazeny paralelně. Druhý účet úložiště závisí na prvním účtu úložiště a třetí účet úložiště závisí na druhém účtu úložiště. První účet úložiště se spustí, přijme a dokončí před tím, než se spustí další.

   ![sekvenční nasazení](./media/error-not-found/deployment-events-sequence.png)

## <a name="solution-3---get-external-resource"></a>Řešení 3 – získání externího prostředku

Když nasazujete šablonu a potřebujete získat prostředek, který existuje v jiném předplatném nebo skupině prostředků, použijte [funkci ResourceID](template-functions-resource.md#resourceid). Tato funkce vrátí pro získání plně kvalifikovaného názvu prostředku.

Parametry předplatného a skupiny prostředků ve funkci resourceId jsou volitelné. Pokud je nezadáte, budou ve výchozím nastavení pro aktuální předplatné a skupinu prostředků. Když pracujete s prostředkem v jiné skupině prostředků nebo předplatným, ujistěte se, že jste tyto hodnoty zadali.

Následující příklad získá ID prostředku pro prostředek, který existuje v jiné skupině prostředků.

```json
"properties": {
  "name": "[parameters('siteName')]",
  "serverFarmId": "[resourceId('plangroup', 'Microsoft.Web/serverfarms', parameters('hostingPlanName'))]"
}
```

## <a name="solution-4---get-managed-identity-from-resource"></a>Řešení 4 – získání spravované identity z prostředku

Pokud nasazujete prostředek, který implicitně vytvoří [spravovanou identitu](../../active-directory/managed-identities-azure-resources/overview.md), musíte před načtením hodnot na spravované identitě počkat, až se tento prostředek nasadí. Pokud předáte název spravované identity k [referenční](template-functions-resource.md#reference) funkci, správce prostředků se pokusí vyřešit odkaz před nasazením prostředku a identity. Místo toho předejte název prostředku, na který je identita použita. Tento přístup zajišťuje nasazení prostředků a spravované identity před tím, než Správce prostředků vyřeší odkazovou funkci.

V referenční funkci použijte `Full` k získání všech vlastností včetně spravované identity.

Vzor je:

`"[reference(resourceId(<resource-provider-namespace>, <resource-name>), <API-version>, 'Full').Identity.propertyName]"`

> [!IMPORTANT]
> Nepoužívejte tento vzor:
>
> `"[reference(concat(resourceId(<resource-provider-namespace>, <resource-name>),'/providers/Microsoft.ManagedIdentity/Identities/default'),<API-version>).principalId]"`
>
> Vaše šablona selže.

Pokud například chcete získat ID objektu zabezpečení pro spravovanou identitu, která se používá pro virtuální počítač, použijte:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachines', variables('vmName')),'2019-12-01', 'Full').identity.principalId]",
```

Nebo pokud chcete získat ID tenanta pro spravovanou identitu, která se aplikuje na sadu škálování virtuálního počítače, použijte:

```json
"[reference(resourceId('Microsoft.Compute/virtualMachineScaleSets',  variables('vmNodeType0Name')), 2019-12-01, 'Full').Identity.tenantId]"
```

## <a name="solution-5---check-functions"></a>Řešení 5 – kontroly funkcí

Při nasazování šablony hledejte výrazy, které používají funkce [reference](template-functions-resource.md#reference) nebo [klíče listkey](template-functions-resource.md#listkeys) . Hodnoty, které zadáte, se liší v závislosti na tom, jestli je prostředek ve stejné šabloně, skupině prostředků a předplatném. Ověřte, že jste pro svůj scénář poskytovali požadované hodnoty parametrů. Pokud se prostředek nachází v jiné skupině prostředků, zadejte úplné ID prostředku. Pokud například chcete odkazovat na účet úložiště v jiné skupině prostředků, použijte:

```json
"[reference(resourceId('exampleResourceGroup', 'Microsoft.Storage/storageAccounts', 'myStorage'), '2017-06-01')]"
```

## <a name="solution-6---after-deleting-resource"></a>Řešení 6 – po odstranění prostředku

Když prostředek odstraníte, může dojít k krátké době, kdy se prostředek stále zobrazuje na portálu, ale není ve skutečnosti k dispozici. Pokud vyberete prostředek, zobrazí se chyba oznamující, že prostředek nebyl nalezen. Aktualizujte portál, abyste získali nejnovější zobrazení.

Pokud potíže potrvají i po krátkém čekání, obraťte se na [podporu](https://azure.microsoft.com/support/options/).
