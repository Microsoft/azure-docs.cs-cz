---
title: Postup vytváření zásad konfigurace hosta pro Windows
description: Naučte se vytvářet Azure Policy zásady konfigurace hostů pro Windows.
ms.date: 08/17/2020
ms.topic: how-to
ms.openlocfilehash: ae9af51ad3b2eb237f8655c996a1345140a8a635
ms.sourcegitcommit: dd24c3f35e286c5b7f6c3467a256ff85343826ad
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2021
ms.locfileid: "99070640"
---
# <a name="how-to-create-guest-configuration-policies-for-windows"></a>Postup vytváření zásad konfigurace hosta pro Windows

Než začnete vytvářet vlastní definice zásad, je vhodné si přečíst informace o koncepčním přehledu na stránce [Azure Policy konfiguraci hostů](../concepts/guest-configuration.md).
 
Další informace o vytváření zásad konfigurace hostů pro Linux najdete na stránce [Postup vytvoření zásad konfigurace hostů pro Linux](./guest-configuration-create-linux.md) .

Při auditování Windows konfigurace hosta k vytvoření konfiguračního souboru využívá modul prostředků DSC ([Desired State Configuration](/powershell/scripting/dsc/overview/overview)). Konfigurace DSC definuje stav, ve kterém by počítač měl být. Pokud se konfigurace nezdařila, je aktivován efekt zásad **auditIfNotExists** a počítač se považuje za **nevyhovující**.

[Konfiguraci hosta Azure Policy](../concepts/guest-configuration.md) můžete použít jenom k auditování nastavení v počítačích. Náprava nastavení v počítačích ještě není k dispozici.

Pomocí následujících akcí můžete vytvořit vlastní konfiguraci pro ověření stavu počítače s Azure nebo mimo Azure.

> [!IMPORTANT]
> Vlastní definice zásad s konfigurací hosta v prostředích Azure Government a Azure Čína jsou funkcí ve verzi Preview.
>
> Rozšíření konfigurace hosta se vyžaduje k provádění auditů na virtuálních počítačích Azure.
> Pokud chcete nasadit rozšíření v celém počítači s Windows, přiřaďte následující definice zásad: `Deploy prerequisites to enable Guest Configuration Policy on Windows VMs`
> 
> Nepoužívejte tajné klíče ani důvěrné informace v balíčcích vlastního obsahu.

## <a name="install-the-powershell-module"></a>Instalace modulu PowerShellu

Modul konfigurace hosta automatizuje proces vytváření vlastního obsahu, včetně:

- Vytváření artefaktu obsahu konfigurace hosta (. zip)
- Automatizované testování artefaktu
- Vytvoření definice zásady
- Publikování zásady

Modul lze nainstalovat do počítače se systémem Windows, macOS nebo Linux s prostředím PowerShell 6,2 nebo novějším spuštěným místně nebo pomocí [Azure Cloud Shell](https://shell.azure.com)nebo s [imagí Azure PowerShell Core Docker](https://hub.docker.com/r/azuresdk/azure-powershell-core).

> [!NOTE]
> Kompilace konfigurací zatím není v systému Linux podporována.

### <a name="base-requirements"></a>Základní požadavky

Operační systémy, ve kterých se modul dá nainstalovat:

- Linux
- macOS
- Windows

Modul prostředků konfigurace hosta vyžaduje následující software:

- PowerShell 6,2 nebo novější. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).
  - Vyžadují se jenom moduly AZ. Accounts a AZ. Resources.

### <a name="install-the-module"></a>Nainstalovat modul

Postup instalace modulu **GuestConfiguration** do PowerShellu:

1. Z příkazového řádku PowerShellu spusťte následující příkaz:

   ```azurepowershell-interactive
   # Install the Guest Configuration DSC resource module from PowerShell Gallery
   Install-Module -Name GuestConfiguration
   ```

1. Ověřte, že se modul importoval:

   ```azurepowershell-interactive
   # Get a list of commands for the imported GuestConfiguration module
   Get-Command -Module 'GuestConfiguration'
   ```

## <a name="guest-configuration-artifacts-and-policy-for-windows"></a>Artefakty konfigurace hosta a zásady pro Windows

