---
title: Použití skriptů pro nasazení v šablonách | Microsoft Docs
description: použití skriptů pro nasazení v šablonách Azure Resource Manager.
services: azure-resource-manager
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 05/28/2020
ms.author: jgao
ms.openlocfilehash: 2ca6848ed8fe16baea49311ee4b4b15ae8c64b56
ms.sourcegitcommit: 61d850bc7f01c6fafee85bda726d89ab2ee733ce
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/03/2020
ms.locfileid: "84344709"
---
# <a name="use-deployment-scripts-in-templates-preview"></a>Použití skriptů nasazení v šablonách (Preview)

Naučte se používat skripty pro nasazení v šablonách prostředků Azure. S názvem nový typ prostředku `Microsoft.Resources/deploymentScripts` můžou uživatelé spouštět skripty nasazení v nasazeních šablon a kontrolovat výsledky spuštění. Tyto skripty lze použít k provedení vlastních kroků, jako například:

- Přidání uživatelů do adresáře
- provádět operace roviny dat, například objekty blob kopírování nebo databáze počátečních dat
- vyhledat a ověřit licenční klíč
- Vytvoření certifikátu podepsaného svým držitelem
- Vytvoření objektu ve službě Azure AD
- vyhledat bloky IP adres z vlastního systému

Výhody skriptu nasazení:

- Snadné kódování, použití a ladění. Skripty pro nasazení můžete vyvíjet ve svých oblíbených vývojových prostředích. Skripty mohou být vloženy do šablon nebo externích souborů skriptu.
- Můžete zadat jazyk skriptu a platformu. V současné době jsou podporovány skripty nasazení Azure PowerShell a Azure CLI v prostředí systému Linux.
- Povolte určení identit, které se používají ke spouštění skriptů. V současné době se podporuje jenom [spravovaná identita přiřazená uživatelem Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md) .
- Povolí předání argumentů příkazového řádku do skriptu.
- Může určit výstupy skriptu a předávat je zpátky do nasazení.

Prostředek skriptu nasazení je k dispozici pouze v oblastech, kde je k dispozici Azure Container instance.  Přečtěte si téma [dostupnost prostředků pro Azure Container Instances v oblastech Azure](../../container-instances/container-instances-region-availability.md).

