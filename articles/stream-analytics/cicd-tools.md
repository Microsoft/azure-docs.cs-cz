---
title: Automatizace sestavení, testů a nasazení Azure Stream Analytics úlohy pomocí nástrojů CI/CD
description: Tento článek popisuje, jak použít Azure Stream Analytics nástroje CI/CD pro automatické sestavení, testování a nasazení Azure Stream Analytics projektu.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.service: stream-analytics
ms.topic: how-to
ms.date: 09/10/2020
ms.openlocfilehash: 9e79e0a2c030e2ebfcd5ddfd49e7c05afdb0dc3c
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019546"
---
# <a name="automate-builds-tests-and-deployments-of-an-azure-stream-analytics-job-using-cicd-tools"></a>Automatizace sestavení, testů a nasazení Azure Stream Analytics úlohy pomocí nástrojů CI/CD

Balíček Azure Stream Analytics CI/CD npm můžete použít k automatickému sestavení, otestování a nasazení Azure Stream Analytics Visual Studio Code nebo projektů sady Visual Studio. Projekty lze vytvořit pomocí vývojářských nástrojů nebo je lze exportovat z existujících úloh Stream Analytics. Tento článek popisuje, jak použít balíček npm s jakýmkoli systémem CI/CD. Informace o nasazení pomocí Azure Pipelines najdete v tématu [použití Azure DevOps k vytvoření kanálu CI/CD pro Stream Analytics úlohu](set-up-cicd-pipeline.md).

## <a name="installation"></a>Instalace

