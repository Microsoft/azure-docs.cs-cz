---
title: Vytvoření vlastních artefaktů pro virtuální počítač DevTest Labs | Microsoft Docs
description: Naučte se vytvářet vlastní artefaktů pro použití s Azure DevTest Labs.
services: devtest-lab,virtual-machines
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.assetid: 32dcdc61-ec23-4a01-b731-78c029ea5316
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: spelluru
ms.openlocfilehash: 268b9af7835c51d78812b35aff5aaac585961b01
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
ms.locfileid: "33787623"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Vytvoření vlastních artefaktů pro virtuální počítač DevTest Labs

V následujícím videu přehled kroků popsaných v tomto článku:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
> 
> 

## <a name="overview"></a>Přehled
Můžete použít *artefakty* k nasazení a nastavte aplikaci po zřízení virtuálního počítače. Artefakt se skládá z soubor definice artefaktů a další soubory skriptu, které jsou uložené ve složce v úložišti Git. Soubory definice artefaktů se skládají z JSON a výrazů, které můžete použít k určení, co chcete nainstalovat na virtuální počítač. Například můžete definovat název artefakt, příkaz ke spuštění a parametry, které jsou k dispozici, pokud se příkaz spustí. Podle názvu se může odkazovat na jiné soubory skriptu v souboru definice artefaktů.

## <a name="artifact-definition-file-format"></a>Formát souboru definice artefaktů
Následující příklad ukazuje oddíly, které tvoří základní strukturu definičního souboru:

    {
      "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2016-11-28/dtlArtifacts.json",
      "title": "",
      "description": "",
      "iconUri": "",
      "targetOsType": "",
      "parameters": {
        "<parameterName>": {
          "type": "",
          "displayName": "",
          "description": ""
        }
      },
      "runCommand": {
        "commandToExecute": ""
      }
    }

| Název elementu | Povinné? | Popis |
| --- | --- | --- |
| $schema |Ne |Umístění souboru schématu JSON. Soubor schématu JSON můžete otestovat platnost souboru definice. |
| název |Ano |Název artefaktů zobrazí v testovacím prostředí. |
| description |Ano |Popis artefaktu zobrazí v testovacím prostředí. |
| iconUri |Ne |Identifikátor URI na ikonu se zobrazí v testovacím prostředí. |
| targetOsType |Ano |Operační systém virtuálního počítače, kde je nainstalován artefaktů. Podporované možnosti patří Windows a Linux. |
| parameters |Ne |Hodnoty, které jsou k dispozici při spuštění příkazu install artefaktů na počítači. Díky tomu můžete přizpůsobit vaší artefaktů. |
| SpustitPříkaz |Ano |Artefaktů instalační příkaz, který se spouští na virtuální počítač. |

### <a name="artifact-parameters"></a>Parametry artefaktů
V sekci parametrů souboru definice zadejte hodnoty, které uživatel můžete zadat při instalaci artefakt. Může být tyto hodnoty v příkazu instalace artefaktů.

Pokud chcete definovat parametry, použijte následující strukturu:

    "parameters": {
        "<parameterName>": {
          "type": "<type-of-parameter-value>",
          "displayName": "<display-name-of-parameter>",
          "description": "<description-of-parameter>"
        }
      }

| Název elementu | Povinné? | Popis |
| --- | --- | --- |
| type |Ano |Typ hodnoty parametru. Najdete v následujícím seznamu povolených typů. |
| displayName |Ano |Název parametru, který se zobrazí uživateli v testovacím prostředí. | |
| description |Ano |Popis parametru, který se zobrazí v testovacím prostředí. |

Povolené typy jsou:

* řetězec (string žádné platné JSON)
* číslo (integer žádné platné JSON)
* BOOL (žádné platné JSON logické)
* pole (array žádné platné JSON)

## <a name="artifact-expressions-and-functions"></a>Výrazy artefaktů a funkce
Můžete použít výrazy a funkce Vytvořit artefakt příkaz instalovat.
Výrazy jsou uzavřené do závorek ([a]) a vyhodnocují se při instalaci artefakt. Výrazy může vyskytovat kdekoli v řetězcovou hodnotu JSON. Výrazy vždy vrátí jinou hodnotu JSON. Pokud budete muset použít řetězcový literál, který začíná závorky ([), musíte použít dva závorky ([[).
Obvykle použijete výrazy s funkcemi, vytvořit hodnotu. Stejně jako v jazyce JavaScript, volání funkce jsou formátovány jako **%{FunctionName/ (arg1, arg2, arg3)**.

V následujícím seznamu jsou uvedeny běžné funkce:

* **Parameters(parameterName)**: vrátí hodnotu parametru, který je zadán při spuštění příkazu artefaktů.
* **concat (arg1, arg2, arg3,...)** : Kombinuje více řetězcové hodnoty. Tato funkce může mít různé argumentů.

Následující příklad ukazuje, jak vytvořit hodnotu pomocí výrazy a funkce:

    runCommand": {
         "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Vytvoření vlastních artefaktů

1. Nainstalujte JSON editor. Je nutné editor JSON pro práci s artefaktů definiční soubory. Doporučujeme používat [Visual Studio Code](https://code.visualstudio.com/), která je dostupná pro Windows, Linux a OS X.
2. Získáte definici artifactfile.json ukázkový soubor. Podívejte se na artefakty vytvořené DevTest Labs týmu v našem [úložiště GitHub](https://github.com/Azure/azure-devtestlab). Vytvořili jsme bohatá knihovna artefaktů, které vám pomůžou vytvořit vlastní artefakty. Stáhněte soubor definice artefaktů a změnit jej k vytvoření vlastních artefaktů.
3. Využívat technologie IntelliSense. Použití prvku IntelliSense zobrazíte platná prvky, které můžete použít k vytvoření definice soubor artefaktů. Také se zobrazí různé možnosti pro hodnoty elementu. Například při úpravách **targetOsType** elementu, IntelliSense zobrazí dvě možnosti pro Windows nebo Linux.
4. Ukládání artefaktů v [úložiště Git](devtest-lab-add-artifact-repo.md).
   
   1. Vytvořte samostatný adresář pro každý artefaktů. Název adresáře musí být stejný jako název artefaktů.
   2. Uložte soubor definice artefaktů (artifactfile.json) v adresáři, který jste vytvořili.
   3. Úložiště skriptů, které jsou odkazované z instalace příkazu artefaktů.
      
      Tady je příklad, jak může vypadat na artefaktů složku:
      
      ![Příklad složky artefaktů](./media/devtest-lab-artifact-author/git-repo.png)
5. Přidejte úložiště artefakty do testovacího prostředí. V tématu [přidejte úložiště Git pro artefakty a šablony](devtest-lab-add-artifact-repo.md).

[!INCLUDE [devtest-lab-try-it-out](../../includes/devtest-lab-try-it-out.md)]

## <a name="related-articles"></a>Související články
* [Diagnostika chyb artefaktů v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojení virtuálního počítače do existující domény služby Active Directory pomocí šablony Resource Manageru v DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [přidat artefaktů úložiště Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).

