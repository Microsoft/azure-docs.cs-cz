---
title: Přidání úložiště artefaktů do testovacího prostředí ve službě Azure DevTest Labs | Dokumentace Microsoftu
description: Zjistěte, jak přidat jako úložiště artefaktů do testovacího prostředí ve službě Azure DevTest labs.
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
ms.date: 02/15/2019
ms.author: spelluru
ms.openlocfilehash: c1e74efa9cf99e8510ea17aedc840ce3b0731c3b
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64916680"
---
# <a name="add-an-artifact-repository-to-your-lab-in-devtest-labs"></a>Přidání úložiště artefaktů do testovacího prostředí v DevTest Labs
DevTest Labs umožňuje určit artefakt, který chcete přidat do virtuálního počítače během vytváření virtuálního počítače nebo po vytvoření virtuálního počítače. Tento artefakt může být nástroje nebo aplikace, kterou chcete nainstalovat do virtuálního počítače. Artefakty jsou definovány v souboru JSON načíst z úložiště GitHub nebo VSTS Git. 

[Veřejné úložiště artefaktů](https://github.com/Azure/azure-devtestlab/tree/master/Artifacts), spravován DevTest Labs, poskytuje celou řadu běžných nástrojů pro Windows a Linux. Odkaz na toto úložiště se automaticky přidá do vašeho testovacího prostředí. Můžete vytvořit vlastní úložiště artefaktů s konkrétní nástroje, které nejsou dostupné ve veřejné úložiště artefaktů. Další informace o vytváření vlastních artefaktů, naleznete v tématu [při vytváření vlastních artefaktů](devtest-lab-artifact-author.md).

Tento článek obsahuje informace o tom, jak přidat vlastní artefakt úložiště pomocí webu Azure portal, šablon Azure Resource Manageru a prostředí Azure PowerShell. Přidání úložiště artefaktů do testovacího prostředí podle psaní skriptů prostředí PowerShell nebo rozhraní příkazového řádku můžete automatizovat. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Požadavky
Přidání úložiště do vašeho testovacího prostředí, nejprve získejte informace o klíči z úložiště. Následující části popisují, jak získat požadované informace pro úložiště, které jsou hostované na **Githubu** nebo **Azure DevOps**.

### <a name="get-the-github-repository-clone-url-and-personal-access-token"></a>Získat adresu URL klonování úložiště Githubu a osobní přístupový token

1. Přejděte na domovskou stránku úložiště GitHub obsahující artefakt nebo definice šablon Resource Manageru.
2. Vyberte **Clone or download** (Naklonovat nebo stáhnout).
3. Zkopírujte adresu URL do schránky, vyberte **adresa url HTTPS clone** tlačítko. Uložte adresu URL pro pozdější použití.
4. V pravém horním rohu GitHub, vyberte profilový obrázek a pak vyberte **nastavení**.
5. V **osobní nastavení** nabídky na levé straně vyberte **nastavení pro vývojáře**.
6. Vyberte **osobní přístupové tokeny** v nabídce vlevo.
7. Vyberte **vygenerovat nový token**.
8. Na **nový token pat** stránce v části **Token popis**, zadejte popis. Přijměte výchozí položky v rámci **vyberte obory**a pak vyberte **vygenerovat Token vygenerujte**.
9. Uložte vygenerovaný token. Tento token použijete později.
10. Zavřete Githubu.   

### <a name="get-the-azure-repos-clone-url-and-personal-access-token"></a>Klonování úložiště Azure získat adresu URL a osobní přístupový token
1. Přejděte na domovskou stránku týmu kolekce (například https://contoso-web-team.visualstudio.com)a pak vyberte svůj projekt.
2. Na domovské stránce projektu, vyberte **kód**.
3. Chcete-li zobrazit adresu URL klonu na projekt **kód** stránce **klonování**.
4. Adresu URL si uložte. Adresa URL použijete později.
5. Chcete-li vytvořit osobní přístupový token, v rozevírací nabídce účtu uživatele, vyberte **Můj profil**.
6. Na stránce informace o profilu, vyberte **zabezpečení**.
7. Na **zabezpečení** kartu, vyberte možnost **přidat**.
8. Na **vytvořit osobní přístupový token** stránky:
   1. Zadejte **popis** pro daný token.
   2. V **vyprší v** seznamu vyberte **180 dnů**.
   3. V **účty** seznamu vyberte **všechny přístupné účty**.
   4. Vyberte **všechny obory** možnost.
   5. Vyberte **vytvořit Token**.
9. Nový token se zobrazí v **osobní přístupové tokeny** seznamu. Vyberte **kopírování Token**a potom uložte hodnota tokenu pro pozdější použití.
10. Pokračujte k připojení vašeho testovacího prostředí a v části úložiště.

## <a name="use-azure-portal"></a>Použití webu Azure Portal
Tato část obsahuje postup pro přidání úložiště artefaktů do testovacího prostředí na webu Azure Portal. 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
2. Vyberte **další služby**a pak vyberte **DevTest Labs** ze seznamu služeb.
3. V seznamu testovacích prostředí vyberte vaše testovací prostředí. 
4. Vyberte **konfigurace a zásad** v nabídce vlevo.
5. Vyberte **úložišť** pod **externím prostředkům** části v nabídce vlevo.
6. Vyberte **+ přidat** na panelu nástrojů.

    ![Tlačítko Přidat úložiště](./media/devtest-lab-add-repo/devtestlab-add-repo.png)
5. Na **úložišť** stránky, zadejte následující informace:
   1. **Název**. Zadejte název úložiště.
   2. **Adresa Url klonu Git**. Zadejte adresu URL klonu Git HTTPS, který jste si zkopírovali z Githubu nebo služby Azure DevOps.
   3. **Větev**. Chcete-li získat vaše definice, zadejte větev.
   4. **Token pat**. Zadejte osobní přístupový token, který jste předtím získali z Githubu nebo služby Azure DevOps.
   5. **Cesty ke složkám**. Zadejte alespoň jedna cesta ke složce relativní adresa URL klonu, který obsahuje vaše artefakt nebo definice šablon Resource Manageru. Při zadávání podadresář, nezapomeňte že zahrnout lomítko cesta ke složce.

        ![Oblast úložiště](./media/devtest-lab-add-repo/devtestlab-repo-blade.png)
6. Vyberte **Uložit**.

## <a name="use-azure-resource-manager-template"></a>Použití šablon Azure Resource Manageru
Šablony Azure Resource Management (Azure Resource Manageru) jsou soubory JSON, které popisují prostředky v Azure, který chcete vytvořit. Další informace o těchto šablon naleznete v tématu [šablon pro vytváření Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md).

Tato část obsahuje postup pro přidání úložiště artefaktů do testovacího prostředí s použitím šablony Azure Resource Manageru.  Pokud ještě neexistuje, vytvoří šablona testovacího prostředí. 

### <a name="template"></a>Šablona
Ukázková šablona použitá v tomto článku tyto informace shromažďuje prostřednictvím parametrů. Většina parametrů mají inteligentních výchozích hodnot, ale existuje několik hodnot, které musí být zadán. Musíte zadat název testovacího prostředí, identifikátor URI pro úložiště artefaktů a token zabezpečení pro úložiště. 

- Název testovacího prostředí.
- Zobrazovaný název pro úložiště artefaktů v DevTest Labs uživatelské rozhraní (UI). Výchozí hodnota je: `Team Repository`.
- Identifikátor URI úložiště (Příklad: `https://github.com/<myteam>/<nameofrepo>.git` nebo `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.
- Větve v úložišti, který obsahuje artefakty. Výchozí hodnota je: `master`.
- Název složky, která obsahuje artefakty. Výchozí hodnota je: `/Artifacts`.
- Typ úložiště. Povolené hodnoty jsou `VsoGit` nebo `GitHub`.
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
Existuje několik způsobů, jak nasazovat šablony do Azure a mít prostředek vytvoří, pokud neexistuje, nebo aktualizace, pokud už existoval. Podrobnosti najdete v následujících článcích:

- [Nasazení prostředků pomocí šablon Resource Manageru a Azure PowerShellu](../azure-resource-manager/resource-group-template-deploy.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a rozhraní příkazového řádku Azure](../azure-resource-manager/resource-group-template-deploy-cli.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a webu Azure Portal](../azure-resource-manager/resource-group-template-deploy-portal.md)
- [Nasazení prostředků pomocí šablon Resource Manageru a jeho rozhraní REST API](../azure-resource-manager/resource-group-template-deploy-rest.md)

Nyní pokračujme a zjistit, jak nasadit šablonu v prostředí PowerShell. Rutiny používané k nasazení šablony jsou závislé na kontextu, takže aktuálního tenanta a aktuálním předplatném jsou používány. Použití [Set-AzContext](/powershell/module/az.accounts/set-azcontext) před nasazením šablony, v případě potřeby, chcete-li změnit kontext.

Nejprve vytvořte skupinu prostředků pomocí [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup). Pokud skupina prostředků, kterou chcete použít, již existuje, tento krok přeskočte.

```powershell
New-AzResourceGroup -Name MyLabResourceGroup1 -Location westus
```

Potom vytvořte nasazení do skupiny prostředků pomocí [New-AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment). Tato rutina použije změny prostředků do Azure. Nasazení prostředků a několik provádět jakékoli dané skupině prostředků. Pokud nasazujete do stejné skupiny prostředků několikrát, zkontrolujte, zda že je jedinečný název každého nasazení.

```powershell
New-AzResourceGroupDeployment `
    -Name MyLabResourceGroup-Deployment1 `
    -ResourceGroupName MyLabResourceGroup1 `
    -TemplateFile azuredeploy.json `
    -TemplateParameterFile azuredeploy.parameters.json
```

Po úspěšně spustit nový AzResourceGroupDeployment, příkaz vypíše důležité informace, jako je stav zřizování (by měl být úspěšné) a výstupy pro šablonu.
 
## <a name="use-azure-powershell"></a>Použití Azure Powershell 
Tato část obsahuje ukázkový skript Powershellu, který lze použít k přidání úložiště artefaktů do testovacího prostředí. Pokud nemáte Azure Powershellu, přečtěte si téma [instalace a konfigurace Azure Powershellu](/powershell/azure/overview?view=azps-1.2.0) podrobné pokyny k jeho instalaci.

### <a name="full-script"></a>Celý skript
Tady je úplná skript, včetně některých podrobné zprávy a poznámky:

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

Write-Verbose @"Properties to be passed to New-AzResource:$($propertiesObject | Out-String)"@

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
Ukázkový skript Powershellu v tomto článku mají následující parametry:

| Parametr | Popis | 
| --------- | ----------- | 
| LabName | Název testovacího prostředí. |
| ArtifactRepositoryName | Název nového úložiště artefaktů. Pokud se nezadá, skript vytvoří náhodný název úložiště. |
| ArtifactRepositoryDisplayName | Zobrazovaný název úložiště artefaktů. Toto je název, který se zobrazuje na webu Azure Portal (https://portal.azure.com) při prohlížení všechna úložiště artefaktů pro testovací prostředí. |
| RepositoryUri | Identifikátor URI pro úložiště. Příklady: `https://github.com/<myteam>/<nameofrepo>.git` nebo `"https://MyProject1.visualstudio.com/DefaultCollection/_git/TeamArtifacts"`.| 
| RepositoryBranch | Větev, ve které artefaktů soubory lze nalézt. Výchozí hodnota je "master". | 
| FolderPath | Složka, pod kterým artefakty najdete. Výchozí hodnota je "/ artefaktů |
| PersonalAccessToken | Token zabezpečení pro přístup k úložišti Githubu nebo VSOGit. Naleznete v oddílu požadavky, pokyny k získání tokenu pat |
| sourceType | Určuje, zda artefakt je úložiště VSOGit nebo GitHub. |

Název samotného úložiště potřebujete interní název pro identifikaci, který se liší, zobrazovaný název, který zobrazuje na webu Azure Portal. Interní název pomocí webu Azure portal se nezobrazí, ale zobrazí se při použití rozhraní Azure REST API nebo Azure Powershellu. Skript obsahuje název, pokud není zadaná uživatelem skriptu.

```powershell
#Set artifact repository name, if not set by user
if ($ArtifactRepositoryName -eq $null){
    $ArtifactRepositoryName = "PrivateRepo" + (Get-Random -Maximum 999)
}
```

### <a name="powershell-commands-used-in-the-script"></a>Příkazy prostředí PowerShell použít ve skriptu

| Příkaz prostředí PowerShell | Poznámky |
| ------------------ | ----- |
| [Get-AzResource](/powershell/module/az.resources/get-azresource) | Tento příkaz slouží k získání podrobností o testovacím prostředí například jeho umístění. |
| [New-AzResource](/powershell/module/az.resources/new-azresource) | Neexistuje žádný konkrétní příkaz pro přidání úložiště artefaktů. Obecné [New-AzResource](/powershell/module/az.resources/new-azresource) rutina provede úlohu. Tato rutina se musí buď **ResourceId** nebo **ResourceName** a **ResourceType** pár znát typ prostředku, chcete-li vytvořit. Tento ukázkový skript používá dvojice typ prostředků a název prostředku. <br/><br/>Všimněte si, že vytvoříte zdroj artefaktu úložiště ve stejném umístění a ve stejné skupině prostředků jako testovacího prostředí.|

Skript přidá nový prostředek do aktuálního předplatného. Použití [Get-AzContext](/powershell/module/az.accounts/get-azcontext) tyto informace zobrazit. Použití [Set-AzContext](/powershell/module/az.accounts/set-azcontext) nastavení aktuálního tenanta a předplatné.

Nejlepší způsob, jak zjistit název prostředku a informace o typu prostředek se má používat [Test Drive REST API služby Azure](https://azure.github.io/projects/apis/) webu. Podívejte se [DevTest Labs – 2016-05-15](https://aka.ms/dtlrestapis) poskytovatele dostupná rozhraní API REST pro zprostředkovatele DevTest Labs. Uživatelé skriptu následující ID prostředku. 

```powershell
"/subscriptions/$SubscriptionId/resourceGroups/$($LabResource.ResourceGroupName)/providers/Microsoft.DevTestLab/labs/$LabName/artifactSources/$ArtifactRepositoryName"
```
 
Typ prostředku je, že všechno uvedené po "zprostředkovatele" v identifikátoru URI, s výjimkou položky uvedené ve složených závorkách. Název prostředku je všechno, co je vidět ve složených závorkách. Pokud pro název prostředku se očekává více než jednu položku, jednotlivé položky oddělte lomítko jako jsme udělali. 

```powershell
$resourcetype = 'Microsoft.DevTestLab/labs/artifactSources'
$resourceName = $LabName + '/' + $ArtifactRepositoryName
```


## <a name="next-steps"></a>Další postup
- [Zadejte povinné artefakty testovacího prostředí ve službě Azure DevTest Labs](devtest-lab-mandatory-artifacts.md)
- [Při vytváření vlastních artefaktů pro virtuální počítač DevTest Labs](devtest-lab-artifact-author.md)
- [Diagnostikování selhání artefaktů v testovacím prostředí](devtest-lab-troubleshoot-artifact-failure.md)

