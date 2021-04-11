---
title: Spouštění automatizovaných testů pomocí Azurite
titleSuffix: Azure Storage
description: Naučte se psát automatizované testy proti soukromým koncovým bodům pro Azure Blob Storage pomocí Azurite.
services: storage
author: ikivanc
ms.service: storage
ms.devlang: python
ms.topic: how-to
ms.date: 03/31/2021
ms.author: ikivanc
ms.openlocfilehash: c4e8a11e0c46cb9a138a1a66060d9fdcc72c192e
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111571"
---
# <a name="run-automated-tests-by-using-azurite"></a>Spouštění automatizovaných testů pomocí Azurite

Naučte se psát automatizované testy proti soukromým koncovým bodům pro Azure Blob Storage pomocí emulátoru úložiště Azurite.

## <a name="run-tests-on-your-local-machine"></a>Spuštění testů na místním počítači

1. Instalace nejnovější verze [Pythonu](https://www.python.org/)

1. Nainstalovat [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/)

1. Nainstalujte a spusťte [Azurite](../common/storage-use-azurite.md):

   **Možnost 1:** K instalaci použijte npm a pak místně spusťte Azurite.

   ```bash
   # Install Azurite
   npm install -g azurite
   
   # Create an Azurite directory
   mkdir c:/azurite
   
   # Launch Azurite locally
   azurite --silent --location c:\azurite --debug c:\azurite\debug.log
   ```

   **Možnost 2:** Použití Docker ke spuštění Azurite

   ```bash
   docker run -p 10000:10000 mcr.microsoft.com/azure-storage/azurite azurite-blob --blobHost 0.0.0.0
   ```

1. V Průzkumník služby Azure Storage vyberte **připojit k místnímu emulátoru** .

    :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection.png" alt-text="Snímek obrazovky Průzkumník služby Azure Storage připojení ke zdroji Azure Storage":::

1. Zadejte **Zobrazovaný název** a číslo **portu objektů BLOB** pro připojení Azurite a použijte Průzkumník služby Azure Storage ke správě místního úložiště objektů BLOB.

   :::image type="content" source="media/use-azurite-to-run-automated-tests/blob-storage-connection-attach.png" alt-text="Snímek obrazovky Průzkumník služby Azure Storage připojení k místnímu emulátoru":::

1. Vytvoření virtuálního prostředí Pythonu

   ```bash
   python -m venv .venv
   ```

1. Vytvořte kontejner a inicializujte proměnné prostředí. K vygenerování testů použijte soubor [PyTest](https://docs.pytest.org/) [conftest.py](https://docs.pytest.org/en/2.1.0/plugins.html) . Tady je příklad souboru conftest.py:

   ```python
   from azure.storage.blob import BlobServiceClient
   import os

   def pytest_generate_tests(metafunc):
      os.environ['AZURE_STORAGE_CONNECTION_STRING'] = 'DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;'
      os.environ['STORAGE_CONTAINER'] = 'test-container'

      # Create a container for Azurite for the first run
      blob_service_client = BlobServiceClient.from_connection_string(os.environ.get("AZURE_STORAGE_CONNECTION_STRING"))
      try:
         blob_service_client.create_container(os.environ.get("STORAGE_CONTAINER"))
      except Exception as e:
         print(e)
   ```

   > [!NOTE]
   > Hodnota zobrazená jako `AZURE_STORAGE_CONNECTION_STRING` je výchozí hodnota pro Azurite, není to privátní klíč.

1. Nainstalovat závislosti uvedené v souboru [requirements.txt](https://github.com/Azure-Samples/automated-testing-with-azurite/blob/main/requirements.txt)

   ```bash
   pip install -r requirements.txt
   ```

1. Spustit testy:

   ```bash
   python -m pytest ./tests
   ```

Po spuštění testů můžete zobrazit soubory v úložišti objektů BLOB Azurite pomocí Průzkumník služby Azure Storage.

:::image type="content" source="media/use-azurite-to-run-automated-tests/http-local-blob-storage.png" alt-text="Snímek obrazovky Průzkumník služby Azure Storage znázorňující soubory vygenerované testy":::

## <a name="run-tests-on-azure-pipelines"></a>Spustit testy na Azure Pipelines

Po spuštění testů místně se ujistěte, že testy proběhnou [Azure Pipelines](/azure/devops/pipelines). Použijte bitovou kopii Docker Azurite jako hostovaného agenta v Azure nebo použijte npm k instalaci Azurite. Následující příklad Azure Pipelines používá npm k instalaci Azurite.
  
```yaml
trigger:
- master

steps:
- task: UsePythonVersion@0
  displayName: 'Use Python 3.7'
  inputs:
    versionSpec: 3.7

- bash: |
    pip install -r requirements_tests.txt
  displayName: 'Setup requirements for tests'
  
- bash: |
    sudo npm install -g azurite
    sudo mkdir azurite
    sudo azurite --silent --location azurite --debug azurite\debug.log &
  displayName: 'Install and Run Azurite'

- bash: |
    python -m pytest --junit-xml=unit_tests_report.xml --cov=tests --cov-report=html --cov-report=xml ./tests
  displayName: 'Run Tests'

- task: PublishCodeCoverageResults@1
  inputs:
    codeCoverageTool: Cobertura
    summaryFileLocation: '$(System.DefaultWorkingDirectory)/**/coverage.xml'
    reportDirectory: '$(System.DefaultWorkingDirectory)/**/htmlcov'

- task: PublishTestResults@2
  inputs:
    testResultsFormat: 'JUnit'
    testResultsFiles: '**/*_tests_report.xml'
    failTaskOnFailedTests: true
```

Po spuštění Azure Pipelines testů by se měl zobrazit výstup podobný tomuto:

:::image type="content" source="media/use-azurite-to-run-automated-tests/azure-pipeline.png" alt-text="Snímek obrazovky s Azure Pipelines výsledky testů":::

## <a name="next-steps"></a>Další kroky

- [Použití emulátoru Azurite pro vývoj místních Azure Storage](../common/storage-use-azurite.md)
- [Ukázka: použití Azurite ke spouštění testů BLOB Storage v kanálu Azure DevOps](https://github.com/Azure-Samples/automated-testing-with-azurite)
