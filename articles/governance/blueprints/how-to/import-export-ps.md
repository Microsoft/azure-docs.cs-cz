---
title: Import a export podrobných plánů pomocí PowerShellu
description: Naučte se pracovat s definicemi podrobných plánů jako s kódem. Sdílejte, spravujte zdroj a spravujte je pomocí příkazů pro export a import.
ms.date: 09/03/2019
ms.topic: how-to
ms.openlocfilehash: fc7b9818072665d79deaf8a456868943e8428730
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873195"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Import a export definic podrobných plánů pomocí PowerShellu

Azure Blueprints můžete plně spravovat prostřednictvím portálu Azure. Jak organizace postupují v používání podrobných plánů, měly by začít přemýšlet o definicích podrobných plánů jako spravovaného kódu. Tento koncept se často označuje jako infrastruktura jako kód (IaC). Zacházení s definicemi podrobného plánu jako kódu nabízí další výhody nad rámec toho, co nabízí portál Azure. Mezi tyto výhody patří:

- Sdílení definic podrobných plánů
- Zálohování definic podrobných plánů
- Opětovné použití definic podrobného plánu v různých tenantech nebo předplatných
- Umístění definic podrobného plánu do správy zdrojového kódu
  - Automatizované testování definic podrobných plánů v testovacích prostředích
  - Podpora kontinuální integrace a průběžného nasazování (CI/CD) potrubí

Ať už jsou vaše důvody jakékoli, správa definic podrobných plánů jako kódu má výhody. Tento článek ukazuje, `Import-AzBlueprintWithArtifact` jak `Export-AzBlueprintWithArtifact` používat příkazy a v modulu [Az.Blueprint.](https://powershellgallery.com/packages/Az.Blueprint/)

## <a name="prerequisites"></a>Požadavky

Tento článek předpokládá mírné pracovní znalosti Azure Blueprints. Pokud jste tak dosud neučinili, postupujte takto:

- [Vytvoření podrobného plánu na portálu](../create-blueprint-portal.md)
- Přečtěte si o [fázích nasazení](../concepts/deployment-stages.md) a [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- [Vytváření](../create-blueprint-powershell.md) a [správa](./manage-assignments-ps.md) definic a přiřazení podrobného plánu pomocí PowerShellu

Pokud ještě není nainstalovaný, postupujte podle pokynů v [přidat modul Az.Blueprint](./manage-assignments-ps.md#add-the-azblueprint-module) k instalaci a ověření modulu **Az.Blueprint** z Galerie prostředí PowerShell.

## <a name="folder-structure-of-a-blueprint-definition"></a>Struktura složek definice podrobného plánu

Než se podíváte na export a import podrobných plánů, podívejme se na to, jak jsou strukturovány soubory, které tvoří definici podrobného plánu. Definice podrobného plánu by měla být uložena ve vlastní složce.

> [!IMPORTANT]
> Pokud žádná hodnota je **předánname** `Import-AzBlueprintWithArtifact` parametr rutiny, název složky definice podrobného plánu je uložen v se používá.

Spolu s definicí podrobného `blueprint.json`plánu, která musí být pojmenována , jsou artefakty, které se skládá z definice podrobného plánu. Každý artefakt musí být v `artifacts`podsložce s názvem .
Dohromady by měla vypadat struktura definice podrobného plánu jako souborů JSON ve složkách takto:

```text
.
|
|- MyBlueprint/  _______________ # Root folder name becomes default name of blueprint definition
|  |- blueprint.json  __________ # The blueprint definition. Fixed name.
|
|  |- artifacts/  ______________ # Subfolder for all blueprint artifacts. Fixed name.
|     |- artifact.json  ________ # Blueprint artifact as JSON file. Artifact named from file.
|     |- ...
|     |- more-artifacts.json

```

## <a name="export-your-blueprint-definition"></a>Export definice podrobného plánu

Kroky k exportu definice podrobného plánu jsou jednoduché. Export definice podrobného plánu může být užitečné pro sdílení, zálohování nebo umístění do správy zdrojového kódu.

- **Plán** [povinné]
  - Určuje definici podrobného plánu.
  - Slouží `Get-AzBlueprint` k získání referenčního objektu
- **OutputPath** [povinné]
  - Určuje cestu k uložení souborů JSON definice podrobného plánu
  - Výstupní soubory jsou v podsložce s názvem definice podrobného plánu
- **Verze** (volitelně)
  - Určuje verzi výstupu, pokud referenční objekt **podrobného plánu** obsahuje odkazy na více než jednu verzi.

1. Získejte odkaz na definici podrobného plánu `{subId}`pro export z předplatného reprezentovaného jako :

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   # Get version '1.1' of the blueprint definition in the specified subscription
   $bpDefinition = Get-AzBlueprint -SubscriptionId '{subId}' -Name 'MyBlueprint' -Version '1.1'
   ```

1. Pomocí `Export-AzBlueprintWithArtifact` rutiny exportujte zadanou definici podrobného plánu:

   ```azurepowershell-interactive
   Export-AzBlueprintWithArtifact -Blueprint $bpDefinition -OutputPath 'C:\Blueprints'
   ```

## <a name="import-your-blueprint-definition"></a>Import definice podrobného plánu

Jakmile máte [exportovanou definici podrobného plánu](#export-your-blueprint-definition) nebo máte ručně vytvořenou definici podrobného plánu v [požadované struktuře složek](#folder-structure-of-a-blueprint-definition), můžete importovat tuto definici podrobného plánu do jiné skupiny pro správu nebo předplatného.

Příklady předdefinovaných definic podrobných plánů najdete v [úložišti Azure Blueprint GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/builtins).

- **Jméno** [povinné]
  - Určuje název nové definice podrobného plánu.
- **InputPath** [povinné]
  - Určuje cestu k vytvoření definice podrobného plánu z
  - Musí odpovídat [požadované struktuře složek.](#folder-structure-of-a-blueprint-definition)
- **ManagementGroupId** (volitelné)
  - ID skupiny pro správu pro uložení definice podrobného plánu na aktuální výchozí kontext
  - Musí být **zadáno id managementgroupid** nebo **SubscriptionId.**
- **SubscriptionId** (volitelné)
  - ID předplatného pro uložení definice podrobného plánu na neaktuální výchozí kontext
  - Musí být **zadáno id managementgroupid** nebo **SubscriptionId.**

1. Pomocí `Import-AzBlueprintWithArtifact` rutiny importujte zadanou definici podrobného plánu:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Po importu definice podrobného plánu [ji přiřaďte pomocí prostředí PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Informace o vytváření pokročilých definic podrobných plánů naleznete v následujících článcích:

- Používejte [statické a dynamické parametry](../concepts/parameters.md).
- Přizpůsobte [pořadí sekvencování podrobného plánu](../concepts/sequencing-order.md).
- Chraňte nasazení pomocí [uzamčení prostředků podrobného plánu](../concepts/resource-locking.md).
- [Správa podrobných plánů jako kódu](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si o [životním cyklu podrobného plánu](../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../concepts/resource-locking.md).
- Vyřešte problémy během přiřazení podrobného plánu s [obecným řešením potíží](../troubleshoot/general.md).