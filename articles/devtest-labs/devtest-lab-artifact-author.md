---
title: Vytvoření vlastních artefaktů pro virtuální počítač s DevTest Labs | Microsoft Docs
description: Naučte se vytvářet artefakty pro použití s Azure DevTest Labs k nasazení a nastavení aplikací po zřízení virtuálního počítače.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 85acfcc3811e671e58fadab08a23951778e1323d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "88270678"
---
# <a name="create-custom-artifacts-for-your-devtest-labs-virtual-machine"></a>Vytvoření vlastních artefaktů pro virtuální počítač s DevTest Labs

V následujícím videu najdete přehled kroků popsaných v tomto článku:

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/how-to-author-custom-artifacts/player]
>
>

## <a name="overview"></a>Přehled
Můžete použít *artefakty* k nasazení a nastavení aplikace po zřízení virtuálního počítače. Artefakt se skládá z definičního souboru artefaktu a dalších souborů skriptu, které jsou uloženy ve složce v úložišti Git. Definiční soubory artefaktů se skládají z formátu JSON a výrazů, které můžete použít k určení toho, co chcete nainstalovat na virtuální počítač. Můžete například definovat název artefaktu, příkaz ke spuštění a parametry, které jsou k dispozici při spuštění příkazu. Můžete odkazovat na jiné soubory skriptu v rámci definičního souboru artefaktu podle názvu.

## <a name="artifact-definition-file-format"></a>Formát definičního souboru artefaktu
Následující příklad ukazuje oddíly, které tvoří základní strukturu definičního souboru:

```json
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
```

| Název elementu | Povinné? | Popis |
| --- | --- | --- |
| $schema |Ne |Umístění souboru schématu JSON. Soubor schématu JSON vám může přispět k otestování platnosti definičního souboru. |
| title |Ano |Název artefaktu zobrazeného v testovacím prostředí |
| description |Ano |Popis artefaktu zobrazeného v testovacím prostředí |
| iconUri |Ne |Identifikátor URI ikony zobrazené v testovacím prostředí |
| targetOsType |Ano |Operační systém virtuálního počítače, ve kterém je artefakt nainstalovaný. Podporované možnosti jsou Windows a Linux. |
| parameters |Ne |Hodnoty, které jsou k dispozici při spuštění instalačního příkazu artefaktu v počítači. To vám pomůže přizpůsobit svůj artefakt. |
| SpustitPříkaz |Ano |Instalační příkaz artefaktu, který je spuštěný na virtuálním počítači. |

### <a name="artifact-parameters"></a>Parametry artefaktů
V oddílu Parameters souboru definice určete, které hodnoty může uživatel zadat při instalaci artefaktu. Na tyto hodnoty můžete odkazovat v příkazu pro instalaci artefaktů.

Chcete-li definovat parametry, použijte následující strukturu:

```json
  "parameters": {
    "<parameterName>": {
      "type": "<type-of-parameter-value>",
      "displayName": "<display-name-of-parameter>",
      "description": "<description-of-parameter>"
    }
  }
```

| Název elementu | Povinné? | Popis |
| --- | --- | --- |
| typ |Ano |Typ hodnoty parametru Seznam povolených typů najdete v následujícím seznamu. |
| displayName |Ano |Název parametru, který se zobrazí uživateli v testovacím prostředí. |
| description |Ano |Popis parametru, který se zobrazí v testovacím prostředí. |

Povolené typy:

* řetězec (libovolný platný řetězec JSON)
* int (libovolné platné celé číslo JSON)
* bool (jakákoli platná logická hodnota JSON)
* Array (jakékoli platné pole JSON)

## <a name="secrets-as-secure-strings"></a>Tajné klíče jako zabezpečené řetězce
Deklarace tajných kódů jako zabezpečených řetězců. Tady je syntaxe pro deklaraci parametru zabezpečeného řetězce v `parameters` oddílu **artifactfile.js** souboru:

```json

    "securestringParam": {
      "type": "securestring",
      "displayName": "Secure String Parameter",
      "description": "Any text string is allowed, including spaces, and will be presented in UI as masked characters.",
      "allowEmpty": false
    },
```

Pro příkaz pro instalaci artefaktu spusťte PowerShellový skript, který převezme zabezpečený řetězec vytvořený pomocí příkazu ConvertTo-SecureString. 

```json
  "runCommand": {
    "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./artifact.ps1 -StringParam ''', parameters('stringParam'), ''' -SecureStringParam (ConvertTo-SecureString ''', parameters('securestringParam'), ''' -AsPlainText -Force) -IntParam ', parameters('intParam'), ' -BoolParam:$', parameters('boolParam'), ' -FileContentsParam ''', parameters('fileContentsParam'), ''' -ExtraLogLines ', parameters('extraLogLines'), ' -ForceFail:$', parameters('forceFail'), '\"')]"
  }
```

