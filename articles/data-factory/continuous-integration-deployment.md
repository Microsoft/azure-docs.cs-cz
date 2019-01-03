---
title: Průběžná integrace a doručování ve službě Azure Data Factory | Dokumentace Microsoftu
description: Další informace o použití průběžnou integraci a doručování kanálů Data Factory přesunout z jednoho prostředí (vývojové, testovací, produkční) do jiného.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/12/2018
ms.author: douglasl
ms.openlocfilehash: 950336db215bbca76f20c15527397212c6fe5ffd
ms.sourcegitcommit: b767a6a118bca386ac6de93ea38f1cc457bb3e4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/18/2018
ms.locfileid: "53554924"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Průběžná integrace a doručování (CI/CD) v Azure Data Factory

Průběžná integrace spočívá v testování každé změny udělat, aby vašeho základu kódu automaticky a co nejdříve. Průběžné doručování se řídí testování, který se stane během průběžnou integraci a nasdílí změny do pracovní nebo produkční systém.

Pro službu Azure Data Factory průběžná integrace a doručování znamená přesun kanálů Data Factory z jednoho prostředí (vývojové, testovací, produkční) do jiného. Průběžná integrace a doručování proveďte můžete integrace uživatelské rozhraní služby Data Factory pomocí šablony Azure Resource Manageru. Uživatelské rozhraní služby Data Factory můžete vygenerovat šablonu Resource Manageru, když vyberete **šablony ARM** možnosti. Když vyberete **šablony ARM exportovat**, portálu vygeneruje šablony Resource Manageru pro vytváření dat a konfigurační soubor, který zahrnuje všechny řetězce připojení a další parametry. Pak budete muset vytvořit jeden konfigurační soubor pro každé prostředí (vývojové, testovací, produkčním prostředí). Hlavní soubor šablony Resource Manageru zůstává stejná pro všechna prostředí.

Pro zavedení devět po minutách a ukázku této funkce z následujícího videa:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Vytvoření šablony Resource Manageru pro jednotlivá prostředí
Vyberte **šablony ARM exportovat** pro export šablony Resource Manageru pro vaši službu data factory ve vývojovém prostředí.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Potom přejděte na objekt pro vytváření dat testovací a produkční data factory a vyberte **šablony ARM Import**.

![](media/continuous-integration-deployment/continuous-integration-image2.png)

Tím přejdete na webu Azure portal, kde můžete importovat vyexportované šablony. Vyberte **vytvořit vlastní šablonu v editoru** a potom **načíst soubor** a vyberte vygenerované šablony Resource Manageru. Zadejte nastavení a data factory a celý kanál se importují v produkčním prostředí.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Vyberte **načíst soubor** vyberte vyexportované šablony Resource Manageru a zadejte všechny hodnoty konfigurace (například propojené služby).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Připojovací řetězce**. Můžete najít informace potřebné k vytvoření připojovací řetězce v článcích o jednotlivých konektorech. Například pro službu Azure SQL Database, najdete v článku [kopírování dat do nebo ze služby Azure SQL Database s použitím služby Azure Data Factory](connector-azure-sql-database.md). Chcete-li ověřit správný připojovací řetězec – pro propojenou službu, například – můžete také otevřít zobrazení kódu pro prostředek v Uživatelském rozhraní služby Data Factory. V zobrazení kódu ale heslo nebo účet klíč připojovacího řetězce se odstraní. Chcete-li otevřít zobrazení kódu, vyberte ikonu zvýrazněný na následujícím snímku obrazovky.

