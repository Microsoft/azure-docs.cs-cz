---
title: Postup vytvoření zásad konfigurace hostů
description: Naučte se vytvářet Azure Policy zásady konfigurace hostů pro virtuální počítače se systémem Windows nebo Linux.
author: DCtheGeek
ms.author: dacoulte
ms.date: 09/20/2019
ms.topic: conceptual
ms.service: azure-policy
ms.openlocfilehash: fcb65e75de730178901742dc36c72776e39b044b
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2019
ms.locfileid: "71977977"
---
# <a name="how-to-create-guest-configuration-policies"></a>Postup vytvoření zásad konfigurace hostů

Konfigurace hosta používá modul prostředků [požadované konfigurace stavu](/powershell/dsc) (DSC) k vytvoření konfigurace pro auditování počítačů Azure. Konfigurace DSC definuje stav, ve kterém má být počítač. Pokud se konfigurace nezdařila, je aktivován efekt zásad **auditIfNotExists** a počítač se považuje za **nevyhovující**.

[Konfiguraci hosta Azure Policy](/azure/governance/policy/concepts/guest-configuration) můžete použít jenom k auditování nastavení v počítačích. Náprava nastavení v počítačích ještě není k dispozici.

Pomocí následujících akcí vytvořte vlastní konfiguraci pro ověření stavu počítače Azure.

> [!IMPORTANT]
> Vlastní zásady s konfigurací hosta jsou funkcí verze Preview.

## <a name="add-the-guestconfiguration-resource-module"></a>Přidat modul prostředků GuestConfiguration

Chcete-li vytvořit zásadu konfigurace hosta, je nutné přidat modul prostředků. Tento modul prostředků se dá použít s místně nainstalovaným PowerShellem, s [Azure Cloud Shell](https://shell.azure.com), nebo s [imagí Azure PowerShell Docker](https://hub.docker.com/rsdk-powershell/).

### <a name="base-requirements"></a>Základní požadavky

Modul prostředků konfigurace hosta vyžaduje následující software:

- Prostředí. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/scripting/install/installing-powershell).
- Azure PowerShell 1.5.0 nebo vyšší. Pokud ještě není nainstalovaný, postupujte podle [těchto pokynů](/powershell/azure/install-az-ps).

### <a name="install-the-module"></a>Nainstalovat modul

Konfigurace hosta používá modul prostředků **GuestConfiguration** k vytváření konfigurací DSC a jejich publikování do Azure Policy:

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

## <a name="create-custom-guest-configuration-configuration-and-resources"></a>Vytvoření vlastní konfigurace a prostředků konfigurace hosta

Prvním krokem k vytvoření vlastní zásady pro konfiguraci hosta je vytvoření konfigurace DSC. Přehled konceptů a terminologie DSC najdete v tématu [Přehled prostředí POWERSHELL DSC](/powershell/dsc/overview/overview).

Pokud vaše konfigurace vyžaduje jenom prostředky, které jsou integrované s instalací agenta konfigurace hosta, stačí vytvořit konfigurační soubor MOF. Pokud potřebujete spustit další skript, budete muset vytvořit vlastní modul prostředků.

### <a name="requirements-for-guest-configuration-custom-resources"></a>Požadavky na vlastní prostředky konfigurace hosta

Když konfigurace hosta Audituje počítač, nejdřív se spustí `Test-TargetResource`, aby se zjistilo, jestli je ve správném stavu. Logická hodnota vrácená funkcí určuje, zda má být stav Azure Resource Manager pro přiřazení hostů kompatibilní/nekompatibilní. Pokud je logická hodnota `$false` pro libovolný prostředek v konfiguraci, pak se zprostředkovatel spustí `Get-TargetResource`. Pokud je logická hodnota `$true`, není volána `Get-TargetResource`.

Funkce `Get-TargetResource` má zvláštní požadavky na konfiguraci hosta, která není potřebná pro konfiguraci požadovaného stavu Windows.

- Vrácená zatřiďovací tabulka musí zahrnovat vlastnost s názvem **důvody**.
- Vlastnost důvody musí být pole.
- Každá položka v poli musí být zatřiďovací tabulka s klíči s názvem **Code** a **frází**.

