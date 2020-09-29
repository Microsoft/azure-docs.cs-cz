---
title: Použití Azure Pipelines k sestavení & nasazení řešení HPC
description: Naučte se, jak nasadit kanál sestavení nebo vydání pro aplikaci HPC běžící na Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: d125427a3b2e519e909aad9e6f9cf08a963d2d46
ms.sourcegitcommit: a0c4499034c405ebc576e5e9ebd65084176e51e4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91461525"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Použití Azure Pipelines k sestavení a nasazení řešení HPC

Azure DevOps Services poskytuje řadu nástrojů, které vývojové týmy používají při vytváření vlastní aplikace. Nástroje poskytované službou Azure DevOps se můžou překládat na automatizované sestavování a testování vysoce výkonných výpočetních řešení. Tento článek ukazuje, jak nastavit průběžnou integraci (CI) a průběžné nasazování (CD) pomocí Azure Pipelines pro vysoce výkonné výpočetní řešení nasazené v Azure Batch.

Azure Pipelines poskytuje řadu moderních procesů CI/CD pro sestavování, nasazování, testování a monitorování softwaru. Tyto procesy urychlují doručování softwaru, což vám umožní soustředit se na váš kód, a ne na podporu infrastruktury a provozu.

## <a name="create-an-azure-pipeline"></a>Vytvoření kanálu Azure

V tomto příkladu vytvoříme kanál sestavení a vydání pro nasazení infrastruktury Azure Batch a vydání balíčku aplikace. Za předpokladu, že je kód vyvíjen místně, je to obecný tok nasazení:

