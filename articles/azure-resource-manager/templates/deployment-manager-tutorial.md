---
title: Použití Azure Správce nasazení k nasazení šablon
description: Naučte se používat Správce prostředků šablony s Azure Správce nasazení k nasazení prostředků Azure.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 95d5067eccff5c847588834061db8454f75e55d7
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627579"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Kurz: Použití Azure Deployment Manageru s šablonami Resource Manageru (Public Preview)

Zjistěte, jak pomocí [Azure Deployment Manageru](./deployment-manager-overview.md) nasazovat aplikace napříč několika oblastmi. Pokud dáváte přednost rychlejšímu přístupu, [Azure Správce nasazení rychlý Start](https://github.com/Azure-Samples/adm-quickstart) vytvoří požadované konfigurace v předplatném a přizpůsobí artefakty k nasazení aplikace napříč několika oblastmi. Rychlý Start provádí stejné úlohy jako v tomto kurzu.

Chcete-li použít Správce nasazení, je nutné vytvořit dvě šablony:

* **Šablona topologie:** popisuje prostředky Azure, ze kterých se skládá vaše aplikace, a kam se mají nasadit.
* **Šablona uvedení:** popisuje kroky, které se mají provést při nasazování aplikací.

> [!IMPORTANT]
> Pokud je vaše předplatné označené k testování nových funkcí Azure na Kanárských, můžete k nasazení do oblastí pro Kanárské použití použít jenom Azure Správce nasazení.

Tento kurz se zabývá následujícími úkony:

> [!div class="checklist"]
> * Vysvětlení scénáře
> * Stažení výukových souborů
> * Příprava artefaktů
> * Vytvoření uživatelem definované spravované identity
> * Vytvoření šablony topologie služby
> * Vytvoření šablony uvedení
> * Nasazení šablon
> * Ověření nasazení
> * Nasazení nejnovější verze
> * Vyčištění prostředků

Další prostředky:

* [Reference k Azure Správce nasazení REST API](/rest/api/deploymentmanager/).
* [Kurz: použití kontroly stavu v Azure Správce nasazení](./deployment-manager-tutorial-health-check.md).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

* Předplatné Azure. Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).
* Určité zkušenosti s vývojem [šablon Azure Resource Manageru](overview.md).
* Azure Powershell Další informace najdete v tématu [Začínáme s Azure PowerShellem](/powershell/azure/get-started-azureps).
* Rutiny Deployment Manageru. K instalaci těchto předběžných verzí rutin potřebujete nejnovější verzi modulu PowerShellGet. Pokud chcete získat nejnovější verzi, přečtěte si článek [Instalace modulu PowerShellGet](/powershell/scripting/gallery/installing-psget). Jakmile nainstalujete PowerShellGet, zavřete okno PowerShellu. Otevřete nové okno prostředí PowerShell se zvýšenými oprávněními a použijte tento příkaz:

    ```powershell
    Install-Module -Name Az.DeploymentManager
    ```

## <a name="understand-the-scenario"></a>Vysvětlení scénáře

Šablona topologie služby popisuje prostředky Azure, které tvoří vaši službu a kam je nasazovat. Definice topologie služby má následující hierarchii:

* Topologie služby
  * Služby
    * Jednotky služeb

Následující diagram znázorňuje topologii služby použitou v tomto kurzu:

![Kurz Azure Deployment Manageru – diagram scénáře](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

V Západní USA a umístěních Východní USA jsou přiděleny dvě služby. Každá služba má dvě jednotky služby: front-endové webové aplikace a účet back-endu. Definice jednotek služby obsahují odkazy na šablony a soubory parametrů, které vytvářejí webové aplikace a účty úložiště.

## <a name="download-the-tutorial-files"></a>Stažení výukových souborů

1. Stáhněte si [šablony a artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) použité v tomto kurzu.
1. Rozbalte soubory na místním počítači.

Kořenový adresář obsahuje dvě složky:

* _ADMTemplates:_ obsahuje šablony Deployment Manageru, mezi které patří:
  * _CreateADMServiceTopology.json_
  * _CreateADMServiceTopology.Parameters.json_
  * _CreateADMRollout.json_
  * _CreateADMRollout.Parameters.json_
* _ArtifactStore:_ obsahuje artefakty šablony i binární artefakty. Viz [Příprava artefaktů](#prepare-the-artifacts).

Existují dvě sady šablon. Jedna sada je Správce nasazení šablony, které slouží k nasazení topologie služby a zavedení. Druhá sada je volána z jednotek služby k vytvoření webových služeb a účtů úložiště.

## <a name="prepare-the-artifacts"></a>Příprava artefaktů

Stažená složka ArtifactStore obsahuje dvě složky:

![Kurz Azure Deployment Manageru – diagram zdroje artefaktů](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Složka _templates_: obsahuje artefakty šablony. Složky _1.0.0.0_ a _1.0.0.1_ reprezentují dvě verze binárních artefaktů. V rámci každé verze je k dispozici složka pro každou službu: _ServiceEUS_ (Service východní USA) a _ServiceWUS_ (západní USA Service). Každá služba obsahuje pár souborů šablony a parametrů pro vytvoření účtu úložiště a další pár pro vytvoření webové aplikace. Šablona webové aplikace volá komprimovaný balíček, který obsahuje soubory webové aplikace. Komprimovaný soubor je binární artefakt uložený ve složce binaries.
* Složka _binaries_: obsahuje binární artefakty. Složky _1.0.0.0_ a _1.0.0.1_ reprezentují dvě verze binárních artefaktů. V rámci každé verze je k dispozici jeden soubor zip pro vytvoření webové aplikace v umístění Západní USA a druhý soubor zip pro vytvoření webové aplikace v umístění Východní USA.

Tyto dvě verze (1.0.0.0 a 1.0.0.1) jsou určené k [nasazení revize](#deploy-the-revision). Přestože artefakty šablony i binární artefakty mají dvě verze, mezi těmito dvěma verzemi se liší pouze binární artefakty. Binární artefakty se v praxi aktualizují častěji než artefakty šablony.

1. V textovém editoru otevřete soubor _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json_. Je to základní šablona pro vytvoření účtu úložiště.
1. Otevřete soubor _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json_.

    ![Kurz Azure Deployment Manageru – šablona pro vytvoření webové aplikace](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Tato šablona volá balíček pro nasazení, který obsahuje soubory webové aplikace. V tomto kurzu komprimovaný balíček obsahuje pouze soubor _index.html_ .
1. Otevřete soubor _\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json_.

    ![Kurz Azure Deployment Manageru – šablona pro vytvoření webové aplikace – parametr containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Hodnota `deployPackageUri` je cesta k balíčku pro nasazení. Parametr obsahuje `$containerRoot` proměnnou. Hodnota `$containerRoot` je uvedena v [šabloně zavedení](#create-the-rollout-template) zřetězením umístění zdroje SAS pro artefakty, kořene artefaktu a `deployPackageUri` .
1. Otevřete soubor _\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html_.

    ```html
    <html>
      <head>
        <title>Azure Deployment Manager tutorial</title>
      </head>
      <body>
        <p>Hello world from west U.S.!</p>
        <p>Version 1.0.0.0</p>
      </body>
    </html>
    ```

    HTML zobrazuje umístění a informace o verzi. Binární soubor ve složce _1.0.0.1_ zobrazuje _verzi 1.0.0.1_. Po nasazení služby můžete na tyto stránky přejít.
1. Prohlédněte si i ostatní soubory artefaktů. Pomůže vám to lépe porozumět scénáři.

Artefakty šablony se používají v šabloně topologie služby a binární artefakty se používají v šabloně uvedení. Šablona topologie i šablona uvedení definují jako zdroj artefaktů prostředek Azure. Tento prostředek odkazuje Resource Manager na artefakty šablony a binární artefakty použité v nasazení. Pro zjednodušení tohoto kurzu se k uložení artefaktů šablony i binárních artefaktů používá jeden účet úložiště. Oba zdroje artefaktů odkazují na stejný účet úložiště.

Spuštěním následujícího skriptu PowerShellu vytvořte skupinu prostředků, vytvořte kontejner úložiště, vytvořte kontejner objektů blob, nahrajte stažené soubory a pak vytvořte token SAS.

> [!IMPORTANT]
> `projectName` ve skriptu PowerShellu se používá ke generování názvů služeb Azure, které jsou nasazené v tomto kurzu. Různé služby Azure mají různé požadavky na názvy. Chcete-li zajistit, aby bylo nasazení úspěšné, vyberte název, který má méně než 12 znaků a použijte pouze malá písmena a číslice.
> Uložte kopii názvu projektu. V `projectName` průběhu tohoto kurzu budete používat stejný.

```azurepowershell
$projectName = Read-Host -Prompt "Enter a project name that is used to generate Azure resource names"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$filePath = Read-Host -Prompt "Enter the folder that contains the downloaded files"


$resourceGroupName = "${projectName}rg"
$storageAccountName = "${projectName}store"
$containerName = "admfiles"
$filePathArtifacts = "${filePath}\ArtifactStore"

New-AzResourceGroup -Name $resourceGroupName -Location $location

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$storageContext = $storageAccount.Context

$storageContainer = New-AzStorageContainer -Name $containerName -Context $storageContext -Permission Off


$filesToUpload = Get-ChildItem $filePathArtifacts -Recurse -File

foreach ($x in $filesToUpload) {
    $targetPath = ($x.fullname.Substring($filePathArtifacts.Length + 1)).Replace("\", "/")

    Write-Verbose "Uploading $("\" + $x.fullname.Substring($filePathArtifacts.Length + 1)) to $($storageContainer.CloudBlobContainer.Uri.AbsoluteUri + "/" + $targetPath)"
    Set-AzStorageBlobContent -File $x.fullname -Container $storageContainer.Name -Blob $targetPath -Context $storageContext | Out-Null
}

$token = New-AzStorageContainerSASToken -name $containerName -Context $storageContext -Permission rl -ExpiryTime (Get-date).AddMonths(1)

$url = $storageAccount.PrimaryEndpoints.Blob + $containerName + $token

Write-Host $url
```

Vytvořte kopii adresy URL s tokenem SAS. Tato adresa URL je nutná k naplnění pole v obou souborech parametrů: soubor parametrů topologie a soubor parametrů zavedení.

Otevřete kontejner z Azure Portal a ověřte, zda jsou uloženy _binární soubory_ i složky _šablon_ i soubory.

## <a name="create-the-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

V pozdější části kurzu nasadíte uvedení. K provedení akcí nasazení (například nasazení webových aplikací a účtu úložiště) je potřeba spravovaná identita přiřazená uživatelem. Tato identita musí mít udělený přístup k předplatnému Azure, do kterého službu nasazujete, a musí mít dostatečná oprávnění k dokončení nasazení artefaktů.

Je potřeba vytvořit spravovanou identitu přiřazenou uživatelem a nakonfigurovat řízení přístupu pro vaše předplatné.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vytvořte [spravovanou identitu přiřazenou uživatelem](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
1. V levé nabídce portálu vyberte **Předplatná** a pak vyberte své předplatné.
1. Vyberte **řízení přístupu (IAM)** a pak vyberte **Přidat přiřazení role**.
1. Zadejte nebo vyberte tyto hodnoty:

    ![Kurz Azure Deployment Manageru – řízení přístupu spravované identity přiřazené uživatelem](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Role:** Udělte dostatečná oprávnění k dokončení nasazení artefaktů (webové aplikace a účty úložiště). Pro účely tohoto kurzu vyberte roli **Přispěvatel**. V praxi byste měli oprávnění omezit na minimum.
    * **Přiřadit přístup k**: vyberte **spravovanou identitu přiřazenou uživatelem**.
    * Vyberte spravovanou identitu přiřazenou uživatelem, kterou jste vytvořili v dřívější části kurzu.
1. Vyberte **Uložit**.

## <a name="create-the-service-topology-template"></a>Vytvoření šablony topologie služby

Otevřete soubor _\ADMTemplates\CreateADMServiceTopology.json_.

### <a name="the-parameters"></a>Parametry

Šablona obsahuje následující parametry:

* `projectName`: Tento název se používá k vytvoření názvů pro prostředky Správce nasazení. Například v **ukázce** je název topologie služby ServiceTopology **demo**. Názvy prostředků jsou definovány v `variables` oddílu šablony.
* `azureResourcelocation`: Pro zjednodušení kurzu budou všechny prostředky sdílet toto umístění, pokud není určeno jinak.
* `artifactSourceSASLocation`: Identifikátor URI SAS pro kontejner objektů blob, kde jsou uloženy soubory pro šablonu a parametry jednotky služby pro nasazení. Viz [Příprava artefaktů](#prepare-the-artifacts).
* `templateArtifactRoot`: Posunutí cesty z kontejneru objektů blob, kde jsou uložené šablony a parametry. Výchozí hodnota je _templates/1.0.0.0_. Tuto hodnotu neměňte, pokud nechcete změnit strukturu složek, jak je popsáno v části [Příprava artefaktů](#prepare-the-artifacts). V tomto kurzu se používají relativní cesty. Úplná cesta je vytvořena zřetězením `artifactSourceSASLocation` , `templateArtifactRoot` a `templateArtifactSourceRelativePath` (nebo `parametersArtifactSourceRelativePath` ).
* `targetSubscriptionID`: ID předplatného, na které se Správce nasazení prostředky nasazují a účtují. Pro účely tohoto kurzu použijte vlastní ID předplatného.

### <a name="the-variables"></a>Proměnné

Oddíl Variables definuje názvy prostředků, umístění Azure pro tyto dvě služby: `ServiceWUS` a a `ServiceEUS` cesty artefaktů:

![Kurz Azure Deployment Manageru – proměnné šablony topologie](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Porovnejte cesty k artefaktům se strukturou složek, kterou jste nahráli do účtu úložiště. Všimněte si, že cesty k artefaktům jsou relativní cesty. Úplná cesta je vytvořena zřetězením `artifactSourceSASLocation` , `templateArtifactRoot` a `templateArtifactSourceRelativePath` (nebo `parametersArtifactSourceRelativePath` ).

### <a name="the-resources"></a>Prostředky

Na kořenové úrovni jsou definovány dva prostředky: *zdroj artefaktů* a *topologie služby*.

Definice zdroje artefaktů vypadá takto:

![Kurz Azure Deployment Manageru – prostředky šablony topologie – zdroj artefaktů](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Následující snímek obrazovky ukazuje pouze několik částí definice topologie služby, služeb a jednotek služeb:

![Kurz Azure Deployment Manageru – prostředky šablony topologie – topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* `artifactSourceId`: Používá se k přidružení zdrojového prostředku artefaktu k prostředku topologie služby.
* `dependsOn`: Všechny prostředky topologie služby závisí na zdrojovém prostředku artefaktu.
* `artifacts`: Přejděte na artefakty šablony. Používají se zde relativní cesty. Úplná cesta je vytvořena zřetězením `artifactSourceSASLocation` (definovaným ve zdroji artefaktů), `artifactRoot` (definovaným ve zdroji artefaktů) a `templateArtifactSourceRelativePath` (nebo `parametersArtifactSourceRelativePath` ).

### <a name="topology-parameters-file"></a>Soubor parametrů topologie

Vytvoříte soubor parametrů, který se použije pro šablonu topologie.

1. Otevřete _\ADMTemplates\CreateADMServiceTopology.Parameters.js_ v v Visual Studio Code nebo v libovolném textovém editoru.
1. Zadejte hodnoty parametru:

    * `projectName`: Zadejte řetězec s 4-5 znaky. Tento název se používá k vytváření jedinečných názvů prostředků Azure.
    * `azureResourceLocation`: Pokud nejste obeznámeni s umístěním Azure, použijte **centralus** v tomto kurzu.
    * `artifactSourceSASLocation`: Zadejte identifikátor URI SAS do kořenového adresáře (kontejner objektů BLOB), kde jsou uloženy soubory pro šablonu a parametry jednotky služby pro nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
    * `templateArtifactRoot`: Pokud nezměníte strukturu složek artefaktů, použijte _šablony/1.0.0.0_ v tomto kurzu.

> [!IMPORTANT]
> Šablona topologie a šablona uvedení sdílí několik společných parametrů. Tyto parametry musí mít stejné hodnoty. Tyto parametry jsou: `projectName` , `azureResourceLocation` a `artifactSourceSASLocation` (oba zdroje artefaktů sdílejí stejný účet úložiště v tomto kurzu).

## <a name="create-the-rollout-template"></a>Vytvoření šablony uvedení

Otevřete soubor _\ADMTemplates\CreateADMRollout.json_.

### <a name="the-parameters"></a>Parametry

Šablona obsahuje následující parametry:

![Kurz Azure Deployment Manageru – parametry šablony uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* `projectName`: Tento název se používá k vytvoření názvů pro prostředky Správce nasazení. Například použití **ukázky** znamená, že název zavedení je **ukázková** implementace. Názvy jsou definovány v `variables` oddílu šablony.
* `azureResourcelocation`: Pro zjednodušení kurzu budou všechny Správce nasazení prostředky sdílet toto umístění, pokud není určeno jinak.
* `artifactSourceSASLocation`: Identifikátor URI SAS pro kořenový adresář (kontejner objektů BLOB), kde jsou uloženy soubory šablon a parametrů jednotky služby pro nasazení. Viz [Příprava artefaktů](#prepare-the-artifacts).
* `binaryArtifactRoot`: Výchozí hodnota je _binární soubory/1.0.0.0_. Tuto hodnotu neměňte, pokud nechcete změnit strukturu složek, jak je popsáno v části [Příprava artefaktů](#prepare-the-artifacts). V tomto kurzu se používají relativní cesty. Úplná cesta je vytvořena zřetězením `artifactSourceSASLocation` , `binaryArtifactRoot` a `deployPackageUri` zadaným v _CreateWebApplicationParameters.js_. Viz [Příprava artefaktů](#prepare-the-artifacts).
* `managedIdentityID`: Spravovaná identita přiřazená uživatelem, která provádí akce nasazení. Viz [Vytvoření spravované identity přiřazené uživatelem](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Proměnné

`variables`Oddíl definuje názvy prostředků. Ujistěte se, že název topologie služby, názvy služeb a názvy jednotek služeb odpovídají názvům definovaným v [šabloně topologie](#create-the-service-topology-template).

![Kurz Azure Deployment Manageru – proměnné šablony uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Prostředky

Na kořenové úrovni se definují tři prostředky: zdroj artefaktů, krok a uvedení.

Definice zdroje artefaktů je stejná jako v šabloně topologie. Další informace najdete v části [Vytvoření šablony topologie služby](#create-the-service-topology-template).

Následující snímek obrazovky ukazuje `wait` definici kroku:

![Kurz Azure Deployment Manageru – prostředky šablony uvedení – krok čekání](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Doba trvání používá [Standard ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (velká písmena jsou povinná) je příkladem 1minutového čekání.

Následující snímek obrazovky ukazuje pouze několik částí definice uvedení:

![Kurz Azure Deployment Manageru – prostředky šablony uvedení – uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* `dependsOn`: Prostředek zavedení závisí na zdrojovém prostředku artefaktu a na všech definovaných krocích.
* `artifactSourceId`: Používá se k přidružení zdrojového prostředku artefaktu k prostředku zavedení.
* `targetServiceTopologyId`: Používá se k přidružení prostředku topologie služby ke zdroji nasazení.
* `deploymentTargetId`: Toto je ID prostředku jednotky služby prostředku topologie služby.
* `preDeploymentSteps` a `postDeploymentSteps` : obsahuje kroky zavedení. V šabloně se `wait` volá krok.
* `dependsOnStepGroups`: Nakonfigurujte závislosti mezi skupinami kroků.

### <a name="rollout-parameters-file"></a>Soubor parametrů uvedení

Vytvoříte soubor parametrů, který se použije pro šablonu uvedení.

1. Otevřete _\ADMTemplates\CreateADMRollout.Parameters.js_ v v Visual Studio Code nebo v libovolném textovém editoru.
1. Zadejte hodnoty parametru:

    * `projectName`: Zadejte řetězec s 4-5 znaky. Tento název se používá k vytváření jedinečných názvů prostředků Azure.
    * `azureResourceLocation`: Zadejte umístění Azure.
    * `artifactSourceSASLocation`: Zadejte identifikátor URI SAS do kořenového adresáře (kontejner objektů BLOB), kde jsou uloženy soubory pro šablonu a parametry jednotky služby pro nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
    * `binaryArtifactRoot`: Pokud nezměníte strukturu složek artefaktů, použijte _binární soubory/1.0.0.0_ v tomto kurzu.
    * `managedIdentityID`: Zadejte spravovanou identitu přiřazenou uživatelem. Viz [Vytvoření spravované identity přiřazené uživatelem](#create-the-user-assigned-managed-identity). Syntaxe je:

        ```json
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Šablona topologie a šablona uvedení sdílí několik společných parametrů. Tyto parametry musí mít stejné hodnoty. Tyto parametry jsou: `projectName` , `azureResourceLocation` a `artifactSourceSASLocation` (oba zdroje artefaktů sdílejí stejný účet úložiště v tomto kurzu).

## <a name="deploy-the-templates"></a>Nasazení šablon

K nasazení šablon je možné použít Azure PowerShell.

1. Spuštěním tohoto skriptu nasaďte topologii služby.

    ```azurepowershell
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

    Spouštíte-li tento skript z jiné relace prostředí PowerShell než z toho, kterou jste spustili skript [Příprava artefaktů](#prepare-the-artifacts) , je nutné nejprve přeplnit proměnné, které zahrnují `$resourceGroupName` a `$filePath` .

    > [!NOTE]
    > `New-AzResourceGroupDeployment` je asynchronní volání. Zpráva o úspěchu pouze znamená, že nasazení bylo úspěšně zahájeno. Chcete-li ověřit nasazení, viz krok 2 a krok 4 tohoto postupu.

1. Pomocí webu Azure Portal ověřte úspěšné vytvoření topologie služby a prostředků:

    ![Kurz Azure Deployment Manageru – nasazené prostředky topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Políčko **Zobrazit skryté typy** musí být zaškrtnuté, aby se prostředky zobrazily.

1. <a id="deploy-the-rollout-template"></a>Nasaďte šablonu uvedení:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

1. Pomocí následujícího skriptu PowerShellu zkontrolujte průběh uvedení:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Před spuštěním této rutiny je potřeba nainstalovat rutiny PowerShellu pro Deployment Manager. Viz [Požadavky](#prerequisites). `-Verbose`Parametr lze použít k zobrazení celého výstupu.

    V následující ukázce se zobrazuje stav Running (Spuštěno):

    ```Output
    VERBOSE:

    Status: Succeeded
    ArtifactSourceId: /subscriptions/<AzureSubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    BuildVersion: 1.0.0.0

    Operation Info:
        Retry Attempt: 0
        Skip Succeeded: False
        Start Time: 03/05/2019 15:26:13
        End Time: 03/05/2019 15:31:26
        Total Duration: 00:05:12

    Service: adm0925ServiceEUS
        TargetLocation: EastUS
        TargetSubscriptionId: <AzureSubscriptionID>

        ServiceUnit: adm0925ServiceEUSStorage
            TargetResourceGroup: adm0925ServiceEUSrg

            Step: Deploy
                Status: Succeeded
                StepGroup: stepGroup3
                Operation Info:
                    DeploymentName: 2F535084871E43E7A7A4CE7B45BE06510adm0925ServiceEUSStorage
                    CorrelationId: 0b6f030d-7348-48ae-a578-bcd6bcafe78d
                    Start Time: 03/05/2019 15:26:32
                    End Time: 03/05/2019 15:27:41
                    Total Duration: 00:01:08
                Resource Operations:

                    Resource Operation 1:
                    Name: txq6iwnyq5xle
                    Type: Microsoft.Storage/storageAccounts
                    ProvisioningState: Succeeded
                    StatusCode: OK
                    OperationId: 64A6E6EFEF1F7755

    ...

    ResourceGroupName       : adm0925rg
    BuildVersion            : 1.0.0.0
    ArtifactSourceId        : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/artifactSources/adm0925ArtifactSourceRollout
    TargetServiceTopologyId : /subscriptions/<SubscriptionID>/resourceGroups/adm0925rg/providers/Microsoft.DeploymentManager/serviceTopologies/adm0925ServiceTopology
    Status                  : Running
    TotalRetryAttempts      : 0
    OperationInfo           : Microsoft.Azure.Commands.DeploymentManager.Models.PSRolloutOperationInfo
    Services                : {adm0925ServiceEUS, adm0925ServiceWUS}
    Name                    : adm0925Rollout
    Type                    : Microsoft.DeploymentManager/rollouts
    Location                : centralus
    Id                      : /subscriptions/<SubscriptionID>/resourcegroups/adm0925rg/providers/Microsoft.DeploymentManager/rollouts/adm0925Rollout
    Tags                    :
    ```

    Po úspěšném nasazení se zobrazí dvě další skupiny prostředků, jednu pro každou službu.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Otevřete [Azure Portal](https://portal.azure.com).
1. Přejděte k nově vytvořeným webovým aplikacím v rámci nových skupin prostředků vytvořených nasazením zavedení.
1. Otevřete webovou aplikaci ve webovém prohlížeči. Ověřte umístění a verzi v souboru _index.html_ .

## <a name="deploy-the-revision"></a>Nasazení revize

Jakmile budete mít novou verzi (1.0.0.1) webové aplikace, můžete ji nasadit znovu pomocí následujícího postupu.

1. Otevřete _CreateADMRollout.Parameters.js_.
1. Aktualizace `binaryArtifactRoot` _binárních souborů/1.0.0.1_
1. Znovu nasaďte uvedení podle pokynů v části [Nasazení šablon](#deploy-the-rollout-template).
1. Ověřte nasazení podle pokynů v části [Ověření nasazení](#verify-the-deployment). Na webové stránce by se teď měla zobrazit verze 1.0.0.1.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
1. Pomocí pole **Filtrovat podle názvu** můžete vyfiltrovat skupiny prostředků vytvořené v tomto kurzu.

    * **&lt; ProjectName>RG**: obsahuje prostředky Správce nasazení.
    * **&lt; ProjectName>ServiceWUSrg**: obsahuje prostředky definované pomocí ServiceWUS.
    * **&lt; ProjectName>ServiceEUSrg**: obsahuje prostředky definované pomocí ServiceEUS.
    * Skupina prostředků pro spravovanou identitu přiřazenou uživatelem.
1. Vyberte název skupiny prostředků.
1. V horní nabídce vyberte **Odstranit skupinu prostředků** .
1. Zopakujte poslední dva kroky a odstraňte ostatní skupiny prostředků vytvořené v rámci tohoto kurzu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat Azure Deployment Manager. Informace o integraci monitorování stavu v Azure Správce nasazení najdete v tématu [kurz: použití kontroly stavu ve službě azure Správce nasazení](./deployment-manager-tutorial-health-check.md).