Vlastnost důvody používá služba ke standardizaci způsobu, jakým jsou informace zobrazeny, když je počítač nekompatibilní. Jednotlivé položky si můžete představit z důvodů, proč prostředek není kompatibilní. Vlastnost je pole, protože prostředek může být nekompatibilní s více než jedním důvodem.

Služba očekává **kód** a **frázi** vlastností. Při vytváření vlastního prostředku nastavte text (obvykle STDOUT), který chcete zobrazit jako důvod, proč prostředek není kompatibilní jako hodnota **fráze**. **Kód** má specifické požadavky na formátování, takže hlášení může jasně zobrazit informace o prostředku, který se použil k provedení auditu. Toto řešení zajišťuje rozšiřitelnou konfiguraci hostů. Libovolný příkaz lze spustit pro audit počítače, pokud je možné zachytit výstup a vrátit ho jako řetězcovou hodnotu pro vlastnost **fráze** .

- **Code** (String): název prostředku, opakuje a pak krátký název bez mezer jako identifikátor z důvodu. Tyto tři hodnoty by měly být odděleny dvojtečkami bez mezer.
  - Příklad by `registry:registry:keynotpresent`
- **Fráze** (String): text čitelný lidmi pro vysvětlení, proč nastavení není vyhovující.
  - Příklad by `The registry key $key is not present on the machine.`

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

#### <a name="scaffolding-a-guest-configuration-project"></a>Generování uživatelského rozhraní projektu konfigurace hosta

