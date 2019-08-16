---
title: Postup vytvoření zásad konfigurace hostů
description: Naučte se vytvářet Azure Policy zásady konfigurace hostů pro virtuální počítače se systémem Windows nebo Linux.
author: DCtheGeek
ms.author: dacoulte
ms.date: 07/26/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.openlocfilehash: 131d6865c47a32bbefbfbd397a5f0f88dedc9c35
ms.sourcegitcommit: 0c906f8624ff1434eb3d3a8c5e9e358fcbc1d13b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/16/2019
ms.locfileid: "69543507"
---
# <a name="how-to-create-guest-configuration-policies"></a>Postup vytvoření zásad konfigurace hostů

Konfigurace hosta používá modul prostředku [Konfigurace požadovaného stavu](/powershell/dsc) (DSC) k vytvoření konfigurace pro auditování virtuálních počítačů Azure. Konfigurace DSC definuje podmínku, ve které by se měl virtuální počítač nacházet. Pokud se konfigurace nezdařila, je aktivován **audit** účinku zásad a virtuální počítač se považuje za nevyhovující.

[Konfiguraci hosta Azure Policy](/azure/governance/policy/concepts/guest-configuration) můžete použít jenom k auditování nastavení v rámci virtuálních počítačů. Náprava nastavení v rámci virtuálních počítačů ještě není dostupná.

Pomocí následujících akcí vytvořte vlastní konfiguraci pro ověření stavu virtuálního počítače Azure.

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

## <a name="create-custom-guest-configuration-configuration"></a>Vytvořit vlastní konfiguraci hosta

Prvním krokem k vytvoření vlastní zásady pro konfiguraci hosta je vytvoření konfigurace DSC. Přehled konceptů a terminologie DSC najdete v tématu [Přehled prostředí POWERSHELL DSC](/powershell/dsc/overview/overview).

### <a name="custom-guest-configuration-configuration-on-linux"></a>Konfigurace vlastní konfigurace hosta v systému Linux