Kompletní příklad artifactfile.jsv a artifact.ps1 (skript PowerShellu) najdete v [této ukázce na GitHubu](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts/windows-test-paramtypes).

Dalším důležitým bodem, který je třeba poznamenat, není protokolovat tajné klíče do konzoly, protože výstup je zachycen pro ladění uživatele. 

## <a name="artifact-expressions-and-functions"></a>Výrazy a funkce artefaktů
Pomocí výrazů a funkcí můžete vytvořit příkaz pro instalaci artefaktu.
Výrazy jsou uzavřeny v závorkách ([a]) a jsou vyhodnocovány při instalaci artefaktu. Výrazy se můžou objevit kdekoli v hodnotě řetězce JSON. Výrazy vždycky vracejí jinou hodnotu JSON. Pokud potřebujete použít řetězcový literál, který začíná hranatou závorkou ([), musíte použít dvě hranaté závorky ([[).
Obvykle používáte výrazy s funkcemi k vytvoření hodnoty. Stejně jako v jazyce JavaScript jsou volání funkcí formátována jako **Functions (arg1, arg2, Arg3)**.

V následujícím seznamu jsou uvedeny běžné funkce:

* **Parameters (ParameterName)**: vrátí hodnotu parametru, která je k dispozici při spuštění příkazu artefaktu.
* **Concat (arg1, arg2, arg3,.....)**: kombinuje více řetězcových hodnot. Tato funkce může mít různé argumenty.

Následující příklad ukazuje, jak použít výrazy a funkce k vytvoření hodnoty:

```json
  runCommand": {
      "commandToExecute": "[concat('powershell.exe -ExecutionPolicy bypass \"& ./startChocolatey.ps1'
  , ' -RawPackagesList ', parameters('packages')
  , ' -Username ', parameters('installUsername')
  , ' -Password ', parameters('installPassword'))]"
  }
```

## <a name="create-a-custom-artifact"></a>Vytvoření vlastního artefaktu

1. Nainstalujte Editor JSON. Pro práci se soubory definice artefaktů potřebujete Editor JSON. Doporučujeme použít [Visual Studio Code](https://code.visualstudio.com/), který je k dispozici pro Windows, Linux a OS X.
2. Získejte vzorový artifactfile.jsv definičním souboru. Podívejte se na artefakty vytvořené týmem DevTest Labs v našem [úložišti GitHub](https://github.com/Azure/azure-devtestlab). Vytvořili jsme bohatou knihovnu artefaktů, které vám pomůžou vytvořit vlastní artefakty. Stáhněte soubor definice artefaktů a proveďte změny v něm, aby bylo možné vytvořit vlastní artefakty.
3. Využijte technologii IntelliSense. Použijte technologii IntelliSense k zobrazení platných prvků, které lze použít k vytvoření souboru definice artefaktů. Můžete také zobrazit různé možnosti pro hodnoty prvku. Například při úpravách prvku **targetOsType** vám IntelliSense zobrazí dvě volby, pro Windows nebo Linux.
4. Uložte artefakt ve [veřejném úložišti Git pro DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts) nebo [vlastní úložiště Git](devtest-lab-add-artifact-repo.md). Ve veřejném úložišti můžete zobrazit artefakty sdílené jinými uživateli, které můžete použít přímo, nebo je přizpůsobit podle svých potřeb.
   
   1. Vytvořte samostatný adresář pro každý artefakt. Název adresáře by měl být stejný jako název artefaktu.
   2. Uložte soubor definice artefaktu (artifactfile.json) do adresáře, který jste vytvořili.
   3. Uložte skripty, na které se odkazuje pomocí příkazu pro instalaci artefaktu.
      
      Tady je příklad, jak může složka artefaktů vypadat:
      
      ![Příklad složky artefaktů](./media/devtest-lab-artifact-author/git-repo.png)
5. Pokud používáte vlastní úložiště pro ukládání artefaktů, přidejte úložiště do testovacího prostředí podle pokynů v článku: [Přidání úložiště Git pro artefakty a šablony](devtest-lab-add-artifact-repo.md).

## <a name="related-articles"></a>Související články
* [Postup diagnostiky selhání artefaktů v DevTest Labs](devtest-lab-troubleshoot-artifact-failure.md)
* [Připojení virtuálního počítače k existující doméně služby Active Directory pomocí Správce prostředků šablony v DevTest Labs](https://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs)

## <a name="next-steps"></a>Další kroky
* Naučte se [Přidat úložiště artefaktů Git do testovacího prostředí](devtest-lab-add-artifact-repo.md).