> [!IMPORTANT]
> K provádění skriptů a odstraňování potíží je potřeba účet úložiště a instance kontejneru. Máte možnost zadat existující účet úložiště, jinak se služba skriptu automaticky vytvoří účet úložiště spolu s instancí kontejneru. Tyto dva automaticky vytvořené prostředky obvykle odstraní služba skriptu, když se spuštění skriptu nasazení dostane do stavu terminálu. Budou se vám účtovat prostředky, dokud se prostředky neodstraní. Další informace najdete v tématu [vyčištění prostředků skriptů nasazení](#clean-up-deployment-script-resources).

## <a name="prerequisites"></a>Požadavky

- **Spravovaná identita přiřazená uživatelem s rolí přispěvatele do cílové skupiny prostředků**. Tato identita se používá ke spouštění skriptů nasazení. K provedení operací mimo skupinu prostředků je potřeba udělit další oprávnění. Například pokud chcete vytvořit novou skupinu prostředků, přiřaďte identitu k úrovni předplatného.

  > [!NOTE]
  > Služba skriptu vytvoří účet úložiště (Pokud nezadáte existující účet úložiště) a instanci kontejneru na pozadí.  Spravovaná identita přiřazená uživateli s rolí přispěvatele na úrovni předplatného je povinná, pokud předplatné nezaregistrovalo službu Azure Storage Account (Microsoft. Storage) a poskytovatele prostředků Azure Container instance (Microsoft. ContainerInstance).

  Pokud chcete vytvořit identitu, přečtěte si článek [Vytvoření spravované identity přiřazené uživatelem pomocí Azure Portal](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)nebo pomocí rozhraní příkazového [řádku Azure](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)nebo [pomocí Azure PowerShell](../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md). ID identity budete potřebovat při nasazení šablony. Formát identity je:

  ```json
  /subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<IdentityID>
  ```

  Pomocí následujícího skriptu CLI nebo skriptu PowerShellu Získejte ID zadáním názvu skupiny prostředků a názvu identity.

  # <a name="cli"></a>[Rozhraní příkazového řádku](#tab/CLI)

  ```azurecli-interactive
  echo "Enter the Resource Group name:" &&
  read resourceGroupName &&
  echo "Enter the managed identity name:" &&
  read idName &&
  az identity show -g $resourceGroupName -n $idName --query id
  ```

  # <a name="powershell"></a>[PowerShell](#tab/PowerShell)

  ```azurepowershell-interactive
  $idGroup = Read-Host -Prompt "Enter the resource group name for the managed identity"
  $idName = Read-Host -Prompt "Enter the name of the managed identity"

  (Get-AzUserAssignedIdentity -resourcegroupname $idGroup -Name $idName).Id
  ```

  ---

- **Azure PowerShell** nebo **Azure CLI**. Seznam podporovaných verzí Azure PowerShell najdete [tady](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list). Seznam podporovaných verzí Azure CLI najdete [tady](https://mcr.microsoft.com/v2/azuredeploymentscripts-powershell/tags/list).

    >[!IMPORTANT]
    > Skript nasazení používá dostupné image CLI z Microsoft Container Registry (MCR). K certifikaci bitové kopie rozhraní příkazového řádku pro skript nasazení trvá zhruba jeden měsíc. Nepoužívejte verze rozhraní příkazového řádku, které byly vydány do 30 dnů. Data vydání imagí najdete v [poznámkách k verzi Azure CLI](https://docs.microsoft.com/cli/azure/release-notes-azure-cli?view=azure-cli-latest). Pokud se používá Nepodporovaná verze, zobrazí se chybová zpráva s přehledem podporovaných verzí.

    Tyto verze nepotřebujete pro nasazování šablon. Tyto verze jsou ale nutné pro místní testování skriptů nasazení. Přečtěte si téma [Instalace modulu Azure PowerShell](/powershell/azure/install-az-ps). Můžete použít předem nakonfigurovanou bitovou kopii Docker.  Viz [Konfigurace vývojového prostředí](#configure-development-environment).

## <a name="sample-templates"></a>Ukázkové šablony

Následující kód JSON je příklad.  Nejnovější schéma šablony najdete [tady](/azure/templates/microsoft.resources/deploymentscripts).

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
    "containerSettings": {
      "containerGroupName": "mycustomaci"
    },
    "storageAccountSettings": {
      "storageAccountName": "myStorageAccount",
      "storageAccountKey": "myKey"
    },
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
> Příklad je určen pro demonstrační účely.  **scriptContent** a **primaryScriptUri** se v šabloně nedají koexistovat.

Podrobnosti hodnoty vlastnosti:

- **Identita**: služba skriptu nasazení používá ke spouštění skriptů spravovanou identitu přiřazenou uživatelem. V současné době je podporována pouze spravovaná identita přiřazená uživatelem.
- **druh**: zadejte typ skriptu. V současné době jsou podporovány skripty Azure PowerShell a Azure CLI. Hodnoty jsou **AzurePowerShell** a **Azure CLI**.
- **forceUpdateTag**: Změna této hodnoty mezi nasazeními šablon vynutí opětovné spuštění skriptu nasazení. Použijte funkci newGuid () nebo utcNow (), která musí být nastavena jako výchozí hodnota parametru. Další informace najdete v tématu [spuštění skriptu více než jednou](#run-script-more-than-once).
- **containerSettings**: Zadejte nastavení pro přizpůsobení instance kontejneru Azure.  **containerGroupName** je určen pro zadání názvu skupiny kontejnerů.  Pokud není zadaný, název skupiny se automaticky vygeneruje.
- **storageAccountSettings**: Zadejte nastavení pro použití existujícího účtu úložiště. Pokud není zadaný, automaticky se vytvoří účet úložiště. Viz [použití existujícího účtu úložiště](#use-an-existing-storage-account).
- **azPowerShellVersion** / **azCliVersion**: Zadejte verzi modulu, která se má použít. Seznam podporovaných verzí PowerShellu a rozhraní příkazového řádku najdete v tématu [požadavky](#prerequisites).
- **argumenty**: zadejte hodnoty parametrů. Hodnoty jsou oddělené mezerami.
- **environmentVariables**: Určete proměnné prostředí, které se mají předat skriptu. Další informace najdete v tématu [vývoj skriptů nasazení](#develop-deployment-scripts).
- **scriptContent**: Zadejte obsah skriptu. Pokud chcete spustit externí skript, použijte `primaryScriptUri` místo toho. Příklady najdete v tématu [použití vloženého skriptu](#use-inline-scripts) a [použití externího skriptu](#use-external-scripts).
- **primaryScriptUri**: zadejte veřejně přístupnou adresu URL ke skriptu primárního nasazení s podporovanými příponami souborů.
- **supportingScriptUris**: Určete pole veřejně přístupných adres URL k podpůrným souborům, které jsou volány buď `ScriptContent` nebo `PrimaryScriptUri` .
- **timeout**: zadejte maximální povolenou dobu spuštění skriptu zadanou ve [formátu ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**.
- **cleanupPreference**. Určete předvolbu čisticích prostředků nasazení, když se spuštění skriptu dostane do stavu terminálu. Výchozí nastavení je **vždy**, což znamená odstranění prostředků navzdory stavu terminálu (úspěšné, neúspěšné, zrušené). Další informace najdete v tématu [vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources).
- **retentionInterval**: zadejte interval, po který služba uchovává prostředky skriptu nasazení poté, co spuštění skriptu nasazení dosáhne stavu terminálu. Prostředky skriptu nasazení budou odstraněny po uplynutí této doby. Doba trvání vychází ze [vzoru ISO 8601](https://en.wikipedia.org/wiki/ISO_8601). Výchozí hodnota je **P1D**, což znamená sedm dní. Tato vlastnost se používá, pokud je cleanupPreference nastaveno na hodnotu- *vypršení platnosti*. Vlastnost- *vypršení platnosti* není v současné době povolena. Další informace najdete v tématu [vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources).

### <a name="additional-samples"></a>Další ukázky

- [Vytvoření a přiřazení certifikátu k trezoru klíčů](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault.json)

- [Vytvoření a přiřazení spravované identity přiřazené uživateli do skupiny prostředků a spuštění skriptu nasazení](https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-keyvault-mi.json).

> [!NOTE]
> Doporučuje se vytvořit uživatelem přiřazenou identitu a předem udělit oprávnění. Pokud vytvoříte identitu a udělíte oprávnění ve stejné šabloně, kde spouštíte skripty nasazení, můžete získat chyby související s přihlášením a oprávněními. Bude nějakou dobu trvat, než se oprávnění dostanou platit.

## <a name="use-inline-scripts"></a>Použít vložené skripty

Následující šablona má definován jeden prostředek s `Microsoft.Resources/deploymentScripts` typem. Zvýrazněná část je vložený skript.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-helloworld.json" range="1-54" highlight="34-40":::

> [!NOTE]
> Vzhledem k tomu, že vložené skripty pro nasazení jsou uzavřeny do dvojitých uvozovek, musí být řetězce uvnitř skriptů nasazení uvozeny pomocí **&#92;** nebo uzavřeny v jednoduchých uvozovkách. Můžete také zvážit použití náhrady řetězce, jak je znázorněno v předchozí ukázce JSON.

Skript přijímá jeden parametr a výstupní hodnotu parametru. **DeploymentScriptOutputs** se používá k ukládání výstupů.  V části výstupy zobrazuje řádek **hodnoty** jak získat přístup k uloženým hodnotám. `Write-Output`se používá pro účely ladění. Informace o tom, jak získat přístup k výstupnímu souboru, najdete v tématu [ladění skriptů nasazení](#debug-deployment-scripts).  Popis vlastností naleznete v tématu [Sample Templates](#sample-templates).

Chcete-li spustit skript, vyberte příkaz **zkusit** pro otevření Cloud Shell a poté vložte následující kód do podokna prostředí.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the name of the resource group to be created"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$id = Read-Host -Prompt "Enter the user-assigned managed identity ID"

New-AzResourceGroup -Name $resourceGroupName -Location $location

New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.json" -identity $id

Write-Host "Press [ENTER] to continue ..."
```

Výstup bude vypadat následovně:

![Výstup skriptu Hello World pro Správce prostředků šablon nasazení](./media/deployment-script-template/resource-manager-template-deployment-script-helloworld-output.png)

## <a name="use-external-scripts"></a>Použití externích skriptů

Kromě vložených skriptů můžete použít také externí soubory skriptu. Podporují se jenom primární skripty PowerShellu s příponou souboru **ps1** . U skriptů CLI můžou primární skripty mít jakákoli rozšíření (nebo bez přípony), pokud jsou tyto skripty platné bash skripty. Chcete-li použít externí soubory skriptu, nahraďte parametr `scriptContent` `primaryScriptUri` . Příklad:

```json
"primaryScriptURI": "https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/deployment-script/deploymentscript-helloworld.ps1",
```

Příklad zobrazíte výběrem [sem](https://github.com/Azure/azure-docs-json-samples/blob/master/deployment-script/deploymentscript-helloworld-primaryscripturi.json).

Soubory externího skriptu musí být přístupné.  Informace o zabezpečení souborů skriptu uložených v účtech úložiště Azure najdete v tématu [nasazení privátní šablony ARM pomocí tokenu SAS](./secure-template-with-sas-token.md).

Zodpovídáte za zajištění integrity skriptů, které jsou odkazovány pomocí skriptu nasazení, buď **PrimaryScriptUri** nebo **SupportingScriptUris**.  Odkazovat pouze na skripty, kterým důvěřujete.

## <a name="use-supporting-scripts"></a>Použití podpůrných skriptů

Složité logiky můžete oddělit do jednoho nebo více podpůrných souborů skriptu. `supportingScriptURI`Vlastnost umožňuje v případě potřeby zadat pole identifikátorů URI pro podpůrné soubory skriptu:

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

Podpůrné soubory se zkopírují do azscripts/azscriptinput za běhu. Pomocí relativní cesty můžete odkazovat na podpůrné soubory z vložených skriptů a souborů primárního skriptu.

## <a name="work-with-outputs-from-powershell-script"></a>Práce s výstupy ze skriptu PowerShellu

Následující šablona ukazuje, jak předat hodnoty mezi dvěma deploymentScripts prostředky:

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic.json" range="1-84" highlight="39-40,66":::

V prvním prostředku definujete proměnnou s názvem **$DeploymentScriptOutputs**a použijete ji k uložení výstupních hodnot. Chcete-li získat přístup k výstupní hodnotě z jiného prostředku v rámci šablony, použijte:

```json
reference('<ResourceName>').output.text
```

## <a name="work-with-outputs-from-cli-script"></a>Práce s výstupy ze skriptu CLI

Liší se od skriptu nasazení prostředí PowerShell, Podpora CLI/bash nevystavuje společnou proměnnou pro ukládání výstupů skriptu. místo toho je k dispozici proměnná prostředí s názvem **AZ_SCRIPTS_OUTPUT_PATH** , která ukládá umístění, kde se nachází soubor výstupy skriptu. Pokud se skript nasazení spustí ze šablony Správce prostředků, tato proměnná prostředí se pro vás automaticky nastaví pomocí prostředí bash.

Výstupy skriptu nasazení musí být uloženy v umístění AZ_SCRIPTS_OUTPUT_PATH a výstupy musí být platný objekt řetězce JSON. Obsah souboru musí být uložen jako dvojice klíč-hodnota. Například pole řetězců je uloženo jako {"MyResult": ["foo", "bar"]}.  Ukládání pouze výsledků pole, například ["foo", "bar"], je neplatné.

:::code language="json" source="~/resourcemanager-templates/deployment-script/deploymentscript-basic-cli.json" range="1-44" highlight="32":::

[JQ](https://stedolan.github.io/jq/) se používá v předchozí ukázce. Dodává se s imagemi kontejneru. Viz [Konfigurace vývojového prostředí](#configure-development-environment).

## <a name="develop-deployment-scripts"></a>Vývoj skriptů nasazení

### <a name="handle-non-terminating-errors"></a>Zpracování neukončujících chyb

Pomocí proměnné [**$ErrorActionPreference**](/powershell/module/microsoft.powershell.core/about/about_preference_variables?view=powershell-7#erroractionpreference
) ve skriptu pro nasazení můžete řídit, jak PowerShell odpoví na neukončující chyby. Služba skriptu nenastavuje nebo nemění hodnotu.  Navzdory hodnotě nastavené pro $ErrorActionPreference skript nasazení nastaví stav zřizování prostředků na *neúspěšné* , když skript narazí na chybu.

### <a name="pass-secured-strings-to-deployment-script"></a>Předání zabezpečených řetězců skriptu nasazení

Nastavení proměnných prostředí (objekt EnvironmentVariable) ve vašich kontejnerových instancích vám umožní poskytnout dynamickou konfiguraci aplikace nebo skriptu spouštěného kontejnerem. Skript nasazení zpracovává nezabezpečené a zabezpečené proměnné prostředí stejným způsobem jako instance kontejneru Azure. Další informace najdete v tématu [nastavení proměnných prostředí v instancích kontejnerů](../../container-instances/container-instances-environment-variables.md#secure-values).

Maximální povolená velikost pro proměnné prostředí je 64 KB.

## <a name="debug-deployment-scripts"></a>Ladit skripty nasazení

Služba skriptu vytvoří [účet úložiště](../../storage/common/storage-account-overview.md) (Pokud nezadáte existující účet úložiště) a [instanci kontejneru](../../container-instances/container-instances-overview.md) pro spuštění skriptu. Pokud tyto prostředky služba Script Service automaticky vytvoří, oba prostředky mají v názvech prostředků příponu **azscripts** .

![Názvy prostředků skriptů nasazení Správce prostředků šablon](./media/deployment-script-template/resource-manager-template-deployment-script-resources.png)

Uživatelský skript, výsledky spuštění a soubor stdout jsou uloženy ve sdílených složkách účtu úložiště. Existuje složka s názvem **azscripts**. Ve složce jsou k dispozici dvě další složky pro vstup a výstupní soubory: **azscriptinput** a **azscriptoutput**.

Výstupní složka obsahuje **ExecutionResult. JSON** a výstupní soubor skriptu. Zobrazí se chybová zpráva spuštění skriptu v **ExecutionResult. JSON**. Výstupní soubor je vytvořen pouze v případě, že byl skript úspěšně proveden. Vstupní složka obsahuje systémový soubor skriptu PowerShellu a soubory skriptu nasazení uživatele. Můžete nahradit soubor skriptu nasazení uživatele revidovanou a znovu spustit skript nasazení z instance kontejneru Azure.

Informace o nasazení prostředku skriptu nasazení můžete získat na úrovni skupiny prostředků a na úrovni předplatného pomocí REST API:

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

Následující REST API vrátí protokol:

```rest
/subscriptions/<SubscriptionID>/resourcegroups/<ResourceGroupName>/providers/microsoft.resources/deploymentScripts/<DeploymentScriptResourceName>/logs?api-version=2019-10-01-preview
```

Funguje pouze před odstraněním prostředků skriptu nasazení.

Pokud chcete zobrazit prostředek deploymentScripts na portálu, vyberte **Zobrazit skryté typy**:

![Správce prostředků skript nasazení šablony, zobrazit skryté typy, portál](./media/deployment-script-template/resource-manager-deployment-script-portal-show-hidden-types.png)

## <a name="use-an-existing-storage-account"></a>Použít existující účet úložiště

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

    Tyto kombinace podporují sdílení souborů.  Další informace najdete v tématu [Vytvoření sdílené složky Azure](../../storage/files/storage-how-to-create-file-share.md) a [typů účtů úložiště](../../storage/common/storage-account-overview.md).
- Pravidla brány firewall účtu úložiště ještě nejsou podporovaná. Další informace najdete v tématu [Konfigurace virtuálních sítí a bran firewall Azure Storage](../../storage/common/storage-network-security.md).
- Spravovaná identita přiřazená uživatelem skriptu pro nasazení musí mít oprávnění ke správě účtu úložiště, který zahrnuje čtení, vytváření a odstraňování sdílených složek.

Chcete-li zadat existující účet úložiště, přidejte následující JSON do elementu Property elementu `Microsoft.Resources/deploymentScripts` :

```json
"storageAccountSettings": {
  "storageAccountName": "myStorageAccount",
  "storageAccountKey": "myKey"
},
```

- **storageAccountName**: zadejte název účtu úložiště.
- **storageAccountKey "**: zadejte jeden z klíčů účtu úložiště. [`listKeys()`](./template-functions-resource.md#listkeys)K načtení klíče lze použít funkci. Příklad:

    ```json
    "storageAccountSettings": {
        "storageAccountName": "[variables('storageAccountName')]",
        "storageAccountKey": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('storageAccountName')), '2019-06-01').keys[0].value]"
    }
    ```

Kompletní ukázku definice najdete v tématu [ukázkové šablony](#sample-templates) `Microsoft.Resources/deploymentScripts` .

Když se použije existující účet úložiště, služba skriptu vytvoří sdílený soubor s jedinečným názvem. Informace o tom, jak služba skriptu vyčistí sdílenou složku, najdete v tématu [vyčištění prostředků skriptu nasazení](#clean-up-deployment-script-resources) .

## <a name="clean-up-deployment-script-resources"></a>Vyčistit prostředky skriptu nasazení

K provádění skriptů a odstraňování potíží je potřeba účet úložiště a instance kontejneru. Máte možnost zadat existující účet úložiště, jinak se služba skriptu automaticky vytvoří účet úložiště spolu s instancí kontejneru. Tyto dva automaticky vytvořené prostředky odstraní služba skriptu, když se spuštění skriptu nasazení dostane do stavu terminálu. Budou se vám účtovat prostředky, dokud se prostředky neodstraní. Informace o cenách najdete v článku [Container Instances](https://azure.microsoft.com/pricing/details/container-instances/) ceny a ceny [Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Životní cyklus těchto prostředků je řízen následujícími vlastnostmi v šabloně:

- **cleanupPreference**: vyčistěte předvolby, když se spuštění skriptu dostane do stavu terminálu. Podporované hodnoty jsou:

  - **Always**: odstranit automaticky vytvořené prostředky, jakmile se spuštění skriptu dostane do stavu terminálu. Pokud se použije existující účet úložiště, služba skriptu odstraní sdílenou složku vytvořenou v účtu úložiště. Vzhledem k tomu, že prostředek deploymentScripts může být stále přítomen po vyčištění prostředků, skriptovací služby uchovávají výsledky spuštění skriptu, například stdout, výstupy, návratové hodnoty atd. před odstraněním prostředků.
  - **Úspěch**: Odstraňte automaticky vytvořené prostředky pouze v případě, že je spuštění skriptu úspěšné. Pokud se použije existující účet úložiště, služba skriptu odstraní sdílenou složku, jenom když je spuštění skriptu úspěšné. K vyhledání informací o ladění máte stále přístup k prostředkům.
  - **Vypršení platnosti**: odstranit automaticky prostředky jenom v případě, že vypršela platnost nastavení **retentionInterval** Pokud se použije existující účet úložiště, služba skriptu odebere sdílenou složku, ale zachová účet úložiště.

- **retentionInterval**: zadejte časový interval, po který se zachová prostředek skriptu, a potom vyprší platnost a odstraní se.

> [!NOTE]
> Pro jiné účely se nedoporučuje používat účet úložiště a instanci kontejneru vygenerované službou skriptu. Tyto dva prostředky mohou být odstraněny v závislosti na životním cyklu skriptu.

## <a name="run-script-more-than-once"></a>Spustit skript více než jednou

Spuštění skriptu nasazení je operace idempotentní. Pokud se nezměnila žádná z vlastností prostředku deploymentScripts (včetně vloženého skriptu), skript se nespustí při opětovném nasazení šablony. Služba skriptu nasazení porovnává názvy prostředků v šabloně se stávajícími prostředky ve stejné skupině prostředků. Existují dvě možnosti, pokud chcete spustit stejný skript nasazení několikrát:

- Změňte název prostředku deploymentScripts. Použijte například funkci šablony [UtcNow](./template-functions-date.md#utcnow) jako název prostředku nebo jako součást názvu prostředku. Změna názvu prostředku vytvoří nový prostředek deploymentScripts. Je vhodné uchovávat historii provádění skriptu.

    > [!NOTE]
    > Funkci utcNow lze použít pouze ve výchozí hodnotě pro parametr.

- Zadejte jinou hodnotu ve `forceUpdateTag` vlastnosti Template.  Jako hodnotu použijte například utcNow.

> [!NOTE]
> Zapište skripty nasazení, které jsou idempotentní. Tím se zajistí, že pokud se znovu spustí, nezpůsobí se změny systému. Například pokud se skript nasazení používá k vytvoření prostředku Azure, ověřte, že prostředek neexistuje, než ho vytvoříte, takže skript bude úspěšný, nebo znovu nevytvářejte prostředek.

## <a name="configure-development-environment"></a>Konfigurace vývojového prostředí

Jako vývojové prostředí skriptu nasazení můžete použít předem nakonfigurovanou image kontejneru Docker. Následující postup ukazuje, jak nakonfigurovat bitovou kopii Docker ve Windows. Pro Linux a Mac můžete najít informace na internetu.

1. Nainstalujte na svém vývojovém počítači [Docker Desktop](https://www.docker.com/products/docker-desktop) .
1. Otevřete Docker Desktop.
1. Vyberte ikonu Docker Desktop z hlavních panelů a pak vyberte **Nastavení**.
1. Vyberte **sdílené jednotky**, vyberte místní jednotku, kterou chcete mít k dispozici pro vaše kontejnery, a pak vyberte **použít** .

    ![Správce prostředkůá jednotka Docker skriptu nasazení šablony](./media/deployment-script-template/resource-manager-deployment-script-docker-setting-drive.png)

1. Na příkazovém řádku zadejte svoje přihlašovací údaje pro Windows.
1. Otevřete okno terminálu, buď příkazový řádek, nebo Windows PowerShell (nepoužívejte PowerShell ISE).
1. Načíst image kontejneru skriptu nasazení do místního počítače:

    ```command
    docker pull mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    V příkladu se používá verze PowerShellu 2.7.0.

    Načtení image CLI z Microsoft Container Registry (MCR):

    ```command
    docker pull mcr.microsoft.com/azure-cli:2.0.80
    ```

    V tomto příkladu je použita verze rozhraní příkazového řádku 2.0.80. Skript nasazení používá výchozí image kontejnerů rozhraní příkazového řádku, které najdete [tady](https://hub.docker.com/_/microsoft-azure-cli).

1. Spusťte image Docker lokálně.

    ```command
    docker run -v <host drive letter>:/<host directory name>:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    Nahraďte ** &lt; písmeno hostitelského ovladače>** a ** &lt; název adresáře hostitele>** existující složkou na sdílené jednotce.  Namapuje složku do složky **/data** v kontejneru. Příklady pro mapování D:\docker:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azuredeploymentscripts-powershell:az2.7
    ```

    **–** znamená, že udržuje image kontejneru aktivní.

    Příklad rozhraní příkazového řádku:

    ```command
    docker run -v d:/docker:/data -it mcr.microsoft.com/azure-cli:2.0.80
    ```

1. Po zobrazení výzvy vyberte možnost **sdílet** .
1. Následující snímek obrazovky ukazuje, jak spustit skript prostředí PowerShell s ohledem, že máte soubor HelloWorld. ps1 ve složce d:\docker.

    ![Správce prostředků skript nasazení skriptu Docker cmd](./media/deployment-script-template/resource-manager-deployment-script-docker-cmd.png)

Po úspěšném otestování skriptu ho můžete použít jako skript nasazení.

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak používat skripty pro nasazení. Návod k procházení skriptu nasazení:

> [!div class="nextstepaction"]
> [Kurz: použití skriptů nasazení v šablonách Azure Resource Manager](./template-tutorial-deployment-script.md)