Konfigurace hosta používá konfiguraci požadovaného stavu PowerShellu jako abstrakci jazyka pro psaní, co auditovat v systému Windows. Agent načte samostatnou instanci PowerShellu 6,2, takže nedochází ke konfliktu s využitím prostředí PowerShell DSC v prostředí Windows PowerShell 5,1 a neexistuje žádný požadavek na předběžnou instalaci PowerShellu 6,2 nebo novější.

Přehled konceptů a terminologie DSC najdete v tématu [Přehled prostředí POWERSHELL DSC](/powershell/scripting/dsc/overview/overview).

### <a name="how-guest-configuration-modules-differ-from-windows-powershell-dsc-modules"></a>Způsob, jakým se v modulech konfigurace hosta liší od modulů Windows PowerShell DSC

Když Audituje konfigurace hosta počítač, sekvence událostí se liší od rozhraní Windows PowerShell DSC.

1. Agent se nejdřív spustí `Test-TargetResource` , aby zjistil, jestli je konfigurace ve správném stavu.
1. Logická hodnota vrácená funkcí určuje, zda má být stav Azure Resource Manager pro přiřazení hostů kompatibilní/nekompatibilní.
1. Zprostředkovatel spustí, `Get-TargetResource` aby vrátil aktuální stav každého nastavení, takže podrobnosti jsou k dispozici jak k tomu, proč počítač nedodržuje předpisy, a ověřil, zda je aktuální stav kompatibilní.

Parametry v Azure Policy, které předávají hodnoty přiřazení konfigurace hosta musí být typu _řetězec_ . Není možné předat pole pomocí parametrů, i když prostředek DSC podporuje pole.

### <a name="get-targetresource-requirements"></a>Get-TargetResource požadavky

Funkce `Get-TargetResource` má zvláštní požadavky na konfiguraci hosta, která není potřebná pro konfiguraci požadovaného stavu Windows.

- Vrácená zatřiďovací tabulka musí zahrnovat vlastnost s názvem **důvody**.
- Vlastnost důvody musí být pole.
- Každá položka v poli musí být zatřiďovací tabulka s klíči s názvem **Code** a **frází**.

Vlastnost důvody používá služba ke standardizaci způsobu, jakým jsou informace zobrazeny, když je počítač nekompatibilní. Jednotlivé položky si můžete představit z důvodů, proč prostředek není kompatibilní. Vlastnost je pole, protože prostředek může být nekompatibilní s více než jedním důvodem.

Služba očekává **kód** a **frázi** vlastností. Při vytváření vlastního prostředku nastavte text (obvykle STDOUT), který chcete zobrazit jako důvod, proč prostředek není kompatibilní jako hodnota **fráze**. **Kód** má specifické požadavky na formátování, takže hlášení může jasně zobrazit informace o prostředku, který se používá k provedení auditu. Toto řešení zajišťuje rozšiřitelnou konfiguraci hostů. Libovolný příkaz lze spustit, dokud bude výstup vrácen jako řetězcová hodnota pro vlastnost **fráze** .

- **Code** (String): název prostředku, opakuje a pak krátký název bez mezer jako identifikátor z důvodu. Tyto tři hodnoty by měly být odděleny dvojtečkami bez mezer.
  - Příkladem může být `registry:registry:keynotpresent`
- **Fráze** (String): text čitelný lidmi pro vysvětlení, proč nastavení není vyhovující.
  - Příkladem může být `The registry key $key is not present on the machine.`

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

Vlastnost důvody musí být přidána do souboru MOF schématu pro prostředek jako vložená třída.

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

Pokud má prostředek požadované vlastnosti, musí být také vráceny `Get-TargetResource` paralelně s `reasons` třídou. Pokud `reasons` není zahrnuta, služba obsahuje chování "catch-All", který porovnává vstupní hodnoty s `Get-TargetResource` hodnotami, které vrátí `Get-TargetResource` , a poskytuje podrobné porovnání jako `reasons` .

### <a name="configuration-requirements"></a>Požadavky na konfiguraci

Název vlastní konfigurace musí být konzistentní všude. Název souboru. zip pro balíček obsahu, název konfigurace v souboru MOF a název přiřazení hosta v šabloně Azure Resource Manager (šablona ARM) musí být stejné.

