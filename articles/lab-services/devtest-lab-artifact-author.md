---
title: Vytvoření vlastních artefaktů pro váš virtuální počítač DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak vytvářet vlastní artefakty, které se mají používat v laboratořích Azure DevTest Labs.
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
ms.date: 05/30/2019
ms.author: spelluru
ms.openlocfilehash: 69b83590fb9b25c68d231b732b985ba633bb6884
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66399205"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Vytvoření vlastních artefaktů pro váš virtuální počítač DevTest Labs

Podívejte se na následující video, kde najdete přehled kroků popsaných v tomto článku:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Přehled
*Artefakty* můžete použít k nasazení a nastavení aplikace po zřízení virtuálního počítače. Artefakt se skládá z definičního souboru artefaktu a dalších souborů skriptů, které jsou uloženy ve složce v úložišti Git. Soubory definice artefaktů se skládají z JSON a výrazů, které můžete použít k určení, co chcete nainstalovat na virtuální počítač. Můžete například definovat název artefaktu, příkaz ke spuštění a parametry, které jsou k dispozici při spuštění příkazu. Můžete odkazovat na jiné soubory skriptů v souboru definice artefaktu podle názvu.

## <a name="artifact-definition-file-format"></a>Formát souboru definice artefaktu
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
| $schema |Ne |Umístění souboru schématu JSON. Soubor schématu JSON vám může pomoci otestovat platnost definičního souboru. |
| title |Ano |Název artefaktu zobrazeného v laboratoři. |
| description |Ano |Popis artefaktu zobrazeného v laboratoři. |
| iconUri |Ne |Identifikátor URI ikony zobrazené v testovacím prostředí. |
| targetOsType |Ano |Operační systém virtuálního zařízení, kde je artefakt nainstalován. Podporované možnosti jsou Windows a Linux. |
| parameters |Ne |Hodnoty, které jsou k dispozici při spuštění příkazu instalace artefaktu v počítači. To vám pomůže přizpůsobit artefakt. |
| Spustitpříkaz |Ano |Příkaz pro instalaci artefaktů, který se spouští na virtuálním počítači. |

### <a name="artifact-parameters"></a>Parametry artefaktů
V části parametry definičního souboru určete, které hodnoty může uživatel zadat při instalaci artefaktu. Na tyto hodnoty můžete odkazovat v příkazu instalace artefaktu.

Chcete-li definovat parametry, použijte následující strukturu:

    "parameters": {
      "<parameterName>": {
        "type": "<type-of-parameter-value>",
        "displayName": "<display-name-of-parameter>",
        "description": "<description-of-parameter>"
      }
    }

| Název elementu | Povinné? | Popis |
| --- | --- | --- |
| type |Ano |Typ hodnoty parametru. Povolené typy naleznete v následujícím seznamu. |
| displayName |Ano |Název parametru, který se zobrazí uživateli v testovacím prostředí. |
| description |Ano |Popis parametru, který je zobrazen v testovacím prostředí. |

Povolené typy jsou:

* řetězec (libovolný platný řetězec JSON)
* int (libovolné platné celé číslo JSON)
* bool (jakýkoliv platný JSON Boolean)
* array (libovolné platné pole JSON)

## <a name="secrets-as-secure-strings"></a>Tajné klíče jako zabezpečené řetězce
Deklarujte tajné kódy jako zabezpečené řetězce. Zde je syntaxe pro deklarování `parameters` parametru zabezpečeného řetězce v části souboru **artifactfile.json:**

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Pro příkaz pro instalaci artefaktu spusťte skript prostředí PowerShell, který přebírá zabezpečený řetězec vytvořený pomocí příkazu ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Úplný příklad artefaktfile.json a artefakt.ps1 (skript Prostředí PowerShell), naleznete [v této ukázce na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Dalším důležitým bodem na vědomí, není protokolovat tajné kódy do konzoly jako výstup je zachycen pro ladění uživatele. 

## <a name="artifact-expressions-and-functions"></a>Artefaktové výrazy a funkce
Výrazy a funkce můžete použít k vytvoření příkazu instalace artefaktu.
Výrazy jsou uzavřeny s závorkami ([ a ]) a jsou vyhodnoceny při instalaci artefaktu. Výrazy se mohou objevit kdekoli v řetězcové hodnotě JSON. Výrazy vždy vrátí jinou hodnotu JSON. Pokud potřebujete použít literál řetězec, který začíná závorkou ([), musíte použít dvě závorky ([[].
Obvykle používáte výrazy s funkcemi k vytvoření hodnoty. Stejně jako v JavaScriptu jsou volání funkcí formátována jako **functionName (arg1, arg2, arg3)**.

V následujícím seznamu jsou uvedeny běžné funkce:

* **parameters(parameterName)**: Vrátí hodnotu parametru, která je k dispozici při spuštění příkazu artefaktu.
* **concat(arg1, arg2, arg3,..... )**: Kombinuje více řetězcových hodnot. Tato funkce může trvat různé argumenty.

Následující příklad ukazuje, jak používat výrazy a funkce k vytvoření hodnoty:

    runCommand": {
        "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
    , ' -RawPackagesList ', parameters('packages')
    , ' -Username ', parameters('installUsername')
    , ' -Password ', parameters('installPassword'))]"
    }

## <a name="create-a-custom-artifact"></a>Vytvoření vlastního artefaktu

1. Nainstalujte editor JSON. Pro práci s definičními soubory artefaktů potřebujete editor JSON. Doporučujeme používat [Visual Studio Code](https://code.visualstudio.com/), který je k dispozici pro Windows, Linux a OS X.
2. Získejte ukázkový soubor definice artefaktfile.json. Podívejte se na artefakty vytvořené týmem DevTest Labs v našem [úložišti GitHub](https://github.com/Azure/azure-devtestlab). Vytvořili jsme bohatou knihovnu artefaktů, které vám pomohou vytvořit si vlastní artefakty. Stáhněte si soubor definice artefaktu a proveďte v něm změny a vytvořte si vlastní artefakty.
3. Využijte technologie IntelliSense. Pomocí technologie IntelliSense můžete zobrazit platné prvky, které můžete použít k vytvoření souboru definice artefaktu. Můžete také zobrazit různé možnosti pro hodnoty prvku. Například při úpravě **targetOsType** element, IntelliSense vám ukáže dvě možnosti, pro Windows nebo Linux.
4. Uložte artefakt do [veřejného úložiště Git pro DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) nebo [vlastní úložiště Git](devtest-lab-add-artifact-repo.md). Ve veřejném úložišti můžete zobrazit artefakty sdílené ostatními uživateli, které můžete použít přímo, nebo je přizpůsobit tak, aby vyhovovaly vašim potřebám.
   
   1. Vytvořte samostatný adresář pro každý artefakt. Název adresáře by měl být stejný jako název artefaktu.
   2. Uložte soubor definice artefaktu (artifactfile.json) do adresáře, který jste vytvořili.
   3. Uložte skripty, na které odkazuje příkaz instalace artefaktu.
      
      Zde je příklad, jak může vypadat složka artefaktů:
      
      ![Příklad složky artefaktů](./media/devtest-lab-artifact-author/git-repo.png)
5. Pokud k ukládání artefaktů používáte vlastní úložiště, přidejte úložiště do testovacího prostředí podle následujících pokynů v článku: [Přidejte úložiště Git pro artefakty a šablony](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Související články
* [Jak diagnostikovat selhání artefaktů v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí šablony Správce prostředků v devTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak [přidat úložiště artefaktů Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).
