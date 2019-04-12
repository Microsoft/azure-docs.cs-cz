---
title: Kanály Azure můžete vytvářet a nasazovat řešení HPC – Azure Batch | Dokumentace Microsoftu
description: Informace o nasazení kanálu buildů a vydání pro prostředí HPC aplikace běžící v Azure Batch.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: 1a19a5845ae5d9f5752b4c905a43bf959a60eb9d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2019
ms.locfileid: "59494640"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Kanály Azure můžete vytvářet a nasazovat řešení HPC

Azure DevOps služby poskytují celou řadu nástrojů používané vývojové týmy při vytváření vlastních aplikací. Nástroje poskytované systémem Azure DevOps může znamenat automatické vytváření a testování vysoce výkonných výpočetních řešení. Tento článek ukazuje, jak nastavit průběžnou integraci (CI) a průběžného nasazování (CD) pomocí Azure kanály pro vysoce výkonné výpočetní řešení, které jsou nasazené v Azure Batch.

Kanály Azure poskytuje celou řadu moderní procesy CI/CD pro sestavení, nasazení, testování a monitorování softwaru. Tyto procesy zrychlení vaší doručování softwaru, abyste mohli soustředit na kód než podporu infrastruktury a operací.

## <a name="create-an-azure-pipeline"></a>Vytvoření kanálu Azure

V tomto příkladu vytvoříme bude sestavení a vydání kanálu pro nasazení infrastruktury Azure Batch a verze balíčku aplikace. Za předpokladu, že kód je vyvinutý místně, to představuje obecné nasazení kroky:

