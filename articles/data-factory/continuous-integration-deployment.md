---
title: Průběžná integrace a doručování v Azure Data Factory
description: Zjistěte, jak pomocí průběžné integrace a doručování přesunout kanály datové továrny z jednoho prostředí (vývoj, testování, výroba) do jiného.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 8ce954e956da62d645e9b1e852ce7a7318c85791
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415362"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Průběžná integrace a doručování v Azure Data Factory

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="overview"></a>Přehled

Průběžná integrace je praxe testování každé změny provedené na základu kódu automaticky a co nejdříve.Průběžné doručování následuje po testování, ke kterému dochází během průběžné integrace a odesílá změny do pracovního nebo produkčního systému.

V Azure Data Factory znamená průběžná integrace a doručování (CI/CD) přesun kanálů Data Factory z jednoho prostředí (vývoj, testování, výroba) do jiného. K vytváření ci/CD můžete použít integraci ux Data Factory se šablonami Azure Resource Manageru.

V uživatelském prostředí Data Factory můžete vygenerovat šablonu Správce prostředků z rozevírací nabídky **Šablony ARM.** Když vyberete **export arm šablony**, portál generuje šablonu Správce prostředků pro továrnu dat a konfigurační soubor, který obsahuje všechny připojovací řetězce a další parametry. Potom vytvoříte jeden konfigurační soubor pro každé prostředí (vývoj, test, výroba). Hlavní soubor šablony Správce prostředků zůstává stejný pro všechna prostředí.

Devítiminutový úvod k této funkci a ukázku najdete v tomto videu:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Životní cyklus CI/CD

Níže je ukázkový přehled životního cyklu CI/CD v datové továrně Azure, která je nakonfigurovaná s Azure Repos Git. Další informace o konfiguraci úložiště Git najdete v tématu [Source control v Azure Data Factory](source-control.md).

1.  Vývojová datová továrna se vytvoří a nakonfiguruje pomocí Azure Repos Git. Všichni vývojáři by měli mít oprávnění k vytváření prostředků Data Factory, jako jsou kanály a datové sady.

1.  Jako vývojáři provádět změny ve svých větvích funkcí, ladí jejich kanál běží s jejich nejnovější změny. Další informace o tom, jak ladit spuštění kanálu, najdete v tématu [iterativní vývoj a ladění s Azure Data Factory](iterative-development-debugging.md).

1.  Poté, co vývojáři jsou spokojeni s jejich změny, vytvoří žádost o přijetí změn z jejich větev funkce do hlavní nebo větev spolupráce získat jejich změny přezkoumány partnery.

1.  Po schválení žádosti o přijetí změn a sloučení změn v hlavní větvi mohou být změny publikovány do vývojové továrny.

1.  Když je tým připraven nasadit změny do testovací továrny a potom do výrobní továrny, tým exportuje šablonu Správce prostředků z hlavní větve.

1.  Exportovaná šablona Správce prostředků je nasazena s různými soubory parametrů do testovací továrny a výrobní továrny.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Vytvoření šablony Správce prostředků pro každé prostředí

1. V seznamu **ŠABLONY ARM** vyberte **exportovat šablonu ARM,** chcete-li exportovat šablonu Správce prostředků pro vaši datovou továrnu ve vývojovém prostředí.

   ![Export šablony Správce prostředků](media/continuous-integration-deployment/continuous-integration-image1.png)