### <a name="policy-requirements"></a>Požadavky na zásady

Oddíl definice zásad `metadata` musí zahrnovat dvě vlastnosti pro službu konfigurace hosta pro automatizaci zřizování a vytváření sestav přiřazení konfigurace hostů. `category`Vlastnost musí být nastavená na "konfigurace hosta" a oddíl s názvem `Guest Configuration` musí obsahovat informace o přiřazení konfigurace hostů. `New-GuestConfigurationPolicy`Rutina tento text automaticky vytvoří.
Podrobné pokyny najdete na této stránce.

Následující příklad ukazuje `metadata` oddíl.

```json
    "metadata": {
      "category": "Guest Configuration",
      "guestConfiguration": {
        "name": "test",
        "version": "1.0.0",
        "contentType": "Custom",
        "contentUri": "CUSTOM-URI-HERE",
        "contentHash": "CUSTOM-HASH-VALUE-HERE",
        "configurationParameter": {}
      }
    },
```

### <a name="scaffolding-a-guest-configuration-project"></a>Generování uživatelského rozhraní projektu konfigurace hosta

Vývojáři, kteří chtějí urychlit proces Začínáme a práci z ukázkového kódu, mohou nainstalovat projekt komunity s názvem **projekt konfigurace hosta**. Projekt nainstaluje šablonu pro modul PowerShellu pro [sádru](https://github.com/powershell/plaster) . Tento nástroj lze použít k vytvoření uživatelského rozhraní projektu, včetně pracovní konfigurace a ukázkového prostředku, a sady testů [platformy pester](https://github.com/pester/pester) pro ověření projektu. Šablona obsahuje také Spouštěče úloh pro Visual Studio Code pro automatizaci vytváření a ověřování konfiguračního balíčku hosta. Další informace najdete v [projektu konfigurace hosta](https://github.com/microsoft/guestconfigurationproject)projektu GitHubu.

Další informace o práci s konfiguracemi obecně naleznete v tématu [Write, Compile a Apply Configuration](/powershell/scripting/dsc/configurations/write-compile-apply-configuration).

### <a name="expected-contents-of-a-guest-configuration-artifact"></a>Očekával se obsah artefaktu konfigurace hosta.

Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic. Balíček se skládá z těchto součástí:

- Kompilovaná konfigurace DSC jako MOF
- Složka modulů
  - Modul GuestConfiguration
  - Modul DscNativeResources
  - Systému Moduly prostředků DSC vyžadované MOF

Rutiny prostředí PowerShell pomáhají při vytváření balíčku.
Není požadována žádná složka kořenové úrovně ani složka verze.
Formát balíčku musí být soubor. zip a při nekomprimaci nemůže překročit celkovou velikost 100 MB.

### <a name="storing-guest-configuration-artifacts"></a>Ukládání artefaktů konfigurace hosta

Balíček. zip musí být uložený v umístění, ke kterému mají přístup spravované virtuální počítače.
Mezi příklady patří úložiště GitHub, úložiště Azure nebo Azure Storage. Pokud nechcete, aby balíček byl veřejný, můžete do adresy URL přidat [token SAS](../../../storage/common/storage-sas-overview.md) . Můžete také implementovat [koncový bod služby](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pro počítače v privátní síti, i když tato konfigurace platí pouze pro přístup k balíčku a nekomunikuje se službou.

## <a name="step-by-step-creating-a-custom-guest-configuration-audit-policy-for-windows"></a>Krok za krokem – vytvoření vlastní zásady auditu konfigurace hosta pro Windows

Vytvořte konfiguraci DSC pro auditování nastavení. Následující příklad skriptu PowerShellu vytvoří konfiguraci s názvem **AuditBitLocker**, importuje modul prostředků **PsDscResources** a použije `Service` prostředek k auditování spuštěné služby. Konfigurační skript se dá spustit z počítače s Windows nebo macOS.

```powershell
# Add PSDscResources module to environment
Install-Module 'PSDscResources'

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
AuditBitLocker ./Config
```

Uložte tento soubor s názvem `config.ps1` do složky projektu. Spusťte ji v PowerShellu tak, že ji spustíte `./config.ps1` v terminálu. Vytvoří se nový soubor MOF.

`Node AuditBitlocker`Příkaz není technicky vyžadován, ale vytváří soubor s názvem `AuditBitlocker.mof` , nikoli jako výchozí `localhost.mof` . Pokud má název souboru. mof postupovat podle konfigurace, usnadňuje uspořádání mnoha souborů při škálování.

Jakmile je soubor MOF zkompilován, podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic.

`New-GuestConfigurationPackage`Rutina vytvoří balíček. Moduly, které jsou potřebné pro konfiguraci, musí být k dispozici v `$Env:PSModulePath` . Parametry `New-GuestConfigurationPackage` rutiny při vytváření obsahu Windows:

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: kompilovaná úplná cesta dokumentu konfigurace DSC.
- **Cesta**: cesta ke výstupní složce. Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.

Spuštěním následujícího příkazu vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditBitlocker' `
  -Configuration './Config/AuditBitlocker.mof'
```

Po vytvoření konfiguračního balíčku, ale před jeho publikováním do Azure, můžete balíček otestovat z prostředí pracovní stanice nebo průběžné integrace a prostředí průběžného nasazování (CI/CD). Rutina GuestConfiguration `Test-GuestConfigurationPackage` zahrnuje stejného agenta ve vývojovém prostředí, které se používá v počítačích Azure. Pomocí tohoto řešení můžete provádět testování integrací místně před vydáním do fakturovaných cloudových prostředí.

Vzhledem k tomu, že agent ve skutečnosti vyhodnocuje místní prostředí, ve většině případů je třeba spustit rutinu test-rutiny na stejné platformě operačního systému, jakou máte v plánu auditovat. Test používá pouze moduly, které jsou součástí balíčku obsahu.

Parametry `Test-GuestConfigurationPackage` rutiny:

- **Název**: název zásad konfigurace hostů.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.

Spusťte následující příkaz, který otestuje balíček vytvořený předchozím krokem:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditBitlocker.zip
```

Rutina podporuje také vstup z kanálu PowerShellu. Přesměrování výstupu `New-GuestConfigurationPackage` rutiny do `Test-GuestConfigurationPackage` rutiny.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditBitlocker -Configuration ./Config/AuditBitlocker.mof | Test-GuestConfigurationPackage
```

Dalším krokem je publikování souboru do Azure Blob Storage. Příkaz `Publish-GuestConfigurationPackage` vyžaduje `Az.Storage` modul.

Parametry `Publish-GuestConfigurationPackage` rutiny:

- **Cesta**: umístění balíčku, který se má publikovat
- **ResourceGroupName**: název skupiny prostředků, ve které se nachází účet úložiště.
- **StorageAccountName**: název účtu úložiště, do kterého se má balíček publikovat
- **StorageContainerName**: (výchozí: *guestconfiguration*) název kontejneru úložiště v účtu úložiště
- **Force (vynutit**): přepsat existující balíček v účtu úložiště se stejným názvem

Následující příklad publikuje balíček do kontejneru úložiště s názvem guestconfiguration.

```azurepowershell-interactive
Publish-GuestConfigurationPackage -Path ./AuditBitlocker.zip -ResourceGroupName myResourceGroupName -StorageAccountName myStorageAccountName
```

Po vytvoření a nahrání balíčku vlastní zásady konfigurace hosta se vytvoří definice zásady konfigurace hosta. `New-GuestConfigurationPolicy`Rutina převezme vlastní balíček zásad a vytvoří definici zásady.

Parametry `New-GuestConfigurationPolicy` rutiny:

- **ContentUri**: veřejné http (s) URI balíčku obsahu konfigurace hosta.
- **DisplayName**: zobrazovaný název zásad.
- **Popis**: popis zásady.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Verze**: verze zásad
- **Cesta**: cílová cesta, kde jsou vytvořeny definice zásad.
- **Platforma**: cílová platforma (Windows/Linux) pro zásady konfigurace hosta a balíček obsahu.
- **Tag** přidá do definice zásady jeden nebo více filtrů značek.
- **Kategorie** nastaví pole metadata kategorie v definici zásady.

Následující příklad vytvoří definice zásad v zadané cestě z vlastního balíčku zásad:

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

Následující soubory vytvořil `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsna**

Výstup rutiny vrátí objekt, který obsahuje zobrazovaný název iniciativy a cestu k souborům zásad.

Nakonec publikujte definice zásad pomocí `Publish-GuestConfigurationPolicy` rutiny. Rutina má pouze parametr **path** , který odkazuje na umístění souborů JSON, které vytvořil `New-GuestConfigurationPolicy` .

K provedení příkazu Publikovat budete potřebovat přístup k vytváření zásad v Azure. Konkrétní autorizační požadavky jsou zdokumentovány na stránce [přehled Azure Policy](../overview.md) . Nejlepší integrovanou rolí je **Přispěvatel zásad prostředků**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions'
```

`Publish-GuestConfigurationPolicy`Rutina akceptuje cestu z kanálu PowerShellu. Tato funkce znamená, že můžete vytvořit soubory zásad a publikovat je v jedné sadě příkazů s použitím kanálu.

```azurepowershell-interactive
New-GuestConfigurationPolicy `
 -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit BitLocker service.' `
  -Description 'Audit if the BitLocker service is not enabled on Windows machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
```

V rámci zásad vytvořených v Azure je posledním krokem přiřazení definice. Podívejte se, jak přiřadit definici k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrování zásad konfigurace hostů pomocí značek

Definice zásad vytvořené rutinami v modulu Konfigurace hosta můžou volitelně zahrnovat filtr pro značky. Parametr **značky** pro `New-GuestConfigurationPolicy` podporuje pole zatřiďovacími tabulkami obsahující jednotlivá označení celého značky. Značky se přidají do `If` oddílu definice zásady a nedají se upravit přiřazením zásad.

Příklad fragmentu definice zásady, která filtruje značky, je uveden níže.

```json
"if": {
  "allOf" : [
    {
      "allOf": [
        {
          "field": "tags.Owner",
          "equals": "BusinessUnit"
        },
        {
          "field": "tags.Role",
          "equals": "Web"
        }
      ]
    },
    {
      // Original Guest Configuration content
    }
  ]
}
```

### <a name="using-parameters-in-custom-guest-configuration-policy-definitions"></a>Použití parametrů ve vlastních definicích zásad konfigurace hostů

Konfigurace hosta podporuje přepsání vlastností konfigurace v době běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku není nutné považovat za statické. Hodnoty přepsání jsou poskytovány prostřednictvím Azure Policy a neovlivňují způsob, jakým jsou vytvořeny nebo kompilovány konfigurace.

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` zahrnují parametr pojmenovaný **parametr**. Tento parametr převezme definici zatřiďovací tabulky včetně všech podrobností o jednotlivých parametrech a vytvoří požadované oddíly každého souboru používaného pro definici Azure Policy.

Následující příklad vytvoří definici zásady pro audit služby, kde uživatel vybere ze seznamu v okamžiku přiřazení zásady.

```azurepowershell-interactive
# This DSC Resource text:
Service 'UserSelectedNameExample'
      {
          Name = 'ParameterValue'
          Ensure = 'Present'
          State = 'Running'
      }

# Would require the following hashtable:
$PolicyParameterInfo = @(
    @{
        Name = 'ServiceName'                                            # Policy parameter name (mandatory)
        DisplayName = 'windows service name.'                           # Policy parameter display name (mandatory)
        Description = "Name of the windows service to be audited."      # Policy parameter description (optional)
        ResourceType = "Service"                                        # DSC configuration resource type (mandatory)
        ResourceId = 'UserSelectedNameExample'                                   # DSC configuration resource id (mandatory)
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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

## <a name="extending-guest-configuration-with-third-party-tools"></a>Rozšíření konfigurace hostů pomocí nástrojů třetích stran

Balíčky artefaktů pro konfiguraci hosta se dají rozšířit tak, aby zahrnovaly nástroje třetích stran.
Rozšíření konfigurace hosta vyžaduje vývoj dvou součástí.

- Prostředek konfigurace požadovaného stavu, který zpracovává všechny aktivity související se správou nástroje třetí strany.
  - Instalace
  - Invoke
  - Převést výstup
- Obsah ve správném formátu, který nástroj nativně spotřebovává

Pokud řešení komunity ještě neexistuje, vyžaduje prostředek DSC vlastní vývoj.
Řešení komunity je možné zjistit vyhledáním Galerie prostředí PowerShell pro tag [GuestConfiguration](https://www.powershellgallery.com/packages?q=Tags%3A%22GuestConfiguration%22).

> [!Note]
> Rozšiřitelnost konfigurace hosta je scénář "Přineste si vlastní licenci". Před použitím se ujistěte, že jste splnili podmínky a ujednání všech nástrojů třetích stran.

Po instalaci prostředku DSC do vývojového prostředí použijte parametr **FilesToInclude** pro `New-GuestConfigurationPackage` zahrnutí obsahu pro platformu třetí strany v artefaktu obsahu.

### <a name="step-by-step-creating-a-content-artifact-that-uses-third-party-tools"></a>Krok za krokem, vytvoření artefaktu obsahu, který používá nástroje třetích stran

Pouze `New-GuestConfigurationPackage` rutina vyžaduje změnu z podrobných pokynů pro artefakty obsahu DSC. V tomto příkladu použijte `gcInSpec` modul k prodloužení konfigurace hosta pro audit počítačů s Windows pomocí INSPEC Platform a nikoli integrovaného modulu používaného v systému Linux. Modul komunity je udržován jako [Open source projekt v GitHubu](https://github.com/microsoft/gcinspec).

Nainstalujte požadované moduly do vývojového prostředí:

```azurepowershell-interactive
# Update PowerShellGet if needed to allow installing PreRelease versions of modules
Install-Module PowerShellGet -Force

# Install GuestConfiguration module prerelease version
Install-Module GuestConfiguration -allowprerelease

# Install commmunity supported gcInSpec module
Install-Module gcInSpec
```

Nejprve vytvořte soubor YaML používaný nespecifikací. Soubor poskytuje základní informace o prostředí. Příklad je uveden níže:

```YaML
name: wmi_service
title: Verify WMI service is running
maintainer: Microsoft Corporation
summary: Validates that the Windows Service 'winmgmt' is running
copyright: Microsoft Corporation
license: MIT
version: 1.0.0
supports:
  - os-family: windows
```

Uložte tento soubor s názvem `wmi_service.yml` do složky s názvem `wmi_service` v adresáři projektu.

Pak vytvořte soubor Ruby s abstrakcí jazyka INSPEC, který se používá k auditování počítače.

```Ruby
control 'wmi_service' do
  impact 1.0
  title 'Verify windows service: winmgmt'
  desc 'Validates that the service, is installed, enabled, and running'

  describe service('winmgmt') do
    it { should be_installed }
    it { should be_enabled }
    it { should be_running }
  end
end

```

Uložte tento soubor `wmi_service.rb` do nové složky s názvem v `controls` `wmi_service` adresáři.

Nakonec vytvořte konfiguraci, importujte modul prostředků **GuestConfiguration** a pomocí `gcInSpec` prostředku nastavte název INSPEC Profile.

```powershell
# Define the configuration and import GuestConfiguration
Configuration wmi_service
{
    Import-DSCResource -Module @{ModuleName = 'gcInSpec'; ModuleVersion = '2.1.0'}
    node 'wmi_service'
    {
        gcInSpec wmi_service
        {
            InSpecProfileName       = 'wmi_service'
            InSpecVersion           = '3.9.3'
            WindowsServerVersion    = '2016'
        }
    }
}

# Compile the configuration to create the MOF files
wmi_service -out ./Config
```

Nyní byste měli mít strukturu projektu, jak je uvedeno níže:

```file
/ wmi_service
    / Config
        wmi_service.mof
    / wmi_service
        wmi_service.yml
        / controls
            wmi_service.rb 
```

Podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic.

`New-GuestConfigurationPackage`Rutina vytvoří balíček. Pro obsah třetích stran přidejte do balíčku obsah INSPEC pomocí parametru **FilesToInclude** . Nemusíte zadávat **ChefProfilePath** jako balíčky pro Linux.

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: úplná cesta k kompilované konfiguraci dokumentu.
- **Cesta**: cesta ke výstupní složce. Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.
- **FilesoInclude**: úplná cesta k profilu INSPEC.

Spuštěním následujícího příkazu vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'wmi_service' `
  -Configuration './Config/wmi_service.mof' `
  -FilesToInclude './wmi_service'  `
  -Path './package' 
```

## <a name="policy-lifecycle"></a>Životní cyklus zásad

Pokud chcete vydat aktualizaci zásady, proveďte změnu pro konfigurační balíček hosta a podrobnosti definice Azure Policy.

> [!NOTE]
> `version`Vlastnost přiřazení konfigurace hosta má jenom balíčky, které hostuje Microsoft. Osvědčeným postupem pro správu verzí vlastního obsahu je zahrnutí verze do názvu souboru.

Nejprve `New-GuestConfigurationPackage` Zadejte název balíčku, který bude v předchozích verzích jedinečný. Do názvu můžete zahrnout číslo verze, například `PackageName_1.0.0` .
Číslo v tomto příkladu se používá jenom k tomu, aby balíček byl jedinečný, a neměl by určovat, že by se měl balíček považovat za novější nebo starší než jiné balíčky.

Potom aktualizujte parametry používané pomocí `New-GuestConfigurationPolicy` rutiny podle každé z následujících vysvětlení.

- **Verze**: když spustíte `New-GuestConfigurationPolicy` rutinu, musíte zadat číslo verze, které je větší než aktuálně publikované.
- **contentUri**: když spustíte `New-GuestConfigurationPolicy` rutinu, musíte zadat identifikátor URI do umístění balíčku. Zahrnutí verze balíčku do názvu souboru zajistí, že se hodnota této vlastnosti změní v každé vydané verzi.
- **contentHash**: Tato vlastnost je automaticky aktualizována `New-GuestConfigurationPolicy` rutinou. Jedná se o hodnotu hash balíčku, kterou vytvořil `New-GuestConfigurationPackage` . Vlastnost musí být správná pro `.zip` soubor, který publikujete. Pokud se aktualizuje jenom vlastnost **contentUri** , rozšíření nepřijme balíček obsahu.

Nejjednodušším způsobem, jak vydat aktualizovaný balíček, je opakovat postup popsaný v tomto článku a zadat aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti jsou správně aktualizované.

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta používají SHA256 hash k ověření, že se balíček zásad nezměnil.
V případě potřeby mohou zákazníci také použít certifikát k podepisování balíčků a vynucení rozšíření konfigurace hosta pouze k povolení podepsaného obsahu.

Chcete-li povolit tento scénář, je třeba provést dva kroky. Spusťte rutinu pro podepsání balíčku obsahu a přidejte značku do počítačů, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci ověřování podpisem, spusťte `Protect-GuestConfigurationPackage` rutinu pro podepsání balíčku před jeho publikováním. Tato rutina vyžaduje certifikát pro podepsání kódu.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry `Protect-GuestConfigurationPackage` rutiny:

- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.
- **Certifikát**: certifikát pro podepsání kódu pro podepsání balíčku. Tento parametr je podporován pouze při podepisování obsahu pro systém Windows.

Agent GuestConfiguration očekává, že se veřejný klíč certifikátu nachází v počítačích se systémem Windows v části Důvěryhodné kořenové certifikační autority a v cestě `/usr/local/share/ca-certificates/extra` k počítačům se systémem Linux. Aby mohl uzel ověřit podepsaný obsah, nainstalujte na počítači veřejný klíč certifikátu a pak použijte vlastní zásady. Tento proces se dá provést pomocí jakékoli techniky v rámci virtuálního počítače nebo pomocí Azure Policy. [Tady je uvedena](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)Ukázková šablona.
Zásady přístupu Key Vault musí umožňovat poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobný postup najdete v tématu [nastavení Key Vault pro virtuální počítače v Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Následuje příklad exportu veřejného klíče z podpisového certifikátu pro import do počítače.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Po publikování obsahu přidejte značku s názvem `GuestConfigPolicyCertificateValidation` a hodnotou `enabled` do všech virtuálních počítačů, kde by mělo být požadováno podepisování kódu. Podívejte se na [ukázky značek](../samples/built-in-policies.md#tags) , jak mohou být značky doručovány ve velkém rozsahu pomocí Azure Policy. Jakmile je tato značka nastavená, definice zásady vytvořená pomocí `New-GuestConfigurationPolicy` rutiny povolí požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](./programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](./get-compliance-data.md).
