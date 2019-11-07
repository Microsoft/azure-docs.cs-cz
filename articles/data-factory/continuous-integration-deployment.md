---
title: Průběžná integrace a doručování v Azure Data Factory
description: Naučte se používat průběžnou integraci a doručování k přesunu Data Factory kanálů z jednoho prostředí (vývoj, testování, produkce) do jiného.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 08/14/2019
ms.openlocfilehash: 7c5c1e91e97087bf28b03629659e5194f67c22b3
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/06/2019
ms.locfileid: "73680031"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Průběžná integrace a doručování (CI/CD) v Azure Data Factory

## <a name="overview"></a>Přehled

Nepřetržitá integrace je postup testování každé změny v základu kódu, a to co nejdříve. Průběžné doručování se řídí testováním, které se provádí během nepřetržité integrace a vkládání změn do pracovního nebo produkčního systému.

V Azure Data Factory průběžná integrace & doručována znamená přesunutí Data Factory kanálů z jednoho prostředí (vývoj, testování, produkce) do jiného. Pokud chcete průběžnou integraci & doručování, můžete použít integraci Data Factory UX s Azure Resource Managermi šablonami. Data Factory UX může vytvořit šablonu Správce prostředků z rozevírací nabídky **šablony ARM** . Když vyberete **Exportovat šablonu ARM**, portál vygeneruje šablonu správce prostředků pro objekt pro vytváření dat a konfigurační soubor, který obsahuje všechny řetězce připojení a další parametry. Pak můžete vytvořit jeden konfigurační soubor pro každé prostředí (vývoj, testování, produkce). Hlavní soubor šablony Správce prostředků zůstává stejný pro všechna prostředí.

Po devět minut Úvod a ukázku této funkce se podívejte na toto video:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Životní cyklus nepřetržité integrace

Níže je ukázkový Přehled životního cyklu průběžná integrace a doručování v datové továrně Azure nakonfigurované pomocí Azure Repos Git. Další informace o tom, jak nakonfigurovat úložiště Git, najdete [v tématu Správa zdrojového kódu v Azure Data Factory](source-control.md).

1.  Vývojovou datovou továrnu vytvoří a nakonfiguruje s Azure Repos Git, kde všichni vývojáři mají oprávnění vytvářet Data Factory prostředky, jako jsou kanály a datové sady.

1.  Vzhledem k tomu, že vývojáři provádějí změny ve své větvi funkčních větví, ladí jejich kanál s nejnovějšími změnami. Další informace o tom, jak ladit spuštění kanálu, najdete v tématu [iterativní vývoj a ladění pomocí Azure Data Factory](iterative-development-debugging.md).

1.  Jakmile se vývojářům splní jejich změny, vytvoří žádost o přijetí změn ze své větve funkce do hlavní větve nebo do větve pro spolupráci, aby bylo možné jejich změny zkontrolovat v partnerských vztazích.

1.  Po schválení žádosti o přijetí změn a jejich sloučení do hlavní větve se můžou publikovat do továrny pro vývoj.

1.  Když je tým připraven k nasazení změn do testovací továrny a následně do produkční továrny, exportujte šablonu Správce prostředků z hlavní větve.

1.  Exportovaná šablona Správce prostředků bude nasazena s různými soubory parametrů do testovací továrny a produkční továrny.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Vytvoření šablony Správce prostředků pro každé prostředí

V rozevíracím seznamu **šablon ARM** vyberte **Exportovat šablonu ARM** a exportujte šablonu správce prostředků pro vaši datovou továrnu ve vývojovém prostředí.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

V továrnách testovacích a produkčních dat vyberte **Importovat šablonu ARM**. Tato akce přejde do Azure Portal, kde můžete importovat exportovanou šablonu. Vyberte **vytvořit vlastní šablonu v editoru** a otevřete tak editor šablon Správce prostředků.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Klikněte na **načíst soubor** a vyberte vygenerovanou šablonu správce prostředků.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

V podokně nastavení zadejte hodnoty konfigurace, například přihlašovací údaje k propojeným službám. Až budete hotovi, kliknutím na **koupit** nasaďte šablonu správce prostředků.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Připojovací řetězce

