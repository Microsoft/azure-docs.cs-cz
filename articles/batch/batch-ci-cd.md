---
title: Použití Azure Pipelines k sestavení & nasazení řešení HPC
description: Naučte se, jak nasadit kanál sestavení nebo vydání pro aplikaci HPC běžící na Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/04/2021
ms.topic: how-to
ms.openlocfilehash: 7170044af58a508ff5a43751cc376f8b8d498444
ms.sourcegitcommit: ba676927b1a8acd7c30708144e201f63ce89021d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102435541"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Použití Azure Pipelines k sestavení a nasazení řešení HPC

Nástroje poskytované službou Azure DevOps se můžou překládat na automatizované sestavování a testování řešení s vysokým výkonem (HPC-Performance Computing). [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) poskytuje řadu moderních procesů kontinuální integrace (CI) a průběžného nasazování (CD) pro sestavování, nasazování, testování a monitorování softwaru. Tyto procesy urychlují doručování softwaru, což vám umožní soustředit se na váš kód, a ne na podporu infrastruktury a provozu.

Tento článek vysvětluje, jak nastavit procesy CI/CD pomocí [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) pro řešení HPC nasazená v Azure Batch.

## <a name="prerequisites"></a>Požadavky

Pokud chcete postupovat podle kroků v tomto článku, potřebujete [organizaci Azure DevOps](/azure/devops/organizations/accounts/create-organization). Budete také muset [vytvořit projekt v Azure DevOps](/azure/devops/organizations/projects/create-project).

Než začnete, je vhodné mít základní znalosti o [správě zdrojového kódu](/azure/devops/user-guide/source-control) a [Azure Resource Manager syntaxi šablon](../azure-resource-manager/templates/template-syntax.md) .

## <a name="create-an-azure-pipeline"></a>Vytvoření kanálu Azure

V tomto příkladu vytvoříte kanál sestavení a vydání pro nasazení infrastruktury Azure Batch a vydání balíčku aplikace. Za předpokladu, že je kód vyvíjen místně, je to obecný tok nasazení:

![Diagram znázorňující tok nasazení v kanálu,](media/batch-ci-cd/DeploymentFlow.png)

Tato ukázka používá několik šablon Azure Resource Manager a existujících binárních souborů. Tyto příklady můžete zkopírovat do úložiště a vložit je do Azure DevOps.

### <a name="understand-the-azure-resource-manager-templates"></a>Pochopení šablon Azure Resource Manager

Tento příklad používá několik šablon Azure Resource Manager k nasazení řešení. Tři šablony schopností (podobně jako jednotky nebo moduly) se používají k implementaci konkrétní funkce. K nasazení těchto základních šablon schopností se pak použije ucelená šablona řešení (deployment.jszapnutá). Tato [Struktura propojených šablon ](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) umožňuje individuálně otestovat a znovu použít jednotlivé šablony schopností v rámci řešení.

![Diagram znázorňující propojenou strukturu šablony s použitím šablon Azure Resource Manager.](media/batch-ci-cd/ARMTemplateHierarchy.png)

