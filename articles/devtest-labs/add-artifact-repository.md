---
title: Přidání úložiště artefaktů do testovacího prostředí v Azure DevTest Labs | Microsoft Docs
description: Naučte se, jak zadat vlastní úložiště artefaktů pro testovací prostředí v Azure DevTest Labs k ukládání nástrojů nedostupných ve veřejném úložišti artefaktů.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: da4e345b18a46226853d71bbf66af0487f1a761f
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102502191"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Přidání úložiště artefaktů do testovacího prostředí v DevTest Labs
DevTest Labs umožňuje zadat artefakt, který se má přidat k virtuálnímu počítači v době vytváření nebo po vytvoření virtuálního počítače. Tento artefakt může být nástroj nebo aplikace, kterou chcete nainstalovat na virtuální počítač. Artefakty jsou definované v souboru JSON, který se načte z GitHubu nebo z úložiště Git Azure DevOps.

[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts)udržované DevTest Labs poskytuje mnoho běžných nástrojů pro Windows i Linux. Odkaz na toto úložiště se automaticky přidá do vašeho testovacího prostředí. Můžete vytvořit vlastní úložiště artefaktů s konkrétními nástroji, které nejsou k dispozici ve veřejném úložišti artefaktů. Další informace o vytváření vlastních artefaktů najdete v tématu [Vytvoření vlastních artefaktů](devtest-lab-artifact-author.md).

Tento článek poskytuje informace o tom, jak přidat vlastní úložiště artefaktů pomocí Azure Portal, šablon správy prostředků Azure a Azure PowerShell. Pomocí skriptů PowerShellu nebo rozhraní příkazového řádku můžete automatizovat Přidání úložiště artefaktů do testovacího prostředí.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Předpoklady
Pokud chcete do testovacího prostředí přidat úložiště, nejdřív Získejte klíčové informace z vašeho úložiště. Následující části popisují, jak získat požadované informace pro úložiště, která jsou hostovaná na **GitHubu** nebo v **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získání adresy URL pro klonování úložiště GitHub a tokenu osobního přístupu

1. Přejít na domovskou stránku úložiště GitHub, které obsahuje definice artefaktů nebo Správce prostředků šablon.
2. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
3. Chcete-li zkopírovat adresu URL do schránky, vyberte tlačítko **Adresa URL klonování https** . Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu GitHubu vyberte profilový obrázek a pak vyberte **Nastavení**.
5. V nabídce **osobní nastavení** na levé straně vyberte **Nastavení vývojáře**.
6. V nabídce vlevo vyberte možnost **osobní přístupové tokeny** .
7. Vyberte možnost **generovat nový token**.
8. Na stránce **Nový token osobního přístupu** zadejte v části **Popis tokenu** popis. Přijměte výchozí položky v části **Vybrat obory** a pak vyberte **vygenerovat token**.
9. Uložte vygenerovaný token. Token použijete později.
10. Zavřete GitHub.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Získat adresu URL pro klonování Azure Repos a osobní přístupový token
1. Přejít na domovskou stránku kolekce týmu (například `https://contoso-web-team.visualstudio.com` ) a pak vyberte svůj projekt.
2. Na domovské stránce projektu vyberte možnost **kód**.
3. Chcete-li zobrazit adresu URL klonování, vyberte na stránce **kód** projektu možnost **klonovat**.
4. Uložte adresu URL. Adresu URL použijete později.
5. Pokud chcete vytvořit osobní přístupový token, v rozevírací nabídce uživatelský účet vyberte **můj profil**.
6. Na stránce informace o profilu vyberte **zabezpečení**.
7. Na kartě **zabezpečení > tokeny osobních přístupů** vyberte **+ Nový token**.
8. Na stránce **Vytvoření nového osobního přístupového tokenu** :
   1. Zadejte **název** tokenu.
   2. V seznamu **organizace** vyberte **všechny dostupné organizace**.
   3. V seznamu **vypršení platnosti (UTC)** vyberte **90 dní** nebo vlastní definované období vypršení platnosti.
   4. Vyberte možnost **úplný přístup** pro rozsahy.
   5. Vyberte **Vytvořit**.