Konfigurace DSC pro konfiguraci hosta v systému Linux používá `ChefInSpecResource` prostředek k tomu, aby modul poskytoval název definice [INSPEC](https://www.chef.io/inspec/) . **Název** je jediná požadovaná vlastnost prostředku.

V následujícím příkladu se vytvoří konfigurace s **názvem Směrný plán**, naimportuje se modul prostředků GuestConfiguration `ChefInSpecResource` a použije se tento prostředek, který nastaví název INSPEC definice na **Linux-patch-směrný plán**:

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

Následující příklad vytvoří konfiguraci s názvem **AuditBitLocker**, importuje modul prostředků **GuestConfiguration** `Service` a použije prostředek k auditu pro běžící službu:

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

`New-GuestConfigurationPackage` Rutina vytvoří balíček. Pro vytvoření vlastního balíčku se používá následující formát:

```azurepowershell-interactive
New-GuestConfigurationPackage -Name '{PackageName}' -Configuration '{PathToMOF}' `
    -Path '{OutputFolder}' -Verbose
```

`New-GuestConfigurationPackage` Parametry rutiny:

- **Název**: Název konfiguračního balíčku hosta
- **Konfigurace**: Úplná cesta ke zkompilované konfiguraci DSC dokumentu
- **Cesta**: Cesta k výstupní složce Tento parametr je volitelný. Pokud není zadaný, balíček se vytvoří v aktuálním adresáři.
- **ChefProfilePath**: Úplná cesta k profilu INSPEC. Tento parametr je podporován pouze při vytváření obsahu pro audit systému Linux.

Dokončený balíček musí být uložený v umístění, ke kterému mají přístup spravované virtuální počítače. Mezi příklady patří úložiště GitHub, úložiště Azure nebo Azure Storage. Pokud nechcete, aby balíček byl veřejný, můžete do adresy URL přidat [token SAS](../../../storage/common/storage-dotnet-shared-access-signature-part-1.md) . Můžete také implementovat [koncový bod služby](../../../storage/common/storage-network-security.md#grant-access-from-a-virtual-network) pro virtuální počítače v privátní síti, i když tato konfigurace platí pouze pro přístup k balíčku a nekomunikuje se službou.

### <a name="working-with-secrets-in-guest-configuration-packages"></a>Práce s tajnými kódy v konfiguračních balíčcích hosta

V konfiguraci Azure Policy hosta je optimální způsob, jak spravovat tajné klíče používané v době běhu, ukládat je do Azure Key Vault. Tento návrh se implementuje v rámci vlastních prostředků DSC.

Nejprve v Azure vytvořte spravovanou identitu přiřazenou uživatelem. Identita je používána virtuálními počítači pro přístup k tajným klíčům uloženým v Key Vault. Podrobný postup najdete v tématu [Vytvoření, vypsání nebo odstranění spravované identity přiřazené uživatelem pomocí Azure PowerShell](../../../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md).

Dále vytvořte instanci Key Vault. Podrobné pokyny najdete v tématu [nastavení a načtení tajného kódu – PowerShell](../../../key-vault/quick-create-powershell.md).
Přiřazením oprávnění k instanci udělte přístup k identitám přiřazeným uživateli k tajným klíčům uloženým v Key Vault. Podrobné pokyny najdete v tématu [nastavení a načtení tajného kódu – .NET](../../../key-vault/quick-create-net.md#assign-permissions-to-your-application-to-read-secrets-from-key-vault).

Pak přiřaďte k virtuálnímu počítači identitu přiřazenou uživateli. Podrobný postup najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí PowerShellu](../../../active-directory/managed-identities-azure-resources/qs-configure-powershell-windows-vm.md#user-assigned-managed-identity).
Ve velkém měřítku přiřaďte tuto identitu pomocí Azure Resource Manager přes Azure Policy. Podrobný postup najdete v tématu [Konfigurace spravovaných identit pro prostředky Azure na virtuálním počítači Azure pomocí šablony](../../../active-directory/managed-identities-azure-resources/qs-configure-template-windows-vm.md#assign-a-user-assigned-managed-identity-to-an-azure-vm).

Nakonec v rámci vlastního prostředku použijte ID klienta vygenerované výše pro přístup k Key Vault pomocí tokenu dostupného z počítače. Adresu URL třídy Key Vault lze předat prostředku jako vlastnosti, takže prostředek nebude nutné aktualizovat pro více prostředí nebo v případě, že je třeba změnit hodnoty. [](/powershell/dsc/resources/authoringresourcemof#creating-the-mof-schema) `client_id`

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

Po vytvoření konfiguračního balíčku, ale před jeho publikováním do Azure, můžete otestovat funkčnost balíčku z prostředí pro pracovní stanici nebo CI/CD. Modul GuestConfiguration zahrnuje rutinu `Test-GuestConfigurationPackage` , která načte stejného agenta ve vývojovém prostředí, protože se používá ve virtuálních počítačích Azure. Pomocí tohoto řešení můžete provádět testování integrací místně ještě předtím, než vydáte účtované/testovací/provozní prostředí.

```azurepowershell-interactive
Test-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Verbose
```

`Test-GuestConfigurationPackage` Parametry rutiny:

- **Název**: Název zásad konfigurace hostů.
- **Parametr**: Parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Cesta**: Úplná cesta k balíčku pro konfiguraci hosta

Rutina podporuje také vstup z kanálu PowerShellu. Přesměrování výstupu `New-GuestConfigurationPackage` rutiny `Test-GuestConfigurationPackage` do rutiny.

```azurepowershell-interactive
New-GuestConfigurationPackage -Name AuditWindowsService -Configuration .\DSCConfig\localhost.mof -Path .\package -Verbose | Test-GuestConfigurationPackage -Verbose
```

Další informace o tom, jak testovat pomocí parametrů, najdete v níže uvedené části [použití parametrů ve vlastních zásadách konfigurace hostů](/azure/governance/policy/how-to/guest-configuration-create#using-parameters-in-custom-guest-configuration-policies).

## <a name="create-the-azure-policy-definition-and-initiative-deployment-files"></a>Vytvoření souborů nasazení definice Azure Policy a iniciativa

Po vytvoření balíčku vlastní zásady konfigurace hosta a nahrání do umístění přístupného pro virtuální počítače vytvořte definici zásady konfigurace hosta pro Azure Policy. Rutina používá veřejně přístupný balíček vlastní zásady konfigurace hosta a vytvoří definici zásady **auditIfNotExists** a **deployIfNotExists.** `New-GuestConfigurationPolicy` Vytvoří se také definice iniciativy zásad, která zahrnuje definice obou zásad.

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

`New-GuestConfigurationPolicy` Parametry rutiny:

- **ContentUri**: Veřejné http (s) identifikátor URI balíčku obsahu konfigurace hosta
- **Zobrazovaný**název: Zobrazovaný název zásad
- **Popis**: Popis zásady
- **Parametr**: Parametry zásad, které jsou k dispozici ve formátu zatřiďovací tabulky.
- **Verze**: Verze zásady
- **Cesta**: Cílová cesta, kde se vytvářejí definice zásad
- **Platforma**: Cílová platforma (Windows/Linux) pro zásady konfigurace hosta a balíček obsahu.

Následující soubory vytvořil `New-GuestConfigurationPolicy`:

- **auditIfNotExists. JSON**
- **deployIfNotExists. JSON**
- **Iniciativa. JSON**

Výstup rutiny vrátí objekt, který obsahuje zobrazovaný název iniciativy a cestu k souborům zásad.

Pokud chcete tento příkaz použít k vytvoření uživatelského rozhraní pro vlastní projekt zásad, můžete provádět změny těchto souborů. Příkladem je změna oddílu if, který vyhodnotí, jestli je pro virtuální počítače přítomná konkrétní značka. Podrobnosti o vytváření zásad najdete v tématu [programové vytváření zásad](./programmatically-create.md).

### <a name="using-parameters-in-custom-guest-configuration-policies"></a>Použití parametrů ve vlastních zásadách konfigurace hostů

Konfigurace hosta podporuje přepsání vlastností konfigurace v době běhu. Tato funkce znamená, že hodnoty v souboru MOF v balíčku není nutné považovat za statické. Hodnoty přepsání jsou poskytovány prostřednictvím Azure Policy a neovlivňují způsob, jakým jsou vytvořeny nebo kompilovány konfigurace.

Rutiny `New-GuestConfigurationPolicy` a `Test-GuestConfigurationPolicyPackage` zahrnují parametr s názvem **Parameters**.
Tento parametr převezme definici zatřiďovací tabulky včetně všech podrobností o jednotlivých parametrech a automaticky vytvoří všechny požadované oddíly souborů použité k vytvoření každé definice Azure Policy.

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

V případě zásad pro Linux zahrňte `AttributesYmlContent` do konfigurace vlastnost a příslušné hodnoty odpovídajícím způsobem přepsat. Agent konfigurace hosta automaticky vytvoří soubor YaML používaný nespecifikací k ukládání atributů. Viz následující příklad.

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

Modul **GuestConfiguration** Resource nabízí způsob, jak v Azure vytvořit definice zásad a definice iniciativ v Azure jediným krokem prostřednictvím `Publish-GuestConfigurationPolicy` rutiny.
Rutina má pouze parametr **path** , který odkazuje na umístění tří souborů JSON vytvořených pomocí `New-GuestConfigurationPolicy`.

```azurepowershell-interactive
Publish-GuestConfigurationPolicy -Path '.\policyDefinitions' -Verbose
```

`Publish-GuestConfigurationPolicy` Rutina akceptuje cestu z kanálu PowerShellu. Tato funkce znamená, že můžete vytvořit soubory zásad a publikovat je v jedné sadě příkazů s použitím kanálu.

```azurepowershell-interactive
New-GuestConfigurationPolicy -ContentUri 'https://storageaccountname.blob.core.windows.net/packages/AuditBitLocker.zip?st=2019-07-01T00%3A00%3A00Z&se=2024-07-01T00%3A00%3A00Z&sp=rl&sv=2018-03-28&sr=b&sig=JdUf4nOCo8fvuflOoX%2FnGo4sXqVfP5BYXHzTl3%2BovJo%3D' -DisplayName 'Audit BitLocker service.' -Description 'Audit if the BitLocker service is not enabled on Windows machine.' -Path '.\policyDefinitions' -Platform 'Windows' -Version 1.2.3.4 -Verbose | ForEach-Object {$_.Path} | Publish-GuestConfigurationPolicy -Verbose
```

S definicemi zásad a iniciativ vytvořenými v Azure se posledním krokem přiřadí iniciativa. Podívejte se, jak přiřadit iniciativu k [portálu](../assign-policy-portal.md), rozhraní příkazového [řádku Azure](../assign-policy-azurecli.md)a [Azure PowerShell](../assign-policy-powershell.md).

> [!IMPORTANT]
> Zásady konfigurace hosta se musí **vždy** přiřadit pomocí iniciativy, která kombinuje zásady _AuditIfNotExists_ a _DeployIfNotExists_ . Pokud je přiřazena pouze zásada _AuditIfNotExists_ , požadavky nejsou nasazeny a zásady vždy ukazují, že jsou servery "0" kompatibilní.

## <a name="policy-lifecycle"></a>Životní cyklus zásad

Po publikování vlastního Azure Policy pomocí vlastního balíčku obsahu jsou k dispozici dvě pole, která je potřeba aktualizovat, pokud chcete publikovat novou verzi.

- **Verze**: Když spustíte rutinu rutiny `New-GuestConfigurationPolicy` , musíte zadat číslo verze, které je větší než aktuálně publikované.  Tím se aktualizuje verze přiřazení konfigurace hosta v novém souboru zásad, takže rozšíření rozpozná, že balíček se aktualizoval.
- **contentHash**: Tato `New-GuestConfigurationPolicy` rutina se automaticky aktualizuje pomocí rutiny.  Jedná se o hodnotu hash balíčku, kterou `New-GuestConfigurationPackage`vytvořil.  Toto musí být správné pro `.zip` soubor, který publikujete.  Je-li `contentUri` aktualizována pouze vlastnost, například v případě, že by někdo mohl provést ruční změnu definice zásady z portálu, rozšíření nepřijme balíček obsahu.

Nejjednodušším způsobem, jak vydat aktualizovaný balíček, je opakovat postup popsaný v tomto článku a zadat aktualizované číslo verze.
Tím budou zaručeny správné aktualizace všech vlastností.

## <a name="converting-windows-group-policy-content-to-azure-policy-guest-configuration"></a>Převod obsahu Windows Zásady skupiny na Azure Policy konfiguraci hosta

Konfigurace hosta, při auditování počítačů s Windows, je implementovaná syntaxe konfigurace požadovaného stavu prostředí PowerShell.
Komunita DSC zveřejnila nástroje pro převod exportovaných šablon Zásady skupiny do formátu DSC.
Pomocí tohoto nástroje spolu s rutinami konfigurace hosta, které jsou popsané výše, můžete převést Windows Zásady skupiny obsah a balíček/publikovat pro Azure Policy k auditování.
Podrobnosti o používání tohoto nástroje najdete v článku [rychlý Start: Převeďte Zásady skupiny do](/powershell/dsc/quickstarts/gpo-quickstart)DSC.
Až se obsah převede, výše uvedené kroky pro vytvoření pakcage a jeho publikování jako Azure Policy budou stejné jako u jakéhokoli obsahu DSC.

## <a name="optional-signing-guest-configuration-packages"></a>VOLITELNÉ Podepisování balíčků konfigurace hosta

Vlastní zásady konfigurace hosta ve výchozím nastavení používají SHA256 hash k ověření, že se balíček zásad nezměnil z okamžiku, kdy byl publikován na serveru, který je auditován.
V případě potřeby mohou zákazníci také použít certifikát k podepisování balíčků a vynucení rozšíření konfigurace hosta pouze k povolení podepsaného obsahu.

Chcete-li povolit tento scénář, je třeba provést dva kroky. Spusťte rutinu pro podepsání balíčku obsahu a přidejte značku k virtuálním počítačům, které by měly vyžadovat podepsání kódu.

Chcete-li použít funkci ověřování podpisem, `Protect-GuestConfigurationPackage` spusťte rutinu pro podepsání balíčku před jeho publikováním. Tato rutina vyžaduje certifikát pro podepsání kódu.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert") }
Protect-GuestConfigurationPackage -Path .\package\AuditWindowsService\AuditWindowsService.zip -Certificate $Cert -Verbose
```

`Protect-GuestConfigurationPackage` Parametry rutiny:

- **Cesta**: Úplná cesta k balíčku pro konfiguraci hosta
- **Certifikát**: Certifikát pro podepsání kódu pro podepsání balíčku. Tento parametr je podporován pouze při podepisování obsahu pro systém Windows.
- **PrivateGpgKeyPath**: Privátní cesta ke klíči GPG Tento parametr je podporován pouze při podepisování obsahu pro Linux.
- **PublicGpgKeyPath**: Cesta k veřejnému klíči GPG Tento parametr je podporován pouze při podepisování obsahu pro Linux.

Agent GuestConfiguration očekává, že se veřejný klíč certifikátu nachází v počítačích se systémem Windows v části Důvěryhodné kořenové certifikační autority a v `/usr/local/share/ca-certificates/extra` cestě k počítačům se systémem Linux. Aby mohl uzel ověřit podepsaný obsah, před použitím vlastních zásad nainstalujte na virtuální počítač veřejný klíč certifikátu. Tento proces se dá provést pomocí libovolné techniky uvnitř virtuálního počítače nebo pomocí Azure Policy. [Tady je uvedena](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vm-push-certificate-windows)Ukázková šablona.
Zásady přístupu Key Vault musí umožňovat poskytovateli výpočetních prostředků přístup k certifikátům během nasazení. Podrobný postup najdete v tématu [nastavení Key Vault pro virtuální počítače v Azure Resource Manager](../../../virtual-machines/windows/key-vault-setup.md#use-templates-to-set-up-key-vault).

Následuje příklad exportu veřejného klíče z podpisového certifikátu pro import do virtuálního počítače.

```azurepowershell-interactive
$Cert = Get-ChildItem -Path cert:\LocalMachine\My | Where-Object {($_.Subject-eq "CN=mycert3") } | Select-Object -First 1
$Cert | Export-Certificate -FilePath "$env:temp\DscPublicKey.cer" -Force
```

Dobrá Reference k vytváření GPG klíčů pro použití s virtuálními počítači se systémem Linux je poskytována článkem na GitHubu, který [generuje nový klíč GPG](https://help.github.com/en/articles/generating-a-new-gpg-key).

Po publikování obsahu přidejte značku s názvem `GuestConfigPolicyCertificateValidation` a hodnotou `enabled` do všech virtuálních počítačů, kde by mělo být požadováno podepisování kódu. Tato značka se dá doručovat ve velkém rozsahu pomocí Azure Policy. Podívejte se na ukázku [použít značku a její výchozí hodnotu](../samples/apply-tag-default-value.md) .
Jakmile je tato značka nastavená, definice zásady vytvořená pomocí `New-GuestConfigurationPolicy` rutiny povolí požadavek prostřednictvím rozšíření konfigurace hosta.

## <a name="next-steps"></a>Další postup

- Přečtěte si o auditování virtuálních počítačů pomocí [Konfigurace hostů](../concepts/guest-configuration.md).
- Zjistěte, jak [programově vytvářet zásady](programmatically-create.md).
- Přečtěte si, jak [získat data o dodržování předpisů](getting-compliance-data.md).
