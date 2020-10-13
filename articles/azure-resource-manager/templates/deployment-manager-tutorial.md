---
title: Použití Azure Deployment Manager k nasazení šablon
description: Naučte se používat Správce prostředků šablony s Azure Deployment Manager k nasazení prostředků Azure.
author: mumian
ms.date: 08/25/2020
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 49465f05b5484dfd358136866b67ce35f789799f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88892928"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-public-preview"></a>Kurz: Použití Azure Deployment Manageru s šablonami Resource Manageru (Public Preview)

Zjistěte, jak pomocí [Azure Deployment Manageru](./deployment-manager-overview.md) nasazovat aplikace napříč několika oblastmi. Pokud dáváte přednost rychlejšímu přístupu, [Azure Deployment Manager rychlý Start](https://github.com/Azure-Samples/adm-quickstart) vytvoří požadované konfigurace v předplatném a přizpůsobí artefakty k nasazení aplikace napříč několika oblastmi. Rychlý Start provádí stejné úlohy jako v tomto kurzu.

Chcete-li použít Deployment Manager, je nutné vytvořit dvě šablony:

* **Šablona topologie:** popisuje prostředky Azure, ze kterých se skládá vaše aplikace, a kam se mají nasadit.
* **Šablona uvedení:** popisuje kroky, které se mají provést při nasazování aplikací.

> [!IMPORTANT]
> Pokud je vaše předplatné označené k testování nových funkcí Azure na Kanárských, můžete k nasazení do oblastí pro Kanárské použití použít jenom Azure Deployment Manager. 

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

* [Reference k Azure Deployment Manager REST API](/rest/api/deploymentmanager/).
* [Kurz: použití kontroly stavu v Azure Deployment Manager](./deployment-manager-tutorial-health-check.md).

Pokud předplatné Azure ještě nemáte, napřed si [vytvořte bezplatný účet](https://azure.microsoft.com/free/).

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

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

V umístěních USA – západ a USA – východ jsou přidělené dvě služby.  Obě služby mají dvě jednotky služeb – front-end webové aplikace a účet úložiště jako back-end. Definice jednotek služeb obsahují odkazy na soubory šablony a parametrů pro vytváření webových aplikací a účtů úložiště.

## <a name="download-the-tutorial-files"></a>Stažení výukových souborů

1. Stáhněte si [šablony a artefakty](https://github.com/Azure/azure-docs-json-samples/raw/master/tutorial-adm/ADMTutorial.zip) použité v tomto kurzu.
2. Rozbalte soubory na místním počítači.

Kořenový adresář obsahuje dvě složky:

* **ADMTemplates:** obsahuje šablony Deployment Manageru, mezi které patří:
  * CreateADMServiceTopology.json
  * CreateADMServiceTopology.Parameters.json
  * CreateADMRollout.json
  * CreateADMRollout.Parameters.json
* **ArtifactStore:** obsahuje artefakty šablony i binární artefakty. Viz [Příprava artefaktů](#prepare-the-artifacts).

Všimněte si, že existují dvě sady šablon.  Jedna sada představuje šablony Deployment Manageru, které se používají k nasazení topologie služby a uvedení. Druhá sada se volá z jednotek služeb za účelem vytváření webových služeb a účtů úložiště.

## <a name="prepare-the-artifacts"></a>Příprava artefaktů

Stažená složka ArtifactStore obsahuje dvě složky:

![Kurz Azure Deployment Manageru – diagram zdroje artefaktů](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

* Složka **templates**: obsahuje artefakty šablony. Složky **1.0.0.0** a **1.0.0.1** představují dvě verze binárních artefaktů. Obě verze obsahují složku pro obě služby (ServiceEUS a ServiceWUS). Každá služba obsahuje pár souborů šablony a parametrů pro vytvoření účtu úložiště a další pár pro vytvoření webové aplikace. Šablona webové aplikace volá komprimovaný balíček, který obsahuje soubory webové aplikace. Komprimovaný soubor je binární artefakt uložený ve složce binaries.
* Složka **binaries**: obsahuje binární artefakty. Složky **1.0.0.0** a **1.0.0.1** představují dvě verze binárních artefaktů. Obě verze obsahují jeden soubor ZIP pro vytvoření webové aplikace v umístění USA – západ a druhý soubor ZIP pro vytvoření webové aplikace v umístění USA – východ.

Tyto dvě verze (1.0.0.0 a 1.0.0.1) jsou určené k [nasazení revize](#deploy-the-revision). Přestože artefakty šablony i binární artefakty mají dvě verze, mezi těmito dvěma verzemi se liší pouze binární artefakty. Binární artefakty se v praxi aktualizují častěji než artefakty šablony.

1. V textovém editoru otevřete soubor **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json**. Toto je základní šablona pro vytvoření účtu úložiště.
2. Otevřete soubor **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**.

    ![Kurz Azure Deployment Manageru – šablona pro vytvoření webové aplikace](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Tato šablona volá balíček pro nasazení, který obsahuje soubory webové aplikace. V tomto kurzu komprimovaný balíček obsahuje pouze soubor index.html.
3. Otevřete soubor **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplicationParameters.json**.

    ![Kurz Azure Deployment Manageru – šablona pro vytvoření webové aplikace – parametr containerRoot](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-parameters-deploypackageuri.png)

    Hodnota deployPackageUri je cesta k balíčku pro nasazení. Tento parametr obsahuje proměnnou **$containerRoot**. Hodnota proměnné $containerRoot je zadaná v [šabloně uvedení](#create-the-rollout-template) jako zřetězení umístění SAS zdroje artefaktů, kořenového adresáře artefaktů a hodnoty deployPackageUri.
4. Otevřete soubor **\ArtifactStore\binaries\1.0.0.0\helloWorldWebAppWUS.zip\index.html**.

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

    V kódu HTML se zobrazí umístění a informace o verzi. Binární soubor ve složce 1.0.0.1 ukazuje „Version 1.0.0.1“ (Verze 1.0.0.1). Po nasazení služby můžete na tyto stránky přejít.
5. Prohlédněte si i ostatní soubory artefaktů. Pomůže vám to lépe porozumět scénáři.

Artefakty šablony se používají v šabloně topologie služby a binární artefakty se používají v šabloně uvedení. Šablona topologie i šablona uvedení definují jako zdroj artefaktů prostředek Azure. Tento prostředek odkazuje Resource Manager na artefakty šablony a binární artefakty použité v nasazení. Pro zjednodušení tohoto kurzu se k uložení artefaktů šablony i binárních artefaktů používá jeden účet úložiště. Oba zdroje artefaktů odkazují na stejný účet úložiště.

Spuštěním následujícího skriptu PowerShellu vytvořte skupinu prostředků, vytvořte kontejner úložiště, vytvořte kontejner objektů blob, nahrajte stažené soubory a pak vytvořte token SAS.

> [!IMPORTANT]
> **ProjectName** ve skriptu PowerShellu se používá ke generování názvů služeb Azure, které jsou nasazené v tomto kurzu. Různé služby Azure mají různé požadavky na názvy. Chcete-li zajistit, aby bylo nasazení úspěšné, vyberte název, který má méně než 12 znaků a použijte pouze malá písmena a číslice.
> Uložte kopii názvu projektu. V tomto kurzu použijete stejný projectName.

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

Vytvořte kopii adresy URL s tokenem SAS. Tuto hodnotu je potřeba vyplnit do příslušného pole v obou souborech parametrů (soubor parametrů topologie a soubor parametrů uvedení).

Otevřete kontejner z Azure Portal a ověřte, zda jsou odesílány **binární soubory** i složky **šablon** i soubory.

## <a name="create-the-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

V pozdější části kurzu nasadíte uvedení. K provedení akcí nasazení (například nasazení webových aplikací a účtu úložiště) je potřeba spravovaná identita přiřazená uživatelem. Tato identita musí mít udělený přístup k předplatnému Azure, do kterého službu nasazujete, a musí mít dostatečná oprávnění k dokončení nasazení artefaktů.

Je potřeba vytvořit spravovanou identitu přiřazenou uživatelem a nakonfigurovat řízení přístupu pro vaše předplatné.

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
2. Vytvořte [spravovanou identitu přiřazenou uživatelem](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. V levé nabídce portálu vyberte **Předplatná** a pak vyberte své předplatné.
4. Vyberte **řízení přístupu (IAM)** a pak vyberte **Přidat přiřazení role**.
5. Zadejte nebo vyberte tyto hodnoty:

    ![Kurz Azure Deployment Manageru – řízení přístupu spravované identity přiřazené uživatelem](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    * **Role:** Udělte dostatečná oprávnění k dokončení nasazení artefaktů (webové aplikace a účty úložiště). Pro účely tohoto kurzu vyberte roli **Přispěvatel**. V praxi byste měli oprávnění omezit na minimum.
    * **Přiřazený přístup:** Vyberte **Spravovaná identita přiřazená uživatelem**.
    * Vyberte spravovanou identitu přiřazenou uživatelem, kterou jste vytvořili v dřívější části kurzu.
6. Vyberte **Uložit**.

## <a name="create-the-service-topology-template"></a>Vytvoření šablony topologie služby

Otevřete soubor **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Parametry

Šablona obsahuje následující parametry:

* **ProjectName**: Tento název slouží k vytvoření názvů pro prostředky Deployment Manager. Například při použití "pnovak" je název topologie služby **pnovak**ServiceTopology.  Názvy prostředků se definují v sekci proměnných této šablony.
* **azureResourcelocation:** Pro zjednodušení tohoto kurzu všechny prostředky sdílí toto umístění, pokud není uvedeno jinak.
* **artifactSourceSASLocation:** Identifikátor URI SAS kontejneru objektů blob, ve kterém jsou uložené soubory šablon a parametrů jednotek služeb pro účely nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
* **templateArtifactRoot:** Posunutí cesty z kontejneru objektů blob, ve kterém jsou uložené šablony a parametry. Výchozí hodnota je **templates/1.0.0.0**. Tuto hodnotu neměňte, pokud nechcete změnit strukturu složek, jak je popsáno v části [Příprava artefaktů](#prepare-the-artifacts). V tomto kurzu se používají relativní cesty.  Úplná cesta se vytvoří zřetězením hodnot **artifactSourceSASLocation**, **templateArtifactRoot** a **templateArtifactSourceRelativePath** (nebo **parametersArtifactSourceRelativePath**).
* **targetSubscriptionID:** ID předplatného, do kterého se nasadí prostředky Deployment Manageru a kde se budou účtovat. Pro účely tohoto kurzu použijte vlastní ID předplatného.

### <a name="the-variables"></a>Proměnné

V sekci proměnných se definují názvy prostředků, umístění Azure pro obě služby (**Service WUS** a **Service EUS**) a cesty k artefaktům:

![Kurz Azure Deployment Manageru – proměnné šablony topologie](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Porovnejte cesty k artefaktům se strukturou složek, kterou jste nahráli do účtu úložiště. Všimněte si, že cesty k artefaktům jsou relativní cesty. Úplná cesta se vytvoří zřetězením hodnot **artifactSourceSASLocation**, **templateArtifactRoot** a **templateArtifactSourceRelativePath** (nebo **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Prostředky

Na kořenové úrovni se definují dva prostředky: *zdroj artefaktů* a *topologie služby*.

Definice zdroje artefaktů vypadá takto:

![Kurz Azure Deployment Manageru – prostředky šablony topologie – zdroj artefaktů](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Následující snímek obrazovky ukazuje pouze několik částí definice topologie služby, služeb a jednotek služeb:

![Kurz Azure Deployment Manageru – prostředky šablony topologie – topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

* **artifactSourceId** slouží k přidružení prostředku zdroje artefaktů k prostředku topologie služby.
* **dependsOn:** Všechny prostředky topologie služby jsou závislé na prostředku zdroje artefaktů.
* **artifacts** odkazuje na artefakty šablony.  Používají se zde relativní cesty. Úplná cesta se vytvoří zřetězením hodnot artifactSourceSASLocation (definovaná ve zdroji artefaktů), artifactRoot (definovaná ve zdroji artefaktů) a templateArtifactSourceRelativePath (nebo parametersArtifactSourceRelativePath).

### <a name="topology-parameters-file"></a>Soubor parametrů topologie

Vytvoříte soubor parametrů, který se použije pro šablonu topologie.

1. Ve Visual Studio Code nebo libovolném textovém editoru otevřete soubor **\ADMTemplates\CreateADMServiceTopology.Parameters**.
2. Vyplňte hodnoty parametrů:

    * **ProjectName**: zadejte řetězec s 4-5 znaky. Tento název se používá k vytváření jedinečných názvů prostředků Azure.
    * **azureResourceLocation:** Pokud jsou pro vás umístění Azure novinkou, použijte pro účely tohoto kurzu **centralus**.
    * **artifactSourceSASLocation:** Zadejte identifikátor URI SAS kořenového adresáře (kontejner objektů blob), ve kterém jsou uložené soubory šablon a parametrů jednotek služeb pro účely nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
    * **templateArtifactRoot:** Pokud nezměníte strukturu složek artefaktů, použijte pro účely tohoto kurzu **templates/1.0.0.0**.

> [!IMPORTANT]
> Šablona topologie a šablona uvedení sdílí několik společných parametrů. Tyto parametry musí mít stejné hodnoty. Tyto parametry jsou: **ProjectName**, **azureResourceLocation**a **artifactSourceSASLocation** (oba zdroje artefaktů sdílejí stejný účet úložiště v tomto kurzu).

## <a name="create-the-rollout-template"></a>Vytvoření šablony uvedení

Otevřete soubor **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Parametry

Šablona obsahuje následující parametry:

![Kurz Azure Deployment Manageru – parametry šablony uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

* **ProjectName**: Tento název slouží k vytvoření názvů pro prostředky Deployment Manager. Například použití "pnovak", název zavedení je **pnovak**zavedení.  Názvy se definují v sekci proměnných této šablony.
* **azureResourcelocation:** Pro zjednodušení tohoto kurzu všechny prostředky Deployment Manageru sdílí toto umístění, pokud není uvedeno jinak.
* **artifactSourceSASLocation:** Identifikátor URI SAS kořenového adresáře (kontejner objektů blob), ve kterém jsou uložené soubory šablon a parametrů jednotek služeb pro účely nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
* **binaryArtifactRoot:** Výchozí hodnota je **binaries/1.0.0.0**. Tuto hodnotu neměňte, pokud nechcete změnit strukturu složek, jak je popsáno v části [Příprava artefaktů](#prepare-the-artifacts). V tomto kurzu se používají relativní cesty.  Úplná cesta se vytvoří zřetězením hodnot **artifactSourceSASLocation**, **binaryArtifactRoot** a **deployPackageUri** zadaných v souboru CreateWebApplicationParameters.json.  Viz [Příprava artefaktů](#prepare-the-artifacts).
* **managedIdentityID:** Spravovaná identita přiřazená uživatelem, která provádí akce nasazení. Viz [Vytvoření spravované identity přiřazené uživatelem](#create-the-user-assigned-managed-identity).

### <a name="the-variables"></a>Proměnné

V sekci proměnných se definují názvy prostředků. Ujistěte se, že název topologie služby, názvy služeb a názvy jednotek služeb odpovídají názvům definovaným v [šabloně topologie](#create-the-service-topology-template).

![Kurz Azure Deployment Manageru – proměnné šablony uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-variables.png)

### <a name="the-resources"></a>Prostředky

Na kořenové úrovni se definují tři prostředky: zdroj artefaktů, krok a uvedení.

Definice zdroje artefaktů je stejná jako v šabloně topologie.  Další informace najdete v části [Vytvoření šablony topologie služby](#create-the-service-topology-template).

Následující snímek obrazovky ukazuje definici kroku čekání:

![Kurz Azure Deployment Manageru – prostředky šablony uvedení – krok čekání](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-wait-step.png)

Formát doby trvání odpovídá [standardu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601#Durations). **PT1M** (velká písmena jsou povinná) je příkladem 1minutového čekání.

Následující snímek obrazovky ukazuje pouze několik částí definice uvedení:

![Kurz Azure Deployment Manageru – prostředky šablony uvedení – uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-resources-rollout.png)

* **dependsOn:** Prostředek uvedení závisí na prostředku zdroje artefaktů a všech definovaných krocích.
* **artifactSourceId:** Slouží k přidružení prostředku zdroje artefaktů k prostředku uvedení.
* **targetServiceTopologyId:** Slouží k přidružení prostředku topologie služby k prostředku uvedení.
* **deploymentTargetId:** Toto je ID prostředku jednotky služby pro topologii služby.
* **preDeploymentSteps** a **postDeploymentSteps** obsahují kroky uvedení. V šabloně se volá krok čekání.
* **dependsOnStepGroups:** Konfigurace závislostí mezi skupinami kroků.

### <a name="rollout-parameters-file"></a>Soubor parametrů uvedení

Vytvoříte soubor parametrů, který se použije pro šablonu uvedení.

1. Ve Visual Studio Code nebo libovolném textovém editoru otevřete soubor **\ADMTemplates\CreateADMRollout.Parameters**.
2. Vyplňte hodnoty parametrů:

    * **ProjectName**: zadejte řetězec s 4-5 znaky. Tento název se používá k vytváření jedinečných názvů prostředků Azure.
    * **azureResourceLocation**: zadejte umístění Azure.
    * **artifactSourceSASLocation:** Zadejte identifikátor URI SAS kořenového adresáře (kontejner objektů blob), ve kterém jsou uložené soubory šablon a parametrů jednotek služeb pro účely nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
    * **binaryArtifactRoot:** Pokud nezměníte strukturu složek artefaktů, použijte pro účely tohoto kurzu **binaries/1.0.0.0**.
    * **managedIdentityID:** Zadejte ID spravované identity přiřazené uživatelem. Viz [Vytvoření spravované identity přiřazené uživatelem](#create-the-user-assigned-managed-identity). Syntaxe je:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Šablona topologie a šablona uvedení sdílí několik společných parametrů. Tyto parametry musí mít stejné hodnoty. Tyto parametry jsou: **ProjectName**, **azureResourceLocation**a **artifactSourceSASLocation** (oba zdroje artefaktů sdílejí stejný účet úložiště v tomto kurzu).

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

    Spouštíte-li tento skript z jiné relace prostředí PowerShell než z toho, který jste spustili skript [Příprava artefaktů](#prepare-the-artifacts) , je třeba nejprve přeplnit proměnné, které zahrnují **$resourceGroupName** a **$FilePath**.

    > [!NOTE]
    > `New-AzResourceGroupDeployment` je asynchronní volání. Zpráva o úspěchu pouze znamená, že nasazení bylo úspěšně zahájeno. Chcete-li ověřit nasazení, viz krok 2 a krok 4 tohoto postupu.

2. Pomocí webu Azure Portal ověřte úspěšné vytvoření topologie služby a prostředků:

    ![Kurz Azure Deployment Manageru – nasazené prostředky topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Políčko **Zobrazit skryté typy** musí být zaškrtnuté, aby se prostředky zobrazily.

3. <a id="deploy-the-rollout-template"></a>Nasaďte šablonu uvedení:

    ```azurepowershell
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Pomocí následujícího skriptu PowerShellu zkontrolujte průběh uvedení:

    ```azurepowershell
    # Get the rollout status
    $rolloutname = "${projectName}Rollout" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName `
        -Verbose
    ```

    Před spuštěním této rutiny je potřeba nainstalovat rutiny PowerShellu pro Deployment Manager. Viz Požadavky. Přepínač-verbose se dá použít k zobrazení celého výstupu.

    V následující ukázce se zobrazuje stav Running (Spuštěno):

    ```
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

    Po úspěšném nasazení uvedení by se měly zobrazit další dvě vytvořené skupiny prostředků, pro obě služby jedna.

## <a name="verify-the-deployment"></a>Ověření nasazení

1. Otevřete web [Azure Portal](https://portal.azure.com).
2. Přejděte k nově vytvořeným webovým aplikacím v nových skupinách prostředků vytvořených nasazením uvedení.
3. Otevřete webovou aplikaci ve webovém prohlížeči. Zkontrolujte umístění a verzi souboru index.html.

## <a name="deploy-the-revision"></a>Nasazení revize

Jakmile budete mít novou verzi (1.0.0.1) webové aplikace, můžete ji nasadit znovu pomocí následujícího postupu.

1. Otevřete soubor CreateADMRollout.Parameters.json.
2. Aktualizujte hodnotu **binaryArtifactRoot** na **binaries/1.0.0.1**.
3. Znovu nasaďte uvedení podle pokynů v části [Nasazení šablon](#deploy-the-rollout-template).
4. Ověřte nasazení podle pokynů v části [Ověření nasazení](#verify-the-deployment). Na webové stránce by se teď měla zobrazit verze 1.0.0.1.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už nasazené prostředky Azure nepotřebujete, vyčistěte je odstraněním skupiny prostředků.

1. Z Azure Portal v nabídce vlevo vyberte **Skupina prostředků** .
2. Pomocí pole **Filtrovat podle názvu** můžete vyfiltrovat skupiny prostředků vytvořené v tomto kurzu. Měly by být 3 až 4:

    * ** &lt; ProjectName>RG**: obsahuje prostředky Deployment Manager.
    * ** &lt; ProjectName>ServiceWUSrg**: obsahuje prostředky definované pomocí ServiceWUS.
    * ** &lt; ProjectName>ServiceEUSrg**: obsahuje prostředky definované pomocí ServiceEUS.
    * Skupina prostředků pro spravovanou identitu přiřazenou uživatelem.
3. Vyberte název skupiny prostředků.
4. V horní nabídce vyberte **Odstranit skupinu prostředků** .
5. Zopakujte poslední dva kroky a odstraňte ostatní skupiny prostředků vytvořené v rámci tohoto kurzu.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat Azure Deployment Manager. Informace o integraci monitorování stavu v Azure Deployment Manager najdete v tématu [kurz: použití kontroly stavu ve službě azure Deployment Manager](./deployment-manager-tutorial-health-check.md).