Tato šablona definuje účet služby Azure Storage, který je nutný k nasazení aplikace na účet Batch. Podrobné informace najdete v tématu [Referenční příručka k šabloně správce prostředků pro typy prostředků Microsoft. Storage](/azure/templates/microsoft.storage/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Další Šablona definuje [účet Azure Batch](accounts.md). Účet Batch funguje jako platforma pro spouštění mnoha aplikací napříč [fondy](nodes-and-pools.md#pools). Podrobné informace najdete v tématu [Referenční příručka k šabloně správce prostředků pro Microsoft.Batch typy prostředků](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

Další šablona vytvoří fond dávek v účtu Batch. Podrobné informace najdete v tématu [Referenční příručka k šabloně správce prostředků pro Microsoft.Batch typy prostředků](/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Finální šablona funguje jako produkt Orchestrator a nasazuje tři základní šablony schopností.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resource Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resource Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

### <a name="understand-the-hpc-solution"></a>Pochopení řešení HPC

Jak bylo uvedeno dříve, Tato ukázka používá několik šablon Azure Resource Manager a existujících binárních souborů. Tyto příklady můžete zkopírovat do úložiště a vložit je do Azure DevOps.

Pro toto řešení se jako balíček aplikace používá ffmpeg. [Balíček ffmpeg](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08) si můžete stáhnout, pokud ho ještě nemáte.

![Snímek obrazovky se strukturou úložiště.](media/batch-ci-cd/git-repository.jpg)

Existují čtyři hlavní části tohoto úložiště:

- Složka s **šablonami ARM** , která obsahuje šablony Azure Resource Manager
- **HPC-složka aplikace** , která obsahuje verzi Windows 64-bitovou kopii [ffmpeg 4.3.1](https://github.com/GyanD/codexffmpeg/releases/tag/4.3.1-2020-11-08).
- Složka s **kanály** obsahující soubor YAML, který definuje proces kanálu sestavení.
- Volitelné: složka **klient-aplikace** , což je kopie [souboru Azure Batch .NET s ukázkou ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Sample. Tato aplikace není pro tento článek nutná.


> [!NOTE]
> Toto je pouze jeden příklad struktury na základ kódu. Tento přístup se používá pro účely demonstrace, že se aplikace, infrastruktura a kód kanálu ukládají do stejného úložiště.

Teď, když je zdrojový kód nastavený, můžete začít s prvním sestavením.

## <a name="continuous-integration"></a>Kontinuální integrace

[Azure Pipelines](/azure/devops/pipelines/get-started/)v rámci Azure DevOps Services vám pomůže s implementací kanálu sestavení, testování a nasazení pro vaše aplikace.

V této fázi kanálu jsou testy obvykle spouštěny k ověření kódu a sestavování vhodné části softwaru. Počet a typy testů a všechny další úlohy, které spustíte, budou záviset na vaší širší strategii sestavení a vydání.

## <a name="prepare-the-hpc-application"></a>Příprava aplikace HPC

V této části budete pracovat se složkou **aplikace HPC-Application** . Tato složka obsahuje software (ffmpeg), který se spustí v rámci účtu Azure Batch.

1. Přejděte do části Builds (sestavení) Azure Pipelines ve vaší organizaci Azure DevOps. Vytvořte **Nový kanál**.

    ![Snímek obrazovky s novou obrazovkou kanálu](media/batch-ci-cd/new-build-pipeline.jpg)

1. Máte dvě možnosti, jak vytvořit kanál sestavení:

    a. [Použijte vizuálního návrháře](/azure/devops/pipelines/get-started-designer). Pokud to chcete udělat, vyberte na stránce **Nový kanál** možnost použít vizuálního návrháře.

    b. [Použijte sestavení YAML](/azure/devops/pipelines/get-started-yaml). Nový kanál YAML můžete vytvořit kliknutím na možnost Azure Repos nebo GitHub na stránce **Nový kanál** . Alternativně můžete uložit níže uvedený příklad v rámci správy zdrojového kódu a odkazovat na existující soubor YAML výběrem vizuálního návrháře a pak pomocí šablony YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Po nakonfigurování sestavení podle potřeby vyberte **uložit & Queue**. Pokud máte povolenou nepřetržitou integraci (v oddílu **triggery** ), sestavení se automaticky aktivuje při provedení nového potvrzení do úložiště, které splňuje podmínky nastavené v sestavení.

    ![Snímek obrazovky existujícího kanálu sestavení](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Projděte si část **sestavení** v Azure Pipelines a podívejte se na průběh sestavení v Azure DevOps. Vyberte odpovídající sestavení z definice sestavení.

    ![Snímek z živých výstupů z buildu v Azure DevOps.](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Použijete-li k provedení řešení HPC klientskou aplikaci, je nutné pro tuto aplikaci vytvořit samostatnou definici sestavení. V dokumentaci k [Azure Pipelines](/azure/devops/pipelines/get-started/index) můžete najít několik průvodců.

## <a name="continuous-deployment"></a>Průběžné nasazování

Azure Pipelines slouží také k nasazení aplikace a základní infrastruktury. [Kanály](/azure/devops/pipelines/release) vydaných verzí umožňují průběžné nasazování a automatizují proces vydávání verzí.

### <a name="deploy-your-application-and-underlying-infrastructure"></a>Nasazení aplikace a základní infrastruktury

K nasazení infrastruktury se zapojí řada kroků. Vzhledem k tomu, že toto řešení používá [propojené šablony](../azure-resource-manager/templates/linked-templates.md), bude nutné, aby tyto šablony byly dostupné z veřejného koncového bodu (http nebo https). Může to být úložiště na GitHubu, nebo účet Azure Blob Storage nebo jiné umístění úložiště. Nahrané artefakty šablony mohou zůstat v bezpečí, protože mohou být uchovávány v privátním režimu, ale přístupné pomocí nějaké formy tokenu sdíleného přístupového podpisu (SAS).

Následující příklad ukazuje, jak nasadit infrastrukturu se šablonami z Azure Storageého objektu BLOB.

1. Vytvořte **novou definici vydané verze** a pak vyberte prázdnou definici. Přejmenujte nově vytvořené prostředí na něco relevantního pro váš kanál.

    ![Snímek obrazovky s počátečním kanálem verze](media/batch-ci-cd/Release-0.jpg)

1. Vytvořte závislost na kanálu sestavení, abyste získali výstup pro aplikaci HPC.

    > [!NOTE]
    > Poznamenejte si **zdrojový alias**, protože bude potřeba, když se v definici vydané verze vytvoří úkoly.

    ![Snímek obrazovky ukazující odkaz na artefakt na HPCApplicationPackage v příslušném kanálu sestavení](media/batch-ci-cd/Release-1.jpg)

1. Vytvořte odkaz na jiný artefakt a tentokrát na úložiště Azure. Tento postup je vyžadován pro přístup k šablonám Správce prostředků uložených ve vašem úložišti. Jelikož šablony Správce prostředků nevyžadují kompilaci, nemusíte je vkládat prostřednictvím kanálu sestavení.

    > [!NOTE]
    > Znovu si poznamenejte **zdrojový alias**, jak bude potřeba později.

    ![Snímek obrazovky znázorňující odkaz artefaktu na Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Přejděte do části **proměnné** . V kanálu budete chtít vytvořit několik proměnných, takže nebudete muset znovu zadávat stejné informace do více úloh. V tomto příkladu se používají následující proměnné:

   - **applicationStorageAccountName**: název účtu úložiště, který obsahuje binární soubory aplikace HPC.
   - **batchAccountApplicationName**: název aplikace v účtu Batch
   - **batchAccountName**: název účtu Batch
   - **batchAccountPoolName**: název fondu virtuálních počítačů provádějících zpracování
   - **batchApplicationId**: jedinečné ID pro aplikaci Batch
   - **batchApplicationVersion**: sémantická verze vaší aplikace Batch (tj. binární soubory ffmpeg)
   - **umístění**: umístění prostředků Azure, které se mají nasadit
   - **resourceGroupName**: název skupiny prostředků, která se má vytvořit, a místa, kde budou nasazeny vaše prostředky
   - **storageAccountName**: název účtu úložiště, který obsahuje propojené šablony Správce prostředků.

   ![Snímek obrazovky zobrazující sady proměnných pro Azure Pipelines vydání](media/batch-ci-cd/Release-4.jpg)

1. Přejděte k úkolům pro vývojové prostředí. V níže uvedeném snímku vidíte šest úkolů. Tyto úlohy budou: Stáhněte si soubory ffmpeg, nasaďte účet úložiště, který bude hostovat vnořené Správce prostředků šablony, zkopírujte tyto Správce prostředků šablony do účtu úložiště, nasaďte účet Batch a požadované závislosti, vytvořte aplikaci v účtu Azure Batch a nahrajte balíček aplikace na účet Azure Batch.

    ![Snímek obrazovky znázorňující úlohy používané k uvolnění aplikace HPC do Azure Batch.](media/batch-ci-cd/Release-3.jpg)

1. Přidejte úlohu **Stáhnout artefakt kanálu (Preview)** a nastavte následující vlastnosti:
    - **Zobrazovaný název:** Stáhnout ApplicationPackage do agenta
    - **Název artefaktu, který se má stáhnout:** HPC-Application
    - **Cesta pro stažení do**: $ (System. DefaultWorkingDirectory)

1. Vytvořte účet úložiště pro uložení šablon Azure Resource Manager. Můžete použít existující účet úložiště z řešení, ale na podporu tohoto samostatného vzorku a izolace obsahu, vytvoříte vyhrazený účet úložiště.

    Přidejte úlohu **nasazení skupiny prostředků Azure** a nastavte následující vlastnosti:
    - **Zobrazovaný název:** Nasazení účtu úložiště pro šablony Správce prostředků
    - **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    - **Akce**: vytvořit nebo aktualizovat skupinu prostředků
    - **Skupina prostředků**: $ (resourceGroupName)
    - **Umístění**: $ (umístění)
    - **Šablona**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-Templates/storageAccount.jszapnuto
    - **Přepsat parametry šablony**:-account $ (storageAccountName)

1. Nahrajte artefakty ze správy zdrojového kódu do účtu úložiště pomocí Azure Pipelines. V rámci této Azure Pipelines úlohy může být identifikátor URI kontejneru účtu úložiště a token SAS určený k proměnné v Azure Pipelines, což umožňuje jejich znovu použití v rámci této fáze agenta.

    Přidejte úlohu **kopírování souborů Azure** a nastavte následující vlastnosti:
    - **Zdroj:** $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-Templates/
    - **Typ připojení Azure**: Azure Resource Manager
    - **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    - **Cílový typ**: Azure Blob
    - **Účet úložiště RM**: $ (storageAccountName)
    - **Název kontejneru**: šablony
    - **Identifikátor URI kontejneru úložiště**: templateContainerUri
    - **Token SAS kontejneru úložiště**: templateContainerSasToken

1. Nasaďte šablonu Orchestrator. Tato šablona obsahuje parametry pro identifikátor URI kontejneru účtu úložiště a token SAS. Proměnné požadované v šabloně Správce prostředků jsou buď uloženy v oddílu Variables definice verze, nebo byly nastaveny z jiné úlohy Azure Pipelines (například součástí úlohy kopírování objektů BLOB v Azure).

    Přidejte úlohu **nasazení skupiny prostředků Azure** a nastavte následující vlastnosti:
    - **Zobrazovaný název:** Nasazení Azure Batch
    - **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    - **Akce**: vytvořit nebo aktualizovat skupinu prostředků
    - **Skupina prostředků**: $ (resourceGroupName)
    - **Umístění**: $ (umístění)
    - **Šablona**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-Templates/deployment.jszapnuto
    - **Přepsat parametry šablony**: `-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)`

   Běžný postup je použít Azure Key Vault úlohy. Pokud má instanční objekt připojený k vašemu předplatnému Azure odpovídající nastavení zásad přístupu, může stahovat tajné kódy z Azure Key Vault a používat je jako proměnné v kanálu. Název tajného klíče se nastaví s přidruženou hodnotou. V definici vydané verze může být například odkaz na tajný kód sshPassword s použitím $ (sshPassword).

1. Další kroky volají rozhraní příkazového řádku Azure CLI. První slouží k vytvoření aplikace v Azure Batch a nahrání přidružených balíčků.

    Přidejte úlohu **Azure CLI** a nastavte následující vlastnosti:
    - **Zobrazovaný název:** Vytvoření aplikace v účtu Azure Batch
    - **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    - **Umístění skriptu**: vložený skript
    - **Vložený skript**: `az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)`

1. Druhý krok slouží k nahrání přidružených balíčků do aplikace (v tomto případě soubory ffmpeg).

    Přidejte úlohu **Azure CLI** a nastavte následující vlastnosti:
    - **Zobrazovaný název:** Nahrát balíček na účet Azure Batch
    - **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    - **Umístění skriptu**: vložený skript
    - **Vložený skript**: `az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip`

    > [!NOTE]
    > Číslo verze balíčku aplikace je nastaveno na proměnnou. To umožňuje přepsat předchozí verze balíčku a umožňuje ručně řídit číslo verze vloženého balíčku do Azure Batch.

1. Vytvořte novou verzi výběrem možnosti **vydání > vytvořit novou verzi**. Po aktivaci vyberte odkaz na novou verzi pro zobrazení stavu.

1. Pokud chcete zobrazit živý výstup z agenta, vyberte tlačítko **protokoly** pod vaším prostředím.

    ![Snímek obrazovky zobrazující stav vydané verze](media/batch-ci-cd/Release-5.jpg)

## <a name="test-the-environment"></a>Testování prostředí

Po nastavení prostředí potvrďte, že následující testy mohou být úspěšně dokončeny.

Připojte se k novému účtu Azure Batch pomocí Azure CLI z příkazového řádku PowerShellu.

- Přihlaste se ke svému účtu Azure pomocí `az login` a postupujte podle pokynů k ověření.
- Nyní ověřte účet Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Seznam dostupných aplikací

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Ověřte, že fond je platný.

```azurecli
az batch pool list
```

Všimněte si hodnoty `currentDedicatedNodes` z výstupu tohoto příkazu. Tato hodnota je upravena v dalším testu.

#### <a name="resize-the-pool"></a>Změna velikosti fondu

Změňte velikost fondu tak, aby byly k dispozici výpočetní uzly pro úlohy a testování úloh, pomocí příkazu Seznam fondů zobrazte aktuální stav, dokud se změna velikosti nedokončí a jsou dostupné uzly.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Další kroky

V těchto kurzech se dozvíte, jak pracovat s účtem Batch prostřednictvím jednoduché aplikace.

- [Spuštění paralelní úlohy pomocí Azure Batch s využitím rozhraní Python API](tutorial-parallel-python.md)
- [Spuštění paralelní úlohy pomocí služby Azure Batch s využitím rozhraní .NET API](tutorial-parallel-dotnet.md)
