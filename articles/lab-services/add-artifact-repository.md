---
title: Přidání úložiště artefaktů do testovacího prostředí v laboratořích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: Zjistěte, jak přidat úložiště artefaktů do testovacího prostředí v testovacích prostředích Azure DevTest.
services: devtest-lab
documentationcenter: na
author: spelluru
manager: ''
editor: ''
ms.service: devtest-lab
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/21/2019
ms.author: spelluru
ms.openlocfilehash: 2bb871119bece71c705ad9621a7c76c4b5ed0bc7
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81770240"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Přidání úložiště artefaktů do testovacího prostředí v devtest labs
DevTest Labs umožňuje zadat artefakt, který se má přidat do virtuálního virtuálního soudu v době vytvoření virtuálního virtuálního aplikace nebo po vytvoření virtuálního soudu. Tento artefakt může být nástroj nebo aplikace, kterou chcete nainstalovat na virtuální počítač. Artefakty jsou definovány v souboru JSON načtenéz githubu nebo azure devops git úložiště.

[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), spravované devTest Labs, poskytuje mnoho běžných nástrojů pro Windows i Linux. Do testovacího prostředí se automaticky přidá odkaz na toto úložiště. Můžete vytvořit vlastní úložiště artefaktů s konkrétními nástroji, které nejsou k dispozici ve veřejném úložišti artefaktů. Další informace o vytváření vlastních artefaktů naleznete v [tématu Vytváření vlastních artefaktů](devtest-lab-artifact-author.md).

Tento článek obsahuje informace o tom, jak přidat vlastní úložiště artefaktů pomocí portálu Azure, šablony Azure Resource Management a Azure PowerShell. Přidání úložiště artefaktů do testovacího prostředí můžete automatizovat zápisem skriptů PowerShellu nebo CLI.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Chcete-li přidat úložiště do testovacího prostředí, nejprve získejte klíčové informace z úložiště. Následující části popisují, jak získat požadované informace pro úložiště, které jsou hostované na **GitHubu** nebo **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získání klonované adresy URL úložiště GitHub a osobního přístupového tokenu

1. Přejděte na domovskou stránku úložiště GitHub, která obsahuje definice šablon artefaktů nebo Správce prostředků.
2. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
3. Chcete-li adresu URL zkopírovat do schránky, vyberte tlačítko **URL klonování HTTPS.** Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu GitHubu vyberte profilový obrázek a pak vyberte **Nastavení**.
5. V nabídce **Osobní nastavení** vlevo vyberte **Nastavení vývojáře**.
6. V levé nabídce vyberte **osobní přístupové tokeny.**
7. Vyberte **Generovat nový token**.
8. Na stránce **Nový osobní přístupový token** zadejte v části Popis **tokenu**popis. Přijměte výchozí položky v části **Vybrat obory**a pak vyberte **Generovat token**.
9. Uložte generovaný token. Token použijete později.
10. Zavřete GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Získání adresy URL klonování azure repos a osobnípřístupový token
1. Přejděte na domovskou stránku kolekce `https://contoso-web-team.visualstudio.com`týmů (například) a vyberte projekt.
2. Na domovské stránce projektu vyberte **Kód**.
3. Chcete-li zobrazit adresu URL klonování, vyberte na stránce **Kód** projektu **možnost Klonovat**.
4. Uložte adresu URL. Adresu URL použijete později.
5. Chcete-li vytvořit osobní přístupový token, vyberte v rozevírací nabídce uživatelského účtu **položku Můj profil**.
6. Na stránce s informacemi o profilu vyberte **možnost Zabezpečení**.
7. Na kartě **Bezpečnostní > osobní přístupové tokeny** vyberte **+ Nový token**.
8. Na stránce **Vytvořit nový osobní přístupový token:**
   1. Zadejte **název** tokenu.
   2. V seznamu **Organizace** vyberte **Všechny přístupné organizace**.
   3. V seznamu **Vypršení platnosti (UTC)** vyberte **90 dní**nebo vlastní definovanou dobu vypršení platnosti.
   4. Vyberte možnost **Úplný přístup** pro obory.
   5. Vyberte **Vytvořit**.
