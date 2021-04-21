---
title: Vytváření prostředí s více virtuálními počítači a PaaS prostředky pomocí šablon
description: Naučte se vytvářet prostředí s více virtuálními počítači a PaaS prostředky v Azure DevTest Labs ze šablony Azure Resource Manager
ms.topic: article
ms.date: 08/12/2020
ms.openlocfilehash: f285acffe642a85fa27792ee51ea67a57f6d35a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790108"
---
# <a name="create-multi-vm-environments-and-paas-resources-with-azure-resource-manager-templates"></a>Vytvoření prostředí více virtuálních počítačů a prostředků PaaS pomocí šablony Azure Resource Manageru

Azure DevTest Labs prostředí umožňují uživatelům snadno nasadit složitou infrastrukturu konzistentním způsobem v rámci omezeného prostředí. [Šablony Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md) můžete použít k vytváření prostředí se sadami prostředků v DevTest Labs. Tato prostředí můžou obsahovat jakékoli prostředky Azure, které Správce prostředků šablony můžou vytvořit.

Můžete snadno [Přidat jeden virtuální počítač (VM)](devtest-lab-add-vm.md) do testovacího prostředí pomocí [Azure Portal](https://portal.azure.com). Scénáře, jako jsou vícevrstvé webové aplikace nebo farmy služby SharePoint, ale vyžadují mechanismus pro vytvoření více virtuálních počítačů v jednom kroku. Pomocí šablon Azure Resource Manager můžete definovat infrastrukturu a konfiguraci řešení Azure a opakovaně nasazovat víc virtuálních počítačů v konzistentním stavu.

Šablony Azure Resource Manager také poskytují následující výhody:

- Šablony Azure Resource Manager se načítají přímo z úložiště GitHubu nebo Azure Repos úložiště správy zdrojového kódu.
- Uživatelé můžou vytvořit prostředí vyplněním nakonfigurované šablony Azure Resource Manager z Azure Portal stejně jako u jiných typů [základů virtuálních počítačů](devtest-lab-comparing-vm-base-image-types.md).
- Můžete zřídit prostředky Azure PaaS a také virtuální počítače s IaaS v prostředí ze šablony Azure Resource Manager.
- Kromě jednotlivých virtuálních počítačů vytvořených jinými typy základů můžete sledovat náklady na prostředí v testovacím prostředí. Prostředky PaaS se vytvoří a zobrazí se ve sledování nákladů. Automatické vypnutí virtuálního počítače ale neplatí pro prostředky PaaS.

Další informace o výhodách používání šablon Správce prostředků k nasazení, aktualizaci nebo odstranění mnoha prostředků testovacího prostředí v rámci jedné operace najdete v tématu [výhody používání šablon Správce prostředků](../azure-resource-manager/management/overview.md#the-benefits-of-using-resource-manager).

> [!NOTE]
> Když použijete šablonu Správce prostředků jako základ pro vytvoření testovacích virtuálních počítačů, existují některé rozdíly mezi vytvořením několika virtuálních počítačů nebo jednoho virtuálního počítače. Další informace najdete v tématu [použití šablony Azure Resource Manager virtuálního počítače](devtest-lab-use-resource-manager-template.md).
>

## <a name="use-devtest-labs-public-environments"></a>Použití veřejných prostředí DevTest Labs
Azure DevTest Labs má [veřejné úložiště Azure Resource Managerch šablon](https://github.com/Azure/azure-devtestlab/tree/master/Environments) , které můžete použít k vytváření prostředí, aniž byste se museli připojovat k externímu zdroji na GitHubu. Toto veřejné úložiště je podobné veřejnému úložišti artefaktů, které jsou k dispozici v Azure Portal pro každé testovací prostředí, které vytvoříte. Úložiště prostředí vám umožní rychle začít pracovat s předem vytvořenými šablonami prostředí, které mají několik vstupních parametrů. Tyto šablony vám poskytnou hladké možnosti Začínáme s PaaS prostředky v rámci Labs.

Ve veřejném úložišti tým DevTest Labs a jiní vytvořili a sdíleli často používané šablony jako Azure Web Apps, Service Fabric cluster a vývojové prostředí farmy služby SharePoint. Tyto šablony můžete použít přímo nebo je přizpůsobit tak, aby vyhovovaly vašim potřebám. Další informace najdete v tématu [Konfigurace a používání veřejných prostředí v DevTest Labs](devtest-lab-configure-use-public-environments.md). Po vytvoření vlastních šablon je můžete uložit do tohoto úložiště, abyste je mohli sdílet s ostatními, nebo si nastavit vlastní úložiště Git.

<a name="configure-your-own-template-repositories"></a>
## <a name="create-your-own-template-repositories"></a>Vytvoření vlastních úložišť šablon

Jako jeden z osvědčených postupů s infrastrukturou jako s kódem a konfigurací jako s kódem byste měli spravovat šablony prostředí ve správě zdrojového kódu. Azure DevTest Labs postupuje podle tohoto postupu a načte všechny Azure Resource Manager šablony přímo z vašeho GitHubu nebo z úložišť Azure Repos. V důsledku toho můžete použít šablony Správce prostředků napříč celým cyklem vydávání verzí z testovacího prostředí do produkčního prostředí.

Pro uspořádání šablon Azure Resource Manager v úložišti je potřeba provést několik pravidel:

- Je nutné pojmenovat soubor hlavní šablony *azuredeploy.jsv*.

- Pokud chcete použít hodnoty parametrů definované v souboru parametrů, musí mít soubor s parametrem název *azuredeploy.parameters.js*.

  Pomocí parametrů `_artifactsLocation` a můžete `_artifactsLocationSasToken` vytvořit hodnotu identifikátoru URI parametersLink a povolit tak DevTest Labs automatické správě vnořených šablon. Další informace najdete v tématu [nasazení vnořených Azure Resource Manager šablon pro testovací prostředí](deploy-nested-template-environments.md).

- Metadata můžete definovat k určení zobrazovaného názvu a popisu šablony v souboru s názvem *metadata.jsv*, a to následujícím způsobem:

  ```json
  {
    "itemDisplayName": "<your template name>",
    "description": "<description of the template>"
  }
  ```

![Soubory šablon klíčů Azure Resource Manager](./media/devtest-lab-create-environment-from-arm/master-template.png)

## <a name="add-template-repositories-to-the-lab"></a>Přidání úložišť šablon do testovacího prostředí

Po vytvoření a konfiguraci úložiště ho můžete přidat do testovacího prostředí pomocí Azure Portal:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).
1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .
1. V seznamu cvičení vyberte testovací prostředí, které chcete.
1. V podokně s **přehledem** testovacího prostředí vyberte **Konfigurace a zásady**.

   ![Konfigurace a zásady](./media/devtest-lab-create-environment-from-arm/configuration-and-policies-menu.png)

1. V seznamu nastavení **Konfigurace a zásady** vyberte **úložiště**. Úložiště **veřejného úložiště artefaktů** se automaticky vygeneruje pro všechny laboratoře a připojuje se k [veřejnému úložišti GitHub DevTest Labs](https://github.com/Azure/azure-devtestlab).

1. Pokud chcete přidat úložiště šablon Azure Resource Manager, vyberte **Přidat**.

   ![Veřejné úložiště](./media/devtest-lab-create-environment-from-arm/public-repo.png)

1. V podokně **úložiště** zadejte následující informace:

   - **Název**: zadejte název úložiště pro použití v testovacím prostředí.
   - **Adresa URL klonu Git**: zadejte adresu URL klonu Git HTTPS z GitHubu nebo Azure Repos.
   - **Větev** (volitelné): zadejte název větve pro přístup k definicím šablon Azure Resource Manager.
   - **Osobní přístupový token**: Zadejte osobní přístupový token, který se používá k zabezpečenému přístupu k úložišti.
     - Pokud chcete získat token z Azure Repos, v části váš profil vyberte **uživatelské nastavení**  >  **zabezpečení**  >  **osobní přístupové tokeny**.
     - Pokud chcete získat token z GitHubu, v části váš profil vyberte **Nastavení**  >  **vývojář nastavení**  >  **osobní přístupové tokeny**.
   - **Cesty ke složkám**: zadejte cestu ke složce, která je relativní vzhledem k identifikátoru URI klonu Git, a to buď pro definice artefaktů, nebo pro definice šablon Azure Resource Manager.

1. Vyberte **Uložit**.

   ![Přidat nové úložiště](./media/devtest-lab-create-environment-from-arm/repo-values.png)

Po přidání šablony Azure Resource Manager do testovacího prostředí můžou uživatelé testovacího prostředí vytvářet prostředí pomocí šablony.

## <a name="configure-access-rights-for-lab-users"></a>Konfigurace přístupových práv pro uživatele testovacího prostředí

Uživatelé testovacího prostředí mají ve výchozím nastavení roli **Čtenář** , takže nemohou měnit prostředky ve skupině prostředků prostředí. Nemůžou například zastavit nebo spustit jejich prostředky.

Pokud chcete uživatelům testovacího prostředí poskytnout roli **přispěvatele** , aby mohli upravovat prostředky v jejich prostředích, postupujte podle následujících kroků:

1. V podokně s **přehledem** [Azure Portal](https://portal.azure.com)v podokně Přehled testovacího prostředí vyberte **Konfigurace a zásady** a pak vyberte **Nastavení testovacího prostředí**.

1. V podokně **Nastavení testovacího prostředí** vyberte **Přispěvatel** a pak vyberte **Uložit** a udělte uživatelům testovací oprávnění k zápisu.

   ![Konfigurace přístupových práv uživatele testovacího prostředí](./media/devtest-lab-create-environment-from-arm/config-access-rights.png)

V další části se seznámíte s vytvářením prostředí ze šablony Azure Resource Manager.

## <a name="create-environments-from-templates-in-the-azure-portal"></a>Vytváření prostředí ze šablon v Azure Portal

Po přidání šablony Azure Resource Manager do testovacího prostředí mohou uživatelé testovacího prostředí vytvořit prostředí v Azure Portal pomocí následujících kroků:

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **všechny služby** a v seznamu vyberte **DevTest Labs** .

1. V seznamu cvičení vyberte testovací prostředí, které chcete.

1. Na stránce testovacího prostředí vyberte **Přidat**.

1. V podokně **Vyberte základní** image, které můžete použít, spolu s Azure Resource Manager šablonami uvedenými jako první. Vyberte šablonu Azure Resource Manager kterou chcete.

   ![Výběr základní image](./media/devtest-lab-create-environment-from-arm/choose-a-base.png)

1. V podokně **Přidat** zadejte hodnotu **Název prostředí** , která se zobrazí uživatelům prostředí.

   Šablona Azure Resource Manager definuje zbývající část vstupních polí. Pokud šablona *azuredeploy.parameter.jsv* souboru definuje výchozí hodnoty, zobrazí vstupní pole tyto hodnoty.

   Pro parametry typu *zabezpečený řetězec* můžete použít tajné kódy z Azure Key Vault. Další informace o ukládání tajných klíčů v trezoru klíčů a jejich použití při vytváření prostředků testovacího prostředí najdete [v tématu uložení tajných kódů v Azure Key Vault](devtest-lab-store-secrets-in-key-vault.md).  

   ![Přidat podokno](./media/devtest-lab-create-environment-from-arm/add.png)

   > [!NOTE]
   > Následující hodnoty parametrů se ve vstupních polích nezobrazují, ani když je šablona specifikuje. Místo toho se v tomto formuláři zobrazí prázdná vstupní pole, kde uživatelé testovacího prostředí musí při vytváření prostředí zadat hodnoty.
   >
   > - OBECNÉ – JEDINEČNÉ
   > - OBECNÉ – JEDINEČNÉ – [N]
   > - GEN-SSH-PUB-KEY
   > - OBECNÉ HESLO

1. Vyberte **Přidat** a vytvořte prostředí.

   Prostředí se okamžitě spustí zřizování se stavem zobrazeným v seznamu **moje virtuální počítače** . Testovací prostředí automaticky vytvoří novou skupinu prostředků ke zřízení všech prostředků definovaných v šabloně Azure Resource Manager.

1. Po vytvoření prostředí vyberte prostředí v seznamu **moje virtuální počítače** a otevřete podokno skupina prostředků a procházejte všechny prostředky zřízené prostředím.

   ![Prostředky prostředí](./media/devtest-lab-create-environment-from-arm/all-environment-resources.png)

   Prostředí můžete také rozšířit tak, aby se zobrazil pouze seznam virtuálních počítačů, které byly zřízeny prostředím.

   ![Seznam Moje virtuální počítače](./media/devtest-lab-create-environment-from-arm/my-vm-list.png)

1. Vyberte libovolné prostředí a zobrazte dostupné akce, jako je například použití artefaktů, připojování datových disků, změna času automatického vypnutí a další.

   ![Akce prostředí](./media/devtest-lab-create-environment-from-arm/environment-actions.png)

<a name="automate-deployment-of-environments"></a>
## <a name="automate-environment-creation-with-powershell"></a>Automatizace vytváření prostředí pomocí PowerShellu

Je vhodné použít Azure Portal k přidání jednoho prostředí do testovacího prostředí, ale pokud scénář vývoje nebo testování musí vytvořit více prostředí, je lepším prostředím automatické nasazení.

Než budete pokračovat, ujistěte se, že máte šablonu Azure Resource Manager definující prostředky, které se mají vytvořit. [Přidejte a nakonfigurujte šablonu v úložišti Git](#configure-your-own-template-repositories)a [přidejte úložiště do testovacího prostředí](#add-template-repositories-to-the-lab).

Následující vzorový skript vytvoří prostředí v testovacím prostředí. Komentáře vám pomůžou lépe pochopit skript.

1. Uložte následující vzorový skript PowerShellu na pevný disk jako *deployenv.ps1*.

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
   $UserId = $((Get-AzADUser -UserPrincipalName ((Get-AzContext).Account).Id).Id)

   # Get information about the lab, such as lab location.
   $lab = Get-AzResource -ResourceType "Microsoft.DevTestLab/labs" -Name $LabName
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

1. Spusťte skript následujícím způsobem a použijte konkrétní hodnoty pro ID předplatného, LabName, ResourceGroupName, úložiště, Template (složka v úložišti Git) a prostředí.

   ```powershell
   ./deployenv.ps1 -SubscriptionId "000000000-0000-0000-0000-0000000000000" -LabName "mydevtestlab" -ResourceGroupName "mydevtestlabRG000000" -RepositoryName "myRepository" -TemplateName "My Environment template name" -EnvironmentName "myGroupEnv"
   ```

Pomocí Azure CLI můžete také nasazovat prostředky pomocí Správce prostředků šablon. Další informace najdete v tématu [nasazení prostředků pomocí šablon Správce prostředků a Azure CLI](../azure-resource-manager/templates/deploy-cli.md).

> [!NOTE]
> Jenom uživatel s oprávněním vlastníka testovacího prostředí může vytvořit virtuální počítače ze šablony Správce prostředků pomocí Azure PowerShell. Pokud chcete automatizovat vytváření virtuálních počítačů pomocí šablony Správce prostředků a máte jenom uživatelská oprávnění, můžete použít příkaz CLI [AZ Lab VM Create](/cli/azure/lab/vm#az_lab_vm_create).

## <a name="resource-manager-template-limitations-in-devtest-labs"></a>Omezení šablon Správce prostředků v DevTest Labs

Při používání šablon Správce prostředků v DevTest Labs Vezměte v úvahu tato omezení:

- Z testovacích virtuálních počítačů, které se vytvořily ze šablony Správce prostředků, nemůžete vytvářet vzorce ani vlastní image.

- Většina zásad se při nasazení Správce prostředkůch šablon nevyhodnocují.

Například můžete mít zásadu testovacího prostředí, kterou může uživatel vytvořit pouze pět virtuálních počítačů. Uživatel ale může nasadit šablonu Správce prostředků, která vytvoří spoustu virtuálních počítačů. Mezi nehodnocené zásady patří:

  - Počet virtuálních počítačů na uživatele

  - Počet virtuálních počítačů úrovně Premium na uživatele testovacího prostředí

  - Počet prémiových disků na uživatele testovacího prostředí

## <a name="next-steps"></a>Další kroky
- Po vytvoření virtuálního počítače se můžete připojit k virtuálnímu počítači tak, že v podokně Správa virtuálního počítače vyberete **připojit** .
- Zobrazení a Správa prostředků v prostředí výběrem prostředí v seznamu **moje virtuální počítače** v testovacím prostředí.
- Prozkoumejte [šablony Azure Resource Manager v galerii šablon Azure pro rychlý Start](https://github.com/Azure/azure-quickstart-templates).