Informace o tom, jak nakonfigurovat připojovací řetězce, najdete v článku o jednotlivých konektorech. Například pro Azure SQL Database, přečtěte si téma [kopírování dat do nebo z Azure SQL Database pomocí Azure Data Factory](connector-azure-sql-database.md). Chcete-li ověřit připojovací řetězec, můžete otevřít zobrazení kódu pro prostředek v prostředí Data Factoryho uživatelského rozhraní. V zobrazení kódu je odstraněna část hesla nebo klíč účtu v připojovacím řetězci. Chcete-li otevřít zobrazení kódu, vyberte ikonu zvýrazněnou na následujícím snímku obrazovky.

![Otevřete zobrazení kódu a podívejte se na připojovací řetězec.](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatizace kontinuální integrace s Azure Pipelinesmi verzemi

Níže je uvedený Průvodce nastavením verze Azure Pipelines, která automatizuje nasazení datové továrny do více prostředí.

![Diagram kontinuální integrace s Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Požadavky

-   Předplatné Azure propojené s Team Foundation Server nebo Azure Repos pomocí [koncového bodu služby Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Data Factory nakonfigurovaná s Azure Repos Integration Git.

-    [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) obsahující tajná klíčová pole pro každé prostředí.

### <a name="set-up-an-azure-pipelines-release"></a>Nastavení verze Azure Pipelines

1.  V [uživatelském prostředí Azure DevOps](https://dev.azure.com/)otevřete projekt nakonfigurovaný s vaším Data Factory.

1.  Na levé straně stránky klikněte na **kanály** a pak vyberte **vydání**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Vyberte **Nový kanál** , nebo pokud máte kanály, **nové**a pak **Nový kanál verze**.

1.  Vyberte **prázdnou šablonu úlohy** .

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Do pole **název fáze** zadejte název vašeho prostředí.

1.  Vyberte **Přidat artefakt**a vyberte stejné úložiště nakonfigurované s vaším Data Factory. Vyberte možnost `adf_publish` jako výchozí větev s nejnovější výchozí verzí.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Přidat úlohu nasazení Azure Resource Manager:

    a.  V zobrazení fáze klikněte na odkaz **Zobrazit úlohy fáze** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Vytvoří nový úkol. Vyhledejte **nasazení skupiny prostředků Azure**a klikněte na **Přidat**.

    c.  V úloze nasazení vyberte předplatné, skupinu prostředků a umístění pro cílový Data Factory a v případě potřeby zadejte přihlašovací údaje.

    d.  V rozevíracím seznamu akce vyberte **vytvořit nebo aktualizovat skupinu prostředků**.

    e.  Vybrat **...** v poli **Šablona** . Vyhledejte šablonu Azure Resource Manager vytvořením pomocí kroku **Import šablony ARM** v části [Vytvoření šablony Resource Manageru pro každé prostředí](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Vyhledejte tento soubor ve složce `<FactoryName>` `adf_publish` větvi.

    f.  Vybrat **...** v **poli parametry šablony.** pro výběr souboru parametrů. V závislosti na tom, zda jste vytvořili kopii nebo jste použili výchozí soubor *ARMTemplateParametersForFactory. JSON*, vyberte správný soubor.

    g.  Vybrat **...** vedle pole **přepsat parametry šablony** a vyplňte informace pro cílový Data Factory. Pro přihlašovací údaje, které pocházejí z trezoru klíčů, zadejte název tajného kódu mezi dvojité uvozovky. Pokud je například název tajného klíče `cred1`, zadejte pro jeho hodnotu `"$(cred1)"`.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Vyberte režim **přírůstkového** nasazení.

    > [!WARNING]
    > Pokud vyberete **kompletní** režim nasazení, můžou se odstranit existující prostředky, včetně všech prostředků v cílové skupině prostředků, které nejsou definované v šabloně správce prostředků.

1.  Uložte kanál pro vydávání verzí.

1. Pokud chcete aktivovat vydanou verzi, klikněte na **vytvořit vydání** .

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Získání tajných kódů z Azure Key Vault

Pokud máte tajné kódy, které byste měli předat do šablony Azure Resource Manager, doporučujeme používat Azure Key Vault s Azure Pipelinesou verzí.

Existují dva způsoby, jak pokládat s tajnými kódy:

1.  Přidejte tajné klíče do souboru Parameters. Další informace najdete v tématu [použití Azure Key Vault k předání hodnoty zabezpečeného parametru během nasazování](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Vytvořte kopii souboru parametrů, který se nahraje do větve publikování, a nastavte hodnoty parametrů, které chcete získat z trezoru klíčů, v následujícím formátu:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   Když použijete tuto metodu, tajný kód se automaticky načte z trezoru klíčů.

    -   Soubor parametrů musí být také ve větvi publikování.

1.  Přidejte [úlohu Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) před nasazení Azure Resource Manager popsané v předchozí části:

    -   Vyberte kartu **úlohy** , vytvořte novou úlohu, vyhledejte **Azure Key Vault** a přidejte ji.

    -   V úloze Key Vault vyberte předplatné, ve kterém jste vytvořili Trezor klíčů, v případě potřeby zadejte přihlašovací údaje a pak zvolte Trezor klíčů.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Udělit oprávnění agentovi Azure Pipelines

Pokud nejsou k dispozici správná oprávnění, úloha Azure Key Vault může selhat s chybou odepření přístupu. Stáhněte si protokoly pro vydání a vyhledejte soubor `.ps1` s příkazem pro udělení oprávnění agentovi Azure Pipelines. Můžete spustit příkaz přímo nebo můžete ze souboru zkopírovat ID objektu zabezpečení a zásadu přístupu přidat ručně v Azure Portal. Minimální požadovaná oprávnění jsou **Get** a **list** .

### <a name="update-active-triggers"></a>Aktualizovat aktivní aktivační události

Pokud se pokusíte aktualizovat aktivní aktivační události, může nasazení selhat. Chcete-li aktualizovat aktivní aktivační události, je nutné je ručně zastavit a spustit po nasazení. Můžete to provést prostřednictvím úlohy Azure PowerShellu.

1.  Na kartě úlohy ve vydané verzi přidejte úlohu **Azure PowerShellu** .

1.  Jako typ připojení zvolte **Azure Resource Manager** a vyberte své předplatné.

1.  Zvolte **vložený skript** jako typ skriptu a pak zadejte svůj kód. Následující příklad zastaví triggery:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Můžete postupovat podle podobných kroků (pomocí funkce `Start-AzDataFactoryV2Trigger`) a restartovat triggery po nasazení.

> [!IMPORTANT]
> Ve scénářích kontinuální integrace a nasazování musí být typ Integration Runtime v různých prostředích stejný. Například pokud máte v *prostředí pro vývoj* v místním prostředí Integration runtime (IR), musí být stejný *IR typu v jiném prostředí, jako* je třeba test a produkce. Podobně pokud sdílíte prostředí Integration runtime v několika fázích, je nutné nakonfigurovat prostředí Integration runtime jako *propojená* místně ve všech prostředích, jako je vývoj, testování a produkce.

#### <a name="sample-prepostdeployment-script"></a>Ukázka skriptu před/po nasazení

Níže je ukázkový skript, který před nasazením zastaví triggery a znovu spustí triggery. Skript také obsahuje kód pro odstranění odebraných prostředků. Pokud chcete nainstalovat nejnovější verzi Azure PowerShell, přečtěte si téma [instalace Azure PowerShell ve Windows pomocí PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Použití vlastních parametrů se šablonou Správce prostředků

Pokud jste v režimu GIT, můžete přepsat výchozí vlastnosti v šabloně Správce prostředků, abyste nastavili vlastnosti, které jsou parametrizované v šabloně, a vlastnosti, které jsou pevně kódované. V těchto scénářích možná budete chtít přepsat výchozí šablonu Parametrizace:

* Používáte automatizované CI/CD a chcete změnit některé vlastnosti během nasazení Správce prostředků, ale vlastnosti nejsou ve výchozím nastavení parametrizované.
* Vaše továrna je tak velká, že výchozí šablona Správce prostředků je neplatná, protože má více než maximální povolený počet parametrů (256).

V rámci těchto podmínek můžete přepsat výchozí šablonu Parametrizace vytvořením souboru s názvem *ARM-Template-Parameters-definition. JSON* v kořenové složce úložiště. Název souboru se musí přesně shodovat. Data Factory se pokusí přečíst tento soubor z jakékoli větve, na které jste právě na portálu Azure Data Factory, nikoli jenom z větve pro spolupráci. Můžete vytvořit nebo upravit soubor z privátní větve, kde můžete testovat své změny pomocí **šablony pro export ARM** v uživatelském rozhraní. Pak můžete soubor sloučit do větve pro spolupráci. Pokud se nenajde žádný soubor, použije se výchozí šablona.


### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxe souboru vlastních parametrů

Tady jsou některé pokyny, jak použít při vytváření souboru vlastních parametrů. Soubor se skládá z oddílu pro každý typ entity: aktivační událost, kanál, propojená služba, datová sada, prostředí Integration runtime atd.
* Zadejte cestu k vlastnosti pod odpovídajícím typem entity.
* Když nastavíte název vlastnosti na '\*' ', znamená to, že chcete parametrizovat všechny vlastnosti (pouze na první úrovni, ne rekurzivně). Můžete také zadat případné výjimky.
* Pokud nastavíte hodnotu vlastnosti jako řetězec, označíte, že chcete vlastnost parametrizovat. Použijte `<action>:<name>:<stype>`formátu.
   *  `<action>` může být jedním z následujících znaků:
      * `=` znamená, že aktuální hodnota je jako výchozí hodnota parametru.
      * `-` znamená, že nezachovají výchozí hodnotu parametru.
      * `|` je zvláštní případ pro tajné klíče z Azure Key Vault pro připojovací řetězce nebo klíče.
   * `<name>` je název parametru. Pokud je prázdný, převezme název vlastnosti. Pokud hodnota začíná znakem `-`, název se zkrátí. Například `AzureStorage1_properties_typeProperties_connectionString` by se zkrátila na `AzureStorage1_connectionString`.
   * `<stype>` je typ parametru. Je-li `<stype>` prázdné, je výchozí typ `string`. Podporované hodnoty: `string`, `bool`, `number`, `object`a `securestring`.
* Když zadáte pole v definičním souboru, označíte, že vlastnost Matching v šabloně je pole. Data Factory projde všemi objekty v poli pomocí definice, která je zadána v objektu Integration Runtime pole. Druhý objekt, řetězec, se zobrazí jako název vlastnosti, která se používá jako název parametru pro každou iteraci.
* Není možné mít definici specifickou pro instanci prostředku. Každá definice se vztahuje na všechny prostředky daného typu.
* Ve výchozím nastavení jsou všechny zabezpečené řetězce, například Key Vault tajné klíče a zabezpečené řetězce, jako jsou například připojovací řetězce, klíče a tokeny, parametrizované.
 
### <a name="sample-parameterization-template"></a>Ukázková šablona Parametrizace

Tady je příklad toho, co může šablona Parametrizace vypadat:

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```
Níže je uveden popis způsobu konstrukce výše uvedené šablony, rozepsaný podle typu prostředku.

#### <a name="pipelines"></a>Kanály
    
* Jakákoli vlastnost v aktivitách Path/typeProperties/waitTimeInSeconds je parametrizovaná. Všechny aktivity v kanálu, které mají vlastnost na úrovni kódu s názvem `waitTimeInSeconds` (například aktivita `Wait`), jsou parametrizované jako číslo s výchozím názvem. V šabloně Správce prostředků ale nebude mít výchozí hodnotu. Během nasazení Správce prostředků se bude jednat o povinný vstup.
* Podobně vlastnost s názvem `headers` (například v aktivitě `Web`) je parametrizovaná pomocí typu `object` (JObject). Má výchozí hodnotu, což je stejná hodnota jako ve zdrojové továrně.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Všechny vlastnosti v rámci cesty `typeProperties` jsou parametrizované s příslušnými výchozími hodnotami. Například ve vlastnostech typu **IntegrationRuntimes** existují dvě vlastnosti: `computeProperties` a `ssisProperties`. Oba typy vlastností jsou vytvořeny s příslušnými výchozími hodnotami a typy (Object).

#### <a name="triggers"></a>Triggery

* V části `typeProperties`jsou parametrizované dvě vlastnosti. První z nich je `maxConcurrency`, která má mít výchozí hodnotu a je typu`string`. Má výchozí název parametru `<entityName>_properties_typeProperties_maxConcurrency`.
* Vlastnost `recurrence` je také Parametrizovaná. V takovém případě jsou všechny vlastnosti na dané úrovni parametrizované jako řetězce s výchozími hodnotami a názvy parametrů. Výjimkou je vlastnost `interval`, která je parametrizovaná jako typ Number a s názvem parametru s příponou `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Podobně vlastnost `freq` je řetězec a je parametrizovaná jako řetězec. Vlastnost `freq` je však Parametrizovaná bez výchozí hodnoty. Název je zkrácen a přípona. například `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Propojené služby jsou jedinečné. Vzhledem k tomu, že propojené služby a datové sady mají široké rozsahy typů, můžete zadat vlastní nastavení pro konkrétní typ. V tomto příkladu se použije konkrétní šablona pro všechny propojené služby typu `AzureDataLakeStore`a pro všechny ostatní (přes \*) se použije jiná šablona.
* Vlastnost `connectionString` bude parametrizovaná jako hodnota `securestring`, nebude mít výchozí hodnotu a bude mít zkrácený název parametru, který je s příponou `connectionString`.
* Vlastnost `secretAccessKey` se stává `AzureKeyVaultSecret` (například v propojené službě `AmazonS3`). Je automaticky Parametrizovaná jako Azure Key Vault tajný klíč a načítá se z nakonfigurovaného trezoru klíčů. Můžete také parametrizovat samotný Trezor klíčů.

#### <a name="datasets"></a>Datové sady

* I když je pro datové sady k dispozici přizpůsobení specifické pro typ, může být konfigurace poskytnuta bez explicitního použití konfigurace na úrovni \*. Ve výše uvedeném příkladu jsou všechny vlastnosti datové sady v rámci `typeProperties` parametrizované.

### <a name="default-parameterization-template"></a>Výchozí šablona Parametrizace

Níže je uvedená aktuální výchozí šablona Parametrizace. Pokud potřebujete přidat jenom jeden nebo několik parametrů, může být vhodné přímo upravit tuto úpravu, protože neztratíte stávající strukturu Parametrizace.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

Níže je uveden příklad, jak přidat jednu hodnotu do výchozí šablony Parametrizace. Chceme pro propojenou službu datacihly do souboru parametrů přidat jenom existující interaktivní ID clusteru datacihly. Všimněte si, že níže uvedený soubor je stejný jako u výše uvedeného souboru s výjimkou `existingClusterId` zahrnutých v poli vlastnosti `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

## <a name="linked-resource-manager-templates"></a>Propojené šablony Správce prostředků

Pokud jste pro své datové továrny nastavili průběžnou integraci a nasazování (CI/CD), můžete se setkat s omezeními Azure Resource Manager šablony, když továrna roste. Příkladem omezení je maximální počet prostředků v šabloně Správce prostředků. V souvislosti s vytvářením úplného Správce prostředků šablony pro objekt pro vytváření nyní Data Factory vygeneruje propojené šablony Správce prostředků. Pomocí této funkce je celá datová část továrny rozdělena do několika souborů, takže nebudete spouštět omezení.

Pokud jste nakonfigurovali Git, propojené šablony se generují a ukládají společně s úplnými Správce prostředků šablonami v `adf_publish` větvi v rámci nové složky s názvem `linkedTemplates`.

![Složka propojených šablon Správce prostředků](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Propojené šablony Správce prostředků obvykle mají hlavní šablonu a sadu podřízených šablon, které jsou propojeny s hlavní. Nadřazená šablona se nazývá `ArmTemplate_master.json`a podřízené šablony jsou pojmenovány pomocí vzorového `ArmTemplate_0.json`, `ArmTemplate_1.json`a tak dále. Pokud chcete místo úplné Správce prostředků šablony použít propojené šablony, aktualizujte úlohu CI/CD tak, aby odkazovala na `ArmTemplate_master.json` namísto `ArmTemplateForFactory.json` (úplnou šablonu Správce prostředků). Správce prostředků také vyžaduje, abyste nahráli propojené šablony do účtu úložiště tak, aby k nim měli během nasazení k dispozici Azure. Další informace najdete v tématu [nasazení propojených šablon ARM pomocí VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Nezapomeňte přidat skripty Data Factory do kanálu CI/CD před a po úloze nasazení.

Pokud nemáte nakonfigurovaný Git, propojené šablony jsou přístupné prostřednictvím gesta pro **Export šablony ARM** .

## <a name="hot-fix-production-branch"></a>Produkční větev Hot Fix

Pokud nasadíte továrnu do produkčního prostředí a zjistíte chybu, kterou je potřeba opravit hned, ale nemůžete nasadit aktuální větev pro spolupráci, možná budete muset nasadit opravu Hot. Tento přístup je známý jako rychlý opravový technický nebo QFE. 

1.  Ve službě Azure DevOps se podívejte na verzi, která byla nasazená do produkčního prostředí, a najděte poslední přivedený zápis.

2.  Ze zprávy potvrzení Získejte ID potvrzení větve pro spolupráci.

3.  Vytvořte novou větev Hot-Fix z tohoto potvrzení.

4.  Přejděte do uživatelského rozhraní Azure Data Factory a přepněte do této větve.

5.  Vyřešte chybu pomocí Azure Data Factoryho uživatelského rozhraní. Otestujte provedené změny.

6.  Po ověření opravy klikněte na **Exportovat šablonu ARM** a stáhněte šablonu Hot-Fix správce prostředků.

7.  Ruční vrácení tohoto sestavení se změnami do větve adf_publish

8.  Pokud jste nakonfigurovali svůj kanál pro vydávání verzí, který se automaticky aktivuje na základě vrácení se změnami adf_publish, automaticky se spustí nová verze. V opačném případě ručně vyřadí vydanou verzi.

9.  Nasaďte do testovacího a produkčního továrny vydání Hot-Fix. Tato verze obsahuje předchozí provozní datovou část a opravu provedenou v kroku 5.

10. Přidejte změny z rutiny Hot-Fix do vývojové větve, aby pozdější verze neběžely ve stejné chybě.

## <a name="best-practices-for-cicd"></a>Osvědčené postupy pro CI/CD

Pokud používáte integraci Gitu s datovou továrnou a máte kanál CI/CD, který přesouvá vaše změny z vývoje do testu a následně do produkčního prostředí, doporučujeme následující osvědčené postupy:

-   **Integrace Gitu**. Ke konfiguraci vašeho vývojového objektu pro vytváření dat se používá integrace Gitu. Změny v testovacích a produkčních prostředích se nasazují prostřednictvím CI/CD a nepotřebují integraci Git.

-   **Data Factory skript CI/CD**. Před krokem nasazení Správce prostředků v CI/CD se vyžadují určité úkoly, jako je zastavení nebo spuštění triggerů a vyčištění. Před nasazením a po něm doporučujeme používat skripty prostředí PowerShell. Další informace najdete v tématu [aktualizace aktivních aktivačních událostí](#update-active-triggers). 

-   **Prostředí Integration runtime a sdílení**. Prostředí Integration runtime se často nemění a jsou ve všech fázích CI/CD stejné. V důsledku toho Data Factory očekává, že budete mít stejný název a stejný typ prostředí Integration runtime ve všech fázích CI/CD. Pokud chcete sdílet prostředí Integration runtime ve všech fázích, zvažte použití Ternární továrny jenom pro obsahující sdílené prostředí Integration runtime. Tuto sdílenou továrnu můžete použít ve všech prostředích jako typ propojeného prostředí Integration runtime.

-   **Key Vault**. Při použití propojených služeb založených na Azure Key Vault můžete další výhody využít tak, že v různých prostředích zachováte samostatné trezory klíčů. Pro každý z nich můžete nakonfigurovat také samostatné úrovně oprávnění. Například nebudete chtít, aby členové týmu měli oprávnění k produkčním tajným klíčům. Pokud budete postupovat podle tohoto přístupu, doporučujeme, abyste zachovali stejné tajné názvy ve všech fázích. Pokud zachováte stejné názvy, nemusíte měnit šablony Správce prostředků napříč prostředími CI/CD, protože jediná věc, kterou mění, je název trezoru klíčů, což je jeden z parametrů Správce prostředků šablony.

## <a name="unsupported-features"></a>Nepodporované funkce

- V případě _, že ADF nepovoluje_ zápisy na výběry, ani selektivní publikování prostředků, ADF. Publikování budou zahrnovat **všechny** změny provedené v datové továrně.

    - Entity služby Data Factory jsou na sobě navzájem závislé, například triggery závisejí na kanálech, kanálech závisí na datových sadách a dalších kanálech atd. Selektivní publikování podmnožiny prostředků _může_ vést k neočekávanému chování a chybám.
    - Ve výjimečných případech, kdy je třeba vymezit selektivní publikování, můžete uvažovat o opravách za běhu. Další informace najdete v tématu [provozní větev Hot Fix](#hot-fix-production-branch) .

-   Nemůžete publikovat z privátních větví.

-   Od tohoto okamžiku nemůžete hostovat projekty na Bitbucket