9. Nový token se zobrazí v seznamu **tokenů osobního přístupu.** Vyberte **Kopírovat token**a uložte hodnotu tokenu pro pozdější použití.
10. Pokračujte do části Připojit testovací prostředí k úložišti.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Tato část obsahuje kroky k přidání úložiště artefaktů do testovacího prostředí na webu Azure Portal.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **Další služby**a ze seznamu služeb vyberte **DevTest Labs.**
3. Ze seznamu testovacích prostředí vyberte testovací prostředí.
4. V levé nabídce vyberte **Možnost Konfigurace a zásady.**
5. V levé nabídce vyberte **Úložiště** v části **Externí zdroje.**
6. Vyberte **+ Přidat** na panelu nástrojů.

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na stránce **Repozitáře** zadejte následující informace:
   1. **Název**. Zadejte název úložiště.
   2. **Adresa URL klonování gitu**. Zadejte adresu URL klonování Git HTTPS, kterou jste dříve zkopírovali z GitHubu nebo služby Azure DevOps Services.
   3. **Pobočka**. Chcete-li získat své definice, zadejte větev.
   4. **Osobní přístupový token**. Zadejte osobní přístupový token, který jste získali dříve z GitHubu nebo Služby Azure DevOps.
   5. **Cesty ke složkám**. Zadejte alespoň jednu cestu ke složce složky, která obsahuje definice šablon artefaktů nebo správce prostředků. Když zadáte podadresář, ujistěte se, že do cesty ke složce zahrnete lomítko.

        ![Oblast repozitářů](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

## <a name="use-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager
Šablony Azure Resource Management (Azure Resource Manager) jsou soubory JSON, které popisují prostředky v Azure, které chcete vytvořit. Další informace o těchto šablonách najdete [v tématu Vytváření šablon Azure Resource Manager .](../azure-resource-manager/templates/template-syntax.md)

Tato část obsahuje postup přidání úložiště artefaktů do testovacího prostředí pomocí šablony Azure Resource Manager.  Šablona vytvoří testovací prostředí, pokud ještě neexistuje.

### <a name="template"></a>Šablona
Ukázková šablona použitá v tomto článku shromažďuje následující informace prostřednictvím parametrů. Většina parametrů má inteligentní výchozí hodnoty, ale existuje několik hodnot, které musí být zadány. Je nutné zadat název testovacího prostředí, identifikátor URI pro úložiště artefaktů a token zabezpečení pro úložiště.

- Název laboratoře.
- Zobrazovaný název úložiště artefaktů v uživatelském rozhraní DevTest Labs. Výchozí hodnota je: `Team Repository`.
- Uri do úložiště `https://github.com/<myteam>/<nameofrepo>.git` (Příklad: `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`nebo .
- Větev v úložišti, která obsahuje artefakty. Výchozí hodnota je: `master`.
- Název složky, která obsahuje artefakty. Výchozí hodnota je: `/Artifacts`.
- Typ úložiště. Povolené hodnoty `VsoGit` `GitHub`jsou nebo .
- Přístupový token pro úložiště.

    ```json
    {

        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "labName": {
                "type": "string"
            },
            "artifactRepositoryDisplayName": {
                "type": "string",
                "defaultValue": "Team Repository"
            },
            "artifactRepoUri": {
                "type": "string"
            },
            "artifactRepoBranch": {
                "type": "string",
                "defaultValue": "master"
            },
            "artifactRepoFolder": {
                "type": "string",
                "defaultValue": "/Artifacts"
            },
            "artifactRepoType": {
                "type": "string",
                "allowedValues": ["VsoGit", "GitHub"]
            },
            "artifactRepoSecurityToken": {
                "type": "securestring"
            }
        },
        "variables": {
            "artifactRepositoryName": "[concat('Repo-', uniqueString(subscription().subscriptionId))]"
        },
        "resources": [{
                "apiVersion": "2016-05-15",
                "type": "Microsoft.DevTestLab/labs",
                "name": "[parameters('labName')]",
                "location": "[resourceGroup().location]",
                "resources": [
                    {
                        "apiVersion": "2016-05-15",
                        "name": "[variables('artifactRepositoryName')]",
                        "type": "artifactSources",
                        "dependsOn": [
                            "[resourceId('Microsoft.DevTestLab/labs', parameters('labName'))]"
                        ],
                        "properties": {
                            "uri": "[parameters('artifactRepoUri')]",
                            "folderPath": "[parameters('artifactRepoFolder')]",
                            "branchRef": "[parameters('artifactRepoBranch')]",
                            "displayName": "[parameters('artifactRepositoryDisplayName')]",
                            "securityToken": "[parameters('artifactRepoSecurityToken')]",
                            "sourceType": "[parameters('artifactRepoType')]",
                            "status": "Enabled"
                        }
                    }
                ]
            }
        ]
    }
    ```


### <a name="deploy-the-template"></a>Nasazení šablony
Existuje několik způsobů, jak nasadit šablonu do Azure a mít prostředek vytvořený, pokud neexistuje nebo aktualizován, pokud existuje. Podrobnosti naleznete v následujících článcích:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a webu Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md)

Pojďme dál a uvidíme, jak nasadit šablonu v PowerShellu. Rutiny používané k nasazení šablony jsou specifické pro kontext, takže se používá aktuální klient a aktuální předplatné. Použijte [Set-AzContext](/powershell/module/az.accounts/set-azcontext) před nasazením šablony, v případě potřeby změnit kontext.

Nejprve vytvořte skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Pokud skupina prostředků, kterou chcete použít, již existuje, tento krok přeskočte.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Dále vytvořte nasazení do skupiny prostředků pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Tato rutina použije změny prostředků v Azure. Pro libovolnou skupinu prostředků lze provést několik nasazení prostředků. Pokud nasazujete několikrát do stejné skupiny prostředků, ujistěte se, že název každého nasazení je jedinečný.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Po New-AzResourceGroupDeployment úspěšně spustit, příkaz výstupy důležité informace, jako je stav zřizování (by měla být úspěšná) a všechny výstupy pro šablonu.

## <a name="use-azure-powershell"></a>Použití Azure Powershell
Tato část obsahuje ukázkový skript prostředí PowerShell, který lze použít k přidání úložiště artefaktů do testovacího prostředí. Pokud nemáte Azure PowerShell, najdete [v tématu Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/overview?view=azps-1.2.0) pro podrobné pokyny k jeho instalaci.

### <a name="full-script"></a>Celý skript
Zde je celý skript, včetně některých podrobných zpráv a komentářů:

**New-DevTestLabArtifactRepository.ps1**:

```powershell

<#

.SYNOPSIS
This script creates a new custom repository and adds it to an existing DevTest Lab.

.PARAMETER LabName
The name of the lab.

.PARAMETER LabResourceGroupName
The name of the resource group that contains the lab.

.PARAMETER ArtifactRepositoryName
Name for the new artifact repository.
Script creates a random name for the respository if it is not specified.

.PARAMETER ArtifactRepositoryDisplayName
Display name for the artifact repository.
This is the name that shows up in the Azure portal (https://portal.azure.com) when viewing all the artifact repositories for a lab.

.PARAMETER RepositoryUri
Uri to the repository.

.PARAMETER RepositoryBranch
Branch in which artifact files can be found. Defaults to 'master'.

.PARAMETER FolderPath
Folder under which artifacts can be found. Defaults to '/Artifacts'

.PARAMETER PersonalAccessToken
Security token for access to GitHub or VSOGit repository.
See https://azure.microsoft.com/documentation/articles/devtest-lab-add-artifact-repo/ for instructions to get personal access token

.PARAMETER SourceType
Whether artifact is VSOGit or GitHub repository.

.EXAMPLE
Set-AzContext -SubscriptionId 11111111-1111-1111-1111-111111111111
.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"

.NOTES
Script uses the current Az context. To set the context, use the Set-AzContext cmdlet

#>


[CmdletBinding()]
Param(

    [Parameter(Mandatory=$true)]
    $LabName,

    [Parameter(Mandatory=$true)]
    $LabResourceGroupName,
    $ArtifactRepositoryName,
    $ArtifactRepositoryDisplayName  = 'Team Artifact Repository',

    [Parameter(Mandatory=$true)]
    $RepositoryUri,
    $RepositoryBranch = 'master',
    $FolderPath = '/Artifacts',

    [Parameter(Mandatory=$true)]
    $PersonalAccessToken ,

    [Parameter(Mandatory=$true)]
    [ValidateSet('VsoGit', 'GitHub')]
    $SourceType
)


#Set artifact repository internal name,
# if not set by user.

if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}

# Sign in to Azure
Connect-AzAccount


#Get Lab Resource
$LabResource = Get-AzResource -ResourceType 'Microsoft.DevTestLab/labs' -ResourceName $LabName -ResourceGroupName $LabResourceGroupName

Write-Verbose "Lab Name: $($LabResource.Name)"
Write-Verbose "Lab Resource Group Name: $($LabResource.ResourceGroupName)"
Write-Verbose "Lab Resource Location: $($LabResource.Location)"

Write-Verbose "Artifact Repository Internal Name: $ArtifactRepositoryName"

#Prepare properties object for call to New-AzResource
$propertiesObject = @{
    uri = $RepositoryUri;
    folderPath = $FolderPath;
    branchRef = $RepositoryBranch;
    displayName = $ArtifactRepositoryDisplayName;
    securityToken = $PersonalAccessToken;
    sourceType = $SourceType;
    status = 'Enabled'
}

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"

#Resource will be added to current subscription.
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
Write-Verbose "Az ResourceType: $resourcetype"
Write-Verbose "Az ResourceName: $resourceName"

Write-Verbose "Creating artifact repository '$ArtifactRepositoryDisplayName'..."
$result = New-AzResource -Location $LabResource.Location -ResourceGroupName $LabResource.ResourceGroupName -properties $propertiesObject -ResourceType $resourcetype -ResourceName $resourceName -ApiVersion 2016-05-15 -Force


#Alternate implementation:
# Use resourceId rather than resourcetype and resourcename parameters.
# Using resourceId allows you to specify the $SubscriptionId rather than using the
# subscription id of Get-AzContext.
#$resourceId = "/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
#$result = New-AzResource -properties $propertiesObject -ResourceId $resourceId -ApiVersion 2016-05-15 -Force


# Check the result
if ($result.Properties.ProvisioningState -eq "Succeeded") {
    Write-Verbose ("Successfully added artifact repository source '$ArtifactRepositoryDisplayName'")
}
else {
    Write-Error ("Error adding artifact repository source '$ArtifactRepositoryDisplayName'")
}

#Return the newly created resource so it may be used in subsequent scripts
return $result
```

### <a name="run-the-powershell-script"></a>Spuštění powershellového skriptu
Následující příklad ukazuje, jak spustit skript:

```powershell
Set-AzContext -SubscriptionId <Your Azure subscription ID>

.\New-DevTestLabArtifactRepository.ps1 -LabName "mydevtestlab" -LabResourceGroupName "mydtlrg" -ArtifactRepositoryName "MyTeam Repository" -RepositoryUri "https://github.com/<myteam>/<nameofrepo>.git" -PersonalAccessToken "1111...." -SourceType "GitHub"
```


### <a name="parameters"></a>Parametry
Ukázkový skript prostředí PowerShell v tomto článku má následující parametry:

| Parametr | Popis |
| --------- | ----------- |
| Název lab | Jméno laboratoře. |
| Název úložiště artefaktů | Název nového úložiště artefaktů. Skript vytvoří náhodný název pro respository, pokud není zadán. |
| Název zobrazení artifactrepositorydisplayname | Zobrazovaný název úložiště artefaktů. Toto je název, který sehttps://portal.azure.com) zobrazí na webu Azure Portal ( při zobrazení všech úložišť artefaktů pro testovací prostředí. |
| ÚložištěUri | Uri do úložiště. Příklady: `https://github.com/<myteam>/<nameofrepo>.git` `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`nebo .|
| Pobočka úložiště | Větev, ve které lze nalézt soubory artefaktů. Výchozí hodnota je "master". |
| Cesta složky | Složka, pod kterou lze artefakty nalézt. Výchozí hodnoty na '/Artefakty' |
| Token osobního přístupu | Token zabezpečení pro přístup k úložišti GitHub nebo VSOGit. Pokyny k získání tokenu osobního přístupu naleznete v části požadavky. |
| SourceType | Ať už je artefakt úložiště VSOGit nebo GitHub. |

Samotné úložiště potřebuje interní název pro identifikaci, což se liší od zobrazovaného názvu, který se zobrazuje na webu Azure Portal. Nevidíte interní název pomocí portálu Azure, ale uvidíte ho při použití Azure REST API nebo Azure PowerShell. Skript poskytuje název, pokud není určen uživatelem našeho skriptu.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Příkazy prostředí PowerShell použité ve skriptu

| Příkaz PowerShellu | Poznámky |
| ------------------ | ----- |
| [Get-AzZdroj](/powershell/module/az.resources/get-azresource) | Tento příkaz se používá k získání podrobností o testovacím prostředí, jako je například jeho umístění. |
| [Nový-AzZdroj](/powershell/module/az.resources/new-azresource) | Neexistuje žádný konkrétní příkaz pro přidání úložiště artefaktů. Obecná rutina [New-AzResource](/powershell/module/az.resources/new-azresource) provádí práci. Tato rutina potřebuje buď **ResourceId** nebo **ResourceName** a **ResourceType** dvojice znát typ prostředku k vytvoření. Tento ukázkový skript používá dvojici názvu prostředku a typu prostředku. <br/><br/>Všimněte si, že vytváříte zdroj úložiště artefaktů ve stejném umístění a ve stejné skupině prostředků jako testovací prostředí.|

Skript přidá nový prostředek k aktuálnímu předplatnému. Pomocí [funkce Get-AzContext](/powershell/module/az.accounts/get-azcontext) zobrazíte tyto informace. Pomocí [set-AzContext](/powershell/module/az.accounts/set-azcontext) nastavte aktuální ho klienta a předplatné.

Nejlepší způsob, jak zjistit název prostředku a informace o typu prostředku, je použít web [API Test Drive Azure REST.](https://azure.github.io/projects/apis/) Podívejte se na [devTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) zprostředkovatele zobrazíte dostupné REST API pro poskytovatele DevTest Labs. Uživatelé skriptu následující ID prostředku.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Typ prostředku je vše uvedené po 'zprostředkovatelů' v URI, s výjimkou položek uvedených v složené závorky. Název prostředku je vše, co je vidět v kudrnaté závorky. Pokud je pro název zdroje očekáváno více než jedna položka, oddělte každou položku lomítkem tak, jak jsme to udělali.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Další kroky
- [Určení povinných artefaktů pro testovací prostředí v laboratořích Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Vytvoření vlastních artefaktů pro váš virtuální počítač DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnostika selhání artefaktů v laboratoři](devtest-lab-troubleshoot-artifact-failure.md)
