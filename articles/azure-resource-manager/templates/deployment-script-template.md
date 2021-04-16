---
title: Použití skriptů pro nasazení v šablonách | Microsoft Docs
description: použití skriptů pro nasazení v šablonách Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/15/2021
ms.author: jgao
ms.openlocfilehash: d35deb978b3b60b73ac393b241471cb528817d35
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2021
ms.locfileid: "107536957"
---
# <a name="use-deployment-scripts-in-arm-templates"></a>Použití skriptů pro nasazení v šablonách ARM

Naučte se používat skripty pro nasazení v šablonách prostředků Azure (šablony ARM). S názvem nový typ prostředku `Microsoft.Resources/deploymentScripts` můžou uživatelé spouštět skripty v nasazeních šablon a kontrolovat výsledky spuštění. Tyto skripty lze použít k provedení vlastních kroků, jako například:

- Přidání uživatelů do adresáře
- provádět operace roviny dat, například objekty blob kopírování nebo databáze počátečních dat
- vyhledat a ověřit licenční klíč
- Vytvoření certifikátu podepsaného svým držitelem
- Vytvoření objektu ve službě Azure AD
- vyhledat bloky IP adres z vlastního systému

Výhody skriptu nasazení:

- Snadné kódování, použití a ladění. Skripty pro nasazení můžete vyvíjet ve svých oblíbených vývojových prostředích. Skripty mohou být vloženy do šablon nebo externích souborů skriptu.
- Můžete zadat jazyk skriptu a platformu. V současné době jsou podporovány skripty nasazení Azure PowerShell a Azure CLI v prostředí systému Linux.
- Povolí předání argumentů příkazového řádku do skriptu.
- Může určit výstupy skriptu a předávat je zpátky do nasazení.

