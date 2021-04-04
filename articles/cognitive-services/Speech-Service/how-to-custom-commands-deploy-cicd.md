---
title: Průběžné nasazování pomocí Azure DevOps (Preview)
titleSuffix: Azure Cognitive Services
description: V tomto článku se dozvíte, jak nastavit průběžné nasazování pro aplikace s vlastními příkazy. Vytvoříte skripty pro podporu pracovních postupů průběžného nasazování.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: 686e342eb68e2e5c97bdbae9cd11bdff5819b5d6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98927596"
---
# <a name="continuous-deployment-with-azure-devops"></a>Průběžné nasazování s využitím Azure DevOps

V tomto článku se dozvíte, jak nastavit průběžné nasazování pro aplikace s vlastními příkazy. K dispozici jsou skripty pro podporu pracovního postupu CI/CD.

## <a name="prerequisite"></a>Požadavek
> [!div class = "checklist"]
> * Aplikace pro vlastní příkazy pro vývoj (vývoj)
> * Vlastní aplikace příkazů pro produkci (PROD)
> * Zaregistrujte se [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-sign-up)

## <a name="exportimportpublish"></a>Export/Import/publikování

Skripty se hostují v [Cognitive Services hlasového asistenta – vlastní příkazy](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Naklonujte skripty v adresáři bash do svého úložiště. Ujistěte se, že jste zachovali stejnou cestu.

### <a name="set-up-a-pipeline"></a>Nastavení kanálu 

1. Přejděte na **Azure DevOps-Pipelines** a klikněte na nový kanál.
1. V části **připojit** vyberte umístění úložiště, ve kterém se tyto skripty nacházejí.
1. V části **Vybrat** vyberte úložiště.
1. V části **Konfigurovat** vyberte počáteční kanál.
1. V dalším kroku získáte Editor se souborem YAML a tímto skriptem nahradíte oddíl "Steps".

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Všimněte si, že tyto skripty předpokládají, že používáte oblast, v opačném `westus2` případě neaktualizují argumenty úkolů odpovídajícím způsobem.

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky, který zvýrazní hodnotu oblasti v argumentech.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. V tlačítku Uložit a spustit otevřete rozevírací seznam a klikněte na Uložit.

### <a name="hook-up-the-pipeline-with-your-application"></a>Zapojení kanálu s aplikací

1. Přejděte na hlavní stránku kanálu.
1. V rozevíracím seznamu pravého horního rohu vyberte **Upravit kanál**. Připravuje se k editoru YAML. 
1. V pravém horním rohu vedle tlačítka Spustit vyberte **proměnné**. Klikněte na **Nová proměnná**.
1. Přidejte tyto proměnné:
    
    | Proměnná | Popis |
    | ------- | --------------- | ----------- |
    | SourceAppId | ID aplikace pro vývojáře |
    | TargetAppId | ID aplikace VÝROBNÍho programu |
    | SubscriptionKey | Klíč předplatného použitý pro obě aplikace |
    | Kultura | Jazyková verze aplikací (tj. en-us) |

    > [!div class="mx-imgBorder"]
    > ![Datová část aktivity odeslání](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Klikněte na run (spustit) a pak klikněte na spuštění úlohy. 

    Měl by se zobrazit seznam spuštěných úloh, které obsahují: "Export zdrojové aplikace", "Import do cílové aplikace" & "výukové a publikační cílovou aplikaci"

## <a name="deploy-from-source-code"></a>Nasazení ze zdrojového kódu

V případě, že chcete zachovat definici aplikace v úložišti, poskytujeme skripty pro nasazení ze zdrojového kódu. Vzhledem k tomu, že se skripty nacházejí v bash a používáte Windows, budete muset nainstalovat [subsystém Linux](/windows/wsl/install-win10).

Skripty se hostují v [Cognitive Services hlasového asistenta – vlastní příkazy](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Naklonujte skripty v adresáři bash do svého úložiště. Ujistěte se, že jste zachovali stejnou cestu.

### <a name="prepare-your-repository"></a>Příprava úložiště

1. Vytvořte adresář pro vaši aplikaci, v našem příkladu vytvoříte jednu s názvem "aplikace".
1. Aktualizujte argumenty bash skriptu níže a spusťte příkaz. Naimportuje model dialogu vaší aplikace do souboru myapp.js
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumenty | Description |
    | ------- | --------------- | ----------- |
    | oblast | oblast aplikace, například westus2. |
    | subscriptionkey | klíč předplatného prostředku řeči |
    | identifikátor | Vlastní příkazy ID aplikace, kterou chcete exportovat. |

1. Dodejte tyto změny do úložiště.

### <a name="set-up-a-pipeline"></a>Nastavení kanálu 

1. Přejděte na **Azure DevOps-Pipelines** a klikněte na nový kanál.
1. V části **připojit** vyberte umístění úložiště, ve kterém se tyto skripty nacházejí.
1. V části **Vybrat** vyberte úložiště.
1. V části **Konfigurovat** vyberte počáteční kanál.
1. V dalším kroku získáte Editor se souborem YAML a tímto skriptem nahradíte oddíl "Steps".

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > Tyto skripty předpokládají, že používáte oblast westus2, v opačném případě neaktualizují argumenty úkolů odpovídajícím způsobem.

1. V tlačítku Uložit a spustit otevřete rozevírací seznam a klikněte na Uložit.

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Zapojení kanálu s cílovými aplikacemi

1. Přejděte na hlavní stránku kanálu.
1. V rozevíracím seznamu pravého horního rohu vyberte **Upravit kanál**. Připravuje se k editoru YAML. 
1. V pravém horním rohu vedle tlačítka Spustit vyberte **proměnné**. Klikněte na **Nová proměnná**.
1. Přidejte tyto proměnné:

    | Proměnná | Popis |
    | ------- | --------------- | ----------- |
    | TargetAppId | ID aplikace VÝROBNÍho programu |
    | SubscriptionKey | Klíč předplatného použitý pro obě aplikace |
    | Kultura | Jazyková verze aplikací (tj. en-us) |

1. Klikněte na run (spustit) a pak klikněte na spuštění úlohy.
    Měl by se zobrazit seznam spuštěných úloh, které obsahují: "Import aplikace" & "výuková a publikační aplikace"

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Zobrazit ukázky na GitHubu](https://aka.ms/speech/cc-samples)