Pro vývojáře, kteří chtějí urychlit proces zahájení práce a práci z ukázkového kódu, existuje projekt komunity s názvem **projekt konfigurace hosta** jako šablona pro modul [sádry](https://github.com/powershell/plaster) prostředí PowerShell. Tento nástroj lze použít k vytvoření uživatelského rozhraní projektu, včetně pracovní konfigurace a ukázkového prostředku, a sady testů [platformy pester](https://github.com/pester/pester) pro ověření projektu. Šablona obsahuje také Spouštěče úloh pro Visual Studio Code pro automatizaci vytváření a ověřování konfiguračního balíčku hosta. Další informace najdete v [projektu konfigurace hosta](https://github.com/microsoft/guestconfigurationproject)projektu GitHubu.

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfigurace vlastní konfigurace hosta v systému Linux

Konfigurace DSC pro konfiguraci hosta v systému Linux používá prostředek `ChefInSpecResource` k tomu, aby modul poskytoval název nespecifikované [definice.](https://www.chef.io/inspec/) **Název** je jediná požadovaná vlastnost prostředku.

V následujícím příkladu se vytvoří konfigurace s názvem **směrný plán**, naimportuje se modul prostředků **GuestConfiguration** a pomocí prostředku `ChefInSpecResource` nastaví název INSPEC definice na **Linux-patch-směrného plánu**:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration baseline
{
    Import-DscResource -ModuleName 'GuestConfiguration'

    ChefInSpecResource 'Audit Linux patch baseline'
    {
        Name = 'linux-patch-baseline'
    }
}

# Compile the configuration to create the MOF files
baseline
```

Další informace najdete v tématu [zápis, kompilace a použití konfigurace](/powershell/dsc/configurations/write-compile-apply-configuration).

### <a name="custom-guest-configuration-configuration-on-windows"></a>Konfigurace vlastní konfigurace hosta ve Windows

Konfigurace DSC pro Azure Policy konfiguraci hostů se používá jenom u agenta konfigurace hosta, nekoliduje s konfigurací požadovaného stavu Windows PowerShellu.

Následující příklad vytvoří konfiguraci s názvem **AuditBitLocker**, importuje modul prostředků **GuestConfiguration** a pomocí prostředku `Service` provede audit pro spuštěnou službu:

```azurepowershell-interactive
# Define the DSC configuration and import GuestConfiguration
Configuration AuditBitLocker
{
    Import-DscResource -ModuleName 'PSDscResources'

    Service 'Ensure BitLocker service is present and running'
    {
        Name = 'BDESVC'
        Ensure = 'Present'
        State = 'Running'
    }
}

# Compile the configuration to create the MOF files
AuditBitLocker
```

Další informace najdete v tématu [zápis, kompilace a použití konfigurace](/powershell/dsc/configurations/write-compile-apply-configuration).

## <a name="create-guest-configuration-custom-policy-package"></a>Vytvořit vlastní balíček zásad konfigurace hosta

Jakmile je soubor MOF zkompilován, podpůrné soubory musí být zabaleny dohromady. Dokončený balíček používá konfigurace hosta k vytvoření Azure Policych definic. Balíček se skládá z těchto součástí:

- Kompilovaná konfigurace DSC jako MOF
- Složka modulů
  - Modul GuestConfiguration
  - Modul DscNativeResources
  - Linux Složka s definicí INSPEC a dalším obsahem
  - Systému Moduly prostředků DSC, které nejsou vestavěné

Rutina `New-GuestConfigurationPackage` vytvoří balíček. Pro vytvoření vlastního balíčku se používá následující formát:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

Parametry rutiny `New-GuestConfigurationPackage`:

- **Název**: název konfiguračního balíčku hosta.
- **Konfigurace**: kompilovaná úplná cesta dokumentu konfigurace DSC.
- **Cesta**: cesta ke výstupní složce. Tento parametr je nepovinný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.
- **ChefProfilePath**: úplná cesta k profilu INSPEC. Tento parametr je podporován pouze při vytváření obsahu pro audit systému Linux.

Dokončený balíček musí být uložený v umístění, ke kterému mají přístup spravované virtuální počítače. Mezi příklady patří úložiště GitHub, úložiště Azure nebo Azure Storage. Pokud nechcete, aby balíček byl veřejný, můžete do adresy URL přidat [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) . Můžete také implementovat [koncový bod služby](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pro počítače v privátní síti, i když tato konfigurace platí pouze pro přístup k balíčku a nekomunikuje se službou.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Práce s tajnými kódy v konfiguračních balíčcích hosta

V konfiguraci Azure Policy hosta je optimální způsob, jak spravovat tajné klíče používané v době běhu, ukládat je do Azure Key Vault. Tento návrh se implementuje v rámci vlastních prostředků DSC.

1. Nejprve v Azure vytvořte spravovanou identitu přiřazenou uživatelem.

   Tato identita je používána počítači pro přístup k tajným klíčům uloženým v Key Vault. Podrobný postup najdete v tématu [Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

1. Vytvořte instanci Key Vault.

   Podrobné pokyny najdete v tématu [nastavení a načtení tajného kódu – PowerShell](../../../key-vault/quick-create-powershell.md).
   Přiřazením oprávnění k instanci udělte přístup k identitám přiřazeným uživateli k tajným klíčům uloženým v Key Vault. Podrobné pokyny najdete v tématu [nastavení a načtení tajného kódu – .NET](../../../key-vault/quick-create-net.md#give-the-service-principal-access-to-your-key-vault).

1. Přiřaďte počítači identitu přiřazenou uživatelem.

   Podrobný postup najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
   Ve velkém měřítku přiřaďte tuto identitu pomocí Azure Resource Manager přes Azure Policy. Podrobný postup najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablony](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

1. Nakonec v rámci vlastního prostředku použijte ID klienta vygenerované výše pro přístup k Key Vault pomocí tokenu dostupného z počítače.

   @No__t-0 a adresu URL instance Key Vault lze předat prostředku jako [vlastnosti](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema) , takže prostředek nebude nutné aktualizovat pro více prostředí nebo v případě, že je třeba změnit hodnoty.

Následující příklad kódu lze použít ve vlastním prostředku k načtení tajných kódů z Key Vault pomocí uživatelsky přiřazené identity. Hodnota vrácená z požadavku na Key Vault je prostý text. Jako osvědčený postup si ho uložte v rámci objektu přihlašovacích údajů.

```azurepowershell-interactive
# the following values should be input as properties
$client_id = 'e3a78c9b-4dd2-46e1-8bfa-88c0574697ce'
$keyvault_url = 'https://keyvaultname.vault.azure.net/secrets/mysecret'

$access_token = ((Invoke-WebRequest -Uri "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&client_id=$client_id&resource=https%3A%2F%2Fvault.azure.net" -Method GET -Headers @{Metadata='true'}).Content | ConvertFrom-Json).access_token

$value = ((Invoke-WebRequest -Uri $($keyvault_url+'?api-version=2016-10-01') -Method GET -Headers @{Authorization="Bearer $access_token"}).content | convertfrom-json).value |  ConvertTo-SecureString -asplaintext -force

$credential = New-Object System.Management.Automation.PSCredential('secret',$value)
```

## <a name="test-a-guest-configuration-package"></a>Testování konfiguračního balíčku hosta

Po vytvoření konfiguračního balíčku, ale před jeho publikováním do Azure, můžete otestovat funkčnost balíčku z prostředí pro pracovní stanici nebo CI/CD. Modul GuestConfiguration zahrnuje rutinu `Test-GuestConfigurationPackage`, která ve vývojovém prostředí načte stejného agenta, jako se používá v počítačích Azure. Pomocí tohoto řešení můžete provádět testování integrací místně ještě předtím, než vydáte účtované/testovací/provozní prostředí.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

Parametry rutiny `Test-GuestConfigurationPackage`:

- **Název**: název zásad konfigurace hostů.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.

Rutina podporuje také vstup z kanálu PowerShellu. Zapotrubní výstup rutiny `New-GuestConfigurationPackage` do rutiny `Test-GuestConfigurationPackage`.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Další informace o tom, jak testovat pomocí parametrů, najdete v níže uvedené části [použití parametrů ve vlastních zásadách konfigurace hostů](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Vytvoření souborů nasazení definice Azure Policy a iniciativa

Po vytvoření balíčku vlastní zásady konfigurace hosta a nahrání do umístění přístupného pro počítače vytvořte definici zásady konfigurace hosta pro Azure Policy. Rutina `New-GuestConfigurationPolicy` používá veřejně přístupný balíček vlastní zásady konfigurace hosta a vytvoří definici zásady **auditIfNotExists** a **deployIfNotExists** . Vytvoří se také definice iniciativy zásad, která zahrnuje definice obou zásad.

Následující příklad vytvoří definice zásad a iniciativ v zadané cestě z balíčku vlastní zásady konfigurace hosta pro Windows a poskytuje název, popis a verzi:

```azurepowershell-interactive
New-GuestConfigurationPolicy
    -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' `
    -DisplayName 'Audit BitLocker Service.' `
    -Description 'Audit if BitLocker is not enabled on Windows machine.' `
    -Path '.\policyDefinitions' `
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

Parametry rutiny `New-GuestConfigurationPolicy`:

- **ContentUri**: veřejné http (s) URI balíčku obsahu konfigurace hosta.
- **DisplayName**: zobrazovaný název zásad.
- **Popis**: popis zásady.
- **Parametr**: parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Verze**: verze zásad
- **Cesta**: cílová cesta, kde jsou vytvořeny definice zásad.
- **Platforma**: cílová platforma (Windows/Linux) pro zásady konfigurace hosta a balíček obsahu.

Následující soubory jsou vytvořeny pomocí `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Iniciativa. JSON**

Výstup rutiny vrátí objekt, který obsahuje zobrazovaný název iniciativy a cestu k souborům zásad.

Pokud chcete tento příkaz použít k vytvoření uživatelského rozhraní pro vlastní projekt zásad, můžete provádět změny těchto souborů. Příkladem je změna oddílu if pro vyhodnocení toho, jestli je pro počítače k dispozici konkrétní značka. Podrobnosti o vytváření zásad najdete v tématu [programové vytváření zásad](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Použití parametrů ve vlastních zásadách konfigurace hostů

Konfigurace hosta podporuje přepsání vlastností konfigurace v době běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku není nutné považovat za statické. Hodnoty přepsání jsou poskytovány prostřednictvím Azure Policy a neovlivňují způsob, jakým jsou vytvořeny nebo kompilovány konfigurace.

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` obsahují parametr s názvem **Parameters**. Tento parametr převezme definici zatřiďovací tabulky včetně všech podrobností o jednotlivých parametrech a automaticky vytvoří všechny požadované oddíly souborů použité k vytvoření každé definice Azure Policy.

Následující příklad vytvoří Azure Policy pro audit služby, kde uživatel vybere ze seznamu služeb v době přiřazování zásad.

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
    -Platform 'Windows' `
    -Version 1.2.3.4 `
    -Verbose
```

V případě zásad pro Linux zahrňte vlastnost **AttributesYmlContent** do konfigurace a příslušné hodnoty odpovídajícím způsobem přepsat. Agent konfigurace hosta automaticky vytvoří soubor YaML používaný nespecifikací k ukládání atributů. Příklad je uvedený níže.

```azurepowershell-interactive
Configuration FirewalldEnabled {

    Import-DscResource -ModuleName 'GuestConfiguration'

    Node FirewalldEnabled {

        ChefInSpecResource FirewalldEnabled {
            Name = 'FirewalldEnabled'
            AttributesYmlContent = "DefaultFirewalldProfile: [public]"
        }
    }
}
```

Pro každý další parametr přidejte do pole zatřiďovací tabulku. V souborech zásad se zobrazí vlastnosti přidané do souboru configurationName konfigurace hosta, který identifikuje typ prostředku, název, vlastnost a hodnotu.

```json
{
    "apiVersion": "2018-11-20",
    "type": "Microsoft.Compute/virtualMachines/providers/guestConfigurationAssignments",
    "name": "[concat(parameters('vmName'), '/Microsoft.GuestConfiguration/', parameters('configurationName'))]",
    "location": "[parameters('location')]",
    "properties": {
        "guestConfiguration": {
            "name": "[parameters('configurationName')]",
            "version": "1.*",
            "configurationParameter": [{
                "name": "[Service]windowsService;Name",
                "value": "[parameters('ServiceName')]"
            }]
        }
    }
}
```

## <a name="publish-to-azure-policy"></a>Publikovat do Azure Policy

Modul **GuestConfiguration** Resource nabízí způsob, jak pomocí rutiny `Publish-GuestConfigurationPolicy` vytvořit definice zásad a definice iniciativy v Azure pomocí jednoho kroku.
Rutina má pouze parametr **path** , který odkazuje na umístění tří souborů JSON vytvořených pomocí `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

Rutina `Publish-GuestConfigurationPolicy` akceptuje cestu z kanálu PowerShellu. Tato funkce znamená, že můžete vytvořit soubory zásad a publikovat je v jedné sadě příkazů s použitím kanálu.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

S definicemi zásad a iniciativ vytvořenými v Azure se posledním krokem přiřadí iniciativa. Podívejte se, jak přiřadit iniciativu k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Zásady konfigurace hosta se musí **vždy** přiřadit pomocí iniciativy, která kombinuje zásady _AuditIfNotExists_ a _DeployIfNotExists_ . Pokud je přiřazena pouze zásada _AuditIfNotExists_ , požadavky nejsou nasazeny a zásady vždy ukazují, že jsou servery "0" kompatibilní.

## <a name="policy-lifecycle"></a>Životní cyklus zásad

Po publikování vlastního Azure Policy pomocí vlastního balíčku obsahu jsou k dispozici dvě pole, která je potřeba aktualizovat, pokud chcete publikovat novou verzi.

- **Verze**: když spustíte rutinu `New-GuestConfigurationPolicy`, musíte zadat číslo verze, které je větší než aktuálně publikované. Vlastnost aktualizuje verzi přiřazení konfigurace hosta v novém souboru zásad tak, aby rozšíření rozpoznalo aktualizaci balíčku.
- **contentHash**: Tato vlastnost je automaticky aktualizována pomocí rutiny `New-GuestConfigurationPolicy`. Je to hodnota hash balíčku vytvořeného pomocí `New-GuestConfigurationPackage`. Vlastnost musí být správná pro soubor `.zip`, který publikujete. Pokud je aktualizována pouze vlastnost **contentUri** , například v případě, kdy by někdo mohl provést ruční změnu definice zásady z portálu, rozšíření nepřijme balíček obsahu.

Nejjednodušším způsobem, jak vydat aktualizovaný balíček, je opakovat postup popsaný v tomto článku a zadat aktualizované číslo verze. Tento proces zaručuje, že všechny vlastnosti jsou správně aktualizované.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Převod obsahu Windows Zásady skupiny na Azure Policy konfiguraci hosta

Konfigurace hosta, při auditování počítačů s Windows, je implementovaná syntaxe konfigurace požadovaného stavu prostředí PowerShell. Komunita DSC zveřejnila nástroje pro převod exportovaných šablon Zásady skupiny do formátu DSC. Pomocí tohoto nástroje spolu s rutinami konfigurace hosta, které jsou popsané výše, můžete převést Windows Zásady skupiny obsah a balíček/publikovat pro Azure Policy k auditování. Podrobnosti o používání tohoto nástroje najdete v článku [rychlý Start: převod zásady skupiny do DSC](/powershell/dsc/quickstarts/gpo-quickstart).
Po převedení tohoto obsahu výše uvedené kroky pro vytvoření balíčku a jeho publikování jako Azure Policy budou stejné jako u jakéhokoli obsahu DSC.

## <a name="optional-signing-guest-configuration-packages"></a>Volitelné: podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta ve výchozím nastavení používají SHA256 hash k ověření, že se balíček zásad nezměnil od okamžiku, kdy byl vyčten serverem, který je auditován.
V případě potřeby mohou zákazníci také použít certifikát k podepisování balíčků a vynucení rozšíření konfigurace hosta pouze k povolení podepsaného obsahu.

Chcete-li povolit tento scénář, je třeba provést dva kroky. Spusťte rutinu pro podepsání balíčku obsahu a přidejte značku do počítačů, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci ověření podpisu, spusťte rutinu `Protect-GuestConfigurationPackage` pro podepsání balíčku před jeho publikováním. Tato rutina vyžaduje certifikát pro podepsání kódu.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

Parametry rutiny `Protect-GuestConfigurationPackage`:

- **Cesta**: úplná cesta k balíčku pro konfiguraci hosta.
- **Certifikát**: certifikát pro podepsání kódu pro podepsání balíčku. Tento parametr je podporován pouze při podepisování obsahu pro systém Windows.
- **PrivateGpgKeyPath**: privátní cesta GPG klíče. Tento parametr je podporován pouze při podepisování obsahu pro Linux.
- **PublicGpgKeyPath**: cesta k veřejnému klíči GPG. Tento parametr je podporován pouze při podepisování obsahu pro Linux.

Agent GuestConfiguration očekává, že se veřejný klíč certifikátu nachází v počítačích s Windows v části Důvěryhodné kořenové certifikační autority a v cestě `/usr/local/share/ca-certificates/extra` v počítačích se systémem Linux. Aby mohl uzel ověřit podepsaný obsah, nainstalujte na počítači veřejný klíč certifikátu a pak použijte vlastní zásady. Tento proces se dá provést pomocí libovolné techniky uvnitř virtuálního počítače nebo pomocí Azure Policy. [Tady je uvedena](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)Ukázková šablona.
Zásady přístupu Key Vault musí umožňovat poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobný postup najdete v tématu [nastavení Key Vault pro virtuální počítače v Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Následuje příklad exportu veřejného klíče z podpisového certifikátu pro import do počítače.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Dobrá Reference k vytváření GPG klíčů pro použití s počítači se systémem Linux je poskytována článkem na GitHubu, který [generuje nový klíč GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Po publikování obsahu přidejte značku s názvem `GuestConfigPolicyCertificateValidation` a hodnotou `enabled` do všech virtuálních počítačů, kde by mělo být požadováno podepisování kódu. Tato značka se dá doručovat ve velkém rozsahu pomocí Azure Policy. Podívejte se na ukázku [použít značku a její výchozí hodnotu](../samples/apply-tag-default-value.md) . Jakmile je tato značka na místě, definice zásady vytvořená pomocí rutiny `New-GuestConfigurationPolicy` umožní požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="preview-troubleshooting-guest-configuration-policy-assignments"></a>Tisk Řešení potíží s přiřazením zásad konfigurace hostů

Nástroj je k dispozici ve verzi Preview, který vám pomůže při řešení potíží s Azure Policy přiřazení konfigurace hostů. Nástroj je ve verzi Preview a byl publikován do Galerie prostředí PowerShell jako název modulu [Poradce při potížích s konfigurací hosta](https://www.powershellgallery.com/packages/GuestConfigurationTroubleshooter/).

Další informace o rutinách v tomto nástroji získáte pomocí příkazu Get-Help v prostředí PowerShell k zobrazení integrovaných pokynů. Jak nástroj načítá časté aktualizace, což je nejlepší způsob, jak získat nejnovější informace.

## <a name="next-steps"></a>Další kroky

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](getting-compliance-data.md).