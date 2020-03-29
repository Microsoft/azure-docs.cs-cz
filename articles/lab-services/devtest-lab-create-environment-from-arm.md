---
title: Vytváření prostředí s více virtuálními počítačemi a prostředků PaaS pomocí šablon
description: Zjistěte, jak vytvořit prostředí s více virtuálními počítači a prostředky PaaS v laboratořích Azure DevTest labs ze šablony Azure Resource Manageru
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.assetid: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 1385b20847cf90c212a13591389dfb6cda08432a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76169633"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru

Prostředí Azure DevTest Labs umožňují uživatelům snadno nasadit složité infrastruktury konzistentním způsobem v rámci testovacího prostředí. [Šablony Azure Resource Manageru](../azure-resource-manager/templates/template-syntax.md) můžete použít k vytvoření prostředí se sadami prostředků v devTest Labs. Tato prostředí mohou obsahovat všechny prostředky Azure, které mohou vytvářet šablony Správce prostředků.

Pomocí [portálu Azure](https://portal.azure.com)můžete do testovacího prostředí snadno [přidat jeden virtuální počítač (VM)](devtest-lab-add-vm.md) najednou . Scénáře, jako jsou vícevrstvé webové aplikace nebo sharepointová farma, však potřebují mechanismus k vytvoření více virtuálních počítačů v jednom kroku. Pomocí šablon Azure Resource Manager můžete definovat infrastrukturu a konfiguraci vašeho řešení Azure a opakovaně nasazovat více virtuálních počítačů v konzistentním stavu.

Šablony Azure Resource Manageru také poskytují následující výhody:

- Šablony Azure Resource Manageru se načítají přímo z úložiště správy zdrojového kódu GitHub nebo Azure Repos.
- Vaši uživatelé můžou vytvořit prostředí výběrem nakonfigurované šablony Azure Resource Manageru z webu Azure Portal, stejně jako u jiných typů [základen virtuálních počítačů](devtest-lab-comparing-vm-base-image-types.md).
- Můžete zřídit prostředky Azure PaaS, stejně jako virtuální počítače IaaS v prostředí ze šablony Azure Resource Manager.
- Můžete sledovat náklady na prostředí v testovacím prostředí, kromě jednotlivých virtuálních počítačů vytvořených jinými typy základen. PaaS prostředky jsou vytvořeny a zobrazí se ve sledování nákladů. Automatické vypnutí virtuálního počítače se však nevztahuje na prostředky PaaS.

Další informace o výhodách použití šablon Správce prostředků k nasazení, aktualizaci nebo odstranění mnoha prostředků testovacího prostředí v jedné operaci naleznete v [tématu Výhody použití šablon Správce prostředků](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Když použijete šablonu Správce prostředků jako základ k vytvoření virtuálních počítačích testovacího prostředí, existují určité rozdíly mezi vytvořením více virtuálních počítačů nebo jednoho virtuálního počítače. Další informace najdete [v tématu Použití šablony Správce prostředků virtuálního počítače .](devtest-lab-use-resource-manager-template.md)
>

## <a name="use-devtest-labs-public-environments"></a>Použití veřejných prostředí DevTest Labs
Azure DevTest Labs má [veřejné úložiště šablon Azure Resource Manageru,](https://github.com/Azure/azure-devtestlab/tree/master/Environments) které můžete použít k vytvoření prostředí, aniž byste se museli sami připojovat k externímu zdroji GitHubu. Toto veřejné úložiště je podobné veřejnému úložišti artefaktů, které je dostupné na portálu Azure pro každé testovací prostředí, které vytvoříte. Úložiště prostředí umožňuje rychle začít s předem napájenými šablonami prostředí, které mají málo vstupních parametrů. Tyto šablony poskytují bezproblémové začínáme prostředí pro prostředky PaaS v rámci testovacích prostředí.

Ve veřejném úložišti vytvořiltým DevTest Labs a další vytvořili a sdíleli často používané šablony, jako jsou Azure Web Apps, Cluster Service Fabric a vývojové prostředí SharePoint Farm. Tyto šablony můžete použít přímo nebo je přizpůsobit tak, aby vyhovovaly vašim potřebám. Další informace naleznete v [tématu Konfigurace a použití veřejných prostředí v devTest Labs](devtest-lab-configure-use-public-environments.md). Po vytvoření vlastních šablon je můžete uložit do tohoto úložiště a sdílet je s ostatními nebo si nastavit vlastní úložiště Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Vytvoření vlastních úložišť šablon

Jako jeden z osvědčených postupů s infrastrukturou jako kód a konfigurací jako kód byste měli spravovat šablony prostředí ve správě zdrojového kódu. Azure DevTest Labs se řídí tímto postupem a načte všechny šablony Azure Resource Managerpřímo z vašich úložišť GitHub nebo Azure Repos. V důsledku toho můžete použít šablony Správce prostředků v celém cyklu vydávání, od testovacího prostředí až po produkční prostředí.

Existuje několik pravidel, která je třeba dodržovat při uspořádání šablon Azure Resource Manager v úložišti:

- Je nutné pojmenovat soubor hlavní šablony *azuredeploy.json*.

- Pokud chcete použít hodnoty parametrů definované v souboru parametrů, musí být soubor parametrů pojmenován *azuredeploy.parameters.json*.

  Můžete použít parametry `_artifactsLocation` `_artifactsLocationSasToken` a vytvořit parametryLink URI hodnotu, která umožňuje DevTest Labs automaticky spravovat vnořené šablony. Další informace najdete [v tématu Nasazení vnořených šablon Azure Resource Manageru pro testovací prostředí](deploy-nested-template-environments.md).

- Metadata můžete definovat a určit zobrazovaný název a popis šablony v souboru s názvem *metadata.json*takto:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Klíčové soubory šablon Azure Resource Manageru](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Přidání úložišť šablon do testovacího prostředí

Po vytvoření a konfiguraci úložiště ho můžete přidat do testovacího prostředí pomocí portálu Azure:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**
1. Ze seznamu testovacích prostředí vyberte požadované testovací prostředí.
1. V podokně **Přehled** testovacího prostředí vyberte **Konfigurace a zásady**.

   ![Konfigurace a zásady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. V seznamu **Nastavení konfigurace a zásad** vyberte možnost **Repozitáře**. Úložiště **Repo veřejných artefaktů** se automaticky generuje pro všechna testovací prostředí a připojuje se k [veřejnému úložišti GitHub DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Pokud chcete přidat úložiště šablon Azure Resource Manageru, vyberte **Přidat**.

   ![Veřejné repo](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. V podokně **Úložiště** zadejte následující informace:

   - **Název**: Zadejte název úložiště, který chcete použít v testovacím prostředí.
   - **Adresa URL klonování Gitu**: Zadejte adresu URL klonování Git HTTPS z GitHubu nebo Azure Repos.
   - **Větev** (nepovinné): Zadejte název větve pro přístup k definicím šablon Azure Resource Manageru.
   - **Osobní přístupový token**: Zadejte osobní přístupový token, který se používá k bezpečnému přístupu k úložišti.
     - Pokud chcete získat token z Azure Repos, vyberte v části svůj profil > **tokeny osobního****přístupu** **pro nastavení** > uživatele .
     - To get your token from GitHub, under your profile, select **Settings** > **Developer Settings** > **Personal access tokens**.
   - **Cesty ke složkám**: Zadejte cestu ke složce, která je relativní vzhledem k identifikátoru URI klonování Gitu pro definice artefaktů nebo definice šablon Azure Resource Manageru.

1. Vyberte **Uložit**.

   ![Přidat nové úložiště](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Jakmile přidáte šablonu Azure Resource Manager do testovacího prostředí, uživatelé testovacího prostředí můžete vytvořit prostředí pomocí šablony.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurace přístupových práv pro uživatele testovacího prostředí

Uživatelé testovacího prostředí mají ve výchozím nastavení roli **čtečky,** takže nemohou změnit prostředky ve skupině prostředků prostředí. Nemohou například zastavit nebo spustit své prostředky.

Chcete-li uživatelům testovacího prostředí udělit roli **přispěvatele,** aby mohli upravovat prostředky ve svém prostředí, postupujte takto:

1. Na [webu Azure Portal](https://portal.azure.com)vyberte v podokně **Přehled** testovacího prostředí možnost Konfigurace **a zásady a**pak vyberte Nastavení **testovacího prostředí**.

1. V podokně **Nastavení testovacího prostředí** vyberte **Přispěvatel**a pak vyberte **Uložit,** chcete-li uživatelům testovacího prostředí udělit oprávnění k zápisu.

   ![Konfigurace přístupových práv uživatelů testovacího prostředí](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

Další část vás provede vytvářením prostředí ze šablony Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Vytváření prostředí ze šablon na webu Azure Portal

Jakmile přidáte šablonu Azure Resource Manager do testovacího prostředí, uživatelé testovacího prostředí můžete vytvořit prostředí na webu Azure Portal pomocí následujících kroků:

1. Přihlaste se k [portálu Azure](https://portal.azure.com).

1. Vyberte **Všechny služby**a ze seznamu vyberte **DevTest Labs.**

1. Ze seznamu testovacích prostředí vyberte požadované testovací prostředí.

1. Na stránce testovacího prostředí vyberte **Přidat**.

1. V **podokně Vybrat základní** se zobrazí základní image, které můžete použít, přičemž nejprve jsou uvedeny šablony Azure Resource Manageru. Vyberte požadovanou šablonu Správce prostředků Azure.

   ![Výběr základní image](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. V podokně **Přidat** zadejte hodnotu **názvu prostředí,** která se má zobrazit uživatelům prostředí.

   Šablona Azure Resource Manager definuje zbytek vstupních polí. Pokud soubor šablony *azuredeploy.parameter.json* definuje výchozí hodnoty, vstupní pole tyto hodnoty zobrazí.

   Pro parametry typu *zabezpečeného řetězce*můžete použít tajné klíče z trezoru klíčů Azure. Informace o ukládání tajných kódů v trezoru klíčů a jejich použití při vytváření prostředků testovacího prostředí najdete [v tématu Ukládání tajných kódů v úložišti klíčů Azure](devtest-lab-store-secrets-in-key-vault.md).  

   ![Podokno Přidat](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Následující hodnoty parametrů se ve vstupních polích nezobrazí, a to ani v případě, že je šablona určuje. Místo toho formulář zobrazuje prázdná vstupní pole, kde uživatelé testovacího prostředí musí při vytváření prostředí zadávat hodnoty.
   >
   > - GEN-UNIKÁTNÍ
   > - GEN-UNIQUE-[N]
   > - GEN-SSH-PUB-KLÍČ
   > - GEN-HESLO

1. Chcete-li vytvořit prostředí, vyberte **přidat.**

   Prostředí začne zřizování okamžitě, se stavem zobrazeným v seznamu **Moje virtuální počítače.** Testovací prostředí automaticky vytvoří novou skupinu prostředků pro zřízení všech prostředků definovaných v šabloně Azure Resource Manager.

1. Po vytvoření prostředí vyberte prostředí v seznamu **Moje virtuální počítače,** otevřete podokno skupiny prostředků a projděte si všechny prostředky, které prostředí zřízené prostředí zřizovalo.

   ![Zdroje pro životní prostředí](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Můžete také rozbalit prostředí a zobrazit pouze seznam virtuálních virtuálních disponiál, které prostředí zřízené.

   ![Seznam virtuálních počítačů](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Vyberte libovolné prostředí, chcete-li zobrazit dostupné akce, jako je použití artefaktů, připojení datových disků, změna doby automatického vypnutí a další.

   ![Opatření v oblasti životního prostředí](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizace vytváření prostředí pomocí PowerShellu

Je možné použít portál Azure k přidání jednoho prostředí do testovacího prostředí, ale když vývoj nebo testování scénář musí vytvořit více prostředí, automatizované nasazení je lepší prostředí.

Než budete pokračovat, ujistěte se, že máte šablonu Azure Resource Manager, která definuje prostředky k vytvoření. [Přidejte a nakonfigurujte šablonu v úložišti Git](#configure-your-own-template-repositories)a [přidejte úložiště do testovacího prostředí](#add-template-repositories-to-the-lab).

Následující ukázkový skript vytvoří prostředí v testovacím prostředí. Komentáře vám pomohou lépe pochopit skript.

1. Uložte následující ukázkový skript prostředí PowerShell na pevný disk jako *deployenv.ps1*.

   [!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

   ```powershell
   #Requires -Module Az.Resources

   [CmdletBinding()]

   param (
   # ID of the Azure Subscription for the lab
   [string] [Parameter(Mandatory=$true)] $SubscriptionId,

   # Name of the existing lab in which to create the environment
   [string] [Parameter(Mandatory=$true)] $LabName,

   # Name of the connected repository in the lab
   [string] [Parameter(Mandatory=$true)] $RepositoryName,

   # Name of the template (folder name in the Git repository)
   [string] [Parameter(Mandatory=$true)] $TemplateName,

   # Name of the environment to be created in the lab
   [string] [Parameter(Mandatory=$true)] $EnvironmentName,

   # The parameters to be passed to the template. Each parameter is prefixed with "-param_".
   # For example, if the template has a parameter named "TestVMName" with a value of "MyVMName",
   # the string in $Params will have the form: -param_TestVMName MyVMName.
   # This convention allows the script to dynamically handle different templates.
   [Parameter(ValueFromRemainingArguments=$true)]
       $Params
   )

   # Sign in to Azure.
   # Comment out the following statement to completely automate the environment creation.
   Connect-AzAccount

   # Select the subscription that has the lab.  
   Set-AzContext -SubscriptionId $SubscriptionId | Out-Null

   # Get information about the user, specifically the user ID, which is used later in the script.  
   $UserId = $((Get-AzADUser -UserPrincipalName (Get-AzContext).Account).Id.Guid)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName -ResourceGroupName $ResourceGroupName
   if ($lab -eq $null) { throw "Unable to find lab $LabName in subscription $SubscriptionId." }

   # Get information about the repository in the lab.
   $repository = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType 'Microsoft.DevTestLab/labs/artifactsources' `
       -ResourceName $LabName `
       -ApiVersion 2016-05-15 `
       | Where-Object { $RepositoryName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($repository -eq $null) { throw "Unable to find repository $RepositoryName in lab $LabName." }

   # Get information about the Resource Manager template base for the environment.
   $template = Get-AzResource -ResourceGroupName $lab.ResourceGroupName `
       -ResourceType "Microsoft.DevTestLab/labs/artifactSources/armTemplates" `
       -ResourceName "$LabName/$($repository.Name)" `
       -ApiVersion 2016-05-15 `
       | Where-Object { $TemplateName -in ($_.Name, $_.Properties.displayName) } `
       | Select-Object -First 1
   if ($template -eq $null) { throw "Unable to find template $TemplateName in lab $LabName." }

   # Build the template parameters with parameter name and values.  
   $parameters = Get-Member -InputObject $template.Properties.contents.parameters -MemberType NoteProperty | Select-Object -ExpandProperty Name
   $templateParameters = @()

   # Extract the custom parameters from $Params and format as name/value pairs.
   $Params | ForEach-Object {
       if ($_ -match '^-param_(.*)' -and $Matches[1] -in $parameters) {
           $name = $Matches[1]                
       } elseif ( $name ) {
           $templateParameters += @{ "name" = "$name"; "value" = "$_" }
           $name = $null #reset name variable
       }
   }

   # Once name/value pairs are isolated, create an object to hold the necessary template properties.
   $templateProperties = @{ "deploymentProperties" = @{ "armTemplateId" = "$($template.ResourceId)"; "parameters" = $templateParameters }; }

   # Now, create or deploy the environment in the lab by using the New-AzResource command.
   New-AzResource -Location $Lab.Location `
       -ResourceGroupName $lab.ResourceGroupName `
       -Properties $templateProperties `
       -ResourceType 'Microsoft.DevTestLab/labs/users/environments' `
       -ResourceName "$LabName/$UserId/$EnvironmentName" `
       -ApiVersion '2016-05-15' -Force

   Write-Output "Environment $EnvironmentName completed."
   ```

1. Spusťte skript následujícím způsobem pomocí konkrétních hodnot pro SubscriptionId, LabName, ResourceGroupName, RepositoryName, TemplateName (složka v úložišti Git) a EnvironmentName.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Azure CLI můžete také použít k nasazení prostředků pomocí šablon Správce prostředků. Další informace najdete [v tématu Nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Pomocí Azure PowerShellu může virtuální počítače ze šablony Správce prostředků vytvářet jenom uživatel s oprávněními vlastníka testovacího prostředí. Pokud chcete automatizovat vytváření virtuálních počítače pomocí šablony Správce prostředků a máte jenom uživatelská oprávnění, můžete použít příkaz cli [az lab vm vytvořit](/cli/azure/lab/vm#az-lab-vm-create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Omezení šablon Správce prostředků v devtest labs

Při používání šablon Správce prostředků v laboratořích DevTest labs zvažte tato omezení:

- Šablony Správce prostředků nemohou odkazovat na většinu existujících prostředků. Mohou vytvářet pouze nové prostředky. Pokud máte šablony Správce prostředků, které používáte mimo DevTest Labs, které odkazují na existující prostředky, nemůžete je použít v DevTest Labs. Jedinou výjimkou je, že můžete odkazovat na existující virtuální síť.

- Nemůžete vytvářet vzorce nebo vlastní image z testovacích virtuálních počítače, které byly vytvořeny ze šablony Správce prostředků.

- Většina zásad se při nasazování šablon Správce prostředků nevyhodnocuje.

  Můžete mít například zásady testovacího prostředí, které může uživatel vytvořit pouze pět virtuálních uživatelů. Uživatel však můžete nasadit šablonu Správce prostředků, která vytvoří desítky virtuálních počítače. Mezi zásady, které nejsou vyhodnoceny, patří:

  - Počet virtuálních uživatelů na uživatele

  - Počet prémiových virtuálních uživatelů na uživatele testovacího prostředí

  - Počet disků premium na uživatele testovacího prostředí

## <a name="next-steps"></a>Další kroky
- Po vytvoření virtuálního počítače se můžete k virtuálnímu počítači připojit výběrem **možnosti Připojit v** podokně správy virtuálního počítače.
- Zobrazení a správa prostředků v prostředí výběrem prostředí v seznamu **Moje virtuální počítače** v testovacím prostředí.
- Prozkoumejte [šablony Azure Resource Manageru v galerii šablon Azure Quickstart](https://github.com/Azure/azure-quickstart-templates).
