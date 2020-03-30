---
title: Jak vytvořit zásady konfigurace hosta pro Linux
description: Přečtěte si, jak vytvořit zásady Azure Zásady konfigurace pro Linux.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: f93aafc8f2c016218b1b7fea82558ea6ba4b4ff8
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365397"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Jak vytvořit zásady konfigurace hosta pro Linux

Před vytvořením vlastních zásad si přečtěte informace o přehledu v [části Konfigurace hosta zásad Azure](../concepts/guest-configuration.md).
 
Další informace o vytváření zásad konfigurace hosta pro systém Windows naleznete na stránce [Jak vytvořit zásady konfigurace hosta pro systém Windows.](./guest-configuration-create.md)

Při auditování Linuxu používá konfigurace hosta [Chef InSpec](https://www.inspec.io/). Profil InSpec definuje podmínku, ve které by měl být počítač. Pokud se vyhodnocení konfigurace nezdaří, je spuštěn audit efektu **zásadIfNotExists** a počítač je považován za **nevyhovující**.

[Konfigurace hosta zásad Azure](../concepts/guest-configuration.md) slouží pouze k auditování nastavení uvnitř počítačů. Sanace nastavení uvnitř strojů ještě není k dispozici.

Pomocí následujících akcí vytvořte vlastní konfiguraci pro ověření stavu počítače Azure nebo než Azure.

> [!IMPORTANT]
> Vlastní zásady s konfigurací hosta je funkce náhledu.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Vytvoření artefaktu konfigurace hosta, automatizované testování artefaktu, vytvoření definice zásad a publikování zásad je zcela možné pomocí modulu Konfigurace hosta v prostředí PowerShell. Modul se dá nainstalovat do počítače se systémem Windows, macOS nebo Linux s PowerShellem 6.2 nebo novějším, který běží místně, nebo s [Azure Cloud Shell](https://shell.azure.com)nebo s image Azure [PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilace konfigurací není v Linuxu podporována.

### <a name="base-requirements"></a>Základní požadavky

Operační systémy, ve kterých lze modul nainstalovat:

- Linux
- macOS
- Windows

Modul prostředku konfigurace hosta vyžaduje následující software:

- Prostředí PowerShell 6.2 nebo novější. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).
  - Jsou vyžadovány pouze moduly AZ "Az.Accounts" a "Az.Resources".

### <a name="install-the-module"></a>Instalace modulu

Instalace modulu **GuestConfiguration** v PowerShellu:

1. Z výzvy prostředí PowerShell spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Ověřte, zda byl modul importován:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakty konfigurace hosta a zásady pro Linux

Dokonce i v prostředích Linuxu konfigurace hosta používá konfiguraci požadovaného stavu jako abstrakce jazyka. Implementace je založena v nativním kódu (C++), takže nevyžaduje načítání PowerShellu. Však vyžaduje konfiguraci MOF popisující podrobnosti o prostředí. DSC funguje jako obálka pro InSpec standardizovat, jak je spuštěn, jak jsou k dispozici parametry a jak je vrácen výstup do služby. Při práci s vlastním obsahem InSpec je vyžadována malá znalost DSC.

#### <a name="configuration-requirements"></a>Požadavky na konfiguraci

Název vlastní konfigurace musí být konzistentní všude. Název souboru ZIP pro balíček obsahu, název konfigurace v souboru MOF a název přiřazení hosta v šabloně Správce prostředků musí být stejný.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfigurace vlastní konfigurace hosta v Linuxu

Konfigurace hosta na `ChefInSpecResource` Linuxu používá prostředek k poskytnutí názvu [profilu InSpec](https://www.inspec.io/docs/reference/profiles/). **Název** je jedinou povinnou vlastností prostředku. Vytvořte soubor YaML a soubor skriptu Ruby, jak je podrobně popsáno níže.

Nejprve vytvořte soubor YaML používaný inspec. Soubor poskytuje základní informace o prostředí. Příklad je uveden níže:

```YaML
name: linux-path
title: Linux path
maintainer: Test
summary: Test profile
license: MIT
version: 1.0.0
supports:
    - os-family: unix
```

Uložte tento soubor `linux-path` do složky pojmenované v adresáři projektu.

Dále vytvořte soubor Ruby s abstrakcí jazyka InSpec, která slouží k auditu počítače.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Uložte tento soubor do `controls` nové `linux-path` složky pojmenované uvnitř adresáře.

Nakonec vytvořte konfiguraci, importujte modul prostředku `ChefInSpecResource` **GuestConfiguration** a pomocí prostředku nastavte název profilu InSpec.

```powershell
# Define the configuration and import GuestConfiguration
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
        }
    }
}

# Compile the configuration to create the MOF files
AuditFilePathExists -out ./Config
```

Příkaz `Node AuditFilePathExists` není technicky vyžadován, ale vytvoří soubor `AuditFilePathExists.mof` s názvem `localhost.mof`spíše než výchozí . S .mof název souboru postupujte podle konfigurace usnadňuje uspořádání mnoho souborů při provozu ve velkém měřítku.

Nyní byste měli mít strukturu projektu, jak je uvedeno níže:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        linux-path.yml
        / controls
            linux-path.rb 
```

Podpůrné soubory musí být zabaleny společně. Dokončený balíček se používá konfigurace hosta k vytvoření definice zásad Azure.

Rutina `New-GuestConfigurationPackage` vytvoří balíček. Parametry rutiny `New-GuestConfigurationPackage` při vytváření obsahu Linuxu:

- **Název**: Název balíčku Konfigurace hosta.
- **Konfigurace**: Zkompilovaný konfigurační dokument úplnou cestu.
- **Cesta**: Cesta k výstupní složce. Tento parametr je volitelný. Pokud není zadán, balíček je vytvořen v aktuálním adresáři.
- **ChefProfilePath**: Úplná cesta k profilu InSpec. Tento parametr je podporován pouze při vytváření obsahu pro audit Linuxu.

Spusťte následující příkaz a vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof'
  -ChefProfilePath './'
```

Po vytvoření balíčku konfigurace, ale před publikováním do Azure, můžete otestovat balíček z pracovní stanice nebo CI/CD prostředí. Rutina GuestConfiguration `Test-GuestConfigurationPackage` obsahuje ve vývojovém prostředí stejného agenta, jak se používá v počítačích Azure. Pomocí tohoto řešení můžete provést testování integrace místně před uvolněním do fakturovaných cloudových prostředí.

Vzhledem k tomu, že agent ve skutečnosti vyhodnocuje místní prostředí, ve většině případů je třeba spustit rutinu Test- na stejné platformě operačního systému, jak plánujete auditovat.

Parametry rutiny: `Test-GuestConfigurationPackage`

- **Název**: Název zásadkonfigurace hosta.
- **Parametr**: Parametry zásad uvedené ve formátu hashtable.
- **Cesta**: Úplná cesta balíčku Konfigurace hosta.

Chcete-li otestovat balíček vytvořený předchozím krokem, spusťte následující příkaz:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists.zip
```

Rutina také podporuje vstup z kanálu Prostředí PowerShell. Potrubí výstup `New-GuestConfigurationPackage` rutiny na `Test-GuestConfigurationPackage` rutinu.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefProfilePath './' | Test-GuestConfigurationPackage
```

Dalším krokem je publikování souboru do úložiště objektů blob. Níže uvedený skript obsahuje funkci, kterou můžete použít k automatizaci této úlohy. Příkazy použité ve `publish` funkci `Az.Storage` vyžadují modul.

```azurepowershell-interactive
function publish {
    param(
    [Parameter(Mandatory=$true)]
    $resourceGroup,
    [Parameter(Mandatory=$true)]
    $storageAccountName,
    [Parameter(Mandatory=$true)]
    $storageContainerName,
    [Parameter(Mandatory=$true)]
    $filePath,
    [Parameter(Mandatory=$true)]
    $blobName
    )

    # Get Storage Context
    $Context = Get-AzStorageAccount -ResourceGroupName $resourceGroup `
        -Name $storageAccountName | `
        ForEach-Object { $_.Context }

    # Upload file
    $Blob = Set-AzStorageBlobContent -Context $Context `
        -Container $storageContainerName `
        -File $filePath `
        -Blob $blobName `
        -Force

    # Get url with SAS token
    $StartTime = (Get-Date)
    $ExpiryTime = $StartTime.AddYears('3')  # THREE YEAR EXPIRATION
    $SAS = New-AzStorageBlobSASToken -Context $Context `
        -Container $storageContainerName `
        -Blob $blobName `
        -StartTime $StartTime `
        -ExpiryTime $ExpiryTime `
        -Permission rl `
        -FullUri

    # Output
    return $SAS
}

# replace the $storageAccountName value below, it must be globally unique
$resourceGroup        = 'policyfiles'
$storageAccountName   = 'youraccountname'
$storageContainerName = 'artifacts'

$uri = publish `
  -resourceGroup $resourceGroup `
  -storageAccountName $storageAccountName `
  -storageContainerName $storageContainerName `
  -filePath ./AuditFilePathExists.zip `
  -blobName 'AuditFilePathExists'
```
Po vytvoření a nahrání balíčku vlastních zásad Konfigurace hosta vytvořte definici zásad Konfigurace hosta. Rutina `New-GuestConfigurationPolicy` přebírá vlastní balíček zásad a vytvoří definici zásad.

Parametry rutiny: `New-GuestConfigurationPolicy`

- **ContentUri**: Veřejné http(s) uri balíčku obsahu konfigurace hosta.
- **DisplayName**: Zobrazovaný název zásady.
- **Popis**: Popis zásad.
- **Parametr**: Parametry zásad uvedené ve formátu hashtable.
- **Verze**: Verze zásad.
- **Cesta**: Cílová cesta, kde jsou vytvořeny definice zásad.
- **Platforma:** Cílová platforma (Windows/Linux) pro zásady konfigurace hosta a balíček obsahu.

Následující příklad vytvoří definice zásad v zadané cestě z balíčku vlastních zásad:

```azurepowershell-interactive
New-GuestConfigurationPolicy `
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Následující soubory jsou `New-GuestConfigurationPolicy`vytvořeny :

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Iniciativa.json**

Výstup rutiny vrátí objekt obsahující zobrazovaný název iniciativy a cestu k souborům zásad.

Nakonec publikujte definice zásad `Publish-GuestConfigurationPolicy` pomocí rutiny.
Rutina má pouze **Path** parametr, který odkazuje na umístění souborů `New-GuestConfigurationPolicy`JSON vytvořených .

Chcete-li spustit příkaz Publikovat, potřebujete přístup k vytváření zásad v Azure. Konkrétní požadavky na autorizaci jsou popsány na stránce [Přehled zásad Azure.](../overview.md) Nejlepší předdefinovanou rolí je **přispěvatel zásad zdrojů**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path '.\policyDefinitions'
```

 Rutina `Publish-GuestConfigurationPolicy` přijímá cestu z kanálu Prostředí PowerShell. Tato funkce znamená, že můžete vytvářet soubory zásad a publikovat je v jedné sadě příkazů s potrubím.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

Se zásadou vytvořenou v Azure je posledním krokem přiřazení iniciativy. Přečtěte si, jak přiřadit iniciativu pomocí [portálu](../assign-policy-portal.md), [Azure CLI](../assign-policy-azurecli.md)a [Azure PowerShellu](../assign-policy-powershell.md).

> [!IMPORTANT]
> Zásady konfigurace hosta musí být **vždy** přiřazeny pomocí iniciativy, která kombinuje _zásady AuditIfNotExists_ a _DeployIfNotExists._ Pokud je přiřazena pouze _zásada AuditIfNotExists,_ požadavky nejsou nasazeny a zásady vždy ukazují, že servery "0" jsou kompatibilní.

Přiřazení definice zásady s efektem _DeployIfNotExists_ vyžaduje další úroveň přístupu. Chcete-li udělit nejnižší oprávnění, můžete vytvořit vlastní definici role, která rozšiřuje **přispěvatele zásad zdrojů**. Následující příklad vytvoří roli s názvem **Přispěvatel zásad prostředků DINE** s dalším oprávněním _Microsoft.Authorization/roleAssignments/write_.

```azurepowershell-interactive
$subscriptionid = '00000000-0000-0000-0000-000000000000'
$role = Get-AzRoleDefinition "Resource Policy Contributor"
$role.Id = $null
$role.Name = "Resource Policy Contributor DINE"
$role.Description = "Can assign Policies that require remediation."
$role.Actions.Clear()
$role.Actions.Add("Microsoft.Authorization/roleAssignments/write")
$role.AssignableScopes.Clear()
$role.AssignableScopes.Add("/subscriptions/$subscriptionid")
New-AzRoleDefinition -Role $role
```

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Použití parametrů ve vlastních zásadách konfigurace hosta

Konfigurace hosta podporuje přepsání vlastností konfigurace za běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku nemusí být považovány za statické. Hodnoty přepsání jsou k dispozici prostřednictvím zásad Azure a nemají vliv na to, jak jsou konfigurace vytvářeny nebo kompilovány.

S InSpec, parametry jsou obvykle zpracovány jako vstup za běhu nebo jako kód pomocí atributů. Konfigurace hosta obfuscates tento proces, takže vstup může být poskytnuta při přiřazení zásady. Soubor atributů je automaticky vytvořen v rámci počítače. V projektu není nutné vytvářet a přidávat soubor. Existují dva kroky k přidání parametrů do projektu auditu Linuxu.

Definujte vstup v souboru Ruby, kde skript, co auditovat na počítači. Příklad je uveden níže.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` obsahují parametr s názvem **Parametry**. Tento parametr přebírá hodnotitelnou hodnotu, která obsahuje všechny podrobnosti o jednotlivých parametrech, a automaticky vytvoří všechny požadované části souborů použitých k vytvoření každé definice zásad Azure.

Následující příklad vytvoří definici zásad pro auditování cesty k souboru, kde uživatel poskytuje cestu v době přiřazení zásad.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'FilePath'                             # Policy parameter name (mandatory)
        DisplayName = 'File path.'                    # Policy parameter display name (mandatory)
        Description = "File path to be audited."      # Policy parameter description (optional)
        ResourceType = "ChefInSpecResource"           # Configuration resource type (mandatory)
        ResourceId = 'Audit Linux path exists'        # Configuration resource property name (mandatory)
        ResourcePropertyName = "AttributesYmlContent" # Configuration resource property name (mandatory)
        DefaultValue = '/tmp'                         # Policy parameter default value (optional)
    }
)

# The hashtable also supports a property named 'AllowedValues' with an array of strings to limit input to a list

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

Pro zásady Linuxu zahrňte vlastnost **AttributesYmlContent** do konfigurace a podle potřeby přepište hodnoty. Agent konfigurace hosta automaticky vytvoří soubor YAML používaný inspec k ukládání atributů. Viz následující příklad.

```powershell
Configuration AuditFilePathExists
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    Node AuditFilePathExists
    {
        ChefInSpecResource 'Audit Linux path exists'
        {
            Name = 'linux-path'
            AttributesYmlContent = "path: /tmp"
        }
    }
}
```

## <a name="policy-lifecycle"></a>Životní cyklus zásad

Chcete-li uvolnit aktualizaci definice zásad, existují dvě pole, která vyžadují pozornost.

- **Verze**: Při `New-GuestConfigurationPolicy` spuštění rutiny je nutné zadat číslo verze větší, než je aktuálně publikováno. Vlastnost aktualizuje verzi přiřazení konfigurace hosta, takže agent rozpozná aktualizovaný balíček.
- **contentHash**: Tato vlastnost je `New-GuestConfigurationPolicy` automaticky aktualizována rutinou. Je to hodnota hash balíčku vytvořeného společností `New-GuestConfigurationPackage`. Vlastnost musí být správná pro soubor, který `.zip` publikujete. Pokud je aktualizována pouze **contentUri** vlastnost, rozšíření nepřijme balíček obsahu.

Nejjednodušší způsob, jak uvolnit aktualizovaný balíček, je zopakovat proces popsaný v tomto článku a poskytnout aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti byly správně aktualizovány.

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: Podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta používají sha256 hash k ověření balíčku zásad se nezměnil.
Volitelně mohou zákazníci také použít certifikát k podepsání balíčků a vynutit rozšíření Konfigurace hosta povolit pouze podepsaný obsah.

Chcete-li povolit tento scénář, existují dva kroky, které je třeba provést. Spusťte rutinu k podepsání balíčku obsahu a připojit značku k počítačům, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci Ověření `Protect-GuestConfigurationPackage` podpisu, spusťte rutinu a podepište balíček před jeho publikováním. Tato rutina vyžaduje certifikát podepisování kódu.

Parametry rutiny: `Protect-GuestConfigurationPackage`

- **Cesta**: Úplná cesta balíčku Konfigurace hosta.
- **PublicGpgKeyPath**: Veřejná cesta klíče GPG. Tento parametr je podporován pouze při podepisování obsahu pro Linux.

Dobrý odkaz pro vytváření GPG klíčů pro použití s linuxovými počítači je poskytován v článku na [GitHubu, Generování nového klíče GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

GuestConfiguration agent očekává, že veřejný klíč certifikátu bude přítomen v cestě `/usr/local/share/ca-certificates/extra` na počítačích Linux. Chcete-li uzel ověřit podepsaný obsah, nainstalujte před použitím vlastních zásad do počítače veřejný klíč certifikátu. Tento proces lze provést pomocí libovolné techniky uvnitř virtuálního počítače nebo pomocí zásad Azure. Příklad šablony je [k dispozici zde](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zásady přístupu trezoru klíčů musí umožnit poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobné kroky najdete v tématu [Nastavení trezoru klíčů pro virtuální počítače ve Správci prostředků Azure](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Po publikování obsahu přidejte značku s `GuestConfigPolicyCertificateValidation` názvem `enabled` a hodnotou ke všem virtuálním počítačům, kde by mělo být vyžadováno podepisování kódu. Podívejte se na [ukázky značek,](../samples/built-in-policies.md#tags) jak se značky můžou doručovat ve velkém měřítku pomocí Azure Policy. Jakmile je tato značka na místě, definice `New-GuestConfigurationPolicy` zásad generované pomocí rutiny umožňuje požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Poradce při potížích s přiřazeními zásad konfigurace hosta (preview)

Nástroj je k dispozici ve verzi Preview, který vám pomůže při řešení potíží s přiřazeními konfigurace hosta zásad Azure. Nástroj je ve verzi Preview a byl publikován v galerii prostředí PowerShell jako poradce při potížích s [konfigurací hosta](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Další informace o rutinách v tomto nástroji získáte pomocí příkazu Get-Help v prostředí PowerShell k zobrazení předdefinovaných pokynů. Vzhledem k tomu, že nástroj získává časté aktualizace, je to nejlepší způsob, jak získat nejnovější informace.

## <a name="next-steps"></a>Další kroky

- Další informace o auditování virtuálních počítačů pomocí [konfigurace hosta](../concepts/guest-configuration.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
