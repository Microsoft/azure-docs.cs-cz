---
title: Automatizované publikování pro průběžnou integraci a doručování
description: Přečtěte si, jak publikovat pro průběžnou integraci a doručování automaticky.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 496d2b6b3d669013c8174e9bc961d0a2f640bed3
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "103462078"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatizované publikování pro průběžnou integraci a doručování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Přehled

Kontinuální integrace spočívá v automatickém testování všech změn kódové báze. Co nejdříve se průběžné doručování řídí testováním, které se provádí během nepřetržité integrace a vkládání změn do pracovního nebo produkčního systému.

V Azure Data Factory průběžná integrace a průběžné doručování (CI/CD) znamená přesunutí Data Factory kanálů z jednoho prostředí, jako je vývoj, testování a produkce, do jiné. Data Factory používá [šablony Azure Resource Manager (šablony ARM)](../azure-resource-manager/templates/overview.md) k uložení konfigurace různých entit Data Factory, jako jsou kanály, datové sady a toky dat.

Existují dva navrhované metody, jak propagovat datovou továrnu na jiné prostředí:

- Automatizované nasazení pomocí integrace Data Factory s [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ruční nahrání šablony ARM pomocí Data Factory Integrace uživatelského rozhraní s Azure Resource Manager.

Další informace najdete v tématu [průběžná integrace a doručování v Azure Data Factory](continuous-integration-deployment.md).

Tento článek se zaměřuje na vylepšení průběžného nasazování a funkci automatického publikování pro CI/CD.

## <a name="continuous-deployment-improvements"></a>Vylepšení průběžného nasazování

Funkce automatického publikování přijímá z Data Factory uživatelského prostředí funkce pro **ověřování všech** a **exportů šablon ARM** a zpřístupňuje logiku prostřednictvím veřejně dostupného balíčku npm [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Z tohoto důvodu můžete programově aktivovat tyto akce, aniž byste museli přecházet do uživatelského rozhraní Data Factory a vybírat tlačítko ručně. Díky této možnosti budou kanály CI/CD poskytovat truer prostředí průběžné integrace.

### <a name="current-cicd-flow"></a>Aktuální tok CI/CD

1. Každý uživatel provede změny ve svých privátních větvích.
1. Vložení do hlavní větve není povoleno. Uživatelé musí vytvořit žádost o získání dat, aby mohli provádět změny.
1. Uživatelé musí načítat Data Factory uživatelské rozhraní a vybrat **publikovat** , aby se změny nasadily Data Factory a vygenerovaly šablony ARM ve větvi publikování.
1. Kanál verze DevOps je nakonfigurovaný tak, aby vytvořil novou verzi, a nasadí šablonu ARM pokaždé, když se do větve publikování vloží nová změna.

![Diagram, který zobrazuje aktuální tok CI/CD.](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuální krok

V aktuálním toku CI/CD je činnost koncového uživatele zprostředkujícím vytvořením šablony ARM. V důsledku toho musí uživatel přejít do uživatelského rozhraní Data Factory a ručně vybrat **publikovat** , aby se spustilo generování šablony ARM a vynechal ho ve větvi publikování.

### <a name="the-new-cicd-flow"></a>Nový tok CI/CD

1. Každý uživatel provede změny ve svých privátních větvích.
1. Vložení do hlavní větve není povoleno. Uživatelé musí vytvořit žádost o získání dat, aby mohli provádět změny.
1. Sestavení kanálu služby Azure DevOps se aktivuje při každém provedení nového potvrzení hlavnímu serveru. Ověřuje prostředky a vygeneruje šablonu ARM jako artefakt, pokud je ověření úspěšné.
1. Kanál verze DevOps je nakonfigurovaný tak, aby vytvořil novou verzi, a nasadí šablonu ARM pokaždé, když je k dispozici nové sestavení.

![Diagram, který zobrazuje nový tok CI/CD.](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Co se změnilo?

- Nyní máme proces sestavení, který používá kanál sestavení DevOps.
- Kanál sestavení používá balíček ADFUtilities NPM, který bude ověřovat všechny prostředky a generovat šablony ARM. Tyto šablony můžou být jednoduché a propojené.
- Kanál sestavení zodpovídá za ověřování prostředků Data Factory a místo uživatelského rozhraní Data Factory (tlačítko **publikovat** ), vygeneruje šablonu ARM.
- Definice vydaných verzí DevOps teď bude tento nový kanál sestavení využívat místo artefaktu Git.

> [!NOTE]
> Můžete nadále používat stávající mechanizmus, což je `adf_publish` větev, nebo můžete použít nový tok. Obě jsou podporované.

## <a name="package-overview"></a>Přehled balíčku

V balíčku jsou aktuálně k dispozici dva příkazy:

- Export šablony ARM
- Ověření

### <a name="export-arm-template"></a>Export šablony ARM

Spuštěním `npm run start export <rootFolder> <factoryId> [outputFolder]` vyexportujte šablonu ARM pomocí prostředků dané složky. Tento příkaz také spustí kontrolu ověření před vytvořením šablony ARM. Tady je příklad:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- `RootFolder` je povinné pole, které představuje, kde se nacházejí Data Factory prostředky.
- `FactoryId` je povinné pole, které představuje ID prostředku Data Factory ve formátu `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .
- `OutputFolder` je volitelný parametr, který určuje relativní cestu pro uložení vygenerované šablony ARM.
 
> [!NOTE]
> Vygenerovaná šablona ARM není publikovaná do živé verze objektu factory. Nasazení by se mělo provádět pomocí kanálu CI/CD.
 
### <a name="validate"></a>Ověření

Spusťte příkaz `npm run start validate <rootFolder> <factoryId>` k ověření všech prostředků dané složky. Tady je příklad:

```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- `RootFolder` je povinné pole, které představuje, kde se nacházejí Data Factory prostředky.
- `FactoryId` je povinné pole, které představuje ID prostředku Data Factory ve formátu `/subscriptions/<subId>/resourceGroups/<rgName>/providers/Microsoft.DataFactory/factories/<dfName>` .

## <a name="create-an-azure-pipeline"></a>Vytvoření kanálu Azure

I když lze balíčky npm spotřebovat různými způsoby, jedna z hlavních výhod se spotřebovává prostřednictvím [kanálu Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Při každém sloučení do vaší větve pro spolupráci je možné spustit kanál, který nejprve ověří veškerý kód a potom Exportuje šablonu ARM do [artefaktu sestavení](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) , který lze spotřebovat pomocí kanálu vydání. Způsob, jakým se liší od aktuálního procesu CI/CD, je to, že *kanál pro vydávání verzí bude odkazovat na tento artefakt místo na existující `adf_publish` větev*.

Začněte těmito kroky:

1.  Otevřete projekt Azure DevOps a pokračujte na **kanály**. Vyberte **Nový kanál**.

    ![Snímek obrazovky zobrazující tlačítko nového kanálu](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
1.  Vyberte úložiště, kam chcete uložit skript kanálu YAML. Doporučujeme ho uložit do složky sestavení ve stejném úložišti vašich Data Factorych prostředků. Zajistěte, aby existovala *package.js* v souboru v úložišti, které obsahuje název balíčku, jak je znázorněno v následujícím příkladu:

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.3"
        }
    } 
    ```
    
1.  Vyberte **Počáteční kanál**. Pokud jste nahráli nebo sloučili soubor YAML, jak je znázorněno v následujícím příkladu, můžete také přímo odkazovat na sebe a upravit.

    ![Snímek obrazovky, který ukazuje počáteční kanál.](media/continuous-integration-deployment-improvements/starter-pipeline.png)

    ```yaml
    # Sample YAML file to validate and export an ARM template into a build artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the Data Factory resources in the repository. You'll get the same validation errors as when "Validate All" is selected.
    # Enter the appropriate subscription and name for the source factory.
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder, which is the same as selecting "Publish" from the UX.
    # The ARM template generated isn't published to the live version of the factory. Deployment should be done by using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the artifact to be used as a source for a release pipeline.
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

1.  Zadejte svůj kód YAML. Doporučujeme použít soubor YAML jako výchozí bod.
1.  Uložte a spusťte. Pokud jste YAML použili, spustí se při každém aktualizaci hlavní větve.

## <a name="next-steps"></a>Další kroky

Další informace o průběžné integraci a doručování v Data Factory:

- [Průběžná integrace a doručování v Azure Data Factory](continuous-integration-deployment.md).
