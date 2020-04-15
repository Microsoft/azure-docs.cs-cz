---
title: Jak vytvořit zásady konfigurace hosta pro Windows
description: Přečtěte si, jak vytvořit zásady Azure Zásady konfigurace pro Windows.
ms.date: 03/20/2020
ms.topic: how-to
ms.openlocfilehash: deb51cf502d26dc994bf74ef3cb0c728f624afde
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81313982"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Jak vytvořit zásady konfigurace hosta pro Windows

Před vytvořením vlastních zásad je vhodné přečíst si informace o koncepčním přehledu na stránce [Konfigurace hosta zásad Azure](../concepts/guest-configuration.md).
 
Další informace o vytváření zásad konfigurace hosta pro Linux najdete na stránce [Jak vytvořit zásady konfigurace hosta pro Linux.](./guest-configuration-create-linux.md)

Při auditování systému Windows používá konfigurace hosta do [konfiguračního](/powershell/scripting/dsc/overview/overview) souboru modul prostředku Konfigurace požadovaného stavu (DSC). Konfigurace DSC definuje podmínku, ve které by měl být počítač.
Pokud se vyhodnocení konfigurace nezdaří, je spuštěn audit efektu **zásadIfNotExists** a počítač je považován za **nevyhovující**.

[Konfigurace hosta zásad Azure](../concepts/guest-configuration.md) slouží pouze k auditování nastavení uvnitř počítačů. Sanace nastavení uvnitř strojů ještě není k dispozici.

Pomocí následujících akcí vytvořte vlastní konfiguraci pro ověření stavu počítače Azure nebo než Azure.