9. Nový token se zobrazí v seznamu **tokenů osobního přístupu** . Vyberte možnost **Kopírovat token** a pak hodnotu token uložte pro pozdější použití.
10. Přejděte k části připojení laboratoře k úložišti.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
V této části najdete postup přidání úložiště artefaktů do testovacího prostředí v Azure Portal.

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
2. Vyberte **Další služby** a v seznamu služeb vyberte **DevTest Labs** .
3. V seznamu cvičení vyberte testovací prostředí.
4. V nabídce vlevo vyberte **Konfigurace a zásady** .
5. V části **externí prostředky** v nabídce vlevo vyberte **úložiště** .
6. Na panelu nástrojů vyberte **+ Přidat** .

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na stránce **úložiště** zadejte následující informace:
   1. **Název:** Zadejte název úložiště.
   2. **Adresa Url git clone** Zadejte adresu URL klonu Git HTTPS, kterou jste zkopírovali dříve z GitHubu nebo z Azure DevOps Services.
   3. **Větev**. Pokud chcete získat definice, zadejte větev.
   4. **Osobní přístupový token** Zadejte osobní přístupový token, který jste už dříve získali z GitHubu nebo Azure DevOps Services.
   5. **Cesty ke složkám**. Zadejte alespoň jednu cestu ke složce vzhledem k adrese URL klonování, která obsahuje definice artefaktů nebo Správce prostředků šablon. Když zadáte podadresář, nezapomeňte v cestě ke složce zadat lomítko.

        ![Oblast úložišť](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

## <a name="use-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager
Šablony správy prostředků Azure (Azure Resource Manager) jsou soubory JSON, které popisují prostředky v Azure, které chcete vytvořit. Další informace o těchto šablonách najdete v tématu [vytváření Azure Resource Manager šablon](../azure-resource-manager/templates/template-syntax.md).

V této části najdete postup přidání úložiště artefaktů do testovacího prostředí pomocí šablony Azure Resource Manager.  Šablona vytvoří testovací prostředí, pokud ještě neexistuje.

### <a name="template"></a>Template (Šablona)
Ukázková šablona použitá v tomto článku shromažďuje následující informace prostřednictvím parametrů. Většina parametrů má inteligentní výchozí hodnoty, ale je třeba zadat několik hodnot. Je nutné zadat název testovacího prostředí, identifikátor URI pro úložiště artefaktů a token zabezpečení pro úložiště.

- Název testovacího prostředí.
- Zobrazovaný název úložiště artefaktů v uživatelském rozhraní DevTest Labs Výchozí hodnota je: `Team Repository` .
- Identifikátor URI úložiště (příklad: `https://github.com/<myteam>/<nameofrepo>.git` nebo `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .
- Větev v úložišti, která obsahuje artefakty. Výchozí hodnota je: `master` .
- Název složky, která obsahuje artefakty. Výchozí hodnota je: `/Artifacts` .
- Typ úložiště. Povolené hodnoty jsou `VsoGit` nebo `GitHub` .
- Přístupový token pro úložiště

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
Existuje několik způsobů, jak nasadit šablonu do Azure a vytvořit prostředek, pokud neexistuje, nebo pokud je aktualizovaný, pokud neexistuje. Podrobnosti najdete v následujících článcích:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/templates/deploy-powershell.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/templates/deploy-cli.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a webu Azure Portal](../azure-resource-manager/templates/deploy-portal.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/templates/deploy-rest.md)

Pojďme se podívat, jak nasadit šablonu do PowerShellu. Rutiny použité k nasazení šablony jsou specifické pro kontext, takže se používá aktuální tenant a aktuální předplatné. Použijte [set-AzContext](/powershell/module/az.accounts/set-azcontext) před nasazením šablony, pokud je to potřeba, pro změnu kontextu.

Nejdřív vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Pokud skupina prostředků, kterou chcete použít, už existuje, přeskočte tento krok.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Pak vytvořte nasazení do skupiny prostředků pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Tato rutina aplikuje změny prostředků na Azure. U jakékoli skupiny prostředků je možné provést několik nasazení prostředků. Pokud nasazujete několikrát do stejné skupiny prostředků, ujistěte se, že je název každého nasazení jedinečný.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Po úspěšném spuštění New-AzResourceGroupDeployment výstup příkazu vypíše důležité informace, jako je stav zřizování (měl by být úspěšný), a všechny výstupy pro šablonu.

## <a name="use-azure-powershell"></a>Použití Azure Powershell
V této části najdete ukázkový skript PowerShellu, který se dá použít k přidání úložiště artefaktů do testovacího prostředí. Pokud nemáte Azure PowerShell, přečtěte si téma [Jak nainstalovat a nakonfigurovat Azure PowerShell](/powershell/azure/) podrobné pokyny k jeho instalaci.

### <a name="full-script"></a>Celý skript
Tady je úplný skript, včetně některých podrobných zpráv a komentářů:

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
Script creates a random name for the repository if it is not specified.

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
Vzorový skript PowerShellu v tomto článku má následující parametry:

| Parametr | Popis |
| --------- | ----------- |
| LabName | Název testovacího prostředí. |
| ArtifactRepositoryName | Název nového úložiště artefaktů. Skript vytvoří náhodný název úložiště, pokud není zadaný. |
| ArtifactRepositoryDisplayName | Zobrazovaný název úložiště artefaktů Toto je název, který se zobrazí v Azure Portal ( https://portal.azure.com) při zobrazení všech úložišť artefaktů pro testovací prostředí). |
| RepositoryUri | Identifikátor URI úložiště Příklady: `https://github.com/<myteam>/<nameofrepo>.git` nebo `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"` .|
| RepositoryBranch | Větev, ve které se dají najít soubory artefaktů Výchozí hodnota je "Master". |
| FolderPath | Složka, ve které se mají najít artefakty Výchozí hodnota je '/Artifacts ' |
| PersonalAccessToken | Token zabezpečení pro přístup k úložišti GitHub nebo VSOGit Pokyny k získání tokenu pro osobní přístup najdete v části požadavky. |
| SourceType | Zda je artefakt VSOGit nebo GitHub. |

Samotné úložiště potřebuje pro identifikaci interní název, který se liší od zobrazovaného názvu, který se zobrazuje v Azure Portal. Interní název se nezobrazuje pomocí Azure Portal, ale zobrazí se při použití rozhraní Azure REST API nebo Azure PowerShell. Skript poskytuje název, pokud ho nezadal uživatel našeho skriptu.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Příkazy prostředí PowerShell použité ve skriptu

| Příkaz PowerShellu | Poznámky |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Tento příkaz slouží k získání podrobných informací o testovacím prostředí, jako je jeho umístění. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Neexistuje žádný konkrétní příkaz pro přidání úložišť artefaktů. Obecná rutina [New-AzResource](/powershell/module/az.resources/new-azresource) úlohu provede. Tato rutina potřebuje buď dvojici **ResourceID** , nebo **Resource** a **ResourceType** , aby znala typ prostředku, který se má vytvořit. Tento ukázkový skript používá dvojici název prostředku a typ prostředku. <br/><br/>Všimněte si, že vytváříte zdroj úložiště artefaktů ve stejném umístění a ve stejné skupině prostředků jako testovací prostředí.|

Skript přidá nový prostředek do aktuálního předplatného. Tyto informace zobrazíte pomocí [Get-AzContext](/powershell/module/az.accounts/get-azcontext) . K nastavení aktuálního tenanta a předplatného použijte [set-AzContext](/powershell/module/az.accounts/set-azcontext) .

Nejlepším způsobem, jak zjistit název prostředku a informace o typu prostředku, je použití webu [Test Drive rozhraní Azure REST API](https://azure.github.io/projects/apis/) . Podívejte se na poskytovatele [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) , kde najdete dostupná rozhraní REST API pro poskytovatele DevTest Labs. Skript uživatele následující ID prostředku.

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```

Typ prostředku je vše, co je uvedeno po ' Providers ' v identifikátoru URI, s výjimkou položek uvedených v složených závorkách. Název prostředku je zobrazený v složených závorkách. Pokud se pro název prostředku očekává více než jedna položka, oddělte každou položku znakem lomítka.

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Další kroky
- [Zadejte pro testovací prostředí povinné artefakty v Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Vytvoření vlastních artefaktů pro virtuální počítač s DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnostika selhání artefaktů v testovacím prostředí](devtest-lab-troubleshoot-artifact-failure.md)
