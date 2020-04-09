---
title: Použití skriptů nasazení v šablonách | Dokumenty společnosti Microsoft
description: V šablonách Azure Resource Manageru používejte skripty nasazení.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: jgao
ms.openlocfilehash: f84707adfa406011989c8f9bfdf1e8d9270698a6
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984789"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Použití skriptů nasazení v šablonách (Náhled)

Přečtěte si, jak používat skripty nasazení v šablonách prostředků Azure. Při volání `Microsoft.Resources/deploymentScripts`nového typu prostředku mohou uživatelé spouštět skripty nasazení v nasazení šablon a kontrolovat výsledky spuštění. Tyto skripty lze použít k provádění vlastních kroků, jako jsou:

- přidání uživatelů do adresáře
- vytvoření registrace aplikace
- provádět operace roviny dat, například kopírovat objekty BLOB nebo databázi seed
- vyhledat a ověřit licenční klíč
- vytvoření certifikátu podepsaného svým držitelem
- vytvoření objektu ve službě Azure AD
- Vyhledat bloky IP adres z vlastního systému

Výhody skriptu nasazení:

- Snadné kódování, použití a ladění. Skripty nasazení můžete vyvíjet ve svých oblíbených vývojových prostředích. Skripty mohou být vloženy do šablon nebo do externích souborů skriptů.
- Můžete zadat jazyk skriptu a platformu. V současné době jsou podporované skripty pro nasazení Azure PowerShellu a Azure CLI v prostředí Linuxu.
- Povolit určení identit, které se používají ke spuštění skriptů. V současné době je podporována jenom [spravovaná identita přiřazená uživateli Azure.](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)
- Povolit předávání argumentů příkazového řádku do skriptu.
- Můžete určit výstupy skriptu a předat je zpět do nasazení.