1. V továrnách na testovací a produkční data vyberte **Importovat šablonu ARM**. Tato akce vás přenese na portál Azure, kde můžete exportovnaexportní šablonu importovat. Vyberte **Vytvořit vlastní šablonu v editoru** a otevřete editor šablon Správce prostředků.

   ![Vytvořte si vlastní šablonu](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Vyberte **Načíst soubor**a pak vyberte vygenerovanou šablonu Správce prostředků. Toto je soubor **arm_template.json** umístěný v souboru ZIP exportovaném v kroku 1.

   ![Upravit šablonu](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. V části nastavení zadejte hodnoty konfigurace, jako jsou pověření propojené služby. Až budete hotovi, vyberte **Koupit a** nasaďte šablonu Správce prostředků.

   ![Oddíl Nastavení](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Připojovací řetězce

Informace o konfiguraci připojovacích řetězců naleznete v článku konektoru. Například pro Azure SQL Database, najdete [v tématu Kopírování dat do nebo z Azure SQL Database pomocí Azure Data Factory](connector-azure-sql-database.md). Chcete-li ověřit připojovací řetězec, můžete otevřít zobrazení kódu pro prostředek v ux datové továrny. V zobrazení kódu je odebrána část hesla nebo klíče účtu připojovacího řetězce. Chcete-li otevřít zobrazení kódu, vyberte zde zvýrazněnou ikonu:

![Zobrazení otevřeného kódu pro zobrazení připojovacího řetězce](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Automatizace průběžné integrace pomocí verzí Azure Pipelines

Následuje průvodce pro nastavení verze Azure Pipelines, která automatizuje nasazení datové továrny do více prostředí.

![Diagram průběžné integrace s Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Požadavky

-   Předplatné Azure propojené se serverem Visual Studio Team Foundation Nebo Azure Repos, které používá [koncový bod služby Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Továrna dat nakonfigurovaná s integrací Azure Repos Git.

-    [Trezor klíčů Azure,](https://azure.microsoft.com/services/key-vault/) který obsahuje tajné klíče pro každé prostředí.

### <a name="set-up-an-azure-pipelines-release"></a>Nastavení verze Azure Pipelines

1.  V [Azure DevOps](https://dev.azure.com/)otevřete projekt, který je nakonfigurovaný s vaší továrně dat.

1.  Na levé straně stránky vyberte **Potrubí**a pak vyberte **Zprávy**.

    ![Vybrat kanály, vydání](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Vyberte **Nový kanál**, nebo pokud máte existující kanály, vyberte **Nový** a potom Nový **kanál vydání**.

1.  Vyberte šablonu **Prázdné úlohy.**

    ![Vybrat prázdnou úlohu](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  Do pole **Název vymezené plochy** zadejte název prostředí.

1.  Vyberte **Přidat artefakt**a pak vyberte úložiště nakonfigurované s vaší továrně dat. Vyberte **adf_publish** pro **výchozí větev**. Pro **výchozí verzi**vyberte **možnost Nejnovější z výchozí větve**.

    ![Přidání artefaktu](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Přidejte úlohu nasazení Správce prostředků Azure:

    a.  V zobrazení vymezené plochy vyberte **Zobrazit úkoly vymezené plochy**.

    ![Zobrazení vymezené plochy](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Vytvořte nový úkol. Vyhledejte **nasazení skupiny prostředků Azure**a pak vyberte **Přidat**.

    c.  V úloze nasazení vyberte předplatné, skupinu prostředků a umístění pro cílovou datovou továrnu. V případě potřeby zadejte pověření.

    d.  V seznamu **Akce** vyberte **Vytvořit nebo aktualizovat skupinu prostředků**.

    e.  Vyberte tlačítko se třemi tečkami (**...**) vedle pole **Šablona.** Vyhledejte šablonu Azure Resource Manager, kterou jste vytvořili pomocí **importu ARM šablony** v [šabloně Vytvořit správce prostředků pro každé prostředí](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) části tohoto článku. Vyhledejte tento soubor <FactoryName> ve složce větve adf_publish.

    f.  Vyberte **...** vedle pole **Parametry šablony** zvolte soubor parametrů. Soubor, který zvolíte, bude záviset na tom, zda jste vytvořili kopii nebo používáte výchozí soubor ARMTemplateParametersForFactory.json.

    g.  Vyberte **...** vedle pole **Přepsat parametry šablony** a zadejte informace pro cílovou datovou továrnu. U přihlašovacích údajů, které pocházejí z azure key vault, zadejte název tajného klíče mezi uvozovky. Například pokud je název tajného souboru cred1, zadejte **"$(cred1)"** pro tuto hodnotu.

    h. Vrežimu **nasazení**vyberte **možnost Přírůstkové** .

    > [!WARNING]
    > Pokud pro režim **nasazení**vyberete **možnost Dokončeno** , mohou být odstraněny existující prostředky, včetně všech prostředků v cílové skupině prostředků, které nejsou definovány v šabloně Správce prostředků.

    ![Nasazení prod datové továrny](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Uložte kanál vydání.

1. Chcete-li spustit vydání, vyberte **Vytvořit vydání**.

   ![Vybrat vytvořit verzi](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> Ve scénářích CI/CD musí být typ integračního běhu (IR) v různých prostředích stejný. Například pokud máte samostatně hostované infračervené ovládání ve vývojovém prostředí, stejné infračervené ovládání musí být také typu samostatně hostované v jiných prostředích, jako je například testování a výroba. Podobně pokud sdílíte integrační runtimes napříč několika fázemi, musíte nakonfigurovat integrační runtimes jako propojené vlastní hostované ve všech prostředích, jako je vývoj, testování a výroba.

### <a name="get-secrets-from-azure-key-vault"></a>Získání tajných kódů z azure key vaultu

Pokud máte tajné klíče předat v šabloně Azure Resource Manager, doporučujeme použít Azure Key Vault s vydáním Azure Kanálus.

Existují dva způsoby, jak zacházet s tajnými kódy:

1.  Přidejte tajné klíče do souboru parametrů. Další informace najdete [v tématu Použití trezoru klíčů Azure předat hodnotu zabezpečeného parametru během nasazení](../azure-resource-manager/templates/key-vault-parameter.md).

    Vytvořte kopii souboru parametrů, který je odeslán do větve publikování. Pomocí tohoto formátu nastavte hodnoty parametrů, které chcete získat z trezoru klíčů:

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

    Při použití této metody je tajný klíč automaticky vytažen z trezoru klíčů.

    Soubor parametrů musí být také ve větvi publikování.

1. Před úlohu nasazení Správce prostředků Azure popsané v předchozí části přidejte [úlohu trezoru klíčů Azure:](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault)

    1.  Na kartě **Úkoly** vytvořte nový úkol. Vyhledejte **Azure Key Vault** a přidejte ho.

    1.  V úloze trezoru klíčů vyberte předplatné, ve kterém jste trezor klíčů vytvořili. V případě potřeby zadejte pověření a vyberte trezor klíčů.

    ![Přidání úkolu trezoru klíčů](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Udělit oprávnění agentovi Azure Pipelines

Azure Key Vault úloha může selhat s chybou odepřen přístup, pokud nejsou nastavena správná oprávnění. Stáhněte protokoly pro vydání a vyhledejte soubor PS1, který obsahuje příkaz, který uděluje oprávnění agentovi Azure Pipelines. Příkaz můžete spustit přímo. Nebo můžete zkopírovat id jistiny ze souboru a přidat zásady přístupu ručně na webu Azure Portal. `Get`a `List` jsou minimální požadovaná oprávnění.

### <a name="update-active-triggers"></a>Aktualizovat aktivní aktivační události

Nasazení může selhat, pokud se pokusíte aktualizovat aktivní aktivační události. Chcete-li aktualizovat aktivní aktivační události, je třeba je ručně zastavit a po nasazení je restartovat. To lze provést pomocí úlohy Azure PowerShell:

1.  Na kartě **Úkoly** ve verzi přidejte úlohu **Azure PowerShellu.** Zvolte verzi úkolu 4.*. 

1.  Vyberte předplatné, ve které se nachází vaše továrna.

1.  Jako typ skriptu vyberte **Cesta k souboru** skriptu. To vyžaduje uložení skriptu PowerShellu do úložiště. K zastavení aktivačních událostí lze použít následující skript prostředí PowerShell:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Můžete provést podobné kroky `Start-AzDataFactoryV2Trigger` (s funkcí) restartovat aktivační události po nasazení.

### <a name="sample-pre--and-post-deployment-script"></a>Ukázkový skript před nasazením a po nasazení

Následující ukázkový skript lze zastavit aktivační události před nasazením a restartovat je později. Skript také obsahuje kód pro odstranění prostředků, které byly odebrány. Uložte skript do úložiště Git Azure DevOps a odkazovat pomocí úlohy Azure PowerShell u verze 4.*.

Při spuštění skriptu před nasazením budete muset v poli **Argumenty skriptu** zadat variantu následujících parametrů.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


Při spuštění skriptu po nasazení budete muset v poli **Argumenty skriptu** zadat variantu následujících parametrů.

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Úloha Azure PowerShellu](media/continuous-integration-deployment/continuous-integration-image11.png)

Zde je skript, který lze použít pro pre- a post-nasazení. Účty pro odstraněné prostředky a odkazy na prostředky.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        return $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
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

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Použití vlastních parametrů s šablonou Resource Manageru

Pokud jste v režimu GIT, můžete přepsat výchozí vlastnosti v šabloně Správce prostředků a nastavit vlastnosti, které jsou parametrizovány v šabloně a vlastnostech, které jsou pevně zakódované. V těchto scénářích můžete chtít přepsat výchozí šablonu parametrizace:

* Používáte automatické CI/CD a chcete změnit některé vlastnosti během nasazení Správce prostředků, ale vlastnosti nejsou parametrizovány ve výchozím nastavení.
* Vaše továrna je tak velká, že výchozí šablona Správce prostředků je neplatná, protože má více než maximální povolené parametry (256).

Chcete-li přepsat výchozí šablonu parametrizace, vytvořte za těchto podmínek soubor s názvem **arm-template-parameters-definition.json** ve složce určené jako kořenová složka pro integraci gitu datové továrny. Musíte použít přesný název souboru. Data Factory čte tento soubor z kterékoli větve, ve které jste aktuálně na portálu Azure Data Factory, nejen z větve spolupráce. Soubor můžete vytvořit nebo upravit z privátní větve, kde můžete otestovat změny výběrem **možnosti Exportovat šablonu ARM** v uživatelském uživatelském nastavení. Potom můžete sloučit soubor do větve spolupráce. Pokud není nalezen žádný soubor, použije se výchozí šablona.

> [!NOTE]
> Vlastní šablona parametrizace nezmění limit parametru šablony ARM 256. Umožňuje zvolit a snížit počet parametrizovaných vlastností.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxe souboru vlastních parametrů

Při vytváření souboru vlastních parametrů postupujte podle následujících pokynů. Soubor se skládá z oddílu pro každý typ entity: aktivační událost, kanál, propojená služba, datová sada, integrační runtime a tak dále.
* Zadejte cestu vlastnosti pod příslušným typem entity.
* Nastavení názvu vlastnosti označuje, `*` že chcete parametrizovat všechny vlastnosti pod ním (pouze na první úroveň, nikoli rekurzivně). Můžete také zadat výjimky z této konfigurace.
* Nastavení hodnoty vlastnosti jako řetězec označuje, že chcete parametrizovat vlastnost. Použijte formát `<action>:<name>:<stype>`.
   *  `<action>` může být jedním z těchto znaků:
      * `=` znamená zachovat aktuální hodnotu jako výchozí hodnotu parametru.
      * `-` znamená nezachovávejte výchozí hodnotu parametru.
      * `|` je zvláštní případ pro tajné klíče z Azure Key Vault pro připojovací řetězce nebo klíče.
   * `<name>` je název parametru. Pokud je prázdný, přebírá název vlastnosti. Pokud hodnota začíná `-` znakem, název se zkrátí. Například `AzureStorage1_properties_typeProperties_connectionString` by být zkrácena na `AzureStorage1_connectionString`.
   * `<stype>` je typ parametru. Pokud `<stype>` je prázdný, výchozí `string`typ je . `string`Podporované hodnoty: `bool` `number`, `object`, `securestring`, a .
* Zadání pole v definičním souboru znamená, že odpovídající vlastnost v šabloně je pole. Data Factory iterates přes všechny objekty v poli pomocí definice, která je zadána v integraci runtime objektu pole. Druhý objekt, řetězec, se stane názvem vlastnosti, která se používá jako název parametru pro každou iteraci.
* Definice nemůže být specifická pro instanci prostředku. Všechny definice se vztahuje na všechny prostředky tohoto typu.
* Ve výchozím nastavení jsou parametrizovány všechny zabezpečené řetězce, jako jsou tajné klíče trezoru klíčů, a zabezpečené řetězce, jako jsou připojovací řetězce, klíče a tokeny.
 
### <a name="sample-parameterization-template"></a>Ukázková šablona parametrizace

Tady je příklad toho, jak může vypadat šablona parametrizace:

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
Zde je vysvětlení, jak je předchozí šablona vytvořena, rozdělená podle typu prostředku.

#### <a name="pipelines"></a>Kanály
    
* Každá vlastnost v `activities/typeProperties/waitTimeInSeconds` cestě je parametrizována. Jakákoli aktivita v kanálu, který má `waitTimeInSeconds` vlastnost na `Wait` úrovni kódu s názvem (například aktivita) je parametrizován jako číslo s výchozím názvem. Ale nebude mít výchozí hodnotu v šabloně Správce prostředků. Bude povinný vstup během nasazení Správce prostředků.
* Podobně vlastnost volaná `headers` (například `Web` v aktivitě) je `object` parametrizována typem (JObject). Má výchozí hodnotu, která je stejná jako hodnota zdrojové továrny.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Všechny vlastnosti `typeProperties` pod cestou jsou parametrizovány příslušnými výchozími hodnotami. Například existují dvě vlastnosti v rámci `IntegrationRuntimes` vlastnosti typu: `computeProperties` a `ssisProperties`. Oba typy vlastností jsou vytvořeny s jejich příslušné výchozí hodnoty a typy (Object).

#### <a name="triggers"></a>Aktivační události

* V `typeProperties`části jsou parametrizovány dvě vlastnosti. První z `maxConcurrency`nich je , který je určen pro`string`výchozí hodnotu a je typu . Má výchozí název `<entityName>_properties_typeProperties_maxConcurrency`parametru .
* Vlastnost `recurrence` je také parametrizována. Pod ním jsou všechny vlastnosti na této úrovni určeny k parametrizování jako řetězce s výchozími hodnotami a názvy parametrů. Výjimkou je `interval` vlastnost, která je parametrizována jako typ `number`. Název parametru je suffixed s `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Podobně `freq` vlastnost je řetězec a je parametrizován jako řetězec. `freq` Vlastnost je však parametrizována bez výchozí hodnoty. Název je zkrácen a připnut. Například, `<entityName>_freq`.

#### <a name="linkedservices"></a>Služby LinkedServices

* Propojené služby jsou jedinečné. Vzhledem k tomu, že propojené služby a datové sady mají širokou škálu typů, můžete zadat přizpůsobení specifické pro daný typ. V tomto příkladu bude pro `AzureDataLakeStore`všechny propojené služby typu použita určitá šablona. Pro všechny ostatní `*`(přes ) bude použita jiná šablona.
* Vlastnost `connectionString` bude parametrizována `securestring` jako hodnota. Nebude mít výchozí hodnotu. Bude mít zkrácený název parametru, který je `connectionString`suffixed s .
* Vlastnost `secretAccessKey` se stane `AzureKeyVaultSecret` být (například v amazons3 propojené služby). Automaticky se parametrizuje jako tajný klíč azure trezoru klíčů a načte se z nakonfigurovaného trezoru klíčů. Můžete také parametrizovat samotný trezor klíčů.

#### <a name="datasets"></a>Datové sady

* Přestože je pro datové sady k dispozici přizpůsobení specifické \*pro daný typ, můžete poskytnout konfiguraci bez explicitní konfigurace úrovně. V předchozím příkladu jsou parametrizovány všechny vlastnosti datové sady v písmenu `typeProperties` a).

### <a name="default-parameterization-template"></a>Výchozí šablona parametrizace

Následuje aktuální výchozí šablona parametrizace. Pokud potřebujete přidat pouze několik parametrů, úprava této šablony přímo může být dobrý nápad, protože neztratíte existující strukturu parametrizace.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
                    "poolName": "=",
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

Následující příklad ukazuje, jak přidat jednu hodnotu do výchozí šablony parametrizace. Chceme do souboru parametrů přidat pouze existující ID interaktivního clusteru Azure Databricks pro propojenou službu Databricks. Všimněte si, že tento soubor je stejný `existingClusterId` jako předchozí soubor `Microsoft.DataFactory/factories/linkedServices`s výjimkou přidání pod vlastnosti pole .

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
            "poolName": "=",
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

## <a name="linked-resource-manager-templates"></a>Šablony Správce propojených prostředků

Pokud jste pro továrny na data nastavili CI/CD, můžete překročit limity šablon Azure Resource Manageru, protože vaše továrna se zvětšuje. Například jeden limit je maximální počet prostředků v šabloně Správce prostředků. Chcete-li přizpůsobit velké továrny při generování úplné šablony Správce prostředků pro továrnu, Factory data nyní generuje propojené šablony Správce prostředků. Pomocí této funkce je celá datová část továrny rozdělena do několika souborů, takže nejste omezeni omezeními.

Pokud jste nakonfigurovali Git, propojené šablony se vygenerují a uloží společně s úplnými šablonami Správce prostředků v adf_publish větvi v nové složce s názvem LinkedTemplates:

![Složka šablon Správce propojených prostředků](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Propojené šablony Správce prostředků se obvykle skládají z hlavní šablony a sady podřízených šablon, které jsou propojeny s předlohou. Nadřazená šablona se nazývá ArmTemplate_master.json a podřízené šablony jsou pojmenovány podle vzoru ArmTemplate_0.json, ArmTemplate_1.json a tak dále. 

Chcete-li místo úplné šablony Správce prostředků použít propojené šablony, aktualizujte úlohu CI/CD tak, aby ukazovala na ArmTemplate_master.json místo ArmTemplateForFactory.json (úplná šablona Správce prostředků). Správce prostředků také vyžaduje, abyste připojené šablony nahráli do účtu úložiště, aby k nim měl Azure během nasazení přístup. Další informace najdete [v tématu Nasazení propojených šablon Správce prostředků pomocí VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Nezapomeňte přidat skripty Data Factory v kanálu CI/CD před a po úloze nasazení.

Pokud nemáte nakonfigurovaný Git, můžete přistupovat k propojeným šablonám prostřednictvím **exportu šablony ARM** v seznamu **ŠABLONY ARM.**

## <a name="hotfix-production-branch"></a>Výrobní větev opravy hotfix

Pokud nasadíte továrnu do produkčního prostředí a uvědomíte si, že je chyba, kterou je třeba opravit hned, ale nemůžete nasadit aktuální větev spolupráce, možná budete muset nasadit opravu hotfix. Tento přístup je označován jako rychlé opravy inženýrství nebo QFE.

1.    V Azure DevOps přejděte na verzi, která byla nasazena do produkčního prostředí. Najděte poslední potvrzení, které bylo nasazeno.

2.    Ze zprávy o potvrzení získáte ID potvrzení větve spolupráce.

3.    Vytvořte z tohoto potvrzení novou větev opravy hotfix.

4.    Přejděte na ux Azure Data Factory a přepněte na větev opravy hotfix.

5.    Pomocí Azure Data Factory UX, opravit chybu. Otestujte změny.

6.    Po ověření opravy vyberte **exportovat šablonu ARM,** abyste získali šablonu Správce prostředků opravy hotfix.

7.    Ručně zkontrolujte toto sestavení do větve adf_publish.

8.    Pokud jste nakonfigurovali kanál vydání tak, aby se automaticky aktivoval na základě adf_publish vrácení se změnami, začne se automaticky spustit nová verze. V opačném případě ručně zařazuji vydání do fronty.

9.    Nasaďte verzi opravy hotfix do testovacích a výrobních závodů. Tato verze obsahuje předchozí výrobní datové části plus opravu, kterou jste provedli v kroku 5.

10.    Přidejte změny z opravy hotfix do vývojové větve, aby pozdější verze neobsahovaly stejnou chybu.

## <a name="best-practices-for-cicd"></a>Doporučené postupy pro CI/CD

Pokud používáte integraci Gitu s vaší továrně dat a máte kanál CI/CD, který přesune vaše změny z vývoje do testovacího a pak do produkčního prostředí, doporučujeme tyto osvědčené postupy:

-   **Integrace Gitu**. S integrací Gitu je třeba nakonfigurovat pouze vývojovou datovou továrnu. Změny v testování a produkčním prostředí se nasazují přes CI/CD a nepotřebují integraci Gitu.

-   **Skript CI/CD data Factory**. Před krokem nasazení Správce prostředků v CI/CD, je třeba dokončit určité úkoly, jako je zastavení a restartování aktivačních událostí a provádění vyčištění. Doporučujeme používat skripty prostředí PowerShell před a po nasazení. Další informace naleznete v tématu [Aktualizace aktivních aktivačních událostí](#update-active-triggers).

-   **Integrační runtimes a sdílení**. Integrační runtimes se nemění často a jsou podobné ve všech fázích ci/CD. Takže Data Factory očekává, že budete mít stejný název a typ integrace runtime ve všech fázích CI/CD. Pokud chcete sdílet integrační runtimes ve všech fázích, zvažte použití ternární továrny tak, aby obsahovala zaběhu sdílené integrace. Tuto sdílenou továrnu můžete použít ve všech prostředích jako typ za běhu propojené integrace.

-   **Trezor klíčů**. Když používáte propojené služby založené na Azure Key Vault, můžete využít je dále udržováním samostatné trezory klíčů pro různá prostředí. Můžete také nakonfigurovat samostatné úrovně oprávnění pro každý trezor klíčů. Například nechcete, aby členové týmu měli oprávnění k tajným kódům produkčního prostředí. Pokud budete postupovat podle tohoto přístupu, doporučujeme zachovat stejné tajné názvy ve všech fázích. Pokud zachováte stejné názvy, nemusíte měnit šablony Správce prostředků v prostředích CI/CD, protože jediné, co se změní, je název trezoru klíčů, což je jeden z parametrů šablony Správce prostředků.

## <a name="unsupported-features"></a>Nepodporované funkce

- Podle návrhu Data Factory neumožňuje cherry-picking potvrzení nebo selektivní publikování prostředků. Publikování bude obsahovat všechny změny provedené v datové továrně.

    - Entity datové továrny jsou na sobě závislé. Aktivační události jsou například závislé na kanálech a kanály závisí na datových sadách a dalších kanálech. Selektivní publikování podmnožiny prostředků může vést k neočekávanému chování a chybám.
    - Ve výjimečných případech, kdy potřebujete selektivní publikování, zvažte použití opravy hotfix. Další informace naleznete v tématu [Hotfix production branch](#hotfix-production-branch).

-   Publikovat nelze ze soukromých větví.

-   V bitbucketu nelze v současné době hostovat projekty.