![Otevřete zobrazení kódu zobrazíte připojovací řetězec](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Životní cyklus kontinuální integrace
Tady je celý životní cyklus pro průběžnou integraci a doručování, které můžete použít po povolení integrace Azure úložiště Git v Uživatelském rozhraní služby Data Factory:

1.  Nastavte si vývojové datové továrny pomocí úložiště Azure, ve kterém všichni vývojáři mohou vytvářet prostředky Data Factory jako kanály, datové sady a tak dále.

1.  Vývojáři pak lze změnit prostředky, například kanály. Jak získávají své změny, můžete vybrat **ladění** na tom, jak se kanál poběží s nejnovější změny.

1.  Poté, co vývojáři spokojeni s jejich změny, může vytvořit žádost o přijetí změn ze své větve v hlavní větvi (nebo větve spolupráci) zobrazíte jejich změny zkontroloval partnerských uzlů.

1.  Jakmile jsou změny v hlavní větvi, můžete publikovat k objektu pro vytváření vývoje tak, že vyberete **publikovat**.

1.  Když tým je připraven k podpoře změny objekt pro vytváření testovací a produkční objekt pro vytváření, se můžete exportovat šablonu Resource Manageru z hlavní větve nebo z jiné větve v případě, že jejich hlavní větev zálohuje živé vývoje služby Data Factory.

1.  Exportovaná šablona Resource Manageru můžete nasadit s různými soubory parametrů pro objekt pro vytváření testovací a produkční objekt pro vytváření.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Automatizovat průběžné integrace s Azure kanály vydané verze

Tady je postup nastavení vydání verze Azure kanály, abyste mohli automatizovat nasazení služby data factory do různých prostředí.

![Diagram průběžnou integraci s kanály Azure](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Požadavky

-   Předplatné Azure propojené s Team Foundation Server nebo úložiště Azure pomocí [*koncový bod služby Azure Resource Manageru*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Objekt pro vytváření dat s nakonfigurovanou integraci s Azure úložiště Git.

-    [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) obsahující tajné klíče.

### <a name="set-up-an-azure-pipelines-release"></a>Nastavte si o Azure kanály verzi

1.  Přejděte na stránku úložiště Azure ve stejném projektu, jako je nakonfigurovaný pomocí služby Data Factory.

1.  Klikněte na tlačítko v horní nabídce **kanály Azure** &gt; **verze** &gt; **definice vydané verze vytvořit**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Vyberte **prázdný proces** šablony.

1.  Zadejte název nového prostředí.

1.  Přidání artefaktu Git a vyberte stejné úložiště nakonfigurovat pomocí služby Data Factory. Zvolte `adf_publish` jako výchozí větev s nejnovější verzí výchozí.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Přidáte úkol nasazení Azure Resource Manageru:

    a.  Vytvořit nový úkol, vyhledejte **nasazení skupiny prostředků Azure**a přidejte ji.

    b.  V úloze nasazení zvolte předplatné, skupinu prostředků a umístění pro cílový objekt pro vytváření dat a v případě potřeby zadejte přihlašovací údaje.

    c.  Vyberte **vytvořit nebo aktualizovat skupinu prostředků** akce.

    d.  Vyberte **...** v **šablony** pole. Procházet šablony Resource Manageru (*ARMTemplateForFactory.json*), který vytvořil akci publikovat na portálu. Vyhledejte tento soubor ve složce `<FactoryName>` z `adf_publish` větve.

    e.  To samé udělá pro soubor parametrů. Zvolte správný soubor v závislosti na tom, jestli jste vytvořili kopii nebo používáte výchozí soubor *ARMTemplateParametersForFactory.json*.

    f.  Vyberte **...** vedle položky **přepsání parametrů šablony** pole a zadejte informace pro cílové služby Data Factory. Pro přihlašovací údaje, které pocházejí ze služby key vault, použijte stejný název pro tajný kód v následujícím formátu: název tajného klíče za předpokladu, že je `cred1`, zadejte `"$(cred1)"` (mezi uvozovky).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    g. Vyberte **přírůstkové** režimu nasazení.

    > [!WARNING]
    > Pokud vyberete **Complete** režimu nasazení existující prostředky budou odstraněny, včetně všech prostředků v cílové skupině prostředků, které nejsou definovány v šabloně Resource Manageru.

1.  Uložte kanál pro vydávání verzí.

1.  Vytvořte nové vydání z tohoto kanálu pro vydávání verzí.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Volitelné – získání tajné klíče z Azure Key Vault

Pokud máte tajných kódů a zajistěte tak předání šablony Azure Resource Manageru, doporučujeme používat Azure Key Vault s verzí Azure kanály.

Existují dva způsoby, jak zpracovat tajné klíče:

1.  Přidejte do souboru parametrů tajné klíče. Další informace najdete v tématu [použití Azure Key Vault k předání zabezpečený parametr. hodnoty během nasazení](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Vytvoření kopie souboru parametrů, který se nahraje do publikovat větve a nastavte hodnoty parametrů, které chcete načíst ze služby key vault v následujícím formátu:

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

    -   Při použití této metody je tajný kód automaticky získaných ze služby key vault.

    -   Soubor parametrů musí být ve větvi publikovat.

1.  Přidat [úloh služby Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) před nasazením Azure Resource Manageru, je popsáno v předchozí části:

    -   Vyberte **úlohy** kartu, vytvoří se nový úkol, vyhledejte **Azure Key Vault** a přidejte ji.

    -   V úloze služby Key Vault, vyberte předplatné, ve které jste vytvořili trezor klíčů, zadejte přihlašovací údaje v případě potřeby a klikněte na tlačítko trezoru klíčů.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Udělit oprávnění k agentovi Azure kanály
Úloha služby Azure Key Vault může selhat při prvním s chybou přístup byl odepřen. Stažení protokolů pro vydání a vyhledejte `.ps1` soubor pomocí příkazu udělit oprávnění k agentovi Azure kanály. Příkaz můžete spustit přímo, nebo můžete zkopírovat ID objektu zabezpečení ze souboru a ručně přidat zásady přístupu na webu Azure Portal. (*Získat* a *seznamu* je minimálním předpokladem).

### <a name="update-active-triggers"></a>Aktualizace active aktivační události
Nasazení může selhat, pokud se pokusíte aktualizovat active aktivační události. K aktualizaci aktivního aktivačních událostí, budete muset ručně zastavit a spustit po nasazení. K tomuto účelu můžete přidat úkol prostředí Azure Powershell, jak je znázorněno v následujícím příkladu:

1.  Na kartě úlohy vydané verze, vyhledejte **prostředí Azure Powershell** a přidejte ji.

1.  Zvolte **Azure Resource Manageru** jako připojení zadejte a vyberte své předplatné.

1.  Zvolte **zpracování vloženého skriptu** skript zadejte a potom poskytnutí ověřovacího kódu. Následující příklad zastaví aktivačních událostí:

    ```powershell
    $triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Můžete podobným způsobem a použijte podobně jako kód (s `Start-AzureRmDataFactoryV2Trigger` funkce) po nasazení restartovat aktivační události.

> [!IMPORTANT]
> Scénáře nasazení a průběžnou integraci typ modulu Runtime integrace napříč různými prostředími musí být stejné. Pokud máte například *v místním prostředí* stejné prostředí IR Integration Runtime (IR) ve vývojovém prostředí, musí být typu *v místním prostředí* v jiných prostředích, jako je například testovací a produkční také. Podobně pokud sdílíte prostředí integration Runtime v několika fázích, budete muset nakonfigurovat IRs jako *propojené v místním prostředí* ve všech prostředích, jako je vývoj, testování a produkce.

## <a name="sample-deployment-template"></a>Ukázková šablona nasazení

Tady je ukázka šablony nasazení, který můžete importovat v kanálech Azure.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Ukázkový skript k zastavení a restartování aktivační události a vyčištění

Tady je ukázkový skript zastavit aktivačních událostí před nasazením a později restartuje aktivační události. Skript také zahrnuje kód odstraňte prostředky, které byly odebrány. Pokud chcete nainstalovat nejnovější verzi Azure Powershellu, najdete v článku [nainstalujte prostředí Azure PowerShell ve Windows pomocí Správce balíčků PowerShellGet](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-6.9.0).

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
$triggersADF = Get-AzureRmDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and $_.properties.pipelines.Count -gt 0} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzureRmDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzureRmDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzureRmDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzureRmDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzureRmDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzureRmDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzureRmDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzureRmDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzureRmDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzureRmDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzureRmResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzureRmResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzureRmResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzureRmDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Pomocí vlastních parametrů šablony Resource Manageru

Můžete definovat vlastní parametry pro šablonu Resource Manageru. Chcete-li soubor s názvem `arm-template-parameters-definition.json` v kořenové složce úložiště. (Název souboru musí odpovídat názvu právě ukázali.) Data Factory se pokusí přečíst soubor z toho větve, kterou právě pracujete, ne jenom z větve spolupráci. Pokud se nenajde žádný soubor, datová továrna používá výchozí parametry a hodnoty.

### <a name="syntax-of-a-custom-parameters-file"></a>Syntaxe souboru vlastních parametrů

Zde jsou uvedeny pokyny pro použití při vytváření souboru vlastních parametrů. Podívejte se na příklady syntaxe, naleznete v následující části [ukázkový soubor vlastní parametry](#sample).

1. Když zadáte pole v definičním souboru, určujete, že odpovídající vlastnost v šabloně je pole. Data Factory Iteruje přes všechny objekty v poli pomocí definice zadaná v první objektu array. Druhý objekt, řetězec, se stane názvem vlastnosti, která se používá jako název parametru pro každou iteraci.

    ```json
    ...
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
            }
        }
    },
    ...
    ```

2. Pokud nastavíte vlastnost název na `*`, určujete, že chcete šablonu, kterou chcete používat všechny vlastnosti na této úrovni, s výjimkou těch explicitně definované.

3. Když nastavíte hodnotu vlastnosti jako řetězec, určujete, že chcete parametrizovat vlastnost. Použijte formát `<action>:<name>:<stype>`.
    1.  `<action>` může být jedna z následujících znaků: 
        1.  `=`  prostředky zachovat aktuální hodnoty jako výchozí hodnota pro parametr.
        2.  `-` znamená, že není ponechte výchozí hodnotu pro parametr.
        3.  `|` je zvláštní případ pro tajné klíče z Azure Key Vault pro připojovací řetězec.
    2.  `<name>` je název parametru. Pokud `<name`> je pole prázdné, bude trvat název parametru 
    3.  `<stype>` je typ parametru. Pokud `<stype>` je prázdný, je výchozí typ řetězec.
4.  Pokud zadáte `-` znak na začátku názvu parametru, úplný název parametru se zkrátila na správce prostředků `<objectName>_<propertyName>`.
Například `AzureStorage1_properties_typeProperties_connectionString` zkrátila na `AzureStorage1_connectionString`.


### <a name="sample"></a> Ukázkový soubor vlastní parametry

Následující příklad ukazuje ukázkové parametry souboru. Tuto ukázku použijte jako odkaz na vytvořte svůj vlastní soubor vlastní parametry. Pokud soubor, který zadáte, není ve správném formátu JSON, Data Factory výstup chybovou zprávu v konzole prohlížeče a vrátí na výchozí parametry a hodnoty zobrazené v Uživatelském rozhraní služby Data Factory.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {},
    "Microsoft.DataFactory/factories/integrationRuntimes": {
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
                    }
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
                    "connectionString": "|:-connectionString:secureString"
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
        }
    }
}
```

## <a name="linked-resource-manager-templates"></a>Propojené šablony Resource Manageru

Pokud jste nastavili průběžnou integraci a nasazování (CI/CD) pro datové továrny, a podívat se, jak narůstá svým objektem pro vytváření, narazíte na omezení šablony Resource Manageru, jako je maximální počet prostředků nebo maximální velikost datové části v prostředku Šablona správce. Pro scénáře, jako jsou ty, spolu s kompletní šablonou Resource Manageru pro objekt pro vytváření, generování služby Data Factory také nyní generuje šablon propojené Resource Manageru. V důsledku toho máte datové části celý objekt pro vytváření rozdělit do několika souborů tak, aby při spuštění do uvedené limity.

Pokud máte nakonfigurovaný Git, propojené šablony jsou generovány a uložen spolu s plnou šablon Resource Manageru v `adf_publish` větve pod novou složku s názvem `linkedTemplates`.

![Propojená složka vedoucí šablony Resource Manageru](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Propojené Resource Manageru šablony mají obvykle hlavní šablonu a sadu šablon podřízené propojený na hlavní server. Je volána nadřazené šabloně `ArmTemplate_master.json`, a podřízené šablony jsou pojmenovány se vzorem `ArmTemplate_0.json`, `ArmTemplate_1.json`, a tak dále. Chcete-li přejít z úplnou šablonu Resource Manageru pro použití propojených šablon, aktualizujte úlohy CI/CD přejděte na `ArmTemplate_master.json` místo odkazující na `ArmTemplateForFactory.json` (to znamená úplný šablony Resource Manageru). Resource Manageru také vyžaduje, abyste propojenými šablonami nahrát do účtu úložiště, tak, aby k nim může přistupovat v Azure během nasazení. Další informace najdete v tématu [nasazení propojených šablon ARM pomocí VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Nezapomeňte přidat skripty služby Data Factory v kanálu CI/CD před a po nasazení úloh.

Pokud nemáte nakonfigurované Git, jsou přístupné přes propojenými šablonami **šablony ARM exportovat** gest.
