---
title: Import a export modrotisky pomocí PowerShellu
description: Naučte se pracovat s definicemi podrobného plánu jako s kódem. Sdílení, Správa zdrojového kódu a jejich správa pomocí příkazů exportu a importu.
ms.date: 01/27/2021
ms.topic: how-to
ms.openlocfilehash: a5b1adda0b02e2e2490441c5958ca9334febc24c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98919982"
---
# <a name="import-and-export-blueprint-definitions-with-powershell"></a>Import a export definic podrobných plánů pomocí PowerShellu

Plány Azure je možné plně spravovat prostřednictvím Azure Portal. Protože organizace budou v používání plánů Azure předem platit, měly by začít myslet na definice podrobného plánu jako spravovaný kód. Tento koncept se často označuje jako infrastruktura jako kód (IaC). S tím, jak kód zpracovává definice podrobného plánu, nabízí další výhody nad rámec toho, co Azure Portal nabídky. Mezi tyto výhody patří:

- Sdílení definic podrobného plánu
- Zálohování definic podrobných plánů
- Znovu použít definice podrobného plánu v různých klientech nebo předplatných
- Umístění definic podrobného plánu do správy zdrojového kódu
  - Automatizované testování definic podrobných plánů v testovacích prostředích
  - Podpora kanálů průběžné integrace a průběžného nasazování (CI/CD)

Bez ohledu na vaše důvody a Správa definic podrobných plánů jako kód přináší výhody. V tomto článku se dozvíte, jak používat `Import-AzBlueprintWithArtifact` `Export-AzBlueprintWithArtifact` příkazy a v modulu [AZ. detail](https://powershellgallery.com/packages/Az.Blueprint/) .

## <a name="prerequisites"></a>Požadavky

V tomto článku se předpokládá, že jsou v Azure modrotisky mírné znalosti. Pokud jste to ještě neudělali, Projděte si následující články:

- [Vytvořit podrobný plán na portálu](../create-blueprint-portal.md)
- Přečtěte si o [fázích nasazení](../concepts/deployment-stages.md) a [životním cyklu](../concepts/lifecycle.md) podrobného plánu.
- [Vytváření](../create-blueprint-powershell.md) a [Správa](./manage-assignments-ps.md) definic a přiřazení podrobného plánu pomocí PowerShellu

Pokud ještě není nainstalovaná, nainstalujte a ověřte modul **AZ. detail** z Galerie prostředí PowerShell podle pokynů v tématu [Přidání modulu AZ. detail](./manage-assignments-ps.md#add-the-azblueprint-module) .

## <a name="folder-structure-of-a-blueprint-definition"></a>Struktura složek v definici podrobného plánu

Než začnete s exportem a importem modrotisky, Podívejme se na to, jak jsou strukturované soubory, které tvoří definici podrobného plánu. Definice podrobného plánu by měla být uložena ve své vlastní složce.

> [!IMPORTANT]
> Pokud není předána žádná hodnota parametru **názvu** `Import-AzBlueprintWithArtifact` rutiny, použije se název složky, ve které je definice podrobného plánu uložená.

Spolu s definicí podrobného plánu, který musí být pojmenován `blueprint.json` , jsou artefakty, ze kterých se definice podrobného plánu skládá. Každý artefakt musí být v podsložce s názvem `artifacts` .
Společně se strukturou vaší definice podrobného plánu jako soubory JSON ve složkách, které by se měly podávat, vypadá takto:

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

## <a name="export-your-blueprint-definition"></a>Exportovat definici podrobného plánu

Postup exportu definice podrobného plánu je jednoduchý. Export definice podrobného plánu může být užitečný pro sdílení, zálohování nebo vložení do správy zdrojového kódu.

- Podrobný **plán** [povinné]
  - Určuje definici podrobného plánu.
  - Slouží `Get-AzBlueprint` k získání referenčního objektu.
- **OutputPath** [povinné]
  - Určuje cestu, do které budou uloženy soubory JSON definice podrobného plánu.
  - Výstupní soubory jsou v podsložce s názvem definice podrobného plánu.
- **Verze** (volitelné)
  - Určuje verzi pro **výstup, pokud referenční objekt** podrobného plánu obsahuje odkazy na více než jednu verzi.

1. Získejte odkaz na definici podrobného plánu pro export z předplatného, které je reprezentované jako `{subId}` :

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

Když máte buď [exportovanou definici](#export-your-blueprint-definition) podrobného plánu, nebo máte vytvořenou definici podrobného plánu ve [struktuře požadované složky](#folder-structure-of-a-blueprint-definition), můžete tuto definici podrobného plánu naimportovat do jiné skupiny pro správu nebo předplatného.

Příklady předdefinovaných definic podrobných plánů najdete v tématu [Azure Blueprint úložiště GitHub](https://github.com/Azure/azure-blueprints/tree/master/samples/001-builtins).

- **Název** [povinné]
  - Určuje název nové definice podrobného plánu.
- **InputPath** [povinné]
  - Určuje cestu, ze které se má vytvořit definice podrobného plánu.
  - Musí odpovídat [požadované struktuře složek](#folder-structure-of-a-blueprint-definition) .
- **ManagementGroupId** (volitelné)
  - ID skupiny pro správu, do které se uloží definice podrobného plánu, pokud není aktuální kontextový Standard
  - Musí se zadat buď **ManagementGroupId** , nebo **SubscriptionId** .
- **SubscriptionId** (volitelné)
  - ID předplatného, do kterého se uloží definice podrobného plánu, pokud není aktuální kontext výchozí
  - Musí se zadat buď **ManagementGroupId** , nebo **SubscriptionId** .

1. Pomocí `Import-AzBlueprintWithArtifact` rutiny importujte zadanou definici podrobného plánu:

   ```azurepowershell-interactive
   # Login first with Connect-AzAccount if not using Cloud Shell

   Import-AzBlueprintWithArtifact -Name 'MyBlueprint' -ManagementGroupId 'DevMG' -InputPath 'C:\Blueprints\MyBlueprint'
   ```

Po importu definice podrobného plánu [ji přiřaďte k prostředí PowerShell](./manage-assignments-ps.md#create-blueprint-assignments).

Informace o vytváření pokročilých definic podrobných plánů najdete v následujících článcích:

- Použijte [statické a dynamické parametry](../concepts/parameters.md).
- Přizpůsobení [pořadí klasifikace](../concepts/sequencing-order.md)podrobného plánu.
- Chraňte nasazení pomocí [uzamykání prostředků](../concepts/resource-locking.md)podrobného plánu.
- [Spravujte modrotisky jako kód](https://github.com/Azure/azure-blueprints/blob/master/README.md).

## <a name="next-steps"></a>Další kroky

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Řešení potíží při přiřazení podrobného plánu – [obecné řešení potíží](../troubleshoot/general.md)