---
title: Postup vytváření zásad konfigurace hosta pro Linux
description: Naučte se vytvářet Azure Policy zásady konfigurace hostů pro Linux.
ms.date: 08/17/2020
ms.topic: how-to
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: 1e81d1a5157cc5872ba2628c8d6cb408e35ab9c6
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94694238"
---
# <a name="how-to-create-guest-configuration-policies-for-linux"></a>Postup vytváření zásad konfigurace hosta pro Linux

Než začnete vytvářet vlastní zásady, přečtěte si informace v přehledu [Azure Policy konfiguraci hostů](../concepts/guest-configuration.md).
 
Další informace o vytváření zásad konfigurace hostů pro Windows najdete na stránce [Postup vytvoření zásad konfigurace hostů pro Windows](./guest-configuration-create.md) .

Při auditování Linuxu konfigurace hosta využívá [Chef InSpec](https://www.inspec.io/). Profil InSpec definuje stav, ve kterém by počítač měl být. Pokud se konfigurace nezdařila, je aktivován efekt zásad **auditIfNotExists** a počítač se považuje za **nevyhovující**.

[Konfiguraci hosta Azure Policy](../concepts/guest-configuration.md) můžete použít jenom k auditování nastavení v počítačích. Náprava nastavení v počítačích ještě není k dispozici.

Pomocí následujících akcí můžete vytvořit vlastní konfiguraci pro ověření stavu počítače s Azure nebo mimo Azure.

> [!IMPORTANT]
> Vlastní definice zásad s konfigurací hosta v prostředích Azure Government a Azure Čína jsou funkcí ve verzi Preview.
>
> Rozšíření konfigurace hosta se vyžaduje k provádění auditů na virtuálních počítačích Azure. Pokud chcete nasadit rozšíření v rámci všech počítačů se systémem Linux, přiřaďte následující definici zásady: `Deploy prerequisites to enable Guest Configuration Policy on Linux VMs`
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
> V systému Linux není kompilace konfigurací podporována.

### <a name="base-requirements"></a>Základní požadavky

Operační systémy, ve kterých se modul dá nainstalovat:

- Linux
- macOS
- Windows

> [!NOTE]
> Rutina `Test-GuestConfigurationPackage` vyžaduje OpenSSL verze 1,0 z důvodu závislosti na OMI. To způsobí chybu v jakémkoli prostředí s OpenSSL 1,1 nebo novějším.
>
> Spuštění rutiny `Test-GuestConfigurationPackage` se podporuje jenom ve Windows pro 2.1.0 verze modulu Konfigurace hosta.

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

## <a name="guest-configuration-artifacts-and-policy-for-linux"></a>Artefakty konfigurace hosta a zásady pro Linux

I v prostředích systému Linux používá konfigurace hosta konfiguraci požadovaného stavu jako abstrakci jazyka. Implementace je založena na nativním kódu (C++), takže nevyžaduje načítání PowerShellu. Nicméně vyžaduje konfiguraci MOF popisující podrobnosti o prostředí.
DSC slouží jako obálka pro nespecifikované informace pro standardizaci způsobu jejich spuštění, způsobu zadání parametrů a způsobu, jakým se výstup vrátí službě. Při práci s vlastním netechnickým obsahem se vyžaduje jenom krátké znalosti DSC.

#### <a name="configuration-requirements"></a>Požadavky na konfiguraci

Název vlastní konfigurace musí být konzistentní všude. Název souboru. zip pro balíček obsahu, název konfigurace v souboru MOF a název přiřazení hosta v šabloně Azure Resource Manager (šablona ARM) musí být stejné.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfigurace vlastní konfigurace hosta v systému Linux

Konfigurace hosta v systému Linux používá `ChefInSpecResource` prostředek k poskytnutí modulu s názvem [INSPEC Profile](https://www.inspec.io/docs/reference/profiles/). **Název** je jediná požadovaná vlastnost prostředku. Vytvořte soubor YaML a soubor skriptu Ruby, jak je popsáno níže.

Nejprve vytvořte soubor YaML používaný nespecifikací. Soubor poskytuje základní informace o prostředí. Příklad je uveden níže:

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

Uložte tento soubor s názvem `inspec.yml` do složky s názvem `linux-path` v adresáři projektu.

Pak vytvořte soubor Ruby s abstrakcí jazyka INSPEC, který se používá k auditování počítače.

```Ruby
describe file('/tmp') do
    it { should exist }
end
```

Uložte tento soubor s názvem `linux-path.rb` do nové složky s názvem v `controls` `linux-path` adresáři.

Nakonec vytvořte konfiguraci, importujte modul prostředků **PSDesiredStateConfiguration** a zkompilujte konfiguraci.

```powershell
# import PSDesiredStateConfiguration module
import-module PSDesiredStateConfiguration

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

Uložte tento soubor s názvem `config.ps1` do složky projektu. Spusťte ji v PowerShellu tak, že ji spustíte `./config.ps1` v terminálu. Vytvoří se nový soubor MOF.

`Node AuditFilePathExists`Příkaz není technicky vyžadován, ale vytváří soubor s názvem `AuditFilePathExists.mof` , nikoli jako výchozí `localhost.mof` . Pokud má název souboru. mof postupovat podle konfigurace, usnadňuje uspořádání mnoha souborů při škálování.

Nyní byste měli mít strukturu projektu, jak je uvedeno níže:

```file
/ AuditFilePathExists
    / Config
        AuditFilePathExists.mof
    / linux-path
        inspec.yml
        / controls
            linux-path.rb 
```

Podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic.

`New-GuestConfigurationPackage`Rutina vytvoří balíček. Parametry `New-GuestConfigurationPackage` rutiny při vytváření obsahu pro Linux:

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: úplná cesta k kompilované konfiguraci dokumentu.
- **Cesta**: cesta ke výstupní složce. Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.
- **ChefInspecProfilePath**: úplná cesta k profilu INSPEC. Tento parametr je podporován pouze při vytváření obsahu pro audit systému Linux.

Spuštěním následujícího příkazu vytvořte balíček pomocí konfigurace uvedené v předchozím kroku:

```azurepowershell-interactive
New-GuestConfigurationPackage `
  -Name 'AuditFilePathExists' `
  -Configuration './Config/AuditFilePathExists.mof' `
  -ChefInSpecProfilePath './'
```

Po vytvoření konfiguračního balíčku, ale před jeho publikováním do Azure, můžete balíček otestovat z prostředí pracovní stanice nebo průběžné integrace a prostředí průběžného nasazování (CI/CD). Rutina GuestConfiguration `Test-GuestConfigurationPackage` zahrnuje stejného agenta ve vývojovém prostředí, které se používá v počítačích Azure. Pomocí tohoto řešení můžete provádět testování integrací místně před vydáním do fakturovaných cloudových prostředí.

Vzhledem k tomu, že agent ve skutečnosti vyhodnocuje místní prostředí, ve většině případů je třeba spustit rutinu test-rutiny na stejné platformě operačního systému, jakou máte v plánu auditovat.

Parametry `Test-GuestConfigurationPackage` rutiny:

- **Název**: název zásad konfigurace hostů.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.

Spusťte následující příkaz, který otestuje balíček vytvořený předchozím krokem:

```azurepowershell-interactive
Test-GuestConfigurationPackage `
  -Path ./AuditFilePathExists/AuditFilePathExists.zip
```

Rutina podporuje také vstup z kanálu PowerShellu. Přesměrování výstupu `New-GuestConfigurationPackage` rutiny do `Test-GuestConfigurationPackage` rutiny.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditFilePathExists -Configuration ./Config/AuditFilePathExists.mof -ChefInspecProfilePath './' | Test-GuestConfigurationPackage
```

Dalším krokem je publikování souboru do Azure Blob Storage.  Příkaz `Publish-GuestConfigurationPackage` vyžaduje `Az.Storage` modul.

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
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit Linux file path.' `
    -Description 'Audit that a file path exists on a Linux machine.' `
    -Path './policies' `
    -Platform 'Linux' `
    -Version 1.0.0 `
    -Verbose
```

Následující soubory vytvořil `New-GuestConfigurationPolicy` :

- **auditIfNotExists.jsna**

Výstup rutiny vrátí objekt, který obsahuje zobrazovaný název iniciativy a cestu k souborům zásad.

Nakonec publikujte definice zásad pomocí `Publish-GuestConfigurationPolicy` rutiny. Rutina má pouze parametr **path** , který odkazuje na umístění souborů JSON, které vytvořil `New-GuestConfigurationPolicy` .

K provedení příkazu Publikovat budete potřebovat přístup k vytváření zásad v Azure. Konkrétní autorizační požadavky jsou zdokumentovány na stránce [přehled Azure Policy](../overview.md) . Nejlepší integrovanou rolí je **Přispěvatel zásad prostředků**.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy `
  -Path './policies'
```

 `Publish-GuestConfigurationPolicy`Rutina akceptuje cestu z kanálu PowerShellu. Tato funkce znamená, že můžete vytvořit soubory zásad a publikovat je v jedné sadě příkazů s použitím kanálu.

 ```azurepowershell-interactive
 New-GuestConfigurationPolicy `
  -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditFilePathExists.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
  -DisplayName 'Audit Linux file path.' `
  -Description 'Audit that a file path exists on a Linux machine.' `
  -Path './policies' `
 | Publish-GuestConfigurationPolicy
 ```

V rámci zásad vytvořených v Azure je posledním krokem přiřazení definice. Podívejte se, jak přiřadit definici k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Použití parametrů ve vlastních zásadách konfigurace hostů

Konfigurace hosta podporuje přepsání vlastností konfigurace v době běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku není nutné považovat za statické. Hodnoty přepsání jsou poskytovány prostřednictvím Azure Policy a neovlivňují způsob, jakým jsou vytvořeny nebo kompilovány konfigurace.

S neurčenými specifikacemi jsou parametry obvykle zpracovávány jako vstup buď za běhu, nebo jako kód pomocí atributů. Konfigurace hosta zaznamená tento proces, takže zadání lze zadat při přiřazení zásady. Soubor atributů se automaticky vytvoří v rámci počítače. Nemusíte vytvářet a přidávat soubory do projektu. Existují dva kroky pro přidání parametrů do projektu auditu systému Linux.

V souboru Ruby definujte vstup, ve kterém chcete skriptovat, co se má na počítači auditovat. Příklad je uveden níže.

```Ruby
attr_path = attribute('path', description: 'The file path to validate.')

describe file(attr_path) do
    it { should exist }
end
```

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` zahrnují parametr pojmenovaný **parametr**. Tento parametr přebírá zatřiďovací tabulku, včetně všech podrobností o jednotlivých parametrech, a automaticky vytvoří všechny požadované oddíly souborů, pomocí kterých se vytvoří každá definice Azure Policy.

Následující příklad vytvoří definici zásady pro audit cesty k souboru, kde uživatel zadá cestu v době přiřazení zásady.

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
    -Parameter $PolicyParameterInfo `
    -Version 1.0.0
```

V případě zásad pro Linux zahrňte vlastnost **AttributesYmlContent** do konfigurace a podle potřeby hodnoty přepište. Agent konfigurace hosta automaticky vytvoří soubor YAML používaný nespecifikací k ukládání atributů. Viz následující příklad.

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

Pro vydání aktualizace do definice zásady existují tři pole, která vyžadují pozornost.

> [!NOTE]
> `version`Vlastnost přiřazení konfigurace hosta má jenom balíčky, které hostuje Microsoft. Osvědčeným postupem pro správu verzí vlastního obsahu je zahrnutí verze do názvu souboru.

- **Verze**: když spustíte `New-GuestConfigurationPolicy` rutinu, musíte zadat číslo verze, které je větší než aktuálně publikované.
- **contentUri**: když spustíte `New-GuestConfigurationPolicy` rutinu, musíte zadat identifikátor URI do umístění balíčku. Zahrnutí verze balíčku do názvu souboru zajistí, že se hodnota této vlastnosti změní v každé vydané verzi.
- **contentHash**: Tato vlastnost je automaticky aktualizována `New-GuestConfigurationPolicy` rutinou. Jedná se o hodnotu hash balíčku, kterou vytvořil `New-GuestConfigurationPackage` . Vlastnost musí být správná pro `.zip` soubor, který publikujete. Pokud se aktualizuje jenom vlastnost **contentUri** , rozšíření nepřijme balíček obsahu.

Nejjednodušším způsobem, jak vydat aktualizovaný balíček, je opakovat postup popsaný v tomto článku a zadat aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti jsou správně aktualizované.

### <a name="filtering-guest-configuration-policies-using-tags"></a>Filtrování zásad konfigurace hostů pomocí značek

Zásady vytvořené rutinami v modulu Konfigurace hosta můžou volitelně zahrnovat filtr pro značky. Parametr **-tag** pro `New-GuestConfigurationPolicy` podporuje pole zatřiďovacími tabulkami obsahující jednotlivá označení celých značek. Značky se přidají do `If` oddílu definice zásady a nedá se upravit pomocí přiřazení zásady.

Příklad fragmentu definice zásady, která bude filtrovat značky, je uveden níže.

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
      // Original Guest Configuration content will follow
    }
  ]
}
```

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta používají SHA256 hash k ověření, že se balíček zásad nezměnil.
V případě potřeby mohou zákazníci také použít certifikát k podepisování balíčků a vynucení rozšíření konfigurace hosta pouze k povolení podepsaného obsahu.

Chcete-li povolit tento scénář, je třeba provést dva kroky. Spusťte rutinu pro podepsání balíčku obsahu a přidejte značku do počítačů, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci ověřování podpisem, spusťte `Protect-GuestConfigurationPackage` rutinu pro podepsání balíčku před jeho publikováním. Tato rutina vyžaduje certifikát pro podepsání kódu.

Parametry `Protect-GuestConfigurationPackage` rutiny:

- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.
- **PublicGpgKeyPath**: cesta k veřejnému klíči GPG. Tento parametr je podporován pouze při podepisování obsahu pro Linux.

Dobrá Reference k vytváření GPG klíčů pro použití s počítači se systémem Linux je poskytována článkem na GitHubu, který [generuje nový klíč GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Agent GuestConfiguration očekává, že se veřejný klíč certifikátu nachází v cestě `/usr/local/share/ca-certificates/extra` k počítačům se systémem Linux. Aby mohl uzel ověřit podepsaný obsah, nainstalujte na počítači veřejný klíč certifikátu a pak použijte vlastní zásady. Tento proces se dá provést pomocí libovolné techniky uvnitř virtuálního počítače nebo pomocí Azure Policy. [Tady je uvedena](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)Ukázková šablona.
Zásady přístupu Key Vault musí umožňovat poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobný postup najdete v tématu [nastavení Key Vault pro virtuální počítače v Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Po publikování obsahu přidejte značku s názvem `GuestConfigPolicyCertificateValidation` a hodnotou `enabled` do všech virtuálních počítačů, kde by mělo být požadováno podepisování kódu. Podívejte se na [ukázky značek](../samples/built-in-policies.md#tags) , jak mohou být značky doručovány ve velkém rozsahu pomocí Azure Policy. Jakmile je tato značka nastavená, definice zásady vytvořená pomocí `New-GuestConfigurationPolicy` rutiny povolí požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](./programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](./get-compliance-data.md).