![Diagram znázorňující tok nasazení v našem kanálu](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Nastavení

Pokud chcete postupovat podle kroků v tomto článku, potřebujete organizaci Azure DevOps a týmový projekt.

* [Vytvoření organizace Azure DevOps](/azure/devops/organizations/accounts/create-organization)
* [Vytvoření projektu v Azure DevOps](/azure/devops/organizations/projects/create-project)

### <a name="source-control-for-your-environment"></a>Správa zdrojového kódu pro vaše prostředí

Správa zdrojového kódu umožňuje týmům sledovat změny provedené v základu kódu a kontrolovat předchozí verze kódu.

Obvykle je Správa zdrojového kódu považovat za ruční s kódem softwaru. Jak základní infrastruktura funguje? To nám přináší infrastrukturu jako kód, kde budeme používat šablony Azure Resource Manager nebo jiné Open Source alternativy k deklarativnímu definování naší základní infrastruktury.

Tato ukázka silně spoléhá na řadu šablon Správce prostředků (dokumentů JSON) a existujících binárních souborech. Tyto příklady můžete zkopírovat do úložiště a vložit je do Azure DevOps.

Struktura základu kódu použitá v této ukázce se podobá následujícímu:

* Složka s **šablonami ARM** , která obsahuje několik šablon Azure Resource Manager. Šablony jsou vysvětleny v tomto článku.
* Složka **klient-aplikace** , což je kopie [souboru Azure Batch .NET s ukázkou ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Sample. To není pro tento článek nutné.
* **HPC-složka aplikace** , což je verze Windows 64-bit [ffmpeg 4.3.1](https://www.gyan.dev/ffmpeg/builds/packages/ffmpeg-4.3.1-2020-09-21-full_build.zip).
* Složka s **kanály** . Obsahuje soubor YAML s přehledem našeho procesu sestavení. Tento článek je popsán v článku.

V této části se předpokládá, že máte zkušenosti s řízením verzí a návrhem šablon Správce prostředků. Pokud tyto koncepty neznáte, přečtěte si následující stránky, kde najdete další informace.

* [Co je Správa zdrojového kódu?](/azure/devops/user-guide/source-control)
* [Pochopení struktury a syntaxe šablon Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Tento příklad využívá více šablon Správce prostředků k nasazení řešení. K tomu je potřeba použít řadu šablon schopností (podobně jako jednotky nebo moduly), které implementují určitou část funkčnosti. Používáme také ucelenou šablonu řešení, která zodpovídá za spojování základních možností. Tento přístup má několik výhod:

* Základní šablony schopností mohou být testovány jednotlivě.
* Základní šablony schopností lze definovat jako standardní v rámci organizace a znovu je použít ve více řešeních.

V tomto příkladu je k dispozici ucelená šablona řešení (deployment.json), která nasadí tři šablony. Základní šablony jsou šablony schopností zodpovědné za nasazení konkrétního aspektu řešení.

![Příklad propojené struktury šablony pomocí šablon Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

První šablona, kterou budeme pohlížet, je pro účet Azure Storage. Naše řešení vyžaduje účet úložiště pro nasazení aplikace na náš účet Batch. Při sestavování šablon Správce prostředků pro účty úložiště je vhodné vědět o [Správce prostředků Průvodce referenčními šablonami pro typy prostředků Microsoft. Storage](/azure/templates/microsoft.storage/allversions) .

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

V dalším kroku se podíváme na šablonu účtu Azure Batch. Účet Azure Batch slouží jako platforma pro spouštění mnoha aplikací napříč fondy (skupiny počítačů). [Pro Microsoft.Batch typů prostředků](/azure/templates/microsoft.batch/allversions) při sestavování správce prostředků šablon pro účty Batch je vhodné vědět o Správce prostředků referenční příručce šablon.

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

Další šablona ukazuje příklad vytvoření fondu Azure Batch (back-end počítače pro zpracování našich aplikací). [Pro Microsoft.Batch typů prostředků](/azure/templates/microsoft.batch/allversions) při sestavování správce prostředků šablon pro fondy účtů Batch se dá znát referenční příručka k šabloně správce prostředků.

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

Nakonec máme šablonu, která funguje podobně jako Orchestrator. Tato šablona zodpovídá za nasazení šablon schopností.

Další informace o [Vytváření propojených Azure Resource Manager šablon](../azure-resource-manager/templates/deployment-tutorial-linked-template.md) najdete v samostatném článku.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
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

#### <a name="the-hpc-solution"></a>Řešení HPC

Infrastrukturu a software lze definovat jako kód a společně umístěn ve stejném úložišti.

Pro toto řešení se jako balíček aplikace používá ffmpeg. Balíček ffmpeg se dá stáhnout [tady](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Příklad struktury úložiště Git](media/batch-ci-cd/git-repository.jpg)

Existují čtyři hlavní části tohoto úložiště:

* Složka **ARM-Templates** , která ukládá naši infrastrukturu jako kód
* Složka **HPC-Application** , která obsahuje binární soubory pro ffmpeg
* Složka s **kanály** obsahující definici pro náš kanál sestavení.
* **Volitelné**: složka **klient-aplikace** , která by mohla ukládat kód pro aplikaci .NET. Nepoužíváme ho v ukázce, ale ve vašem vlastním projektu můžete chtít spustit spuštění aplikace Batch pro prostředí HPC prostřednictvím klientské aplikace.

> [!NOTE]
> Toto je pouze jeden příklad struktury na základ kódu. Tento přístup se používá pro účely demonstrace, že se aplikace, infrastruktura a kód kanálu ukládají do stejného úložiště.

Teď, když je zdrojový kód nastavený, můžeme začít s prvním sestavením.

## <a name="continuous-integration"></a>Kontinuální integrace

[Azure Pipelines](/azure/devops/pipelines/get-started/)v rámci Azure DevOps Services vám pomůže s implementací kanálu sestavení, testování a nasazení pro vaše aplikace.

V této fázi kanálu jsou testy obvykle spouštěny k ověření kódu a sestavování vhodné části softwaru. Počet a typy testů a všechny další úlohy, které spustíte, budou záviset na vaší širší strategii sestavení a vydání.

## <a name="preparing-the-hpc-application"></a>Příprava aplikace HPC

V tomto příkladu se zaměříme na složku **HPC-Application** . Složka **HPC-Application** je ffmpeg software, který se spustí v rámci účtu Azure Batch.

1. Přejděte do části Builds (sestavení) Azure Pipelines ve vaší organizaci Azure DevOps. Vytvořte **Nový kanál**.

    ![Vytvořit nový kanál sestavení](media/batch-ci-cd/new-build-pipeline.jpg)

1. Máte dvě možnosti, jak vytvořit kanál sestavení:

    a. [Pomocí vizuálního návrháře](/azure/devops/pipelines/get-started-designer). Pokud ho chcete použít, klikněte na stránce **Nový kanál** na použít vizuálního návrháře.

    b. [Pomocí sestavení YAML](/azure/devops/pipelines/get-started-yaml). Nový kanál YAML můžete vytvořit kliknutím na možnost Azure Repos nebo GitHub na stránce nový kanál. Alternativně můžete uložit níže uvedený příklad v rámci správy zdrojového kódu a odkazovat na existující soubor YAML kliknutím na vizuální Návrhář a pak pomocí šablony YAML.

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

    ![Příklad existujícího kanálu sestavení](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Projděte si část **sestavení** v Azure Pipelines a podívejte se na průběh sestavení v Azure DevOps. Vyberte odpovídající sestavení z definice sestavení.

    ![Zobrazení živých výstupů z buildu](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Pokud používáte klientskou aplikaci ke spuštění aplikace pro dávku HPC, je nutné pro tuto aplikaci vytvořit samostatnou definici sestavení. V dokumentaci k [Azure Pipelines](/azure/devops/pipelines/get-started/index) můžete najít několik průvodců.

## <a name="continuous-deployment"></a>Průběžné nasazování

Azure Pipelines také použít k nasazení aplikace a základní infrastruktury. [Kanály](/azure/devops/pipelines/release) vydaných verzí jsou komponenty, které umožňují průběžné nasazování a automatizují proces vydávání verzí.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Nasazení aplikace a základní infrastruktury

K nasazení infrastruktury se zapojí řada kroků. Vzhledem k použití [propojených šablon](../azure-resource-manager/templates/linked-templates.md)bude nutné, aby tyto šablony byly dostupné z veřejného koncového bodu (http nebo https). Může to být úložiště na GitHubu, nebo účet Azure Blob Storage nebo jiné umístění úložiště. Nahrané artefakty šablony mohou zůstat v bezpečí, protože mohou být uchovávány v privátním režimu, ale přístupné pomocí nějaké formy tokenu sdíleného přístupového podpisu (SAS). Následující příklad ukazuje, jak nasadit infrastrukturu se šablonami z Azure Storageého objektu BLOB.

1. Vytvořte **novou definici vydané verze**a vyberte prázdnou definici. Pak bude potřeba nově vytvořené prostředí přejmenovat na něco důležitého pro náš kanál.

    ![Kanál prvotní verze](media/batch-ci-cd/Release-0.jpg)

1. Vytvořte závislost na kanálu sestavení, abyste získali výstup pro naši aplikaci HPC.

    > [!NOTE]
    > Znovu si poznamenejte **zdrojový alias**, který bude potřeba, když se v definici vydané verze vytvoří úkoly.

    ![Vytvoření odkazu na artefakt na HPCApplicationPackage v příslušném kanálu sestavení](media/batch-ci-cd/Release-1.jpg)

1. Vytvořte odkaz na jiný artefakt a tentokrát na úložiště Azure. Tento postup je vyžadován pro přístup k šablonám Správce prostředků uložených ve vašem úložišti. Jelikož šablony Správce prostředků nevyžadují kompilaci, nemusíte je vkládat prostřednictvím kanálu sestavení.

    > [!NOTE]
    > Znovu si poznamenejte **zdrojový alias**, který bude potřeba, když se v definici vydané verze vytvoří úkoly.

    ![Vytvořit odkaz na artefakt na Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Přejděte do části **proměnné** . V kanálu doporučujeme vytvořit několik proměnných, takže neumístíte stejné informace do více úloh. Jedná se o proměnné používané v tomto příkladu a o tom, jak mají vliv na nasazení.

    * **applicationStorageAccountName**: název účtu úložiště pro ukládání binárních souborů aplikace HPC
    * **batchAccountApplicationName**: název aplikace v účtu Azure Batch
    * **batchAccountName**: název účtu Azure Batch
    * **batchAccountPoolName**: název fondu virtuálních počítačů provádějících zpracování
    * **batchApplicationId**: jedinečné ID pro aplikaci Azure Batch
    * **batchApplicationVersion**: sémantická verze vaší aplikace Batch (tj. binární soubory ffmpeg)
    * **umístění**: umístění prostředků Azure, které se mají nasadit
    * **resourceGroupName**: název skupiny prostředků, která se má vytvořit, a místa, kde budou nasazeny vaše prostředky
    * **storageAccountName**: název účtu úložiště pro ukládání propojených správce prostředků šablon

    ![Příklad sady proměnných nastavených pro Azure Pipelines vydání](media/batch-ci-cd/Release-4.jpg)

1. Přejděte k úkolům pro vývojové prostředí. V níže uvedeném snímku vidíte šest úkolů. Tyto úlohy budou: Stáhněte si soubory ffmpeg, nasaďte účet úložiště, který bude hostovat vnořené Správce prostředků šablony, zkopírujte tyto Správce prostředků šablony do účtu úložiště, nasaďte účet Batch a požadované závislosti, vytvořte aplikaci v účtu Azure Batch a nahrajte balíček aplikace na účet Azure Batch.

    ![Příklad úloh používaných k uvolnění aplikace HPC do Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Přidejte úlohu **Stáhnout artefakt kanálu (Preview)** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Stáhnout ApplicationPackage do agenta
    * **Název artefaktu, který se má stáhnout:** HPC-Application
    * **Cesta pro stažení do**: $ (System. DefaultWorkingDirectory)

1. Vytvořte účet úložiště pro ukládání artefaktů. Mohl by být použit existující účet úložiště z řešení, ale pro vlastní ukázku a izolaci obsahu vytváříme pro naše artefakty vyhrazený účet úložiště (konkrétně šablony Správce prostředků).

    Přidejte úlohu **nasazení skupiny prostředků Azure** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nasazení účtu úložiště pro šablony Správce prostředků
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    * **Akce**: vytvořit nebo aktualizovat skupinu prostředků
    * **Skupina prostředků**: $ (resourceGroupName)
    * **Umístění**: $ (umístění)
    * **Šablona**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-Templates/storageAccount.jszapnuto
    * **Přepsat parametry šablony**:-account $ (storageAccountName)

1. Nahrajte artefakty ze správy zdrojového kódu do účtu úložiště. K provedení tohoto postupu je k dispozici úkol kanálu Azure. V rámci této úlohy se dá adresa URL kontejneru účtu úložiště a token SAS výstupem do proměnné v Azure Pipelines. To znamená, že se dá znovu použít v rámci této fáze agenta.

    Přidejte úlohu **kopírování souborů Azure** a nastavte následující vlastnosti:
    * **Zdroj:** $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-Templates/
    * **Typ připojení Azure**: Azure Resource Manager
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    * **Cílový typ**: Azure Blob
    * **Účet úložiště RM**: $ (storageAccountName)
    * **Název kontejneru**: šablony
    * **Identifikátor URI kontejneru úložiště**: templateContainerUri
    * **Token SAS kontejneru úložiště**: templateContainerSasToken

1. Nasaďte šablonu Orchestrator. Navrácením šablony Orchestrator z předchozích verzí si všimnete, že kromě tokenu SAS se nastavily parametry pro adresu URL kontejneru účtu úložiště. Měli byste si všimnout, že proměnné požadované v šabloně Správce prostředků jsou buď uloženy v oddílu Variables definice verze, nebo byly nastaveny z jiné úlohy Azure Pipelines (například součástí úlohy kopírování objektů BLOB v Azure).

    Přidejte úlohu **nasazení skupiny prostředků Azure** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nasazení Azure Batch
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    * **Akce**: vytvořit nebo aktualizovat skupinu prostředků
    * **Skupina prostředků**: $ (resourceGroupName)
    * **Umístění**: $ (umístění)
    * **Šablona**: $ (System. ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/ARM-Templates/deployment.jszapnuto
    * **Přepsat parametry šablony**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Běžný postup je použít Azure Key Vault úlohy. Pokud má instanční objekt (připojení k vašemu předplatnému Azure) odpovídající nastavení zásad přístupu, může stahovat tajné kódy z Azure Key Vault a používat je jako proměnné v kanálu. Název tajného klíče se nastaví s přidruženou hodnotou. V definici vydané verze může být například odkaz na tajný kód sshPassword s použitím $ (sshPassword).

1. Další kroky volají rozhraní příkazového řádku Azure CLI. První slouží k vytvoření aplikace v Azure Batch. a nahrajte přidružené balíčky.

    Přidejte úlohu **Azure CLI** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Vytvoření aplikace v účtu Azure Batch
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    * **Umístění skriptu**: vložený skript
    * **Vložený skript**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Druhý krok slouží k nahrání přidružených balíčků do aplikace. V našem případě soubory ffmpeg.

    Přidejte úlohu **Azure CLI** a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nahrát balíček na účet Azure Batch
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure.
    * **Umístění skriptu**: vložený skript
    * **Vložený skript**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Číslo verze balíčku aplikace je nastaveno na proměnnou. To je užitečné, pokud přepsané předchozí verze balíčku budou fungovat za vás a pokud chcete ručně řídit číslo verze vloženého balíčku na Azure Batch.

1. Vytvořte novou verzi výběrem možnosti **vydání > vytvořit novou verzi**. Po aktivaci vyberte odkaz na novou verzi pro zobrazení stavu.

1. Živý výstup z agenta si můžete zobrazit tak, že vyberete tlačítko **protokoly** pod vaším prostředím.

    ![Zobrazení stavu vydané verze](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Testování prostředí

Po nastavení prostředí potvrďte, že následující testy mohou být úspěšně dokončeny.

Připojte se k novému účtu Azure Batch pomocí Azure CLI z příkazového řádku PowerShellu.

* Přihlaste se ke svému účtu Azure pomocí `az login` a postupujte podle pokynů k ověření.
* Nyní ověřte účet Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

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

Kromě tohoto článku jsou k dispozici dva kurzy, které využívají ffmpeg, a to pomocí .NET a Pythonu. V těchto kurzech najdete další informace o tom, jak pracovat s účtem Batch prostřednictvím jednoduché aplikace.

* [Spuštění paralelní úlohy pomocí Azure Batch s využitím rozhraní Python API](tutorial-parallel-python.md)
* [Spuštění paralelní úlohy pomocí služby Azure Batch s využitím rozhraní .NET API](tutorial-parallel-dotnet.md)