Prostředek skriptu nasazení je k dispozici jenom v oblastech, kde je k dispozici instance kontejneru Azure.  Viz [Dostupnost prostředků pro instance kontejnerů Azure v oblastech Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> Dva prostředky skriptu nasazení, účet úložiště a instance kontejneru, jsou vytvořeny ve stejné skupině prostředků pro spuštění skriptu a řešení potíží. Tyto prostředky jsou obvykle odstraněny službou skriptu při spuštění skriptu nasazení dostane do terminálového stavu. Prostředky se vám budou účtovat, dokud nebudou prostředky odstraněny. Další informace naleznete v [tématu Clean-up nasazení skriptu prostředky](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Požadavky

- **Uživatelem přiřazená spravovaná identita s rolí přispěvatele cílové skupině prostředků**. Tato identita se používá ke spuštění skriptů nasazení. Chcete-li provádět operace mimo skupinu prostředků, je třeba udělit další oprávnění. Pokud například chcete vytvořit novou skupinu prostředků, přiřaďte identitu na úroveň předplatného.

  > [!NOTE]
  > Modul skriptů nasazení vytvoří účet úložiště a instanci kontejneru na pozadí.  Uživatelem přiřazená spravovaná identita s rolí přispěvatele na úrovni předplatného je vyžadována, pokud předplatné nezaregistrovalo poskytovatele prostředků azure storage account (Microsoft.Storage) a Azure container instance (Microsoft.ContainerInstance).

  Pokud chcete vytvořit identitu, [přečtěte si tématu Vytvoření spravované identity přiřazené uživateli pomocí portálu Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)nebo [pomocí rozhraní příkazového příkazu Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)nebo pomocí Azure [PowerShellu](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). Při nasazování šablony potřebujete ID identity. Formát identity je:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Pomocí následujícího příkazového příkazu k příkazovém příkazu nebo skriptu prostředí PowerShell získáte ID zadáním názvu skupiny prostředků a názvu identity.

  # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g jgaoidentity1008rg -n jgaouami --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** nebo **Azure CLI**. Seznam podporovaných verzí Azure PowerShellu najdete [tady](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list); Seznam podporovaných verzí Azure CLI najdete [v tématu zde](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Skript nasazení používá dostupné imitace rozhraní PŘÍKAZOVÉHO PŘÍKAZU z registru Microsoft Container Registry(MCR) . Certifikace bitové kopie cli pro skript nasazení trvá přibližně jeden měsíc. Nepoužívejte verze cli, které byly vydány do 30 dnů. Pokud chcete najít data vydání pro bitové kopie, najdete [v tématu Poznámky k vydání Azure CLI](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Pokud je použita nepodporovaná verze, zobrazí se v chybové zprávě podporované verze.

    Tyto verze nepotřebujete pro nasazení šablon. Ale tyto verze jsou potřebné pro testování skriptů nasazení místně. Viz [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Můžete použít předkonfigurovanou bitovou kopii Dockeru.  Viz [Konfigurace vývojového prostředí](#configure-development-environment).

## <a name="sample-templates"></a>Ukázkové šablony

Následující json je příkladem.  Nejnovější schéma šablony naleznete [zde](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2019-10-01-preview",
  "name": "runPowerShellInline",
  "location": "[resourceGroup().location]",
  "kind": "AzurePowerShell", // or "AzureCLI"
  "identity": {
    "type": "userAssigned",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myID": {}
    }
  },
  "properties": {
    "forceUpdateTag": 1,
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80"
    "arguments": "[concat('-name ', parameters('name'))]",
    "environmentVariables": [
      {
        "name": "someSecret",
        "secureValue": "if this is really a secret, don't put it here... in plain text..."
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}' -f $name
      Write-Output $output
      $DeploymentScriptOutputs = @{}
      $DeploymentScriptOutputs['text'] = $output
    ", // or "primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
    "supportingScriptUris":[],
    "timeout": "PT30M",
    "cleanupPreference": "OnSuccess",
    "retentionInterval": "P1D"
  }
}
```

> [!NOTE]
> Příklad je určen pro demonstrační účely.  **scriptContent** a **primaryScriptUris** nemohou v šabloně existovat společně.

Podrobnosti o hodnotě nemovitosti:

- **Identita**: Služba skriptu nasazení používá k spuštění skriptů spravovanou identitu přiřazenou uživatelem. V současné době je podporována pouze spravovaná identita přiřazená uživatelem.
- **druh**: Zadejte typ skriptu. V současné době azure powershella a Azure CLI skripty jsou podporu. Hodnoty jsou **AzurePowerShell** a **AzureCLI**.
- **forceUpdateTag**: Změna této hodnoty mezi nasazeními šablony vynutí opětovné spuštění skriptu nasazení. Použijte funkci newGuid() nebo utcNow(), která musí být nastavena jako výchozí Hodnota parametru. Další informace naleznete v [tématu Spuštění skriptu více než jednou](#run-script-more-than-once).
- **azPowerShellVersion**/**azCliVersion**: Zadejte verzi modulu, která má být použita. Seznam podporovaných verzí prostředí PowerShell a CLI naleznete [v tématu Požadavky](#prerequisites).
- **argumenty**: Zadejte hodnoty parametrů. Hodnoty jsou odděleny mezerami.
- **environmentVariables**: Zadejte proměnné prostředí, které mají být předány skriptu. Další informace naleznete v [tématu Vývoj skriptů nasazení](#develop-deployment-scripts).
- **scriptContent**: Zadejte obsah skriptu. Chcete-li spustit externí `primaryScriptUri` skript, použijte místo toho. Příklady najdete v tématech [Použití vřádkového skriptu](#use-inline-scripts) a [Použití externího skriptu](#use-external-scripts).
- **primaryScriptUri**: Zadejte veřejně přístupnou adresu URL pro primární skript nasazení s podporovanými příponami souborů.
- **supportingScriptUris**: Zadejte pole veřejně přístupných adres URL pro `ScriptContent` podpůrné `PrimaryScriptUri`soubory, které jsou volány v jednom nebo .
- **Časový limit**: Zadejte maximální povolenou dobu spuštění skriptu zadanou ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**.
- **cleanupPreference**. Určete předvolbu čištění prostředků nasazení při spuštění skriptu dostane do terminálového stavu. Výchozí nastavení je **Vždy**, což znamená odstranění prostředků navzdory stavu terminálu (Proběhlo úspěšně, Nezdařilo se, Zrušeno). Další informace naleznete v [tématu Vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources).
- **retentionInterval**: Zadejte interval, pro který služba zachová prostředky skriptu nasazení poté, co spuštění skriptu nasazení dosáhne stavu terminálu. Prostředky skriptu nasazení budou odstraněny po uplynutí této doby trvání. Doba trvání je založena na [vzoru ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**, což znamená sedm dní. Tato vlastnost se používá při cleanupPreference je nastavena *na OnExpiration*. Vlastnost *OnExpiration* není aktuálně povolena. Další informace naleznete v [tématu Vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Další vzorky

- [vytvoření a přiřazení certifikátu k trezoru klíčů](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [vytvořte a přiřaďte spravovanou identitu přiřazenou uživateli ke skupině prostředků a spusťte skript nasazení](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Doporučujeme vytvořit identitu přiřazenou uživatelem a udělit oprávnění předem. Pokud vytvoříte identitu a udělíte oprávnění ve stejné šabloně, ve které spouštěte skripty nasazení, můžete získat chyby související s přihlášením a oprávněními. Trvá nějakou dobu, než se oprávnění stanou účinnými.

## <a name="use-inline-scripts"></a>Použití vsazených skriptů

Následující šablona má jeden `Microsoft.Resources/deploymentScripts` prostředek definovaný s typem. Zvýrazněná část je vložkový skript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Vzhledem k tomu, že skripty vsazení jsou uzavřeny v uvozovkách, řetězce uvnitř skriptů nasazení musí být místo toho uzavřeny v jednoduchých uvozovkách. Řídicí znak pro prostředí PowerShell je **&#92;**. Můžete také zvážit použití nahrazení řetězce, jak je znázorněno v předchozí ukázce JSON. Viz výchozí hodnota parametru name.

Skript přebírá jeden parametr a výstup hodnoty parametru. **DeploymentScriptOutputs** se používá pro ukládání výstupů.  V části výstupy řádek **hodnoty** ukazuje, jak získat přístup k uloženým hodnotám. `Write-Output`se používá pro účely ladění. Informace o přístupu k výstupnímu souboru naleznete v tématu [Ladění skriptů nasazení](#debug-deployment-scripts).  Popisy vlastností naleznete v [tématu Ukázkové šablony](#sample-templates).

Chcete-li skript spustit, vyberte **Možnost Vyzkoušet** otevřete prostředí Cloud A vložte následující kód do podokna prostředí.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Výstup bude vypadat následovně:

![Skript nasazení šablony Resource Manager hello world výstup](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Použití externích skriptů

Kromě vložkových skriptů můžete také použít externí soubory skriptů. Podporovány jsou pouze primární skripty prostředí PowerShell s příponou **ps1.** Pro skripty vykázaných kódových řízení mohou mít primární skripty libovolné rozšíření (nebo bez rozšíření), pokud jsou skripty platné skripty bash. Chcete-li použít externí `scriptContent` `primaryScriptUri`soubory skriptů, nahraďte je . Příklad:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Chcete-li zobrazit příklad, vyberte [zde](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Externí soubory skriptů musí být přístupné.  Pokud chcete zabezpečit soubory skriptů uložené v účtech úložiště Azure, [přečtěte si informace o kurzu: Zabezpečené artefakty v nasazeních šablon Azure Resource Manageru](./template-tutorial-secure-artifacts.md).

Jste zodpovědní za zajištění integrity skriptů, na které odkazuje skript nasazení, buď **PrimaryScriptUri** nebo **SupportingScriptUris**.  Odkazujte pouze na skripty, kterým důvěřujete.

## <a name="use-supporting-scripts"></a>Použití podpůrných skriptů

Složité logiky můžete oddělit do jednoho nebo více podpůrných souborů skriptů. Vlastnost `supportingScriptURI` umožňuje v případě potřeby poskytnout pole identifikátorů URI podpůrným souborům skriptů:

```json
"scriptContent": "
    ...
    ./Create-Cert.ps1
    ...
"

"supportingScriptUris": [
  "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/create-cert.ps1"
],
```

Podpůrné soubory skriptů lze volat z vložkových i primárních souborů skriptů. Podpůrné soubory skriptů nemají žádná omezení pro příponu souboru.

Podpůrné soubory jsou zkopírovány do azscripts/azscriptinput za běhu. Relativní cestou můžete odkazovat na podpůrné soubory z vložek skriptů a primárních souborů skriptů.

## <a name="work-with-outputs-from-powershell-script"></a>Práce s výstupy ze skriptu PowerShellu

Následující šablona ukazuje, jak předat hodnoty mezi dvěma prostředky deploymentScripts:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

V prvním prostředku definujete proměnnou nazvanou **$DeploymentScriptOutputs**a použijete ji k uložení výstupních hodnot. Chcete-li získat přístup k výstupní hodnotě z jiného prostředku v rámci šablony, použijte:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Práce s výstupy ze skriptu CLI

Na rozdíl od skriptu nasazení prostředí PowerShell, CLI/bash podpora nezveřejňuje společné proměnné pro ukládání výstupů skriptu, místo toho je proměnná prostředí s názvem **AZ_SCRIPTS_OUTPUT_PATH,** který ukládá umístění, kde se nachází soubor výstupů skriptu. Pokud je skript nasazení spuštěn ze šablony Správce prostředků, je tato proměnná prostředí automaticky nastavena prostředím Bash.

Výstupy skriptu nasazení musí být uloženy v AZ_SCRIPTS_OUTPUT_PATH umístění a výstupy musí být platný objekt řetězce JSON. Obsah souboru musí být uložen jako pár klíč-hodnota. Například pole řetězců je uloženo jako { "MyResult": [ "foo", "bar"] }.  Uložení pouze výsledků pole, například [ "foo", "bar" ], je neplatné.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[jq](https://stedolan.github.io/jq/) se používá v předchozím vzorku. Dodává se s obrázky kontejneru. Viz [Konfigurace vývojového prostředí](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Vývoj skriptů nasazení

### <a name="handle-non-terminating-errors"></a>Zpracování neukončujících chyb

Pomocí proměnné [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) ve skriptu nasazení můžete řídit, jak prostředí PowerShell reaguje na neukončující chyby. Modul skriptů nasazení nenastaví nebo nezmění hodnotu.  Navzdory hodnotě nastavené pro $ErrorActionPreference nastaví skript nasazení stav zřizování prostředků na *Neúspěšný,* když skript narazí na chybu.

### <a name="pass-secured-strings-to-deployment-script"></a>Předání zabezpečených řetězců do skriptu nasazení

Nastavení proměnných prostředí (EnvironmentVariable) v instancích kontejneru umožňuje poskytovat dynamickou konfiguraci aplikace nebo skriptu spuštěného kontejnerem. Skript nasazení zpracovává nezabezpečené a zabezpečené proměnné prostředí stejným způsobem jako instance kontejneru Azure. Další informace naleznete v tématu [Nastavení proměnných prostředí v instancích kontejneru](../../container-instances/container-instances-environment-variables.md#secure-values).

## <a name="debug-deployment-scripts"></a>Ladění skriptů nasazení

Služba skriptu vytvoří [účet úložiště](../../storage/common/storage-account-overview.md) a [instanci kontejneru](../../container-instances/container-instances-overview.md) pro spuštění skriptu. Oba prostředky mají příponu **azscripts** v názvech prostředků.

![Názvy prostředků skriptu nasazení šablony Správce prostředků](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Uživatelský skript, výsledky spuštění a soubor stdout jsou uloženy ve sdílených složkách účtu úložiště. Existuje složka s názvem **azscripts**. Ve složce jsou další dvě složky pro vstup a výstupní soubory: **azscriptinput** a **azscriptoutput**.

Výstupní složka obsahuje **executionresult.json** a výstupní soubor skriptu. Chybová zpráva spuštění skriptu se zobrazí v **souboru executionresult.json**. Výstupní soubor je vytvořen pouze v případě, že je skript úspěšně spuštěn. Vstupní složka obsahuje soubor skriptu systému PowerShell a soubory skriptu pro nasazení uživatele. Soubor skriptu nasazení uživatele můžete nahradit revidovaným souborem a znovu spustit skript nasazení z instance kontejneru Azure.

Informace o nasazení prostředků skriptu nasazení můžete získat na úrovni skupiny prostředků a na úrovni předplatného pomocí rozhraní REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2019-10-01-preview
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2019-10-01-preview
```

Následující příklad používá [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2019-10-01-preview
```

Výstup je podobný tomuto:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-status.json" range="1-37" highlight="15,34":::

Výstup zobrazuje stav nasazení a ID prostředků skriptu nasazení.

Následující rozhraní REST API vrátí protokol:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Funguje pouze před odstraněním prostředků skriptu nasazení.

Chcete-li zobrazit prostředek deploymentScripts na portálu, vyberte **Zobrazit skryté typy**:

![Skript nasazení šablony Správce prostředků, zobrazit skryté typy, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Vyčistit prostředky skriptu nasazení

Skript nasazení vytvoří účet úložiště a instanci kontejneru, které se používají pro provádění skriptů nasazení a ukládání ladicích informací. Tyto dva prostředky jsou vytvořeny ve stejné skupině prostředků jako zřízené prostředky a budou odstraněny službou skriptu po vypršení platnosti skriptu. Můžete řídit životní cyklus těchto prostředků.  Dokud nejsou odstraněny, budou vám účtovány oba prostředky. Informace o cenách najdete v [tématu Ceny instancí kontejnerů](https://azure.microsoft.com/pricing/details/container-instances/) a [ceny azure storage](https://azure.microsoft.com/pricing/details/storage/).

Životní cyklus těchto prostředků je řízen následujícími vlastnostmi v šabloně:

- **cleanupPreference**: Vyčistit předvolbu při spuštění skriptu dostane do terminálového stavu.  Podporované hodnoty jsou:

  - **Vždy**: Odstraňte prostředky, jakmile se spuštění skriptu dostane do terminálového stavu. Vzhledem k tomu, že prostředek deploymentScripts může být stále k dispozici i po vyčištění prostředků, systémový skript by zkopíroval výsledky spuštění skriptu, například stdout, výstupy, vrácenou hodnotu atd.
  - **OnSuccess**: Odstraňte prostředky pouze v případě, že spuštění skriptu je úspěšné. Stále můžete získat přístup k prostředkům najít informace o ladění.
  - **OnExpiration**: Odstraňte prostředky pouze v případě, že vypršela platnost nastavení **retentionInterval.** Tato vlastnost je momentálně zakázána.

- **retentionInterval**: Zadejte časový interval, po kterém bude prostředek skriptu zachován a po kterém vyprší platnost a bude odstraněn.

> [!NOTE]
> Není doporučeno používat prostředky skriptu nasazení pro jiné účely.

## <a name="run-script-more-than-once"></a>Spuštění skriptu více než jednou

Spuštění skriptu nasazení je idempotentní operace. Pokud se nezmění žádná z vlastností prostředku deploymentScripts (včetně vložkového skriptu), skript nebude spuštěn při opětovném nasazení šablony. Služba skriptu nasazení porovnává názvy prostředků v šabloně s existujícími prostředky ve stejné skupině prostředků. Existují dvě možnosti, pokud chcete spustit stejný skript nasazení vícekrát:

- Změňte název prostředku deploymentScripts. Použijte například funkci šablony [utcNow](./template-functions-date.md#utcnow) jako název prostředku nebo jako součást názvu prostředku. Změna názvu prostředku vytvoří nový prostředek deploymentScripts. Je to dobré pro vedení historie spuštění skriptu.

    > [!NOTE]
    > Funkci utcNow lze použít pouze ve výchozí hodnotě parametru.

- Zadejte jinou `forceUpdateTag` hodnotu ve vlastnosti šablony.  Například použijte utcNow jako hodnotu.

> [!NOTE]
> Napište skripty nasazení, které jsou idempotentní. Tím je zajištěno, že pokud se spustí znovu náhodně, nezpůsobí změny systému. Například pokud skript nasazení se používá k vytvoření prostředku Azure, ověřte prostředek neexistuje před jeho vytvořením, takže skript bude úspěšné, nebo nechcete vytvořit prostředek znovu.

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Jako vývojové prostředí skriptu nasazení můžete použít předkonfigurovanou bitovou kopii kontejneru dockeru. Následující postup ukazuje, jak nakonfigurovat image dockeru v systému Windows. Pro Linux a Mac, můžete najít informace na internetu.

1. Nainstalujte [Docker Desktop](https://www.docker.com/products/docker-desktop) do vývojového počítače.
1. Otevřete desktop dockeru.
1. Vyberte ikonu Docker Desktop z hlavních panelů a pak vyberte **Nastavení**.
1. Vyberte **Sdílené jednotky**, vyberte místní jednotku, která má být dostupná pro vaše kontejnery, a pak vyberte **Použít**

    ![Jednotka dockeru pro nasazení šablony Resource Manageru](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Na výzvu zadejte pověření systému Windows.
1. Otevřete okno terminálu, příkazový řádek nebo prostředí Windows PowerShell (Nepoužívejte prostředí PowerShell ISE).
1. Vytáhněte bitovou kopii kontejneru skriptu nasazení do místního počítače:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Příklad používá verzi PowerShellu 2.7.0.

    Chcete-li získat bitovou kopii rozhraní rozhraní se stavu nastavení systému řízení z registru kontejnerů společnosti Microsoft (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    Tento příklad používá verzi CLI 2.0.80. Skript nasazení používá výchozí imbliny kontejnerů v ykoncích, které jsou [zde](https://hub.docker.com/_/microsoft-azure-cli)k dispozici .

1. Spusťte image dockeru místně.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Nahraďte ** &lt;písmeno ovladače hostitele>** a ** &lt;název adresáře hostitele>** existující složkou na sdílené jednotce.  Mapuje složku do složky **/data** v kontejneru. Příklady pro mapování D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **-znamená to** udržet obraz kontejneru naživu.

    Příklad zapisování lisek:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Až se zobrazí výzva, vyberte **Sdílet.**
1. Následující snímek obrazovky ukazuje, jak spustit skript prostředí PowerShell, vzhledem k tomu, že máte soubor helloworld.ps1 ve složce d:\docker.

    ![Centrum infrastruktury pro nasazení skriptu pro nasazení šablony prostředků cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po úspěšném testování skriptu jej můžete použít jako skript nasazení.

## <a name="next-steps"></a>Další kroky

V tomto článku jste se naučili používat skripty nasazení. Chcete-li projít kurz emitačního skriptu nasazení:

> [!div class="nextstepaction"]
> [Kurz: Použití skriptů nasazení v šablonách Azure Resource Manageru](./template-tutorial-deployment-script.md)