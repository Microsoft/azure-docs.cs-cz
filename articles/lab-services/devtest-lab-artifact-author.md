---
title: Při vytváření vlastních artefaktů pro virtuální počítač DevTest Labs | Dokumentace Microsoftu
description: Informace o vytváření vlastních artefaktů pro použití s Azure DevTest Labs.
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
ms.date: 07/11/2018
ms.author: spelluru
ms.openlocfilehash: 05abc61da7af02c56dacd632175d6fbfa64cb9e1
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/28/2019
ms.locfileid: "55098557"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Při vytváření vlastních artefaktů pro virtuální počítač DevTest Labs

Podívejte se na následující video přehled kroků popsaných v tomto článku:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Přehled
Můžete použít *artefakty* k nasazení a nastavení aplikace po zřízení virtuálního počítače. Artefakt se skládá z soubor definice artefaktů a další soubory skriptu, které jsou uloženy do složky v úložišti Git. Soubory definic artefaktu se skládají z JSON a z výrazů, které můžete použít k určení, co byste chtěli nainstalovat na virtuální počítač. Například můžete definovat název artefaktu, příkaz ke spuštění a parametry, které jsou k dispozici při spuštění příkazu. Podle názvu se mohou odkazovat na jiné soubory skriptu v souboru definice artefaktů.

## <a name="artifact-definition-file-format"></a>Formát souboru definice artefaktů
Následující příklad ukazuje oddíly, které tvoří základní strukturu souboru definice:

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
| $schema |Ne |Umístění souboru schématu JSON. Soubor schématu JSON můžete testovat platnost definičního souboru. |
| název |Ano |Název artefaktu, který se zobrazí v testovacím prostředí. |
| description |Ano |Popis artefaktu, který se zobrazí v testovacím prostředí. |
| iconUri |Ne |Identifikátor URI Ikona zobrazená v testovacím prostředí. |
| targetOsType |Ano |Operační systém virtuálního počítače, kde je nainstalován artefakt. Podporované možnosti jsou Windows a Linux. |
| parameters |Ne |Hodnoty, které jsou k dispozici při spuštění příkazu install artefaktů na počítači. Díky tomu můžete přizpůsobit vašich artefaktů. |
| runCommand |Ano |Artefakt instalační příkaz, který se spouští na virtuálním počítači. |

### <a name="artifact-parameters"></a>Parametry artefaktů
V sekci parametrů definičního souboru zadejte hodnoty, které uživatel může zadat při jejich instalaci artefakt. Mohou odkazovat na tyto hodnoty v příkazu install artefaktů.

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

* řetězec (libovolný platný řetězec formátu JSON)
* číslo (integer libovolný platný JSON)
* BOOL (libovolný platný JSON logická hodnota)
* pole (libovolný platný JSON array)

## <a name="artifact-expressions-and-functions"></a>Artefakt výrazy a funkce
Můžete použít výrazy a funkce k vytvoření artefaktu příkaz instalovat.
Výrazy jsou uzavřeny do závorek ([a]) a vyhodnocují při instalaci artefakt. Výrazy může vyskytovat kdekoli v řetězcové hodnotě JSON. Výrazy vždy vrátí jinou hodnotu JSON. Pokud budete muset použít řetězcový literál, který začíná závorky ([), musíte použít dva závorky ([[).
Obvykle použijete výrazy s využitím functions k vytvoření hodnoty. Stejně jako v jazyce JavaScript, volání funkce jsou formátovány jako **functionName (arg1, arg2, arg3)**.

Následující seznam uvádí běžné funkce:

* **parameters(parameterName)**: Vrátí hodnotu parametru, která je k dispozici při spuštění příkazu artefaktů.
* **concat (arg1, arg2, arg3,...)** : Kombinuje více řetězcových hodnot. Tuto funkci můžete využít širokou škálu argumenty.

Následující příklad ukazuje, jak používat výrazy a funkce k vytvoření hodnoty:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Vytvořit vlastní artefakt

1. Nainstalujte JSON editor. Potřebujete editor JSON pro práci se soubory definice artefaktů. Doporučujeme používat [Visual Studio Code](https://code.visualstudio.com/), které je dostupné pro Windows, Linuxu a OS X.
2. Získáte definici artifactfile.json ukázkový soubor. Podívejte se na artefakty vytvořené týmem DevTest Labs v našich [úložiště GitHub](https://github.com/Azure/azure-devtestlab). Vytvořili jsme bohatá knihovna artefakty, které vám pomůžou vytvářet vlastní artefakty. Stáhněte si soubor definice artefaktů a provádění změn, při vytváření vlastních artefaktů.
3. Využijte IntelliSense. Pomocí IntelliSense můžete zobrazit platné prvky, které můžete použít k vytvoření souboru definice artefaktů. Taky uvidíte různé možnosti pro hodnoty prvku. Například při úpravách **targetOsType** elementu, technologie IntelliSense zobrazí dvě možnosti pro Windows nebo Linux.
4. Store artefakt v [veřejného úložiště Git pro DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) nebo [úložiště Git](devtest-lab-add-artifact-repo.md). Ve veřejném úložišti můžete zobrazit artefakty, které sdílí ostatní, můžete použít přímo nebo přizpůsobit je tak, aby odpovídala vašim potřebám.
   
   1. Vytvořte samostatný adresář pro každý artefakt. Název adresáře by měl být stejný jako název artefaktu.
   2. Soubor definice artefaktu (artifactfile.json) Store do adresáře, který jste vytvořili.
   3. Store skripty, které jsou odkazovány z instalačního příkazu artefaktů.
      
      Tady je příklad toho, jak může vypadat složku artefaktu:
      
      ![Příklad složce artefaktů](./media/devtest-lab-artifact-author/git-repo.png)
5. Pokud používáte vlastního úložiště k ukládání artefaktů, přidejte úložiště do testovacího prostředí podle pokynů v následujícím článku: [Přidání úložiště Git pro artefakty a šablony](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Související články
* [Diagnostika selhání artefaktů ve službě DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojte se k virtuální počítač do existující domény služby Active Directory pomocí šablony Resource Manageru ve službě DevTest Labs](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další postup
* Zjistěte, jak [přidání úložiště artefaktů Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).