> [!IMPORTANT]
> Vlastní zásady s konfigurací hosta je funkce náhledu.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Vytvoření artefaktu konfigurace hosta, automatizované testování artefaktu, vytvoření definice zásad a publikování zásad je zcela možné pomocí modulu Konfigurace hosta v prostředí PowerShell. Modul se dá nainstalovat do počítače se systémem Windows, macOS nebo Linux s PowerShellem 6.2 nebo novějším, který běží místně, nebo s [Azure Cloud Shell](https://shell.azure.com)nebo s image Azure [PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilace konfigurací ještě není v Linuxu podporována.

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

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefakty konfigurace hosta a zásady pro Windows

Konfigurace hosta používá konfiguraci požadovaného stavu prostředí PowerShell jako abstrakce jazyka pro zápis co auditovat v systému Windows. Agent načte samostatnou instanci prostředí PowerShell 6.2, takže není v konfliktu s použitím prostředí PowerShell DSC v prostředí Windows PowerShell 5.1 a není nutné předinstalovat prostředí PowerShell 6.2 nebo novější.

Přehled konceptů a terminologie DSC najdete v [tématu Přehled DSC prostředí PowerShell](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Jak se moduly konfigurace hosta liší od modulů DSC prostředí Windows PowerShell

Když konfigurace hosta audituje počítač:

1. Agent nejprve `Test-TargetResource` spustí k určení, pokud je konfigurace ve správném stavu.
1. Logická hodnota vrácená funkcí určuje, jestli by měl být stav Správce prostředků Azure pro přiřazení hosta kompatibilní nebo nekompatibilní.
1. Zprostředkovatel spustí `Get-TargetResource` vrátit aktuální stav každé nastavení, takže podrobnosti jsou k dispozici jak o tom, proč počítač není kompatibilní a potvrdit, že aktuální stav je kompatibilní.

### <a name="get-targetresource-requirements"></a>Požadavky na zdroj Get-Target

Funkce `Get-TargetResource` má speciální požadavky na konfiguraci hosta, které nebyly potřebné pro konfiguraci požadovaného stavu systému Windows.

- Vrátit el-it musí obsahovat vlastnost s názvem **Důvody**.
- Vlastnost Důvody musí být pole.
- Každá položka v poli by měla být hashtable s klíči s názvem **Kód** a **fráze**.

Vlastnost Důvody používá služba standardizovat, jak jsou informace prezentovány, když je počítač mimo dodržování předpisů. Každou položku v důvodech si můžete vypočitat jako "důvod", že prostředek není kompatibilní. Vlastnost je pole, protože prostředek může být mimo dodržování předpisů z více důvodů.

Vlastnosti **Kód** a **Fráze** jsou očekávány službou. Při vytváření vlastního prostředku nastavte text (obvykle stdout), který chcete zobrazit jako důvod, proč prostředek není kompatibilní jako hodnota **phrase**. **Kód** má specifické požadavky na formátování, takže vykazování může jasně zobrazit informace o zdroji použitém k provedení auditu. Toto řešení činí konfiguraci hosta rozšiřitelnou. Libovolný příkaz může být spuštěn tak dlouho, dokud výstup může být vrácen jako hodnota řetězce pro **Phrase** vlastnost.

- **Kód** (řetězec): Název prostředku, opakované a pak krátký název bez mezery jako identifikátor z důvodu. Tyto tři hodnoty by měly být vymezeny dvojtečkou bez mezer.
  - Příkladem by bylo`registry:registry:keynotpresent`
- **Frázi** (řetězec): Text čitelný pro člověka, který vysvětluje, proč nastavení není kompatibilní.
  - Příkladem by bylo`The registry key $key is not present on the machine.`

```powershell
$reasons = @()
$reasons += @{
  Code = 'Name:Name:ReasonIdentifer'
  Phrase = 'Explain why the setting is not compliant'
}
return @{
    reasons = $reasons
}
```

Vlastnost Důvody musí být také přidándo mof schématu pro prostředek jako vložené třídy.

```mof
[ClassVersion("1.0.0.0")] 
class Reason
{
    [Read] String Phrase;
    [Read] String Code;
};

[ClassVersion("1.0.0.0"), FriendlyName("ResourceName")]
class ResourceName : OMI_BaseResource
{
    [Key, Description("Example description")] String Example;
    [Read, EmbeddedInstance("Reason")] String Reasons[];
};
```

### <a name="configuration-requirements"></a>Požadavky na konfiguraci

Název vlastní konfigurace musí být konzistentní všude. Název souboru ZIP pro balíček obsahu, název konfigurace v souboru MOF a název přiřazení hosta v šabloně Správce prostředků musí být stejný.

### <a name="scaffolding-a-guest-configuration-project"></a>Vytvoření uživatelského prohlášení projektu Konfigurace hosta

Vývojáři, kteří by chtěli urychlit proces začínáme a pracovat z ukázkového kódu, mohou nainstalovat komunitní projekt s názvem **Guest Configuration Project**. Projekt nainstaluje šablonu pro modul [Plaster](https://github.com/powershell/plaster) PowerShell. Tento nástroj lze použít k vytvoření uživatelského rozhraní projektu včetně pracovní konfigurace a ukázkového prostředku a sady [testů Pester](https://github.com/pester/pester) k ověření projektu. Šablona také obsahuje úlohy běžců pro Visual Studio kód pro automatizaci vytváření a ověřování konfigurace hosta balíček. Další informace naleznete v projektu GitHub [Guest Configuration Project](https://github.com/microsoft/guestconfigurationproject).

Další informace o práci s konfiguracemi obecně naleznete v [tématu Zápis, kompilace a použití konfigurace](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Očekávaný obsah artefaktu konfigurace hosta

Dokončený balíček se používá konfigurace hosta k vytvoření definice zásad Azure. Balíček se skládá z:

- Zkompilovaná konfigurace DSC jako MOF
- Složka Moduly
  - Modul GuestConfiguration
  - Modul DscNativeResources
  - (Windows) Moduly prostředků DSC vyžadované mof

Rutiny prostředí PowerShell pomáhají při vytváření balíčku.
Není vyžadována žádná složka kořenové úrovně nebo složka verze.
Formát balíčku musí být soubor ZIP.

### <a name="storing-guest-configuration-artifacts"></a>Ukládání artefaktů konfigurace hosta

Balíček ZIP musí být uložen v umístění, které je přístupné spravované virtuální počítače.
Mezi příklady patří úložiště GitHub, Azure Repo nebo úložiště Azure. Pokud nechcete balíček zveřejnit, můžete do adresy URL zahrnout [token SAS.](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md)
Můžete také implementovat [koncový bod služby](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pro počítače v privátní síti, i když tato konfigurace platí pouze pro přístup k balíčku a nekomunikuje se službou.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Postupné vytvoření vlastní zásady auditu konfigurace hosta pro systém Windows

Vytvořte konfiguraci DSC pro nastavení auditování. Následující příklad skriptu prostředí PowerShell vytvoří konfiguraci s názvem **AuditBitLocker**, importuje modul prostředků **PsDscResources** a použije `Service` prostředek k auditování spuštěné služby. Konfigurační skript lze spustit z počítače se systémem Windows nebo macOS.

```powershell
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Node AuditBitlocker {
      Service 'Ensure BitLocker service is present and running'
      {
          Name = 'BDESVC'
          Ensure = 'Present'
          State = 'Running'
      }
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker -out ./Config
```

Příkaz `Node AuditBitlocker` není technicky vyžadován, ale vytvoří soubor `AuditBitlocker.mof` s názvem `localhost.mof`spíše než výchozí . S .mof název souboru postupujte podle konfigurace usnadňuje uspořádání mnoho souborů při provozu ve velkém měřítku.

Po kompilaci MOF musí být podpůrné soubory zabaleny společně. Dokončený balíček se používá konfigurace hosta k vytvoření definice zásad Azure.

Rutina `New-GuestConfigurationPackage` vytvoří balíček. Moduly, které jsou potřebné pro konfiguraci, musí být k dispozici v aplikaci `$Env:PSModulePath`. Parametry rutiny při vytváření obsahu systému `New-GuestConfigurationPackage` Windows:

- **Název**: Název balíčku Konfigurace hosta.
- **Konfigurace**: Zkompilovaný konfigurační dokument DSC úplná cesta.
- **Cesta**: Cesta k výstupní složce. Tento parametr je volitelný. Pokud není zadán, balíček je vytvořen v aktuálním adresáři.

Spusťte následující příkaz a vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Po vytvoření balíčku konfigurace, ale před publikováním do Azure, můžete otestovat balíček z pracovní stanice nebo CI/CD prostředí. Rutina GuestConfiguration `Test-GuestConfigurationPackage` obsahuje ve vývojovém prostředí stejného agenta, jak se používá v počítačích Azure. Pomocí tohoto řešení můžete provést testování integrace místně před uvolněním do fakturovaných cloudových prostředí.

Vzhledem k tomu, že agent ve skutečnosti vyhodnocuje místní prostředí, ve většině případů je třeba spustit rutinu Test- na stejné platformě operačního systému, jak plánujete auditovat. Test bude používat pouze moduly, které jsou součástí balíčku obsahu.

Parametry rutiny: `Test-GuestConfigurationPackage`

- **Název**: Název zásadkonfigurace hosta.
- **Parametr**: Parametry zásad uvedené ve formátu hashtable.
- **Cesta**: Úplná cesta balíčku Konfigurace hosta.

Chcete-li otestovat balíček vytvořený předchozím krokem, spusťte následující příkaz:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Rutina také podporuje vstup z kanálu Prostředí PowerShell. Potrubí výstup `New-GuestConfigurationPackage` rutiny na `Test-GuestConfigurationPackage` rutinu.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
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
  -filePath ./AuditBitlocker.zip `
  -blobName 'AuditBitlocker'
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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path './policies' `
    -Platform 'Windows' `
    -Version 1.0.0 `
    -Verbose
```

Následující soubory jsou `New-GuestConfigurationPolicy`vytvořeny :

- **auditIfNotExists.json**
- **deployIfNotExists.json**
- **Iniciativa.json**

Výstup rutiny vrátí objekt obsahující zobrazovaný název iniciativy a cestu k souborům zásad.

Nakonec publikujte definice zásad `Publish-GuestConfigurationPolicy` pomocí rutiny. Rutina má pouze **Path** parametr, který odkazuje na umístění souborů `New-GuestConfigurationPolicy`JSON vytvořených .

Chcete-li spustit příkaz Publikovat, potřebujete přístup k vytváření zásad v Azure. Konkrétní požadavky na autorizaci jsou popsány na stránce [Přehled zásad Azure.](../overview.md) Nejlepší předdefinovanou rolí je **přispěvatel zásad zdrojů**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

Rutina `Publish-GuestConfigurationPolicy` přijímá cestu z kanálu Prostředí PowerShell. Tato funkce znamená, že můžete vytvářet soubory zásad a publikovat je v jedné sadě příkazů s potrubím.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
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

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` obsahují parametr s názvem **Parametry**. Tento parametr přebírá hodnotitelnou definici včetně všech podrobností o každém parametru a vytvoří požadované oddíly každého souboru použitého pro definici zásad Azure.

Následující příklad vytvoří definici zásad pro audit služby, kde uživatel vybere ze seznamu v době přiřazení zásad.

```azurepowershell-interactive
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'windowsService'                                   # DSC configuration resource property name (mandatory)
        ResourcePropertyName = "Name"                                   # DSC configuration resource property name (mandatory)
        DefaultValue = 'winrm'                                          # Policy parameter default value (optional)
        AllowedValues = @('BDESVC','TermService','wuauserv','winrm')    # Policy parameter allowed values (optional)
    }
)

New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Windows Service.' `
    -Description 'Audit if a Windows Service is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Parameters $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="policy-lifecycle"></a>Životní cyklus zásad

Pokud chcete vydat aktualizaci zásady, existují dvě pole, která vyžadují pozornost.

- **Verze**: Při `New-GuestConfigurationPolicy` spuštění rutiny je nutné zadat číslo verze větší, než je aktuálně publikováno. Vlastnost aktualizuje verzi přiřazení konfigurace hosta, takže agent rozpozná aktualizovaný balíček.
- **contentHash**: Tato vlastnost je `New-GuestConfigurationPolicy` automaticky aktualizována rutinou. Je to hodnota hash balíčku vytvořeného společností `New-GuestConfigurationPackage`. Vlastnost musí být správná pro soubor, který `.zip` publikujete. Pokud je aktualizována pouze **contentUri** vlastnost, rozšíření nepřijme balíček obsahu.

Nejjednodušší způsob, jak uvolnit aktualizovaný balíček, je zopakovat proces popsaný v tomto článku a poskytnout aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti byly správně aktualizovány.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Převod obsahu zásad skupiny Windows na konfiguraci hosta zásad Azure

Konfigurace hosta při auditování počítačů se systémem Windows je implementace syntaxe konfigurace požadovaného stavu prostředí PowerShell. Komunita DSC zveřejnila nástroje pro převod exportovaných šablon zásad skupiny do formátu DSC. Pomocí tohoto nástroje společně s rutinami konfigurace hosta popsanými výše můžete převést obsah zásad skupiny Windows a zabalit/publikovat jej pro zásady Azure k auditování. Podrobnosti o použití nástroje naleznete v článku [Úvodní příručka: Převést zásady skupiny na DSC](/powershell/scripting/dsc/quickstarts/gpo-quickstart).
Po převodu obsahu výše uvedené kroky k vytvoření balíčku a publikovat jako Zásady Azure jsou stejné jako pro jakýkoli obsah DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: Podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta používají sha256 hash k ověření balíčku zásad se nezměnil.
Volitelně mohou zákazníci také použít certifikát k podepsání balíčků a vynutit rozšíření Konfigurace hosta povolit pouze podepsaný obsah.

Chcete-li povolit tento scénář, existují dva kroky, které je třeba provést. Spusťte rutinu k podepsání balíčku obsahu a připojit značku k počítačům, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci Ověření `Protect-GuestConfigurationPackage` podpisu, spusťte rutinu a podepište balíček před jeho publikováním. Tato rutina vyžaduje certifikát podepisování kódu.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry rutiny: `Protect-GuestConfigurationPackage`

- **Cesta**: Úplná cesta balíčku Konfigurace hosta.
- **Certifikát**: Podpisový certifikát kódu pro podepsání balíčku. Tento parametr je podporován pouze při podepisování obsahu pro systém Windows.

GuestConfiguration agent očekává, že veřejný klíč certifikátu bude přítomen v "Důvěryhodné `/usr/local/share/ca-certificates/extra` kořenové certifikační autority" na počítačích se systémem Windows a v cestě na počítačích s Linuxem. Chcete-li uzel ověřit podepsaný obsah, nainstalujte před použitím vlastních zásad do počítače veřejný klíč certifikátu. Tento proces lze provést pomocí libovolné techniky uvnitř virtuálního počítače nebo pomocí zásad Azure. Příklad šablony je [k dispozici zde](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows).
Zásady přístupu trezoru klíčů musí umožnit poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobné kroky najdete v tématu [Nastavení trezoru klíčů pro virtuální počítače ve Správci prostředků Azure](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Následuje příklad exportu veřejného klíče z podpisového certifikátu pro import do počítače.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Po publikování obsahu přidejte značku s `GuestConfigPolicyCertificateValidation` názvem `enabled` a hodnotou ke všem virtuálním počítačům, kde by mělo být vyžadováno podepisování kódu. Podívejte se na [ukázky značek,](../samples/built-in-policies.md#tags) jak se značky můžou doručovat ve velkém měřítku pomocí Azure Policy. Jakmile je tato značka na místě, definice `New-GuestConfigurationPolicy` zásad generované pomocí rutiny umožňuje požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="troubleshooting-guest-configuration-policy-assignments-preview"></a>Poradce při potížích s přiřazeními zásad konfigurace hosta (preview)

Nástroj je k dispozici ve verzi Preview, který vám pomůže při řešení potíží s přiřazeními konfigurace hosta zásad Azure. Nástroj je ve verzi Preview a byl publikován v galerii prostředí PowerShell jako poradce při potížích s [konfigurací hosta](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Další informace o rutinách v tomto nástroji získáte pomocí příkazu Get-Help v prostředí PowerShell k zobrazení předdefinovaných pokynů. Vzhledem k tomu, že nástroj získává časté aktualizace, je to nejlepší způsob, jak získat nejnovější informace.

## <a name="next-steps"></a>Další kroky

- Další informace o auditování virtuálních počítačů pomocí [konfigurace hosta](../concepts/guest-configuration.md).
- Pochopit, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](get-compliance-data.md).