Balíček si můžete [Stáhnout](https://www.npmjs.com/package/azure-streamanalytics-cicd) přímo nebo ho nainstalovat [globálně](https://docs.npmjs.com/downloading-and-installing-packages-globally) pomocí `npm install -g azure-streamanalytics-cicd` příkazu. Doporučujeme použít příkaz, který se dá použít taky v úloze skriptu PowerShellu nebo Azure CLI kanálu sestavení v **Azure Pipelines**.

## <a name="build-the-project"></a>Sestavení projektu

Balíček npm **ASA-streamanalytics-cicd** poskytuje nástroje pro generování Azure Resource Manager šablon Stream Analytics projektů [Visual Studio Code](./quick-create-visual-studio-code.md) nebo [projektů sady Visual Studio](stream-analytics-quick-create-vs.md). Balíček npm můžete také použít ve Windows, macOS a Linux bez instalace Visual Studio Code nebo Visual studia.

Po instalaci balíčku použijte následující příkaz k sestavení Stream Analyticsch projektů.

```powershell
azure-streamanalytics-cicd build -project <projectFullPath> [-outputPath <outputPath>]
```

Příkaz *sestavit* provede kontrolu syntaxe klíčového slova a vytvoří výstup šablony Azure Resource Manager.

| Parametr | Popis |
|---|---|
| `-project` | Absolutní cesta **asaproj.jsv** souboru pro projekt Visual Studio Code nebo **[název projektu]. asaproj** pro projekt sady Visual Studio. |
| `-outputPath` | Cesta k výstupní složce pro šablony Azure Resource Manager. Pokud není zadaný, šablony se umístí do aktuálního adresáře. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd build -project "/Users/username/projects/samplejob/samplejob.asaproj"
```

--- 

Po úspěšném sestavení Stream Analytics projektu vygeneruje následující dva soubory ve výstupní složce:

* Soubor šablony Azure Resource Manager

   `[ProjectName].JobTemplate.json`

* Soubor parametrů Azure Resource Manager

   `[ProjectName].JobTemplate.parameters.json`

Výchozí parametry v souboru parameters.json jsou z nastavení Visual Studio Code nebo projektu sady Visual Studio. Pokud chcete nasazení nasadit do jiného prostředí, nahraďte příslušné parametry odpovídajícím způsobem.

Výchozí hodnoty pro všechny přihlašovací údaje jsou **null**. Před nasazením do Azure budete muset nastavit hodnoty.

```json
"Input_EntryStream_sharedAccessPolicyKey": {
      "value": null
    },
```

Pokud chcete použít spravovanou identitu pro Azure Data Lake Store Gen1 jako výstupní jímku, musíte před nasazením do Azure poskytnout přístup k instančnímu objektu pomocí PowerShellu. Přečtěte si další informace o [nasazení adls Gen1 se správou identity pomocí šablony Správce prostředků](stream-analytics-managed-identities-adls.md#resource-manager-template-deployment).

## <a name="local-run"></a>Místní spuštění

Pokud projekt obsahuje zadané místní vstupní soubory, můžete spustit skript Stream Analytics místně pomocí `localrun` příkazu.

```powershell
azure-streamanalytics-cicd localrun -project <projectFullPath> [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parametr | Popis |
|---|---|
| `-project` | Cesta **asaproj.jsv** souboru pro projekt Visual Studio Code nebo **[název projektu]. asaproj** pro projekt sady Visual Studio. |
| `-outputPath` | Cesta k výstupní složce Pokud není zadaný, výstupní soubory výsledků se umístí do aktuálního adresáře. |
| `-customCodeZipFilePath` | Cesta k souboru zip pro vlastní kód v jazyce C#, jako je například UDF nebo deserializace, pokud se používají. Zabalit knihovny DLL do souboru zip a zadat tuto cestu. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd localrun -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```

---

> [!Note] 
> JavaScript UDF funguje jenom ve Windows.

## <a name="automated-test"></a>Automatizovaný test

Pomocí balíčku CI/CD npm můžete nakonfigurovat a spustit automatizované testy pro skript Stream Analytics.

### <a name="add-a-test-case"></a>Přidat testovací případ

Testovací případy jsou popsány v konfiguračním souboru testu. Chcete-li začít, použijte `addtestcase` příkaz pro přidání šablony testovacího případu do konfiguračního souboru testu. Pokud konfigurační soubor testu neexistuje, vytvoří se ve výchozím nastavení jeden.

```powershell
azure-streamanalytics-cicd addtestcase -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>]
```

| Parametr | Popis |
|---|---|
| `-project` | Cesta **asaproj.jsv** souboru pro projekt Visual Studio Code nebo **[název projektu]. asaproj** pro projekt sady Visual Studio. |
| `-testConfigPath` | Cesta ke konfiguračnímu souboru testu. Pokud není zadán, soubor bude prohledán v **\test** v aktuálním adresáři **asaproj.jsv** souboru s výchozím názvem souboru **testConfig.js**. Pokud neexistuje, vytvoří se nový soubor. |

#### <a name="visual-studio-code"></a>[Visual Studio Code](#tab/visual-studio-code)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/asaproj.json"
```

#### <a name="visual-studio"></a>[Visual Studio](#tab/visual-studio)

```powershell
azure-streamanalytics-cicd addtestcase -project "/Users/roger/projects/samplejob/samplejob.asaproj"
```
---

Pokud je konfigurační soubor testu prázdný, do souboru se zapíše následující obsah. V opačném případě je do pole **zjištěných testovacích případů** přidán testovací případ. Nezbytné vstupní konfigurace se automaticky vyplní podle vstupních konfiguračních souborů, pokud existují. V opačném případě jsou nakonfigurovány výchozí hodnoty. Před spuštěním testu musí být zadáno **FilePath** každého vstupu a očekávaný výstup. Konfiguraci můžete upravit ručně.

Pokud chcete, aby ověření testu ignorovalo určitý výstup, nastavte **požadované** pole s očekávaným výstupem na **hodnotu NEPRAVDA**.

```json
{
  "Script": "",
  "TestCases": [
    {
      "Name": "Case 1",
      "Inputs": [
        {
          "InputAlias": [Input alias string],
          "Type": "Data Stream",
          "Format": "JSON",
          "FilePath": [Required],
          "ScriptType": "InputMock"
        }
      ],
      "ExpectedOutputs": [
        {
          "OutputAlias": [Output alias string],
          "FilePath": "Required",
          "Required": true
        }
      ]
    }
  ]
}
```

### <a name="run-a-unit-test"></a>Spustit test jednotky

Pomocí následujícího příkazu můžete spustit více testovacích případů pro projekt. Ve výstupní složce se vygeneruje souhrn výsledků testů. Proces se ukončí s kódem **0** pro všechny předané testy; **-1** došlo k výjimce; **-2** pro testy selhaly.

```powershell
azure-streamanalytics-cicd test -project <projectFullPath> [-testConfigPath <testConfigFileFullPath>] [-outputPath <outputPath>] [-customCodeZipFilePath <zipFilePath>]
```

| Parametr | Popis |
|---|---|
| `-project` | Cesta **asaproj.jsv** souboru pro projekt Visual Studio Code nebo **[název projektu]. asaproj** pro projekt sady Visual Studio. |
| `-testConfigPath` | Cesta ke konfiguračnímu souboru testu. Pokud není zadán, soubor bude prohledán v **\test** v aktuálním adresáři **asaproj.jsv** souboru s výchozím názvem souboru **testConfig.js**.
| `-outputPath` | Cesta ke složce výstupu výsledků testu Pokud není zadaný, výstupní soubory výsledků se umístí do aktuálního adresáře. |
| `-customCodeZipFilePath` | Cesta k souboru zip pro vlastní kód, jako je například UDF nebo deserializace, pokud se používají. |

Po dokončení všech testů se ve výstupní složce vygeneruje souhrn výsledků testu ve formátu JSON. Soubor Shrnutí má název **testResultSummary.jsv**.

```json
{
  "Total": (integer) total_number_of_test_cases,
  "Passed": (integer) number_of_passed_test_cases,
  "Failed": (integer) number_of_failed_test_cases,
  "Script": (string) absolute_path_to_asaql_file,
  "Results": [ (array) detailed_results_of_test_cases
    {
      "Name": (string) name_of_test_case,
      "Status": (integer) 0(passed)_or_1(failed),
      "Time": (string) time_span_of_running_test_case,
      "OutputMatched": [ (array) records_of_actual_outputs_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputNotEqual": [ (array) records_of_actual_outputs_not_equal_to_expected_outputs
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputMissing": [ (array) records_of_actual_outputs_missing
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": ""
        }
      ],
      "OutputUnexpected": [ (array) records_of_actual_outputs_unexpected
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": "",
          "Output": (string) path_to_the_actual_output_file
        }
      ],
      "OutputUnrequired": [ (array) records_of_actual_outputs_unrequired_to_be_checked
        {
          "OutputAlias": (string) output_alias,
          "ExpectedOutput": (string) path_to_the_expected_output_file,
          "Output": (string) path_to_the_actual_output_file
        }
      ]
    }
  ],
  "Time": (string) time_span_of_running_all_test_cases,
}
```

## <a name="deploy-to-azure"></a>Nasazení do Azure

K [nasazení úlohy do Azure](../azure-resource-manager/templates/template-tutorial-use-parameter-file.md?tabs=azure-powershell#deploy-template)můžete použít šablonu Azure Resource Manager a soubory parametrů vygenerované z buildu.

## <a name="next-steps"></a>Další kroky

* [Průběžná integrace a průběžné nasazování pro Azure Stream Analytics](cicd-overview.md)
* [Nastavení kanálu CI/CD pro Stream Analytics úlohy pomocí Azure Pipelines](set-up-cicd-pipeline.md)