Prostředek skriptu nasazení je k dispozici pouze v oblastech, kde je k dispozici Azure Container instance.  Přečtěte si téma [dostupnost prostředků pro Azure Container Instances v oblastech Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> K provádění skriptů a odstraňování potíží je potřeba účet úložiště a instance kontejneru. Máte možnost zadat existující účet úložiště, jinak se služba skriptu automaticky vytvoří účet úložiště spolu s instancí kontejneru. Tyto dva automaticky vytvořené prostředky obvykle odstraní služba skriptu, když se spuštění skriptu nasazení dostane do stavu terminálu. Budou se vám účtovat prostředky, dokud se prostředky neodstraní. Další informace najdete v tématu [vyčištění prostředků skriptů nasazení](#clean-up-deployment-script-resources).

> [!IMPORTANT]
> Rozhraní deploymentScripts Resource API verze 2020-10-01 podporuje [OnBehalfofTokens (OBO)](../../active-directory/develop/v2-oauth2-on-behalf-of-flow.md). Pomocí OBO využívá služba skriptu nasazení token objektu zabezpečení nasazení k vytvoření základních prostředků pro spouštění skriptů nasazení, mezi které patří Azure Container instance, účet Azure Storage a přiřazení rolí pro spravovanou identitu. Ve starší verzi rozhraní API se k vytváření těchto prostředků používá spravovaná identita.
> Logika opakování pro přihlášení k Azure je teď integrovaná do skriptu obálky. Pokud udělíte oprávnění ve stejné šabloně, kde spouštíte skripty nasazení. Služba skriptu nasazení opakuje přihlášení po dobu 10 minut a 10 sekund, dokud se nereplikuje přiřazení role spravované identity.

## <a name="configure-the-minimum-permissions"></a>Konfigurace minimálních oprávnění

Pro nasazení rozhraní API skriptu verze 2020-10-01 nebo novější se k vytvoření základních prostředků, které jsou potřebné ke spuštění prostředku skriptu nasazení, používá účet úložiště a instance kontejneru Azure. Pokud se skript potřebuje k ověřování v Azure a provádění akcí specifických pro Azure, doporučujeme pro tento skript poskytnout spravovanou identitu přiřazenou uživatelem. Aby bylo možné dokončit operaci ve skriptu, musí mít spravovaná identita požadovaný přístup.

Chcete-li konfigurovat oprávnění pro nejnižší oprávnění, budete potřebovat:

- Přiřaďte k instančnímu objektu nasazení vlastní roli s následujícími vlastnostmi:

  ```json
  {
    "roleName": "deployment-script-minimum-privilege-for-deployment-principal",
    "description": "Configure least privilege for the deployment principal in deployment script",
    "type": "customRole",
    "IsCustom": true,
    "permissions": [
      {
        "actions": [
          "Microsoft.Storage/storageAccounts/*",
          "Microsoft.ContainerInstance/containerGroups/*",
          "Microsoft.Resources/deployments/*",
          "Microsoft.Resources/deploymentScripts/*"
        ],
      }
    ],
    "assignableScopes": [
      "[subscription().id]"
    ]
  }
  ```

  Pokud Azure Storage a poskytovatelé prostředků služby Azure Container instance nejsou zaregistrovaní, je také nutné přidat `Microsoft.Storage/register/action` a `Microsoft.ContainerInstance/register/action` .

- Pokud se používá spravovaná identita, hlavní povinný objekt nasazení potřebuje roli **spravovaného operátora identity** (předdefinované role) přiřazenou spravovanému prostředku identity.

## <a name="sample-templates"></a>Ukázkové šablony

Následující kód JSON je příklad. Další informace najdete v tématu nejnovější [schéma šablony](/azure/templates/microsoft.resources/deploymentscripts).

```json
{
  "type": "Microsoft.Resources/deploymentScripts",
  "apiVersion": "2020-10-01",
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
    "forceUpdateTag": "1",
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
    "azPowerShellVersion": "3.0",  // or "azCliVersion": "2.0.80",
    "arguments": "-name \\\"John Dole\\\"",
    "environmentVariables": [
      {
        "name": "UserName",
        "value": "jdole"
      },
      {
        "name": "Password",
        "secureValue": "jDolePassword"
      }
    ],
    "scriptContent": "
      param([string] $name)
      $output = 'Hello {0}. The username is {1}, the password is {2}.' -f $name,${Env:UserName},${Env:Password}
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
> Příklad je určen pro demonstrační účely. Vlastnosti `scriptContent` a `primaryScriptUri` nemohou koexistovat v šabloně.

> [!NOTE]
> _ScriptContent_ zobrazuje skript s více řádky.  Azure Portal a kanál Azure DevOps nedokáže analyzovat skript nasazení s více řádky. Můžete buď zřetězit příkazy prostředí PowerShell (pomocí středníků nebo _\\ r \\ n_ nebo _\\ n_) na jeden řádek, nebo použít `primaryScriptUri` vlastnost s externím souborem skriptu. K dispozici je mnoho bezplatných řídicích a řídicích nástrojů řetězce JSON. Příklad: [https://www.freeformatter.com/json-escape.html](https://www.freeformatter.com/json-escape.html).

Podrobnosti hodnoty vlastnosti:

- `identity`: Rozhraní API pro nasazení skriptů verze 2020-10-01 nebo novější je uživatelem přiřazená identita volitelná, pokud nepotřebujete ve skriptu provádět žádné akce specifické pro Azure.  Pro rozhraní API verze 2019-10-01-Preview je požadovaná spravovaná identita, protože služba skriptu nasazení ji používá ke spouštění skriptů. Je-li zadána vlastnost identity, služba skriptu před vyvoláním `Connect-AzAccount -Identity` skriptu uživatele zavolá. V současné době je podporována pouze spravovaná identita přiřazená uživatelem. Pokud se chcete přihlásit s jinou identitou, můžete ve skriptu volat [Connect-AzAccount](https://docs.microsoft.com/powershell/module/az.accounts/connect-azaccount) .
- `kind`: Zadejte typ skriptu. V současné době jsou podporovány skripty Azure PowerShell a Azure CLI. Hodnoty jsou **AzurePowerShell** a **Azure CLI**.
- `forceUpdateTag`: Změna této hodnoty mezi nasazeními šablon vynutí opětovné spuštění skriptu nasazení. Použijete-li `newGuid()` funkce nebo `utcNow()` , lze obě funkce použít pouze ve výchozí hodnotě parametru. Další informace najdete v tématu [spuštění skriptu více než jednou](#run-script-more-than-once).
- `containerSettings`: Zadejte nastavení pro přizpůsobení instance kontejneru Azure. Skript nasazení vyžaduje novou instanci kontejneru Azure. Nemůžete zadat existující instanci kontejneru Azure. Název skupiny kontejnerů ale můžete přizpůsobit pomocí `containerGroupName` . Pokud není zadaný, název skupiny se automaticky vygeneruje.
- `storageAccountSettings`: Zadejte nastavení pro použití existujícího účtu úložiště. Pokud `storageAccountName` parametr není zadaný, automaticky se vytvoří účet úložiště. Viz [použití existujícího účtu úložiště](#use-existing-storage-account).
- `azPowerShellVersion`/`azCliVersion`: Zadejte verzi modulu, která se má použít. Podívejte se na seznam [podporovaných verzí Azure PowerShell](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Podívejte se na seznam [podporovaných verzí rozhraní příkazového řádku Azure CLI](https://mcr.microsoft.com/v2/azure-cli/tags/list).

  >[!IMPORTANT]
  > Skript nasazení používá dostupné image CLI z Microsoft Container Registry (MCR). K certifikaci bitové kopie rozhraní příkazového řádku pro skript nasazení trvá zhruba jeden měsíc. Nepoužívejte verze rozhraní příkazového řádku, které byly vydány do 30 dnů. Data vydání imagí najdete v [poznámkách k verzi Azure CLI](/cli/azure/release-notes-azure-cli). Pokud se používá Nepodporovaná verze, zobrazí se chybová zpráva s podporovanými verzemi.

- `arguments`: Zadejte hodnoty parametrů. Hodnoty jsou oddělené mezerami.

  Skripty nasazení rozdělí argumenty do pole řetězců vyvoláním [CommandLineToArgvW ](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw) systému. Tento krok je nezbytný, protože argumenty jsou předány jako [vlastnost příkazu](/rest/api/container-instances/containergroups/createorupdate#containerexec) službě Azure Container instance a vlastnost Command je pole řetězce.

  Pokud argumenty obsahují řídicí znaky, použijte [JsonEscaper](https://www.jsonescaper.com/) pro dvojitou sekvenci znaků. Vložte původní řídicí řetězec do nástroje a pak vyberte **Escape**.  Nástroj vypíše řetězec s dvojitým řídicím znakem. Například v předchozí ukázkové šabloně je argument `-name \"John Dole\"` . Řídicí řetězec je `-name \\\"John Dole\\\"` .

  Chcete-li předat parametr šablony ARM typu Object jako argument, převeďte objekt na řetězec pomocí funkce [String ()](./template-functions-string.md#string) a pak použijte funkci [Replace ()](./template-functions-string.md#replace) , která se má nahradit `\"` `\\\"` . Například:

  ```json
  replace(string(parameters('tables')), '\"', '\\\"')
  ```

  Další informace najdete v [ukázkové šabloně](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-jsonEscape.json).

- `environmentVariables`: Určete proměnné prostředí, které se mají předat skriptu. Další informace najdete v tématu [vývoj skriptů nasazení](#develop-deployment-scripts).
- `scriptContent`: Zadejte obsah skriptu. Pokud chcete spustit externí skript, použijte `primaryScriptUri` místo toho. Příklady najdete v tématu [použití vloženého skriptu](#use-inline-scripts) a [použití externího skriptu](#use-external-scripts).
- `primaryScriptUri`: Zadejte veřejně přístupnou adresu URL ke skriptu primárního nasazení s podporovanými příponami souborů. Další informace najdete v tématu [použití externích skriptů](#use-external-scripts).
- `supportingScriptUris`: Určete pole veřejně přístupných adres URL k podpůrným souborům, které jsou volány buď `scriptContent` nebo `primaryScriptUri` . Další informace najdete v tématu [použití externích skriptů](#use-external-scripts).
- `timeout`: Zadejte maximální povolenou dobu spuštění skriptu určenou ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**.
- `cleanupPreference`. Určete předvolbu čisticích prostředků nasazení, když se spuštění skriptu dostane do stavu terminálu. Výchozí nastavení je **vždy**, což znamená odstranění prostředků navzdory stavu terminálu (úspěšné, neúspěšné, zrušené). Další informace najdete v tématu [vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources).
- `retentionInterval`: Zadejte interval, po který služba uchovává prostředky skriptu nasazení poté, co spuštění skriptu nasazení dosáhne stavu terminálu. Prostředky skriptu nasazení budou odstraněny po uplynutí této doby. Doba trvání vychází ze [vzoru ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Interval uchovávání dat je mezi 1 a 26 hodinami (PT26H). Tato vlastnost se používá v případě, že `cleanupPreference` je nastavena na hodnotu- **vypršení platnosti**. Další informace najdete v tématu [vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Další ukázky

- [Ukázka 1](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json): vytvořte Trezor klíčů a pomocí skriptu pro nasazení přiřaďte certifikát k trezoru klíčů.
- [Ukázka 2](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-subscription.json): Vytvořte skupinu prostředků na úrovni předplatného, ve skupině prostředků vytvořte Trezor klíčů a pak pomocí skriptu pro nasazení přiřaďte certifikát k trezoru klíčů.
- [Ukázka 3](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json): Vytvořte spravovanou identitu přiřazenou uživatelem, přiřaďte roli přispěvatele k identitě na úrovni skupiny prostředků, vytvořte Trezor klíčů a pak pomocí skriptu pro nasazení přiřaďte certifikát k trezoru klíčů.

## <a name="use-inline-scripts"></a>Použít vložené skripty

Následující šablona má definován jeden prostředek s `Microsoft.Resources/deploymentScripts` typem. Zvýrazněná část je vložený skript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-44" highlight="24-30":::

> [!NOTE]
> Vzhledem k tomu, že vložené skripty pro nasazení jsou uzavřeny do dvojitých uvozovek, musí být řetězce uvnitř skriptů nasazení uvozeny pomocí zpětného lomítka (**&#92;**) nebo uzavřeny v jednoduchých uvozovkách. Můžete také zvážit použití náhrady řetězce, jak je znázorněno v předchozí ukázce JSON.

Skript přijímá jeden parametr a výstupní hodnotu parametru. `DeploymentScriptOutputs` slouží k ukládání výstupů. V části výstupy `value` zobrazuje čára přístup k uloženým hodnotám. `Write-Output` se používá pro účely ladění. Informace o tom, jak získat přístup k výstupnímu souboru, najdete v tématu [monitorování a odstraňování potíží se skripty nasazení](#monitor-and-troubleshoot-deployment-scripts). Popis vlastností naleznete v tématu [Sample Templates](#sample-templates).

Chcete-li spustit skript, vyberte příkaz **zkusit** pro otevření Cloud Shell a poté vložte následující kód do podokna prostředí.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json"

Write-Host "Press [ENTER] to continue ..."
```

Výstup vypadá takto:

![Výstup skriptu Hello World pro Správce prostředků šablon nasazení](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Použití externích skriptů

Kromě vložených skriptů můžete použít také externí soubory skriptu. Podporují se jenom primární skripty PowerShellu s příponou souboru _ps1_ . U skriptů CLI můžou primární skripty mít jakákoli rozšíření (nebo bez přípony), pokud jsou tyto skripty platné bash skripty. Chcete-li použít externí soubory skriptu, nahraďte parametr `scriptContent` `primaryScriptUri` . Například:

```json
"primaryScriptUri": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Další informace najdete v [příkladu šablony](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Soubory externího skriptu musí být přístupné. Pokud chcete zabezpečit soubory skriptů, které jsou uložené v účtech Azure Storage, vygenerujte token SAS a zahrňte ho do identifikátoru URI pro šablonu. Nastavte čas vypršení platnosti, abyste měli dostatek času na dokončení nasazení. Další informace najdete v tématu [nasazení privátní šablony ARM pomocí tokenu SAS](./secure-template-with-sas-token.md).

Zodpovídáte za zajištění integrity skriptů, které jsou odkazovány pomocí skriptu nasazení, a to buď `primaryScriptUri` nebo `supportingScriptUris` . Odkazovat pouze na skripty, kterým důvěřujete.

## <a name="use-supporting-scripts"></a>Použití podpůrných skriptů

Složité logiky můžete oddělit do jednoho nebo více podpůrných souborů skriptu. `supportingScriptUris`Vlastnost umožňuje v případě potřeby zadat pole identifikátorů URI pro podpůrné soubory skriptu:

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

Podpůrné soubory skriptu lze volat z vložených skriptů i souborů primárního skriptu. Podpora souborů skriptu nemá žádná omezení na příponu souboru.

Podpůrné soubory jsou zkopírovány do `azscripts/azscriptinput` modulu runtime. Pomocí relativní cesty můžete odkazovat na podpůrné soubory z vložených skriptů a souborů primárního skriptu.

## <a name="work-with-outputs-from-powershell-script"></a>Práce s výstupy ze skriptu PowerShellu

Následující šablona ukazuje, jak předat hodnoty mezi dvěma `deploymentScripts` prostředky:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-68" highlight="30-31,50":::

V prvním prostředku definujete proměnnou s názvem `$DeploymentScriptOutputs` a použijete ji k uložení výstupních hodnot. Chcete-li získat přístup k výstupní hodnotě z jiného prostředku v rámci šablony, použijte:

```json
reference('<ResourceName>').outputs.text
```

## <a name="work-with-outputs-from-cli-script"></a>Práce s výstupy ze skriptu CLI

Liší se od skriptu nasazení prostředí PowerShell, Podpora CLI/bash nezveřejňuje společnou proměnnou pro ukládání výstupů skriptu, místo toho je k dispozici proměnná prostředí `AZ_SCRIPTS_OUTPUT_PATH` s názvem, která ukládá umístění, kde se nachází soubor výstupy skriptu. Pokud se skript nasazení spustí ze šablony Správce prostředků, tato proměnná prostředí se pro vás automaticky nastaví pomocí prostředí bash. Hodnota `AZ_SCRIPTS_OUTPUT_PATH` je */mnt/azscripts/azscriptoutput/scriptoutputs.js*.

Výstupy skriptu nasazení musí být uloženy v `AZ_SCRIPTS_OUTPUT_PATH` umístění a výstupy musí být platný objekt řetězce JSON. Obsah souboru musí být uložen jako dvojice klíč-hodnota. Například pole řetězců je uloženo jako `{ "MyResult": [ "foo", "bar"] }` .  Ukládání pouze výsledků pole `[ "foo", "bar" ]` je například neplatné.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) se používá v předchozí ukázce. Dodává se s imagemi kontejneru. Viz [Konfigurace vývojového prostředí](#configure-development-environment).

## <a name="use-existing-storage-account"></a>Použít existující účet úložiště

K provádění skriptů a odstraňování potíží je potřeba účet úložiště a instance kontejneru. Máte možnost zadat existující účet úložiště, jinak se služba skriptu automaticky vytvoří účet úložiště spolu s instancí kontejneru. Požadavky na používání existujícího účtu úložiště:

- Podporované typy účtů úložiště:

    | SKU             | Podporovaný druh     |
    |-----------------|--------------------|
    | Premium_LRS     | Úložiště        |
    | Premium_ZRS     | Úložiště        |
    | Standard_GRS    | Úložiště, StorageV2 |
    | Standard_GZRS   | StorageV2          |
    | Standard_LRS    | Úložiště, StorageV2 |
    | Standard_RAGRS  | Úložiště, StorageV2 |
    | Standard_RAGZRS | StorageV2          |
    | Standard_ZRS    | StorageV2          |

    Tyto kombinace podporují sdílení souborů. Další informace najdete v tématu [Vytvoření sdílené složky Azure](../../storage/files/storage-how-to-create-file-share.md) a [typů účtů úložiště](../../storage/common/storage-account-overview.md).

- Pravidla brány firewall účtu úložiště ještě nejsou podporovaná. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../../storage/common/storage-network-security.md).
- Objekt zabezpečení nasazení musí mít oprávnění ke správě účtu úložiště, který zahrnuje čtení, vytváření a odstraňování sdílených složek.

Chcete-li zadat existující účet úložiště, přidejte následující JSON do elementu Property elementu `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- `storageAccountName`: zadejte název účtu úložiště.
- `storageAccountKey`: zadejte jeden z klíčů účtu úložiště. K načtení klíče lze použít funkci [klíče listkey ()](./template-functions-resource.md#listkeys) . Například:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Kompletní ukázku definice najdete v tématu [ukázkové šablony](#sample-templates) `Microsoft.Resources/deploymentScripts` .

Když se použije existující účet úložiště, služba skriptu vytvoří sdílený soubor s jedinečným názvem. Informace o tom, jak služba skriptu vyčistí sdílenou složku, najdete v tématu [vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources) .

## <a name="develop-deployment-scripts"></a>Vývoj skriptů nasazení

### <a name="handle-non-terminating-errors"></a>Zpracování neukončujících chyb

Způsob, jakým PowerShell odpoví na neukončující chyby, můžete řídit pomocí `$ErrorActionPreference` proměnné ve skriptu nasazení. Pokud tato proměnná není nastavená ve skriptu nasazení, služba skriptu použije výchozí hodnotu **pokračovat**.

Služba skriptu nastaví stav zřizování prostředků na **neúspěšné** , pokud skript narazí na chybu navzdory nastavení `$ErrorActionPreference` .

### <a name="use-environment-variables"></a>Použití proměnných prostředí

Skript nasazení používá tyto proměnné prostředí:

|Proměnná prostředí|Výchozí hodnota|Rezervováno systémem|
|--------------------|-------------|---------------|
|AZ_SCRIPTS_AZURE_ENVIRONMENT|AzureCloud|N|
|AZ_SCRIPTS_CLEANUP_PREFERENCE|Doba platnosti|N|
|AZ_SCRIPTS_OUTPUT_PATH|<AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY>/<AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME>|Y|
|AZ_SCRIPTS_PATH_INPUT_DIRECTORY|/mnt/azscripts/azscriptinput|Y|
|AZ_SCRIPTS_PATH_OUTPUT_DIRECTORY|/mnt/azscripts/azscriptoutput|Y|
|AZ_SCRIPTS_PATH_USER_SCRIPT_FILE_NAME|Azure PowerShell: userscript.ps1; Azure CLI: userscript.sh|Y|
|AZ_SCRIPTS_PATH_PRIMARY_SCRIPT_URI_FILE_NAME|primaryscripturi.config|Y|
|AZ_SCRIPTS_PATH_SUPPORTING_SCRIPT_URI_FILE_NAME|supportingscripturi.config|Y|
|AZ_SCRIPTS_PATH_SCRIPT_OUTPUT_FILE_NAME|scriptoutputs.jsna|Y|
|AZ_SCRIPTS_PATH_EXECUTION_RESULTS_FILE_NAME|executionresult.jsna|Y|
|AZ_SCRIPTS_USER_ASSIGNED_IDENTITY|/Subscriptions/|N|

Další informace o použití nástroje `AZ_SCRIPTS_OUTPUT_PATH` najdete v tématu [práce s výstupy ze skriptu CLI](#work-with-outputs-from-cli-script).

### <a name="pass-secured-strings-to-deployment-script"></a>Předání zabezpečených řetězců skriptu nasazení

Nastavení proměnných prostředí (objekt EnvironmentVariable) ve vašich kontejnerových instancích vám umožní poskytnout dynamickou konfiguraci aplikace nebo skriptu spouštěného kontejnerem. Skript nasazení zpracovává nezabezpečené a zabezpečené proměnné prostředí stejným způsobem jako instance kontejneru Azure. Další informace najdete v tématu [nastavení proměnných prostředí v instancích kontejnerů](../../container-instances/container-instances-environment-variables.md#secure-values). Příklad naleznete v tématu [Sample Templates](#sample-templates).

Maximální povolená velikost pro proměnné prostředí je 64 KB.

## <a name="monitor-and-troubleshoot-deployment-scripts"></a>Monitorování a odstraňování potíží se skripty nasazení

Služba skriptu vytvoří [účet úložiště](../../storage/common/storage-account-overview.md) (Pokud nezadáte existující účet úložiště) a [instanci kontejneru](../../container-instances/container-instances-overview.md) pro spuštění skriptu. Pokud tyto prostředky služba Script Service automaticky vytvoří, oba prostředky mají `azscripts` příponu v názvech prostředků.

![Názvy prostředků skriptů nasazení Správce prostředků šablon](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Uživatelský skript, výsledky spuštění a soubor stdout jsou uloženy ve sdílených složkách účtu úložiště. Existuje složka s názvem `azscripts` . Ve složce jsou k dispozici dvě další složky pro vstup a výstupní soubory: `azscriptinput` a `azscriptoutput` .

Výstupní složka obsahuje _executionresult.js_ a výstupní soubor skriptu. V _executionresult.js_ se zobrazí chybová zpráva spuštění skriptu. Výstupní soubor je vytvořen pouze v případě, že byl skript úspěšně proveden. Vstupní složka obsahuje systémový soubor skriptu PowerShellu a soubory skriptu nasazení uživatele. Můžete nahradit soubor skriptu nasazení uživatele revidovanou a znovu spustit skript nasazení z instance kontejneru Azure.

### <a name="use-the-azure-portal"></a>Použití webu Azure Portal

Po nasazení prostředku skriptu nasazení se prostředek zobrazí v části Skupina prostředků v Azure Portal. Na následujícím snímku obrazovky vidíte stránku s **přehledem** prostředku skriptu nasazení:

![Správce prostředků – přehled portálu skriptu nasazení šablony](./media/deployment-script-template/resource-manager-deployment-script-portal.png)

Na stránce Přehled se zobrazí některé důležité informace o prostředku, jako je například **stav zřizování**, **účet úložiště**, **instance kontejneru** a **protokoly**.

V nabídce vlevo můžete zobrazit obsah skriptu nasazení, argumenty předané skriptu a výstup. Můžete také exportovat šablonu pro skript nasazení, včetně skriptu nasazení.

### <a name="use-powershell"></a>Použití prostředí PowerShell

Pomocí Azure PowerShell můžete spravovat skripty nasazení v rámci předplatného nebo oboru skupiny prostředků:

- [Get-AzDeploymentScript](/powershell/module/az.resources/get-azdeploymentscript): Získá nebo vypíše skripty pro nasazení.
- [Get-AzDeploymentScriptLog](/powershell/module/az.resources/get-azdeploymentscriptlog): Získá protokol provádění skriptu nasazení.
- [Remove-AzDeploymentScript](/powershell/module/az.resources/remove-azdeploymentscript): Odebere skript nasazení a jeho přidružené prostředky.
- [Save-AzDeploymentScriptLog](/powershell/module/az.resources/save-azdeploymentscriptlog): uloží protokol spuštění skriptu nasazení na disk.

`Get-AzDeploymentScript`Výstup je podobný následujícímu:

```output
Name                : runPowerShellInlineWithOutput
Id                  : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput
ResourceGroupName   : myds0618rg
Location            : centralus
SubscriptionId      : 01234567-89AB-CDEF-0123-456789ABCDEF
ProvisioningState   : Succeeded
Identity            : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/mydentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami
ScriptKind          : AzurePowerShell
AzPowerShellVersion : 3.0
StartTime           : 6/18/2020 7:46:45 PM
EndTime             : 6/18/2020 7:49:45 PM
ExpirationDate      : 6/19/2020 7:49:45 PM
CleanupPreference   : OnSuccess
StorageAccountId    : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.Storage/storageAccounts/ftnlvo6rlrvo2azscripts
ContainerInstanceId : /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0618rg/providers/Microsoft.ContainerInstance/containerGroups/ftnlvo6rlrvo2azscripts
Outputs             :
                      Key                 Value
                      ==================  ==================
                      text                Hello John Dole

RetentionInterval   : P1D
Timeout             : PT1H
```

### <a name="use-azure-cli"></a>Použití Azure CLI

Pomocí Azure CLI můžete spravovat skripty pro nasazení v rámci předplatného nebo oboru skupiny prostředků:

- [AZ Deployment-Scripts Delete](/cli/azure/deployment-scripts#az-deployment-scripts-delete): odstranění skriptu nasazení.
- [AZ Deployment-Scripting list](/cli/azure/deployment-scripts#az-deployment-scripts-list): vypíše všechny skripty nasazení.
- [AZ Deployment-Scripts show](/cli/azure/deployment-scripts#az-deployment-scripts-show): načtení skriptu nasazení.
- [AZ Deployment-skripty show-log](/cli/azure/deployment-scripts#az-deployment-scripts-show-log): zobrazí protokoly skriptu nasazení.

Výstup příkazu list je podobný následujícímu:

```json
[
  {
    "arguments": "-name \\\"John Dole\\\"",
    "azPowerShellVersion": "3.0",
    "cleanupPreference": "OnSuccess",
    "containerSettings": {
      "containerGroupName": null
    },
    "environmentVariables": null,
    "forceUpdateTag": "20200625T025902Z",
    "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
    "identity": {
      "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
      "type": "userAssigned",
      "userAssignedIdentities": {
        "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
          "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF",
          "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF"
        }
      }
    },
    "kind": "AzurePowerShell",
    "location": "centralus",
    "name": "runPowerShellInlineWithOutput",
    "outputs": {
      "text": "Hello John Dole"
    },
    "primaryScriptUri": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "myds0624rg",
    "retentionInterval": "1 day, 0:00:00",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "endTime": "2020-06-25T03:00:16.796923+00:00",
      "error": null,
      "expirationTime": "2020-06-26T03:00:16.796923+00:00",
      "startTime": "2020-06-25T02:59:07.595140+00:00",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts"
    },
    "storageAccountSettings": null,
    "supportingScriptUris": null,
    "systemData": {
      "createdAt": "2020-06-25T02:59:04.750195+00:00",
      "createdBy": "someone@contoso.com",
      "createdByType": "User",
      "lastModifiedAt": "2020-06-25T02:59:04.750195+00:00",
      "lastModifiedBy": "someone@contoso.com",
      "lastModifiedByType": "User"
    },
    "tags": null,
    "timeout": "1:00:00",
    "type": "Microsoft.Resources/deploymentScripts"
  }
]
```

### <a name="use-rest-api"></a>Použití rozhraní REST API

Informace o nasazení prostředku skriptu nasazení můžete získat na úrovni skupiny prostředků a na úrovni předplatného pomocí REST API:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>?api-version=2020-10-01
```

```rest
/subscriptions/<SubscriptionID>/providers/microsoft.resources/deploymentScripts?api-version=2020-10-01
```

Následující příklad používá [ARMClient](https://github.com/projectkudu/ARMClient):

```azurepowershell
armclient login
armclient get /subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourcegroups/myrg/providers/microsoft.resources/deploymentScripts/myDeployementScript?api-version=2020-10-01
```

Výstup je podobný tomuto:

```json
{
  "kind": "AzurePowerShell",
  "identity": {
    "type": "userAssigned",
    "tenantId": "01234567-89AB-CDEF-0123-456789ABCDEF",
    "userAssignedIdentities": {
      "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myidentity1008rg/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myuami": {
        "principalId": "01234567-89AB-CDEF-0123-456789ABCDEF",
        "clientId": "01234567-89AB-CDEF-0123-456789ABCDEF"
      }
    }
  },
  "location": "centralus",
  "systemData": {
    "createdBy": "someone@contoso.com",
    "createdByType": "User",
    "createdAt": "2020-06-25T02:59:04.7501955Z",
    "lastModifiedBy": "someone@contoso.com",
    "lastModifiedByType": "User",
    "lastModifiedAt": "2020-06-25T02:59:04.7501955Z"
  },
  "properties": {
    "provisioningState": "Succeeded",
    "forceUpdateTag": "20200625T025902Z",
    "azPowerShellVersion": "3.0",
    "scriptContent": "\r\n          param([string] $name)\r\n          $output = \"Hello {0}\" -f $name\r\n          Write-Output $output\r\n          $DeploymentScriptOutputs = @{}\r\n          $DeploymentScriptOutputs['text'] = $output\r\n        ",
    "arguments": "-name \\\"John Dole\\\"",
    "retentionInterval": "P1D",
    "timeout": "PT1H",
    "containerSettings": {},
    "status": {
      "containerInstanceId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.ContainerInstance/containerGroups/64lxews2qfa5uazscripts",
      "storageAccountId": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Storage/storageAccounts/64lxews2qfa5uazscripts",
      "startTime": "2020-06-25T02:59:07.5951401Z",
      "endTime": "2020-06-25T03:00:16.7969234Z",
      "expirationTime": "2020-06-26T03:00:16.7969234Z"
    },
    "outputs": {
      "text": "Hello John Dole"
    },
    "cleanupPreference": "OnSuccess"
  },
  "id": "/subscriptions/01234567-89AB-CDEF-0123-456789ABCDEF/resourceGroups/myds0624rg/providers/Microsoft.Resources/deploymentScripts/runPowerShellInlineWithOutput",
  "type": "Microsoft.Resources/deploymentScripts",
  "name": "runPowerShellInlineWithOutput"
}

```

Následující REST API vrátí protokol:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2020-10-01
```

Funguje pouze před odstraněním prostředků skriptu nasazení.

Pokud chcete zobrazit prostředek deploymentScripts na portálu, vyberte **Zobrazit skryté typy**:

![Správce prostředků skript nasazení šablony, zobrazit skryté typy, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="clean-up-deployment-script-resources"></a>Vyčistit prostředky skriptu nasazení

K provádění skriptů a odstraňování potíží je potřeba účet úložiště a instance kontejneru. Máte možnost zadat existující účet úložiště, jinak se služba skriptu automaticky vytvoří účet úložiště spolu s instancí kontejneru. Tyto dva automaticky vytvořené prostředky odstraní služba skriptu, když se spuštění skriptu nasazení dostane do stavu terminálu. Budou se vám účtovat prostředky, dokud se prostředky neodstraní. Informace o cenách najdete v článku [Container Instances](https://azure.microsoft.com/pricing/details/container-instances/) ceny a ceny [Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Životní cyklus těchto prostředků je řízen následujícími vlastnostmi v šabloně:

- `cleanupPreference`: Vyčistěte předvolby, když se spuštění skriptu dostane do stavu terminálu. Podporované hodnoty jsou:

  - **Always**: odstranit automaticky vytvořené prostředky, jakmile se spuštění skriptu dostane do stavu terminálu. Pokud se použije existující účet úložiště, služba skriptu odstraní sdílenou složku vytvořenou v účtu úložiště. Vzhledem k tomu `deploymentScripts` , že prostředek může být stále k dispozici po vyčištění prostředků, služba skriptu zachovává výsledky spuštění skriptu, například stdout, výstupy a návratové hodnoty před odstraněním prostředků.
  - **Úspěch**: Odstraňte automaticky vytvořené prostředky pouze v případě, že je spuštění skriptu úspěšné. Pokud se použije existující účet úložiště, služba skriptu odstraní sdílenou složku, jenom když je spuštění skriptu úspěšné. K vyhledání informací o ladění máte stále přístup k prostředkům.
  - **Vypršení platnosti**: odstranit automaticky vytvořené prostředky jenom v případě, že `retentionInterval` vypršela platnost nastavení. Pokud se použije existující účet úložiště, služba skriptu odebere sdílenou složku, ale zachová účet úložiště.

- `retentionInterval`: Zadejte časový interval, po který bude prostředek skriptu zachován, a poté jeho platnost vyprší a odstraní se.

> [!NOTE]
> Pro jiné účely se nedoporučuje používat účet úložiště a instanci kontejneru vygenerované službou skriptu. Tyto dva prostředky mohou být odstraněny v závislosti na životním cyklu skriptu.

Instance kontejneru a účet úložiště se odstraní podle `cleanupPreference` . Pokud se ale skript nezdařil a `cleanupPreference` není nastaven na hodnotu **vždy**, proces nasazení automaticky udržuje kontejner spuštěný po dobu jedné hodiny. Tuto hodinu můžete použít k řešení potíží se skriptem. Pokud chcete po úspěšném nasazení zachovat kontejner spuštěný, přidejte do skriptu krok přechodu do režimu spánku. Například na konec skriptu přidejte [Start-Sleep](/powershell/module/microsoft.powershell.utility/start-sleep) . Pokud nepřidáte krok spánku, kontejner je nastaven na stav terminálu a nebude k němu mít k dispozici, i když ještě nebyl odstraněn.

## <a name="run-script-more-than-once"></a>Spustit skript více než jednou

Spuštění skriptu nasazení je operace idempotentní. Pokud `deploymentScripts` se nezměnila žádná z vlastností prostředku (včetně vloženého skriptu), skript se nespustí při opětovném nasazení šablony. Služba skriptu nasazení porovnává názvy prostředků v šabloně se stávajícími prostředky ve stejné skupině prostředků. Existují dvě možnosti, pokud chcete spustit stejný skript nasazení několikrát:

- Změňte název `deploymentScripts` prostředku. Použijte například funkci šablony [UtcNow](./template-functions-date.md#utcnow) jako název prostředku nebo jako součást názvu prostředku. Změna názvu prostředku vytvoří nový `deploymentScripts` prostředek. Je vhodné uchovávat historii spuštění skriptu.

    > [!NOTE]
    > `utcNow`Funkci lze použít pouze ve výchozí hodnotě pro parametr.

- Zadejte jinou hodnotu ve `forceUpdateTag` vlastnosti Template. Použijte například `utcNow` jako hodnotu.

> [!NOTE]
> Zapište skripty nasazení, které jsou idempotentní. Tím se zajistí, že pokud se znovu spustí, nezpůsobí se změny systému. Například pokud se skript nasazení používá k vytvoření prostředku Azure, ověřte, že prostředek neexistuje, než ho vytvoříte, takže skript bude úspěšný, nebo znovu nevytvářejte prostředek.

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Můžete použít předem nakonfigurovanou image kontejneru jako vývojové prostředí skriptu nasazení. Další informace najdete v tématu [Konfigurace vývojového prostředí pro skripty nasazení v šablonách](./deployment-script-template-configure-dev.md).

Po úspěšném otestování skriptu ho můžete použít jako skript nasazení v šablonách.

## <a name="deployment-script-error-codes"></a>Kódy chyb skriptu nasazení

| Kód chyby | Popis |
|------------|-------------|
| DeploymentScriptInvalidOperation | Definice prostředku skriptu nasazení v šabloně obsahuje neplatné názvy vlastností. |
| DeploymentScriptResourceConflict | Nelze odstranit prostředek skriptu nasazení, který je v neterminálu, a provádění nepřekročilo 1 hodinu. Nebo nemůžete znovu spustit stejný skript nasazení se stejným identifikátorem prostředku (stejné předplatné, název skupiny prostředků a názvem prostředku), ale zároveň s jiným obsahem textu skriptu. |
| DeploymentScriptOperationFailed | Operace skriptu nasazení se nezdařila interně. Obraťte se na podporu Microsoftu. |
| DeploymentScriptStorageAccountAccessKeyNotSpecified | Přístupový klíč nebyl zadaný pro existující účet úložiště.|
| DeploymentScriptContainerGroupContainsInvalidContainers | Skupina kontejnerů vytvořená službou skriptu nasazení se externě změnila a přidaly se neplatné kontejnery. |
| DeploymentScriptContainerGroupInNonterminalState | Dva nebo více prostředků skriptu nasazení používají stejný název instance kontejneru Azure ve stejné skupině prostředků a jedna z nich ještě nedokončila své provádění. |
| DeploymentScriptStorageAccountInvalidKind | Existující účet úložiště typu BlobBlobStorage nebo BlobStorage nepodporuje sdílené složky a nedá se použít. |
| DeploymentScriptStorageAccountInvalidKindAndSku | Existující účet úložiště nepodporuje sdílení souborů. Seznam podporovaných typů účtů úložiště najdete v tématu [použití existujícího účtu úložiště](#use-existing-storage-account). |
| DeploymentScriptStorageAccountNotFound | Účet úložiště neexistuje nebo byl odstraněn externím procesem nebo nástrojem. |
| DeploymentScriptStorageAccountWithServiceEndpointEnabled | Zadaný účet úložiště má koncový bod služby. Účet úložiště s koncovým bodem služby se nepodporuje. |
| DeploymentScriptStorageAccountInvalidAccessKey | Pro existující účet úložiště je zadaný neplatný přístupový klíč. |
| DeploymentScriptStorageAccountInvalidAccessKeyFormat | Neplatný formát klíče účtu úložiště Viz [Správa přístupových klíčů účtu úložiště](../../storage/common/storage-account-keys-manage.md). |
| DeploymentScriptExceededMaxAllowedTime | Doba spuštění skriptu nasazení překročila hodnotu časového limitu zadanou v definici prostředku skriptu nasazení. |
| DeploymentScriptInvalidOutputs | Výstup skriptu nasazení není platný objekt JSON. |
| DeploymentScriptContainerInstancesServiceLoginFailure | Uživatelsky přiřazená identita se nemohla přihlásit po 10 pokusech v intervalu 1 minut. |
| DeploymentScriptContainerGroupNotFound | Skupina kontejnerů vytvořená službou skriptu nasazení byla odstraněna externím nástrojem nebo procesem. |
| DeploymentScriptDownloadFailure | Nepovedlo se stáhnout podpůrný skript. Viz [použití podpůrného skriptu](#use-supporting-scripts).|
| DeploymentScriptError | V uživatelském skriptu došlo k chybě. |
| DeploymentScriptBootstrapScriptExecutionFailed | Spouštěcí skript vrátil chybu. Spouštěcí skript je systémový skript, který orchestruje spuštění skriptu nasazení. |
| DeploymentScriptExecutionFailed | Během provádění skriptu nasazení došlo k neznámé chybě. |
| DeploymentScriptContainerInstancesServiceUnavailable | Při vytváření instance kontejneru Azure (ACI) ACI vyvolala chybu nedostupné služby. |
| DeploymentScriptContainerGroupInNonterminalState | Při vytváření instance kontejneru Azure (ACI) používá jiný skript nasazení stejný ACI název ve stejném oboru (stejné předplatné, název skupiny prostředků a název prostředku). |
| DeploymentScriptContainerGroupNameInvalid | Zadaný název instance kontejneru Azure (ACI) nesplňuje požadavky ACI. Přečtěte si téma [řešení běžných potíží v Azure Container Instances](../../container-instances/container-instances-troubleshooting.md#issues-during-container-group-deployment).|

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat skripty pro nasazení. Návod k procházení skriptu nasazení:

> [!div class="nextstepaction"]
> [Kurz: použití skriptů nasazení v šablonách Azure Resource Manager](./template-tutorial-deployment-script.md)

> [!div class="nextstepaction"]
> [Seznámení s modulem: roztažení šablon ARM pomocí skriptů pro nasazení](/learn/modules/extend-resource-manager-template-deployment-scripts/)
