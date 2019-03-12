---
title: Požadavky – Azure Disk Encryption pro virtuální počítače IaaS | Dokumentace Microsoftu
description: Tento článek obsahuje požadavky pro používání Microsoft Azure Disk Encryption pro virtuální počítače IaaS.
author: mestew
ms.service: security
ms.topic: article
ms.author: mstewart
ms.date: 03/04/2019
ms.custom: seodec18
ms.openlocfilehash: d10a73eae3546ea0bbf7b6cad86838063ec48520
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2019
ms.locfileid: "57760238"
---
# <a name="azure-disk-encryption-prerequisites"></a>Požadavky Azure Disk Encryption

 Tento článek, požadované součásti služby Azure Disk Encryption, vysvětluje položky, které musí být na místě, abyste mohli používat Azure Disk Encryption. Azure Disk Encryption je integrovaná s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) ke správě šifrovacích klíčů. Můžete použít [prostředí Azure PowerShell](/powershell/azure/overview), [rozhraní příkazového řádku Azure](/cli/azure/), nebo [webu Azure portal](https://portal.azure.com) ke konfiguraci Azure Disk Encryption.

Dříve než povolíte pro podporované scénáře, které byly zmíněny v Azure Disk Encryption ve virtuálních počítačích Azure IaaS [přehledu Azure Disk Encryption](azure-security-disk-encryption-overview.md) článek, je nutné mít požadavky na místě. 

> [!WARNING]
> - Pokud jste už dřív použili [Azure Disk Encryption pomocí Azure AD app](azure-security-disk-encryption-prerequisites-aad.md) pro šifrování tento virtuální počítač, budete muset pokračovat tuto možnost použijte k šifrování virtuálního počítače. Nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) na tento šifrovaný virtuální počítač jako tato akce není podporovaný scénář význam přepnutí mimo aplikaci AAD pro tento šifrovaný virtuální počítač se zatím nepodporuje.
> - Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné. Musíte mít aktivní předplatné Azure platnou k vytváření prostředků v Azure v podporovaných oblastech.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="bkmk_OSs"></a> Podporované operační systémy
Azure Disk Encryption je podporována v následujících operačních systémech:

- Verze Windows serveru: Windows Server 2008 R2, Windows Server 2012, Windows Server 2012 R2 a Windows serveru 2016.
    - Pro Windows Server 2008 R2 musíte mít rozhraní .NET Framework 4.5 nainstalované před povolením šifrování v Azure. Nainstalujte ji z webu Windows Update, s volitelnou aktualizaci Microsoft .NET Framework 4.5.2 x64 systémů Windows Server 2008 R2 ([KB2901983](https://support.microsoft.com/kb/2901983)).    
- Verze klientů Windows: Klient Windows 8 a Windows 10 klient.
- Azure Disk Encryption je pouze podporované na konkrétní Galerie Azure Linux server distribucích a verzích. Seznam aktuálně podporovaných verzí najdete [nejčastější dotazy týkající se Azure Disk Encryption](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport).
- Azure Disk Encryption vyžaduje, aby váš trezor klíčů a virtuální počítače jsou umístěny ve stejné oblasti Azure a předplatné. Konfigurace prostředků v oblastech způsobí selhání při povolování funkce Azure Disk Encryption.

## <a name="bkmk_LinuxPrereq"></a> Další požadavky pro virtuální počítače IaaS s Linuxem 

- Azure Disk Encryption pro Linux vyžaduje 7 GB paměti RAM ve virtuálním počítači povolit šifrování disku operačního systému na [podporované obrázky](azure-security-disk-encryption-faq.md#bkmk_LinuxOSSupport). Po dokončení procesu šifrování disku operačního systému virtuálního počítače může být nakonfigurován pro spouštění s méně paměti.
- Před povolením šifrování, třeba správně zobrazeny v /etc/fstab datových disků k šifrování. Použijte název zařízení trvalé bloku pro tuto položku jako zařízení, které názvy ve formátu "/ dev/sdX" nelze spoléhat na přidruženy stejném disku mezi restartováními, zejména po šifrování se použije. Další podrobnosti o tomto chování najdete v článku: [Řešení potíží s změny názvu zařízení virtuálního počítače s Linuxem](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Ujistěte se, že nastavení /etc/fstab jsou správně nakonfigurovány pro připojení. Tato nastavení nakonfigurujete, spusťte připojení – příkaz nebo restartujte virtuální počítač a aktivuje tímto způsobem opětovné připojení. Jakmile, která se dokončí, zkontrolujte výstup příkazu lsblk k ověření, že na jednotce je pořád připojený. 
    - Pokud soubor /etc/fstab není správně připojit jednotku před povolením šifrování, Azure Disk Encryption nebude možné ji správně připojit.
    - Azure Disk Encryption proces přesune informace připojení mimo /etc/fstab a do jeho vlastní konfigurační soubor jako součást procesu šifrování. Není možné dospod vidět, že položka chybí /etc/fstab po data šifrování jednotky dokončí.
    -  Po restartování počítače bude trvat dobu procesu Azure Disk Encryption pro připojení nově šifrovanými disky. Bude okamžitě k dispozici po restartu. Proces potřebuje čas ke spuštění, odemčení a pak připojte šifrované jednotky, teprve pak ji bude k dispozici pro jiné procesy pro přístup k. Tento proces může trvat déle než minutu po restartování počítače v závislosti na charakteristikách systému.

Příklad příkazy, které je možné připojit datové disky a vytvořte nezbytné/etc/fstab položky lze nalézt v [řádky 244 248 tohoto souboru skriptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 


## <a name="bkmk_GPO"></a> Sítě a zásad skupiny

**Povolit Azure Disk Encryption funkce, virtuální počítače IaaS musí splňovat následující požadavky na konfiguraci koncového bodu sítě:**
  - Získá token pro připojení k vašemu trezoru klíčů, musí být schopný se připojit k Azure Active Directory koncový bod, virtuálních počítačů IaaS \[login.microsoftonline.com\].
  - Zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS může připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit k koncový bod služby Azure storage, který je hostitelem úložiště rozšíření Azure a účet úložiště Azure, který je hostitelem souborů virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure na Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo pro povolení odchozích připojení k IP adres. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../key-vault/key-vault-access-behind-firewall.md).    


**Zásady skupiny:**
 - Řešení Azure Disk Encryption používá ochrana externí klíče nástroje BitLocker pro virtuální počítače IaaS s Windows. Pro virtuální počítače připojené k doméně, push nemáte žádné zásady skupiny, které vynucují ochrany pomocí čipu TPM. Informace o zásadách skupiny na "Povolit BitLocker bez kompatibilního čipu TPM" najdete v tématu [odkaz zásad skupiny Bitlockeru](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Zásady nástroje BitLocker na virtuálních počítačích připojených k doméně pomocí zásad vlastní skupiny, musí obsahovat následující nastavení: [Konfigurace úložiště uživatele bitlockeru informace recovery -> Povolit 256bitový obnovovací klíč](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption selže, když jsou nekompatibilní nastavení zásad vlastní skupiny pro BitLocker. Na počítačích, které nebyly k dispozici nastavení správné zásady, použijí nové zásady vynutí nové zásady aktualizace (gpupdate.exe/Force) a následného restartování může být nutné.

- Azure Disk Encryption se nezdaří, pokud zásady skupiny na úrovni domény blokuje algoritmus AES-CBC, který se používá nástrojem Bitlocker.


## <a name="bkmk_PSH"></a> Prostředí Azure PowerShell
[Prostředí Azure PowerShell](/powershell/azure/overview) poskytuje sadu rutin, které používá [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) modelu pro správu vašich prostředků Azure. Můžete ho použít ve svém prohlížeči prostřednictvím [Azure Cloud Shell](../cloud-shell/overview.md), nebo ho můžete nainstalovat na místním počítači použít v jakékoli relaci Powershellu pomocí následujícího postupu. Pokud už ho máte nainstalovaný místně, ujistěte se, že používáte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [prostředí Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instalace Azure Powershellu pro použití v místním počítači (volitelné): 
1. Postupujte podle pokynů v odkazech pro váš operační systém, pokračujte ale zbytek postupu níže.      
    - [Instalace a konfigurace Azure Powershellu](/powershell/azure/install-az-ps). 
        - Instalace modulu PowerShellGet, prostředí Azure PowerShell a načtení modulu Az. 

2. Ověřte nainstalovaných verzí modulu Az. V případě potřeby [aktualizace modulu Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Doporučuje se použít nejnovější verzi modulu Az.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Přihlaste se k Azure s využitím [připojit AzAccount](/powershell/module/az.accounts/connect-azaccount) rutiny.
     
     ```azurepowershell-interactive
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

4.  V případě potřeby zkontrolujte [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a> Instalace rozhraní příkazového řádku Azure pro použití v místním počítači (volitelné)

[Příkazového řádku Azure CLI 2.0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. Rozhraní příkazového řádku je určené pro flexibilní dotazování na data, podporují dlouho běžící operace jako neblokující procesy a usnadňovalo skriptování. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu.

1. [Instalace Azure CLI](/cli/azure/install-azure-cli) pro použití v místním počítači (volitelné):

2. Zkontrolujte nainstalovanou verzi.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Přihlaste se k Azure s využitím [az login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. Kontrola [Začínáme s Azure CLI 2.0](/cli/azure/get-started-with-azure-cli) v případě potřeby. 


## <a name="prerequisite-workflow-for-key-vault"></a>Požadované pracovní postup pro Key Vault
Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption Key Vault a Azure AD, můžete použít [skript prostředí PowerShell pro Azure Disk Encryption požadované součásti](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Další informace o používání skriptu požadavky najdete v článku [šifrování virtuálního počítače Quickstart](quick-encrypt-vm-powershell.md) a [Azure Disk Encryption příloha](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. V případě potřeby vytvořte skupinu prostředků.
2. Vytvoření trezoru klíčů 
3. Nastavení služby key vault pokročilé zásady přístupu.

>[!WARNING]
>Před odstraněním služby key vault, ujistěte se, že šifrování není žádné stávající virtuální počítače s ním. Chcete-li zabránit nechtěnému odstranění, trezor [povolit obnovitelné odstranění](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) a [zámek prostředku](../azure-resource-manager/resource-group-lock-resources.md) v trezoru. 
 
## <a name="bkmk_KeyVault"></a> Vytvoření trezoru klíčů 
Azure Disk Encryption je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby key vault. Můžete vytvořit trezor klíčů nebo použijte již existující pro Azure Disk Encryption. Další informace o trezorů klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md) a [zabezpečení trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md). Šablony Resource Manageru, Azure Powershellu nebo rozhraní příkazového řádku Azure CLI slouží k vytvoření služby key vault. 


>[!WARNING]
>Pokud chcete mít jistotu, že šifrování tajných kódů není překračují hranice regionální, Azure Disk Encryption musí být umístěné ve stejné oblasti služby Key Vault a virtuální počítače. Vytvoření a použití služby Key Vault, která je ve stejné oblasti jako virtuální počítač k šifrování. 


### <a name="bkmk_KVPSH"></a> Vytvoření služby key vault pomocí Powershellu

Můžete vytvořit trezor klíčů s použitím prostředí Azure PowerShell [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) rutiny. Další rutiny pro Key Vault najdete v části [Az.KeyVault](/powershell/module/az.keyvault/). 

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. V případě potřeby vytvořte novou skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Seznam datacentrum v umístění, použijte [Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Vytvoření nové služby key vault pomocí [AzKeyVault nový](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Poznámka: **název trezoru**, **název skupiny prostředků**, **ID prostředku**, **identifikátor URI trezoru**a **ID objektu** pro pozdější použití, která se vrátí, když šifrování disků. 


### <a name="bkmk_KVCLI"></a> Vytvoření služby key vault pomocí Azure CLI
Váš trezor klíčů s použitím rozhraní příkazového řádku Azure můžete spravovat [az keyvault](/cli/azure/keyvault#commands) příkazy. Chcete-li vytvořit trezor klíčů, použijte [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create).

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. V případě potřeby vytvořte novou skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az-group-create). Chcete-li vypsat umístění, použijte [az účet list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Vytvoření nové služby key vault pomocí [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Poznámka: **Název_trezoru** (název), **název skupiny prostředků**, **ID prostředku** (ID), **identifikátor URI trezoru**a **ID objektu** , která se vrátí k použití později. 

### <a name="bkmk_KVRM"></a> Vytvoření trezoru klíčů pomocí šablony Resource Manageru

Můžete vytvořit trezor klíčů pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Na šablony Azure pro rychlý start, klikněte na tlačítko **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, služby Key Vault název, ID objektu, právní podmínky a smlouvy a klikněte na **nákupní**. 


## <a name="bkmk_KVper"></a> Nastavení trezoru klíčů pokročilé zásady přístupu
Platforma Azure potřebuje přístup k šifrování klíčů nebo tajných klíčů v trezoru klíčů, aby byly k dispozici pro virtuální počítač pro spuštění a dešifrování svazky. Povolit šifrování disku v trezoru klíčů nebo nasazení se nezdaří.  

### <a name="bkmk_KVperPSH"></a> Nastavení trezoru klíčů pokročilé zásady přístupu pomocí Azure Powershellu
 Použijte rutinu prostředí PowerShell služby key vault [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) povolit šifrování disku pro trezor klíčů.

  - **Povolení služby Key Vault pro šifrování disku:** EnabledForDiskEncryption je vyžadován pro Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **Povolení služby Key Vault pro nasazení, v případě potřeby:** Umožňuje zprostředkovateli prostředků Microsoft.Compute. k načítání tajných kódů z trezoru klíčů, když tento trezor klíčů se odkazuje v vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **Povolení služby Key Vault pro šablonu nasazení, v případě potřeby:** Umožňuje získat tajné kódy z trezoru klíčů při tento trezor klíčů se odkazuje v nasazení šablony Azure Resource Manageru.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Nastavení trezoru klíčů pokročilé zásady přístupu pomocí Azure CLI
Použití [az keyvault update](/cli/azure/keyvault#az-keyvault-update) povolit šifrování disku pro trezor klíčů. 

 - **Povolení služby Key Vault pro šifrování disku:** Povolené pro šifrování disků je povinný. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **Povolení služby Key Vault pro nasazení, v případě potřeby:** Umožňuje zprostředkovateli prostředků Microsoft.Compute. k načítání tajných kódů z trezoru klíčů, když tento trezor klíčů se odkazuje v vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **Povolení služby Key Vault pro šablonu nasazení, v případě potřeby:** Povolit správce prostředků k načítání tajných kódů z trezoru.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Nastavení trezoru klíčů pokročilé zásady přístupu k webu Azure portal

1. Vyberte váš trezor klíčů, přejděte na **zásady přístupu**, a **kliknutím zobrazíte pokročilé zásady přístupu**.
2. Vyberte políčko **povolení přístupu k Azure Disk Encryption pro šifrování svazku**.
3. Vyberte **povolit přístup k Azure Virtual Machines pro nasazení** a/nebo **povolit přístup k Azure Resource Manageru pro nasazení šablony**, v případě potřeby. 
4. Klikněte na **Uložit**.

![Azure key vaultu pokročilé zásady přístupu](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Nastavit šifrovací klíč klíče (volitelné)
Pokud chcete použít šifrovací klíč klíče (KEK) pro další úroveň zabezpečení pro šifrovací klíče, přidejte do trezoru klíčů KEK. Použití [přidat AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) rutina pro vytvoření šifrovací klíč klíče v trezoru klíčů. Můžete také importovat KEK z vaší místní správy k klíče HSM. Další informace najdete v tématu [dokumentace ke službě Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault.

* Při generování klíčů, použijte typ klíče RSA. Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

* Tajný kód trezoru klíčů a adres URL KEK musí být označené verzí. Azure vynucuje toto omezení správy verzí. Platný tajný kód a adresy URL KEK viz následující příklady:

  * Příklad platná adresa URL tajného kódu:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příkladem platné adresy URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů služby key vault a KEK adresy URL. Příkladem adresy URL není podporováno a podporované služby key vault najdete v následující příklady:

  * Adresa URL nemůže být přijata trezoru klíčů  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adresa URL přijatelné služby key vault:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a> Nastavit šifrovací klíč klíče pomocí Azure Powershellu 
Před použitím skriptu prostředí PowerShell, měli byste se seznámit s požadavky Azure Disk Encryption pochopit postup ve skriptu. Ukázkový skript může být nutné změny pro vaše prostředí. Tento skript vytvoří všechny požadavky Azure Disk Encryption a zašifruje stávající virtuální počítače IaaS obtékání šifrovací klíč disku s použitím šifrovací klíč klíče. 

 ```powershell
 # Step 1: Create a new resource group and key vault in the same location.
     # Fill in 'MyLocation', 'MyKeyVaultResourceGroup', and 'MySecureVault' with your values.
     # Use Get-AzLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzResourceGroup
     
     $Loc = 'MyLocation';
     $KVRGname = 'MyKeyVaultResourceGroup';
     $KeyVaultName = 'MySecureVault'; 
     New-AzResourceGroup –Name $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname).VaultUri;
     
 #Step 2: Enable the vault for disk encryption.
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
      
 #Step 3: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 4: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
     $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```


 
## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Aktivace Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Aktivace Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md)

