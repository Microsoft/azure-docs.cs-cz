---
title: Použití Azure Deployment Manageru s šablonami Resource Manageru | Microsoft Docs
description: Nasazujte prostředky Azure s využitím šablon Resource Manageru a Azure Deployment Manageru.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: dougeby
editor: tysonn
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 11/27/2018
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 5c88bf00ed1f68e4ddab6175e86a46560c802744
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56238211"
---
# <a name="tutorial-use-azure-deployment-manager-with-resource-manager-templates-private-preview"></a>Kurz: Pomocí Správce nasazení Azure pomocí šablon Resource Manageru (privátní verze preview)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Zjistěte, jak pomocí [Azure Deployment Manageru](./deployment-manager-overview.md) nasazovat aplikace napříč několika oblastmi. Pokud chcete použít nástroj Deployment Manager, je potřeba vytvořit dvě šablony:

* **Šablona topologie:** popisuje prostředky Azure, ze kterých se skládá vaše aplikace, a kam se mají nasadit.
* **Šablona uvedení:** popisuje kroky, které se mají provést při nasazování aplikací.

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

Referenční dokumentace rozhraní REST API služby Azure Deployment Manager můžete najít [tady](https://docs.microsoft.com/rest/api/deploymentmanager/).

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto článku potřebujete:

* Určité zkušenosti s vývojem [šablon Azure Resource Manageru](./resource-group-overview.md).
* Azure Deployment Manager je ve verzi Private Preview. Pokud se chcete zaregistrovat k používání Azure Deployment Manageru, vyplňte [registrační formulář](https://aka.ms/admsignup). 
* Azure PowerShell Další informace najdete v tématu [Začínáme s Azure PowerShellem](https://docs.microsoft.com/powershell/azure/get-started-azureps).
* Rutiny Deployment Manageru. K instalaci těchto předběžných verzí rutin potřebujete nejnovější verzi modulu PowerShellGet. Pokud chcete získat nejnovější verzi, přečtěte si článek [Instalace modulu PowerShellGet](/powershell/gallery/installing-psget). Jakmile nainstalujete PowerShellGet, zavřete okno PowerShellu. Otevřete okno Powershellu new se zvýšenými oprávněními a zadejte následující příkaz:

    ```powershell
    Install-Module -Name AzureRM.DeploymentManager -AllowPrerelease
    ```
* [Průzkumníka služby Microsoft Azure Storage](https://azure.microsoft.com/features/storage-explorer/). Průzkumník služby Azure Storage se nevyžaduje, ale usnadní vám práci.

## <a name="understand-the-scenario"></a>Vysvětlení scénáře

Šablona topologie služby popisuje prostředky Azure, ze kterých se skládá vaše služba, a kam se mají nasadit. Definice topologie služby má následující hierarchii:

* Topologie služby
    * Služby
        * Jednotky služeb

Následující diagram znázorňuje topologii služby použitou v tomto kurzu:

![Kurz Azure Deployment Manageru – diagram scénáře](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-scenario-diagram.png)

V umístěních USA – západ a USA – východ jsou přidělené dvě služby.  Obě služby mají dvě jednotky služeb – front-end webové aplikace a účet úložiště jako back-end. Definice jednotek služeb obsahují odkazy na soubory šablony a parametrů pro vytváření webových aplikací a účtů úložiště.

## <a name="download-the-tutorial-files"></a>Stažení výukových souborů

1. Stáhněte si [šablony a artefakty](https://armtutorials.blob.core.windows.net/admtutorial/ADMTutorial.zip) použité v tomto kurzu.
2. Rozbalte soubory na místním počítači.

Kořenový adresář obsahuje dvě složky:

- **ADMTemplates:** obsahuje šablony Deployment Manageru, mezi které patří:
    - CreateADMServiceTopology.json
    - CreateADMServiceTopology.Parameters.json
    - CreateADMRollout.json
    - CreateADMRollout.Parameters.json
- **ArtifactStore:** obsahuje artefakty šablony i binární artefakty. Viz [Příprava artefaktů](#prepare-the-artifacts).

Všimněte si, že existují dvě sady šablon.  Jedna sada představuje šablony Deployment Manageru, které se používají k nasazení topologie služby a uvedení. Druhá sada se volá z jednotek služeb za účelem vytváření webových služeb a účtů úložiště.

## <a name="prepare-the-artifacts"></a>Příprava artefaktů

Stažená složka ArtifactStore obsahuje dvě složky:

![Kurz Azure Deployment Manageru – diagram zdroje artefaktů](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-artifact-source-diagram.png)

- Složka **templates**: obsahuje artefakty šablony. Složky **1.0.0.0** a **1.0.0.1** představují dvě verze binárních artefaktů. Obě verze obsahují složku pro obě služby (ServiceEUS a ServiceWUS). Každá služba obsahuje pár souborů šablony a parametrů pro vytvoření účtu úložiště a další pár pro vytvoření webové aplikace. Šablona webové aplikace volá komprimovaný balíček, který obsahuje soubory webové aplikace. Komprimovaný soubor je binární artefakt uložený ve složce binaries.
- Složka **binaries**: obsahuje binární artefakty. Složky **1.0.0.0** a **1.0.0.1** představují dvě verze binárních artefaktů. Obě verze obsahují jeden soubor ZIP pro vytvoření webové aplikace v umístění USA – západ a druhý soubor ZIP pro vytvoření webové aplikace v umístění USA – východ.

Tyto dvě verze (1.0.0.0 a 1.0.0.1) jsou určené k [nasazení revize](#deploy-the-revision). Přestože artefakty šablony i binární artefakty mají dvě verze, mezi těmito dvěma verzemi se liší pouze binární artefakty. Binární artefakty se v praxi aktualizují častěji než artefakty šablony.

1. V textovém editoru otevřete soubor **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateStorageAccount.json**. Toto je základní šablona pro vytvoření účtu úložiště.  
2. Otevřete soubor **\ArtifactStore\templates\1.0.0.0\ServiceWUS\CreateWebApplication.json**. 

    ![Kurz Azure Deployment Manageru – šablona pro vytvoření webové aplikace](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-create-web-application-packageuri.png)

    Tato šablona volá balíček pro nasazení, který obsahuje soubory webové aplikace. V tomto kurzu obsahuje komprimovaný balíček pouze soubor index.html.
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

1. Vytvoření účtu úložiště Azure Pokyny najdete v tématu [rychlý start: Nahrávání, stahování a výpis objektů BLOB pomocí webu Azure portal](../storage/blobs/storage-quickstart-blobs-portal.md).
2. Vytvořte v účtu úložiště kontejner objektů blob.
3. Do tohoto kontejneru objektů blob zkopírujte složky binaries a templates i s obsahem. [Průzkumník služby Microsoft Azure Storage](https://go.microsoft.com/fwlink/?LinkId=708343&clcid=0x409) podporuje funkci přetahování.
4. Pomocí následujících pokynů získejte umístění SAS kontejneru:

    1. V Průzkumníku služby Azure Storage přejděte do kontejneru objektů blob.
    2. V levém podokně klikněte na kontejner objektů blob pravým tlačítkem a vyberte **Získat sdílený přístupový podpis**.
    3. Nakonfigurujte **Čas spuštění** a **Čas ukončení platnosti**.
    4. Vyberte **Vytvořit**.
    5. Zkopírujte adresu URL. Tuto hodnotu je potřeba vyplnit do příslušného pole v obou souborech parametrů ([soubor parametrů topologie](#topology-parameters-file) a [soubor parametrů uvedení](#rollout-parameters-file)).

## <a name="create-the-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

V pozdější části kurzu nasadíte uvedení. K provedení akcí nasazení (například nasazení webových aplikací a účtu úložiště) je potřeba spravovaná identita přiřazená uživatelem. Tato identita musí mít udělený přístup k předplatnému Azure, do kterého službu nasazujete, a musí mít dostatečná oprávnění k dokončení nasazení artefaktů.

Je potřeba vytvořit spravovanou identitu přiřazenou uživatelem a nakonfigurovat řízení přístupu pro vaše předplatné.

> [!IMPORTANT]
> Spravovaná identita přiřazená uživatelem musí být ve stejném umístění jako [uvedení](#create-the-rollout-template). V současné době je možné prostředky Deployment Manageru, včetně uvedení, vytvářet pouze v oblastech USA – střed nebo USA – východ 2. Nicméně to platí pouze pro nástroj Deployment Manager prostředky (například topologie služby, služby, služby jednotky, zavedení a kroky). Cílové prostředky je možné nasadit do libovolné podporované oblasti Azure. V tomto kurzu například USA (střed) jsou nasazené prostředky Deployment Manager, ale služby jsou nasazené na USA – východ a USA – západ. Toto omezení se zruší v budoucnu.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vytvořte [spravovanou identitu přiřazenou uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md).
3. V levé nabídce portálu vyberte **Předplatná** a pak vyberte své předplatné.
4. Vyberte **řízení přístupu (IAM)** a pak vyberte **přidat přiřazení role**.
5. Zadejte nebo vyberte tyto hodnoty:

    ![Kurz Azure Deployment Manageru – řízení přístupu spravované identity přiřazené uživatelem](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-access-control.png)

    - **Role:** Udělte dostatečná oprávnění k dokončení nasazení artefaktů (webové aplikace a účty úložiště). Pro účely tohoto kurzu vyberte roli **Přispěvatel**. V praxi byste měli oprávnění omezit na minimum.
    - **Přiřazený přístup:** Vyberte **Spravovaná identita přiřazená uživatelem**.
    - Vyberte spravovanou identitu přiřazenou uživatelem, kterou jste vytvořili v dřívější části kurzu.
6. Vyberte **Uložit**.

## <a name="create-the-service-topology-template"></a>Vytvoření šablony topologie služby

Otevřete soubor **\ADMTemplates\CreateADMServiceTopology.json**.

### <a name="the-parameters"></a>Parametry

Šablona obsahuje následující parametry:

![Kurz Azure Deployment Manageru – parametry šablony topologie](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-parameters.png)

- **namePrefix**: Tato předpona slouží k vytvoření názvy pro prostředky nástroje Deployment Manager. Pokud například použijete předponu „jdoe“, název topologie služby bude **jdoe**ServiceTopology.  Názvy prostředků se definují v sekci proměnných této šablony.
- **azureResourcelocation**: Pro zjednodušení tento kurz, všechny prostředky sdílet toto umístění, pokud není určeno jinak. V současné době je možné prostředky Azure Deployment Manageru vytvářet pouze v oblastech **USA – střed** nebo **USA – východ 2**.
- **artifactSourceSASLocation**: Identifikátor URI SAS pro kontejner objektů Blob ukládat soubory pro šablonu a parametry jednotek služby pro nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
- **templateArtifactRoot**: Posunutí cesty z kontejneru objektů Blob, kde jsou uložené šablony a parametrů. Výchozí hodnota je **templates/1.0.0.0**. Tuto hodnotu neměňte, pokud nechcete změnit strukturu složek, jak je popsáno v části [Příprava artefaktů](#prepare-the-artifacts). V tomto kurzu se používají relativní cesty.  Úplná cesta se vytvoří zřetězením hodnot **artifactSourceSASLocation**, **templateArtifactRoot** a **templateArtifactSourceRelativePath** (nebo **parametersArtifactSourceRelativePath**).
- **targetSubscriptionID**: ID předplatného, ke kterému Správce nasazení prostředků se chystáte nasadit a účtuje. Pro účely tohoto kurzu použijte vlastní ID předplatného.

### <a name="the-variables"></a>Proměnné

Proměnné oddíl definuje názvy prostředků, umístění Azure pro dvě služby: **Služba WUS** a **služby EUS**a cesty artefaktů:

![Kurz Azure Deployment Manageru – proměnné šablony topologie](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-variables.png)

Porovnejte cesty k artefaktům se strukturou složek, kterou jste nahráli do účtu úložiště. Všimněte si, že cesty k artefaktům jsou relativní cesty. Úplná cesta se vytvoří zřetězením hodnot **artifactSourceSASLocation**, **templateArtifactRoot** a **templateArtifactSourceRelativePath** (nebo **parametersArtifactSourceRelativePath**).

### <a name="the-resources"></a>Prostředky

Na kořenové úrovni se definují dva prostředky: *zdroj artefaktů* a *topologie služby*.

Definice zdroje artefaktů vypadá takto:

![Kurz Azure Deployment Manageru – prostředky šablony topologie – zdroj artefaktů](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-artifact-source.png)

Následující snímek obrazovky ukazuje pouze několik částí definice topologie služby, služeb a jednotek služeb:

![Kurz Azure Deployment Manageru – prostředky šablony topologie – topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-topology-template-resources-service-topology.png)

- **artifactSourceId** slouží k přidružení prostředku zdroje artefaktů k prostředku topologie služby.
- **dependsOn**: Všechny prostředky topologie služby závisí na prostředek zdroje artefaktu.
- **artifacts** odkazuje na artefakty šablony.  Používají se zde relativní cesty. Úplná cesta se vytvoří zřetězením hodnot artifactSourceSASLocation (definovaná ve zdroji artefaktů), artifactRoot (definovaná ve zdroji artefaktů) a templateArtifactSourceRelativePath (nebo parametersArtifactSourceRelativePath).

> [!NOTE]
> Názvy jednotek musí obsahovat 31 znaků nebo méně. 

### <a name="topology-parameters-file"></a>Soubor parametrů topologie

Vytvoříte soubor parametrů, který se použije pro šablonu topologie.

1. Ve Visual Studio Code nebo libovolném textovém editoru otevřete soubor **\ADMTemplates\CreateADMServiceTopology.Parameters**.
2. Vyplňte hodnoty parametrů:

    - **namePrefix**: Zadejte řetězec s 4 až 5 znaků. Tato předpona slouží k vytváření jedinečných názvů prostředků Azure.
    - **azureResourceLocation**: Pokud nejste obeznámeni s umístění Azure, použijte **centralus** v tomto kurzu.
    - **artifactSourceSASLocation**: Zadejte identifikátor URI SAS do kořenového adresáře (kontejner objektů Blob) ukládat soubory pro šablonu a parametry jednotek služby pro nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
    - **templateArtifactRoot**: Pokud změníte strukturu složek artefakty, použijte **templates/1.0.0.0** v tomto kurzu.
    - **targetScriptionID**: Zadejte ID svého předplatného Azure.

> [!IMPORTANT]
> Šablona topologie a šablona uvedení sdílí několik společných parametrů. Tyto parametry musí mít stejné hodnoty. Těmito parametry jsou: **namePrefix**, **azureResourceLocation** a **artifactSourceSASLocation** (v tomto kurzu oba zdroje artefaktů sdílí stejný účet úložiště).

## <a name="create-the-rollout-template"></a>Vytvoření šablony uvedení

Otevřete soubor **\ADMTemplates\CreateADMRollout.json**.

### <a name="the-parameters"></a>Parametry

Šablona obsahuje následující parametry:

![Kurz Azure Deployment Manageru – parametry šablony uvedení](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-rollout-template-parameters.png)

- **namePrefix**: Tato předpona slouží k vytvoření názvy pro prostředky nástroje Deployment Manager. Pokud například použijete předponu „jdoe“, název uvedení bude **jdoe**Rollout.  Názvy se definují v sekci proměnných této šablony.
- **azureResourcelocation**: Pro zjednodušení tento kurz, všechny prostředky Deployment Manager sdílet toto umístění, pokud není určeno jinak. V současné době je možné prostředky Azure Deployment Manageru vytvářet pouze v oblastech **USA – střed** nebo **USA – východ 2**.
- **artifactSourceSASLocation**: Identifikátor URI SAS do kořenového adresáře (kontejner objektů Blob) ukládat soubory pro šablonu a parametry jednotek služby pro nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
- **binaryArtifactRoot**:  Výchozí hodnota je **binaries/1.0.0.0**. Tuto hodnotu neměňte, pokud nechcete změnit strukturu složek, jak je popsáno v části [Příprava artefaktů](#prepare-the-artifacts). V tomto kurzu se používají relativní cesty.  Úplná cesta se vytvoří zřetězením hodnot **artifactSourceSASLocation**, **binaryArtifactRoot** a **deployPackageUri** zadaných v souboru CreateWebApplicationParameters.json.  Viz [Příprava artefaktů](#prepare-the-artifacts).
- **managedIdentityID**: Uživatel přiřazenou spravovaná identita, která provede akce nasazení. Viz [Vytvoření spravované identity přiřazené uživatelem](#create-the-user-assigned-managed-identity).

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

- **dependsOn**: Uvedení prostředků závisí na prostředků zdroje artefaktu a všechny kroky uvedené.
- **artifactSourceId:** Slouží k přidružení prostředku zdroje artefaktů k prostředku uvedení.
- **targetServiceTopologyId:** Slouží k přidružení prostředku topologie služby k prostředku uvedení.
- **deploymentTargetId**: Je ID prostředku služby jednotka prostředků topologie služby.
- **preDeploymentSteps** a **postDeploymentSteps** obsahují kroky uvedení. V šabloně se volá krok čekání.
- **dependsOnStepGroups:** Konfigurace závislostí mezi skupinami kroků.

### <a name="rollout-parameters-file"></a>Soubor parametrů uvedení

Vytvoříte soubor parametrů, který se použije pro šablonu uvedení.

1. Ve Visual Studio Code nebo libovolném textovém editoru otevřete soubor **\ADMTemplates\CreateADMRollout.Parameters**.
2. Vyplňte hodnoty parametrů:

    - **namePrefix**: Zadejte řetězec s 4 až 5 znaků. Tato předpona slouží k vytváření jedinečných názvů prostředků Azure.
    - **azureResourceLocation**: V současné době je možné prostředky Azure Deployment Manageru vytvářet pouze v oblastech USA – střed nebo **USA – východ 2**.
    - **artifactSourceSASLocation**: Zadejte identifikátor URI SAS do kořenového adresáře (kontejner objektů Blob) ukládat soubory pro šablonu a parametry jednotek služby pro nasazení.  Viz [Příprava artefaktů](#prepare-the-artifacts).
    - **binaryArtifactRoot**: Pokud změníte strukturu složek artefakty, použijte **binaries/1.0.0.0** v tomto kurzu.
    - **managedIdentityID**: Zadejte spravované uživatelsky přiřazené identity. Viz [Vytvoření spravované identity přiřazené uživatelem](#create-the-user-assigned-managed-identity). Syntaxe je:

        ```
        "/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userassignedidentities/<ManagedIdentityName>"
        ```

> [!IMPORTANT]
> Šablona topologie a šablona uvedení sdílí několik společných parametrů. Tyto parametry musí mít stejné hodnoty. Těmito parametry jsou: **namePrefix**, **azureResourceLocation** a **artifactSourceSASLocation** (v tomto kurzu oba zdroje artefaktů sdílí stejný účet úložiště).

## <a name="deploy-the-templates"></a>Nasazení šablon

K nasazení šablon je možné použít Azure PowerShell. 

1. Spuštěním tohoto skriptu nasaďte topologii služby.

    ```azurepowershell-interactive
    $resourceGroupName = "<Enter a Resource Group Name>"
    $location = "Central US"  
    $filePath = "<Enter the File Path to the Downloaded Tutorial Files>"
    
    # Create a resource group
    New-AzResourceGroup -Name $resourceGroupName -Location $location
    
    # Create the service topology
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMServiceTopology.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMServiceTopology.Parameters.json"
    ```

2. Pomocí webu Azure Portal ověřte úspěšné vytvoření topologie služby a prostředků:

    ![Kurz Azure Deployment Manageru – nasazené prostředky topologie služby](./media/deployment-manager-tutorial/azure-deployment-manager-tutorial-deployed-topology-resources.png)

    Políčko **Zobrazit skryté typy** musí být zaškrtnuté, aby se prostředky zobrazily.

3. <a id="deploy-the-rollout-template"></a>Nasazení šablony nasazení:

    ```azurepowershell-interactive
    # Create the rollout
    New-AzResourceGroupDeployment `
        -ResourceGroupName $resourceGroupName `
        -TemplateFile "$filePath\ADMTemplates\CreateADMRollout.json" `
        -TemplateParameterFile "$filePath\ADMTemplates\CreateADMRollout.Parameters.json"
    ```

4. Pomocí následujícího skriptu PowerShellu zkontrolujte průběh uvedení:

    ```azurepowershell-interactive
    # Get the rollout status
    $rolloutname = "<Enter the Rollout Name>" # "adm0925Rollout" is the rollout name used in this tutorial
    Get-AzureRmDeploymentManagerRollout `
        -ResourceGroupName $resourceGroupName `
        -Name $rolloutName
    ```

    Před spuštěním této rutiny je potřeba nainstalovat rutiny PowerShellu pro Deployment Manager. Viz požadavky.

    V následující ukázce se zobrazuje stav Running (Spuštěno):
    
    ```
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

1. Na portálu Azure Portal vyberte v nabídce nalevo **Skupina prostředků**.
2. Pomocí pole **Filtrovat podle názvu** můžete vyfiltrovat skupiny prostředků vytvořené v tomto kurzu. Měly by být 3 až 4:

    - **&lt;namePrefix>rg:** Obsahuje prostředky Deployment Manageru.
    - **&lt;namePrefix>ServiceWUSrg:** Obsahuje prostředky definované službou ServiceWUS.
    - **&lt;namePrefix>ServiceEUSrg:** Obsahuje prostředky definované službou ServiceEUS.
    - Skupina prostředků pro spravovanou identitu přiřazenou uživatelem.
3. Vyberte název skupiny prostředků.  
4. V nabídce nahoře vyberte **Odstranit skupinu prostředků**.
5. Zopakujte poslední dva kroky a odstraňte ostatní skupiny prostředků vytvořené v rámci tohoto kurzu.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili používat Azure Deployment Manager. Další informace najdete v [dokumentaci k Azure Resource Manageru](/azure/azure-resource-manager/).
