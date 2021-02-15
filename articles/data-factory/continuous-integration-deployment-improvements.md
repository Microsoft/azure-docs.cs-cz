---
title: Automatizované publikování pro průběžnou integraci a doručování
description: Přečtěte si, jak publikovat pro průběžnou integraci a doručování automaticky.
ms.service: data-factory
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: b5becd8ddaf74ab2acd059054a095ce9d21c178f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100366836"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Automatizované publikování pro průběžnou integraci a doručování

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Přehled

Nepřetržitá integrace je postup testování každé změny provedené v základu kódu automaticky a jakmile je možné průběžné doručování následovat za testováním, které se provádí během nepřetržité integrace a vkládání změn do pracovního nebo produkčního systému.

V Azure Data Factory průběžná integrace a doručování (CI/CD) znamená přesun Data Factory kanálů z jednoho prostředí (vývoj, testování, produkce) do jiného. Azure Data Factory využívá [šablony Azure Resource Manager](../azure-resource-manager/templates/overview.md) k uložení konfigurace různých entit ADF (kanálů, datových sad, toků dat atd.). Existují dva navrhované metody, jak propagovat datovou továrnu na jiné prostředí:

- Automatizované nasazení pomocí Data Factory integrace s [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Ručně nahrajte šablonu Správce prostředků pomocí integrace Data Factory UX s Azure Resource Manager.

Další informace najdete v tématu [průběžná integrace a doručování v Azure Data Factory](continuous-integration-deployment.md).

V tomto článku se zaměříme na vylepšení průběžného nasazování a funkci automatického publikování pro CI/CD.

## <a name="continuous-deployment-improvements"></a>Vylepšení průběžného nasazování

Funkce "automatizované publikování" přebírá funkce šablon *ověřování všech* a exportů z *Azure Resource Manager (ARM)* z uživatelského prostředí ADF a zpřístupňuje logiku prostřednictvím veřejně dostupného balíčku npm [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . To vám umožní programově aktivovat tyto akce místo nutnosti přejít do uživatelského rozhraní ADF a provést kliknutí na tlačítko. Tím umožníte kanálům CI/CD truer průběžnou integraci.

### <a name="current-cicd-flow"></a>Aktuální tok CI/CD

1. Každý uživatel provede změny ve svých privátních větvích.
2. Vložení do hlavní větve je zakázané, uživatelé musí pro provedení změn vytvořit pro hlavní stránku PR a.
3. Uživatelé musí načíst uživatelské rozhraní ADF a kliknutím na publikovat nasazovat změny Data Factory a generovat šablony ARM ve větvi publikování.
4. Kanál verze DevOps je nakonfigurovaný tak, aby vytvořil novou verzi, a nasadí šablonu ARM pokaždé, když se do větve publikování vloží nová změna.

![Aktuální tok CI/CD](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Manuální krok

V aktuálním toku CI/CD je uživatelské prostředí prostředník pro vytvoření šablony ARM, proto musí uživatel přejít do uživatelského rozhraní ADF a ručně kliknout na publikovat, aby se vytvořila šablona ARM a vynechal ji ve větvi publikování, což je bit napadení.

### <a name="the-new-cicd-flow"></a>Nový tok CI/CD

1. Každý uživatel provede změny ve svých privátních větvích.
2. Vložení do hlavní větve je zakázané, uživatelé musí pro provedení změn vytvořit pro hlavní stránku PR a.
3. **Sestavení kanálu Azure DevOps se aktivuje při každém provedení nového potvrzení hlavní, ověří prostředky a vygeneruje šablonu ARM jako artefakt, pokud je ověření úspěšné.**
4. Kanál verze DevOps je nakonfigurovaný tak, aby vytvořil novou verzi, a nasadí šablonu ARM pokaždé, když je k dispozici nové sestavení. 

![Nový tok CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Co se změnilo?

- Nyní máme proces sestavení s použitím kanálu sestavení DevOps.
- Kanál sestavení používá balíček ADFUtilities NPM, který ověří všechny prostředky a vygeneruje šablony ARM (jedna a propojená šablona).
- Kanál sestavení bude zodpovědný za ověřování prostředků ADF a místo uživatelského rozhraní ADF (tlačítko publikovat) a generuje šablonu ARM.
- Definice vydané verze DevOps teď bude tento nový kanál sestavení využívat místo artefaktu Git.

> [!NOTE]
> Můžete nadále používat existující mechanizmus (adf_publishovou větev) nebo použít nový tok. Obě jsou podporované. 

## <a name="package-overview"></a>Přehled balíčku

V balíčku jsou aktuálně k dispozici dva příkazy:
- Export šablony ARM
- Ověření

### <a name="export-arm-template"></a>Export šablony ARM

Spusťte příkaz npm spustit Export <rootFolder> <factoryId> [outputFolder] a EXPORTUJTE šablonu ARM pomocí prostředků dané složky. Tento příkaz spustí kontrolu ověření i před vygenerováním šablony ARM. Níže je příklad:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder je povinné pole, které představuje, kde jsou umístěny prostředky Data Factory.
- FactoryId je povinné pole, které představuje ID prostředku datové továrny ve formátu: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/Factories/ <dfName> ".
- OutputFolder je volitelný parametr, který určuje relativní cestu pro uložení vygenerované šablony ARM.
 
> [!NOTE]
> Vygenerovaná šablona ARM není publikovaná ve `Live` verzi objektu factory. Nasazení by se mělo provádět pomocí kanálu CI/CD. 
 

### <a name="validate"></a>Ověření

Spusťte příkaz npm spustit ověřování <rootFolder> <factoryId> a ověřte všechny prostředky dané složky. Níže je příklad:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder je povinné pole, které představuje, kde jsou umístěny prostředky Data Factory.
- FactoryId je povinné pole, které představuje ID prostředku datové továrny ve formátu: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /providers/Microsoft.DataFactory/Factories/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Vytvoření kanálu Azure

I když lze balíčky npm spotřebovat různými způsoby, jedna z hlavních výhod se spotřebovává prostřednictvím [kanálu Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). Při každém sloučení do vaší větve pro spolupráci je možné spustit kanál, který nejprve ověří veškerý kód a potom Exportuje šablonu ARM do [artefaktu sestavení](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) , který lze spotřebovat pomocí kanálu vydání. **Způsob, jakým se liší od aktuálního procesu CI/CD, je to, že kanál pro vydávání verzí bude odkazovat na tento artefakt místo na existující `adf_publish` větev.**

Pokud chcete začít, postupujte podle následujících kroků:

1.  Otevřete projekt Azure DevOps a pokračujte na kanály. Vyberte nový kanál.

    ![Nový kanál](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Vyberte úložiště, kam chcete uložit skript YAML kanálu. Doporučujeme ho uložit do složky *sestavení* v rámci stejného úložiště vašich prostředků ADF. Zajistěte, aby existovala **package.js** v souboru v úložišti, a zároveň obsahuje název balíčku (jak je uvedeno v následujícím příkladu).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Vyberte *Počáteční kanál*. Pokud jste nahráli nebo sloučili soubor YAML (jak je znázorněno v následujícím příkladu), můžete také přímo nasměrovat a upravit ho. 

    ![Počáteční kanál](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
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
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Zadejte kód YAML. Doporučujeme přebírat soubor YAML a používat ho jako výchozí bod.
5.  Uložte a spusťte. Při použití YAML se aktivuje při každém aktualizování "hlavní" větve.

## <a name="next-steps"></a>Další kroky

Další informace o průběžné integraci a doručování v Data Factory: 

- [Průběžná integrace a doručování v Azure Data Factory](continuous-integration-deployment.md).