![Diagram znázorňující tok nasazení do našeho kanálu](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Nastavení

Postupovat podle kroků v tomto článku, budete potřebovat organizaci Azure DevOps a týmového projektu.

* [Vytvořit organizaci Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Vytvoření projektu v Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Správa zdrojového kódu pro vaše prostředí

Správy zdrojového kódu umožňuje týmům ke sledování změn do základu kódu a zkontrolujte předchozí verze kódu.

Obvykle správy zdrojového kódu je představit ručně spolupráce s kódem softwaru. A co základní infrastrukturu? Tím se dostáváme k infrastruktuře jako kódu, kde budeme používat šablon Azure Resource Manageru nebo jiné opensourcové alternativy deklarativně definovat naši základní infrastrukturu.

Tato ukázka silně závisí na počtu šablon Resource Manageru (dokumenty JSON) a existující binární soubory. Můžete tyto příklady kopírovat do svého úložiště a jejich doručování do Azure DevOps.

Základ kódu struktury použité v této ukázce se podobá takto:

* **Šablony arm** složky, který obsahuje několik šablon Azure Resource Manageru. Šablony jsou vysvětleny v tomto článku.
* A **klientská aplikace** složce, která je kopie sady [Azure Batch .NET soubor zpracování pomocí aplikace ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) vzorku. Není to nutné pro účely tohoto článku.
* **Aplikací hpc** složce, která je Windows 64-bit verzi [aplikace ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* A **kanály** složky. Tato položka obsahuje soubor YAML sbalování náš proces sestavení. Tento postup je popsán v článku.

V této části se předpokládá, že máte zkušenosti s šablonami Resource Manageru verze ovládacího prvku a návrhu. Pokud nejste obeznámeni s tyto koncepty, najdete na následujících stránkách pro další informace.

* [Novinky správy zdrojových kódů?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Princip struktury a syntaxe šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Šablony Azure Resource Manageru

Tento příklad využívá nasazovat naše řešení více šablon Resource Manageru. K tomuto účelu Používáme řadu funkce šablony (podobně jako jednotky nebo moduly), které implementují konkrétní funkce. Jsme také použít šablonu-ucelené řešení, který je zodpovědný za přináší tyto podpůrné funkce dohromady. Existuje několik výhod tohoto přístupu:

* Základní funkce šablony mohou být samostatně testování částí.
* Základní šablony funkce lze definovat jako standardní uvnitř organizace a znovu použít ve více řešení.

V tomto příkladu je šablonu řešení začátku do konce (deployment.json), která nasadí tři šablony. Funkce šablony, odpovědného za nasazení specifický aspekt řešení jsou základní šablony.

![Příklad struktury propojené šablony pomocí šablon Azure Resource Manageru](media/batch-ci-cd/ARMTemplateHierarchy.png)

První šablona, která se podíváme na určený účet úložiště Azure. Naše řešení vyžaduje účet úložiště k nasazení aplikace do našich účtu Batch. Stojí za to být vědět, [referenční příručka šablony Resource Manageru pro typy prostředků Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) při vytváření šablon Resource Manageru pro účty úložiště.

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

V dalším kroku se podíváme na šabloně účet Azure Batch. Účet Azure Batch se chová jako platformu pro spouštění řadě aplikací ve fondech (seskupení počítačů). Stojí za to být vědět, [referenční příručka šablony Resource Manageru pro typy prostředků Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) při vytváření šablon Resource Manageru pro účty Batch.

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

Další šablony ukazuje příklad vytvoření fondu služby Azure Batch (počítače back-endu ke zpracování našich aplikací). Stojí za to být vědět, [referenční příručka šablony Resource Manageru pro typy prostředků Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) při vytváření šablon Resource Manageru pro fondy účtu Batch.

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

A konečně máme šablonu, která funguje podobně jako orchestrátor. Tato šablona je odpovědného za nasazení šablony funkce.

Také najdete další informace o [vytváření propojených šablon Azure Resource Manageru](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) v věnovaný samostatný článek.

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

Infrastrukturu a software je možné definovat jako kód a umístěny společně ve stejném úložišti.

Pro tohle řešení se používá aplikaci ffmpeg jako balíček aplikace. Je možné stáhnout balíček aplikace ffmpeg [tady](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Příklad struktury úložiště Git](media/batch-ci-cd/git-repository.jpg)

Existují čtyři hlavní části do tohoto úložiště:

* **Šablony arm** složku, která obsahuje naše infrastruktura jako kód
* **Aplikací hpc** složku, která obsahuje binární soubory pro aplikaci ffmpeg
* **Kanály** složku, která obsahuje definice pro náš kanál sestavení.
* **Volitelné**: **Klientská aplikace** složku, ve kterém bude uložený kódu pro aplikace .NET. Nepoužijeme to ve vzorku, ale ve vašem vlastním projektu můžete chtít provést spuštění aplikace Batch, HPC pomocí klientské aplikace.

> [!NOTE]
> Toto je pouze jeden z příkladů struktury na základ kódu. Tento přístup se používá pro účely prokázat, že aplikace, infrastruktury a kód kanálu jsou uložené ve stejném úložišti.

Teď, když zdrojový kód je nastaven, můžeme začít první sestavení.

## <a name="continuous-integration"></a>Nepřetržitá integrace

[Kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), v rámci služby Azure DevOps, pomáhá implementovat sestavení, testování a nasazení kanálu pro vaše aplikace.

V této fázi vašeho kanálu se obvykle testy k ověření kódu a sestavení odpovídající části softwaru. Počet a typy testů a jakékoli další úlohy, které spustíte bude záviset na širší sestavení a strategie vydání.

## <a name="preparing-the-hpc-application"></a>Příprava aplikací HPC

V tomto příkladu se zaměříme na **aplikací hpc** složky. **Aplikací hpc** složka je software ffmpeg, který se spustí z v rámci účtu Azure Batch.

1. Přejděte do části sestavení kanálů Azure ve vaší organizaci Azure DevOps. Vytvoření **nový kanál**.

    ![Vytvořit nový kanál sestavení](media/batch-ci-cd/new-build-pipeline.jpg)

1. Máte dvě možnosti, jak vytvořit kanál sestavení:

    a. [Pomocí vizuálního návrháře](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Pokud chcete použít, klikněte na tlačítko "Vizuálního návrháře použít" na **nový kanál** stránky.

    b. [Použití YAML sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Kliknutím na možnost Githubu na nové stránce kanálu nebo úložiště Azure můžete vytvořit nový kanál YAML. Alternativně můžete ukládat v příkladu níže ve vaší správě zdrojového kódu a odkazovat na existující soubor YAML, klikněte na vizuálního návrháře a pak pomocí šablony YAML.

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

1. Po nakonfigurování sestavení podle potřeby vyberte **Uložit & frontu**. Pokud máte povolené průběžné integrace (v **triggery** části), sestavení spustí automaticky při vytvoření nové potvrzení do úložiště, splňují podmínky nastavení v sestavení.

    ![Příklad existující kanál sestavení](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Zobrazit živé aktualizace v průběhu sestavení v Azure DevOps otevřením **sestavení** část kanály Azure. Vyberte příslušného sestavení z definice sestavení.

    ![Zobrazit živé výstupy z vašeho sestavení](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Pokud používáte klientskou aplikaci ke spuštění aplikace HPC Batch, potřebujete vytvořit definici samostatné sestavení pro danou aplikaci. Můžete najít několika návody v [kanály Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) dokumentaci.

## <a name="continuous-deployment"></a>Průběžné nasazování

Kanály Azure také použít k nasazení aplikací a podpůrnou infrastrukturou. [Vydávání kanálů](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) je komponenta, která umožňuje průběžné nasazování a automatizuje proces vydávání verzí.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Nasazení aplikace a základní infrastruktury

Existuje několik kroků potřebných k nasazení infrastruktury. Jak jsme použili [propojené šablony](../azure-resource-manager/resource-group-linked-templates.md), bude nutné tyto šablony přístupný z veřejného koncového bodu (HTTP nebo HTTPS). To může být v úložišti na Githubu, nebo účet úložiště objektů Blob v Azure nebo jiného umístění úložiště. Nahraná šablona artefakty může zůstat zabezpečená a jak se můžou uchovávat v privátním režimu ale přistupovat pomocí nějakou formu token sdíleného přístupového podpisu (SAS). Následující příklad ukazuje, jak nasadit infrastrukturu s využitím šablon z objektu blob Azure Storage.

1. Vytvoření **nové definice vydané verze**a vyberte prázdnou definici. Pak potřebujeme přejmenujte nově vytvořený prostředí na něco relevantní pro náš kanál.

    ![Počáteční verze kanálu](media/batch-ci-cd/Release-0.jpg)

1. Vytvoření závislosti na kanálu sestavení získat výstup pro aplikace HPC.

    > [!NOTE]
    > Jednou znovu si všimněte, **Alias zdroje**, jak budete ji potřebovat při vytváření úlohy uvnitř definice vydané verze.

    ![Vytvořit propojení artefaktu do HPCApplicationPackage v kanálu příslušného sestavení](media/batch-ci-cd/Release-1.jpg)

1. Vytvořte odkaz na jiný artefakt, tentokrát úložišti Azure. Je to nutné pro přístup k šablon Resource Manageru uložených v úložišti. Jak šablon Resource Manageru nevyžadují kompilaci, není nutné nabízené prostřednictvím kanálu sestavení.

    > [!NOTE]
    > Jednou znovu si všimněte, **Alias zdroje**, jak budete ji potřebovat při vytváření úlohy uvnitř definice vydané verze.

    ![Vytvořit propojení artefaktu do úložiště Azure](media/batch-ci-cd/Release-2.jpg)

1. Přejděte **proměnné** oddílu. Doporučuje se vytvořit počet proměnných ve vašem kanálu, takže nejsou zadávání stejných informací na více úkolů. Toto jsou proměnné používané v tomto příkladu a jak by mohly mít dopad nasazení.

    * **applicationStorageAccountName**: Název účtu úložiště pro uložení binární soubory aplikace HPC
    * **batchAccountApplicationName**: Název aplikace v účtu Azure Batch
    * **batchAccountName**: Název účtu Azure Batch
    * **batchAccountPoolName**: Název fondu virtuálních počítačů to zpracování
    * **batchApplicationId**: Jedinečné ID aplikace Azure Batch
    * **batchApplicationVersion**: Sémantické verze aplikace batch (to znamená, že aplikace ffmpeg binárních souborů)
    * **Umístění**: Umístění pro prostředky Azure k nasazení
    * **resourceGroupName**: Název skupiny prostředků, které mají být vytvořeny, a kde se prostředky nasadí
    * **storageAccountName**: Název účtu úložiště pro uložení propojenými šablonami Resource Manageru

    ![Příklad proměnné nastavené pro verzi kanály Azure](media/batch-ci-cd/Release-4.jpg)

1. Přejděte na úlohy pro vývojové prostředí. V následujícím snímku, zobrazí se šesti úkolů. Budou tyto úlohy: stahování souborů ZIP ffmpeg, účet úložiště pro hostování vnořené šablony Resource Manageru, zkopírování těchto šablon Resource Manageru k účtu úložiště, nasazení účtu batch a požadované závislosti, vytvořit aplikaci v nasazení Účet Azure Batch a nahrání balíčku aplikace k účtu Azure Batch.

    ![Příklad úloh použijí k uvolnění aplikace prostředí HPC do služby Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Přidat **stažení artefaktů kanálu (Preview)** úloh a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Stáhněte si ApplicationPackage agentům
    * **Název artefaktu ke stažení:** aplikací hpc
    * **Cesta pro stažení do**: $(System.DefaultWorkingDirectory)

1. Vytvořte účet úložiště k ukládání artefaktů. Může použít existující účet úložiště z řešení, ale pro samostatná ukázka a izolace obsahu, provádíme vyhrazeného úložiště účtu pro naše artefakty (konkrétně šablon Resource Manageru).

    Přidat **nasazení skupiny prostředků Azure** úloh a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nasazení účtu úložiště pro šablony Resource Manageru
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure
    * **Akce**: Vytvoření nebo aktualizace skupiny prostředků
    * **Skupina prostředků**: $(resourceGroupName)
    * **Umístění**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Přepsání parametrů šablony**: $(storageAccountName) – název účtu

1. Odešlete artefakty ze správy zdrojového kódu do účtu úložiště. Zde je úloha kanálu Azure k této. V rámci této úlohy se adresa URL kontejneru účtu úložiště a tokenu SAS výstupem proměnné v kanálech Azure. To znamená, že můžete znovu použít v průběhu této fáze agenta.

    Přidat **Azure File Copy** úloh a nastavte následující vlastnosti:
    * **Source:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/
    * **Typ připojení Azure**: Azure Resource Manager
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure
    * **Cílový typ**: Azure Blob
    * **RM Storage Account**: $(storageAccountName)
    * **Název kontejneru**: šablony
    * **Identifikátor URI kontejneru úložiště**: templateContainerUri
    * **Úložiště kontejneru SAS Token**: templateContainerSasToken

1. Nasazení šablony nástroje orchestrator. Odvolat šablony nástroje orchestrator z předchozí, všimnete si, že byly parametry pro adresu kontejneru účtu úložiště kromě tokenu SAS. Měli byste zaznamenat, že se buď nachází v sekci proměnných definice vydané verze proměnné v šabloně Resource Manageru vyžaduje, nebo nebyly nastaveny z jiné kanály Azure úlohy (například součástí úlohu kopírování objektu Blob Azure).

    Přidat **nasazení skupiny prostředků Azure** úloh a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nasazení služby Azure Batch
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure
    * **Akce**: Vytvoření nebo aktualizace skupiny prostředků
    * **Skupina prostředků**: $(resourceGroupName)
    * **Umístění**: $(location)
    * **Template**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Přepsání parametrů šablony**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Běžnou praxí je použití úkolů s Azure Key Vault. Pokud instanční objekt (připojení k vašemu předplatnému Azure) odpovídající přístup zásady s nastavením, můžete stáhnout tajné kódy ze služby Azure Key Vault a použít jako proměnné ve vašem kanálu. Název tajného kódu se nastaví s přidruženou hodnotu. Například mohou být odkazovány tajného kódu sshPassword s $(sshPassword) v definici vydané verze.

1. Další kroky volání rozhraní příkazového řádku Azure. První slouží k vytvoření aplikace ve službě Azure Batch. a odešlete přidružené balíčky.

    Přidat **rozhraní příkazového řádku Azure** úloh a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Vytvoření aplikace v účtu Azure Batch
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure
    * **Umístění skriptu**: Zpracování vloženého skriptu
    * **Zpracování vloženého skriptu**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Druhý krok se používá k odeslání přidružené balíčky do aplikace. V našem případě ffmpeg soubory.

    Přidat **rozhraní příkazového řádku Azure** úloh a nastavte následující vlastnosti:
    * **Zobrazovaný název:** Nahrání balíčku do účtu Azure Batch
    * **Předplatné Azure:** Vyberte příslušné předplatné Azure
    * **Umístění skriptu**: Zpracování vloženého skriptu
    * **Zpracování vloženého skriptu**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Číslo verze balíčku aplikace je nastavena na proměnnou. To je vhodné, pokud přepsat předchozí verze balíčku vám vyhovuje, a pokud chcete ručně řídit číslo verze balíčku do služby Azure Batch.

1. Vytvořit novou verzi výběrem **Release > vytvořit nové vydání**. Po aktivaci, vyberte odkaz na vaše nová verze, chcete-li zobrazit stav.

1. Živé výstup z agenta můžete zobrazit tak, že vyberete **protokoly** tlačítko pod vašeho prostředí.

    ![Zobrazení stavu vydaných verzí](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Prostředí pro testování

Po nastavení prostředí potvrďte, že následující testy lze úspěšně dokončit.

Připojte se k novému účtu Batch Azure, pomocí rozhraní příkazového řádku Azure z příkazového řádku Powershellu.

* Přihlaste se ke svému účtu Azure pomocí `az login` a postupujte podle pokynů k ověření.
* Nyní ověřit účet Batch: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Seznam dostupných aplikací

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Zkontrolujte, že platnost fondu

```azurecli
az batch pool list
```

Poznamenejte si hodnotu `currentDedicatedNodes` z výstupu tohoto příkazu. Tato hodnota se upraví další testu.

#### <a name="resize-the-pool"></a>Změna velikosti fondu

Změňte velikost fondu, takže máte výpočetní uzly, které jsou k dispozici pro úlohy a úkolů testování, obraťte se na příkazu fond seznamu chcete zobrazit aktuální stav, dokud se změna velikosti bylo dokončeno a je k dispozici uzlů

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Další postup

Kromě tohoto článku jsou k dispozici dva kurzy, které využívají aplikace ffmpeg, pomocí .NET a Python. Najdete v těchto kurzech pro další informace o tom, jak pracovat s účtem Batch přes jednoduchou aplikaci.

* [Spuštění paralelní úlohy pomocí služby Azure Batch s využitím rozhraní Python API](tutorial-parallel-python.md)
* [Spuštění paralelní úlohy pomocí služby Azure Batch pomocí rozhraní API .NET](tutorial-parallel-dotnet.md)
