---
title: Požadavky – Azure Disk Encryption pro virtuální počítače IaaS | Dokumentace Microsoftu
description: Tento článek obsahuje požadavky pro používání Microsoft Azure Disk Encryption pro virtuální počítače IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/25/2019
ms.custom: seodec18
ms.openlocfilehash: 423fad943190232d9e5e674b98b62f4f0dffb8ae
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2019
ms.locfileid: "68728753"
---
# <a name="azure-disk-encryption-prerequisites"></a>Požadavky Azure Disk Encryption

Tento článek, požadované součásti služby Azure Disk Encryption, vysvětluje položky, které musí být na místě, abyste mohli používat Azure Disk Encryption. Azure Disk Encryption je integrovaná s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) ke správě šifrovacích klíčů. Můžete použít [prostředí Azure PowerShell](/powershell/azure/overview), [rozhraní příkazového řádku Azure](/cli/azure/), nebo [webu Azure portal](https://portal.azure.com) ke konfiguraci Azure Disk Encryption.

Dříve než povolíte pro podporované scénáře, které byly zmíněny v Azure Disk Encryption ve virtuálních počítačích Azure IaaS [přehledu Azure Disk Encryption](azure-security-disk-encryption-overview.md) článek, je nutné mít požadavky na místě. 

> [!WARNING]
> - Pokud jste už dříve použili [Azure Disk Encryption se službou Azure AD](azure-security-disk-encryption-prerequisites-aad.md) k šifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD.
> - Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné. Musíte mít aktivní předplatné Azure platnou k vytváření prostředků v Azure v podporovaných oblastech.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Azure Disk Encryption je k dispozici na virtuálních počítačích, které splňují tyto minimální požadavky na paměť:

| Virtuální počítač | Minimální požadavek na paměť |
|--|--|
| Virtuální počítače s Windows | 2 GB |
| Virtuální počítače se systémem Linux jenom při šifrování datových svazků| 2 GB |
| Virtuální počítače se systémem Linux při šifrování dat a svazků operačních systémů a v případě použití systému souborů root (/) je 4 GB nebo méně | 8 GB |
| Virtuální počítače se systémem Linux při šifrování dat a svazků operačních systémů a využití systému souborů root (/) je větší než 4 GB | Použití kořenového souborového systému * 2. Například 16 GB použití kořenového systému souborů vyžaduje aspoň 32 GB paměti RAM. |

Po dokončení procesu šifrování disku s operačním systémem u virtuálních počítačů se systémem Linux lze virtuální počítač nakonfigurovat tak, aby běžel s méně paměti. 

> [!NOTE]
> Pro [Virtual Machine Scale Sets](../virtual-machine-scale-sets/index.yml)není k dispozici šifrování disku s operačním systémem Linux.

Azure Disk Encryption je k dispozici také pro virtuální počítače s Premium Storage. 

## <a name="supported-operating-systems"></a>Podporované operační systémy

### <a name="windows"></a>Windows

- Klient Windows: Windows 8 a novější.
- Windows Server: Windows Server 2008 R2 a novější.  
 
> [!NOTE]
> Systém Windows Server 2008 R2 vyžaduje instalaci .NET Framework 4,5 pro šifrování; nainstalujte ji z web Windows Update s volitelnou aktualizací Microsoft .NET Framework 4.5.2 pro systémy Windows Server 2008 R2 x64 ([KB2901983](https://www.catalog.update.microsoft.com/Search.aspx?q=KB2901983)).  
>  
> Windows Server 2012 R2 Core a Windows Server 2016 Core vyžadují, aby byla na virtuálním počítači nainstalovaná součást BdeHdCfg pro šifrování.


### <a name="linux"></a>Linux 

Azure Disk Encryption je podporovaná u podmnožiny [distribucí systému Linux schváleného službou Azure](../virtual-machines/linux/endorsed-distros.md), což je podmnožina všech možných distribucí serveru se systémem Linux.

![Vennův diagram distribucí serveru Linux, které podporují Azure Disk Encryption](./media/azure-security-disk-encryption-faq/ade-supported-distros.png)

Distribuce serverů Linux, které nejsou schválené v Azure, nepodporují Azure Disk Encryption a, z nichž jsou schválené, Azure Disk Encryption podporují jenom následující distribuce a verze:

| Linuxové distribuce | Verze | Typ svazku podporovaný pro šifrování|
| --- | --- |--- |
| Ubuntu | 18,04| Disk operačního systému a dat |
| Ubuntu | 16.04| Disk operačního systému a dat |
| Ubuntu | 14.04.5</br>[s vyladěným jádrem Azure se aktualizovala na 4,15 nebo novější.](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Disk operačního systému a dat |
| RHEL | 7,6 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.5 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.4 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.3 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.2 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 6.8 | Datový disk (viz poznámka níže) |
| RHEL | 6.7 | Datový disk (viz poznámka níže) |
| CentOS | 7,6 | Disk operačního systému a dat |
| CentOS | 7.5 | Disk operačního systému a dat |
| CentOS | 7.4 | Disk operačního systému a dat |
| CentOS | 7.3 | Disk operačního systému a dat |
| CentOS | 7.2N | Disk operačního systému a dat |
| CentOS | 6.8 | Datový disk |
| openSUSE | 42,3 | Datový disk |
| SLES | 12-SP4 | Datový disk |
| SLES | 12-SP3 | Datový disk |

> [!NOTE]
> Nová implementace ADE je podporovaná pro RHEL operační systém a datový disk pro Image RHEL7 s průběžnými platbami. ADE se v současné době nepodporuje pro image RHEL s využitím vlastních předplatných (BYOS). Další informace najdete v tématu [Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md) .

- Azure Disk Encryption vyžaduje, aby váš trezor klíčů a virtuální počítače jsou umístěny ve stejné oblasti Azure a předplatné. Konfigurace prostředků v oblastech způsobí selhání při povolování funkce Azure Disk Encryption.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Další požadavky pro virtuální počítače s IaaS Linux 

- Azure Disk Encryption vyžaduje, aby byly v systému přítomné moduly dm-crypt a vfat. Odebráním nebo zakázáním VFAT z výchozí image znemožníte systému číst klíč a získat klíč potřebný k odemknutí disků při dalším restartování. Kroky pro posílení zabezpečení systému, které odebírají modul VFAT ze systému, nejsou kompatibilní s Azure Disk Encryption. 
- Před povolením šifrování, třeba správně zobrazeny v /etc/fstab datových disků k šifrování. Použijte název zařízení trvalé bloku pro tuto položku jako zařízení, které názvy ve formátu "/ dev/sdX" nelze spoléhat na přidruženy stejném disku mezi restartováními, zejména po šifrování se použije. Další podrobnosti o tomto chování najdete v těchto tématech: [Řešení potíží se změnami názvu zařízení virtuálních počítačů se systémem Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md)
- Ujistěte se, že nastavení /etc/fstab jsou správně nakonfigurovány pro připojení. Tato nastavení nakonfigurujete, spusťte připojení – příkaz nebo restartujte virtuální počítač a aktivuje tímto způsobem opětovné připojení. Jakmile, která se dokončí, zkontrolujte výstup příkazu lsblk k ověření, že na jednotce je pořád připojený. 
  - Pokud soubor /etc/fstab není správně připojit jednotku před povolením šifrování, Azure Disk Encryption nebude možné ji správně připojit.
  - Azure Disk Encryption proces přesune informace připojení mimo /etc/fstab a do jeho vlastní konfigurační soubor jako součást procesu šifrování. Není možné dospod vidět, že položka chybí /etc/fstab po data šifrování jednotky dokončí.
  - Před zahájením šifrování nezapomeňte zastavit všechny služby a procesy, které by mohly být zapsány do připojených datových disků, a zakázat je, aby se po restartování nerestartoval automaticky. V těchto oddílech můžou být soubory otevřené a brání tak postupu šifrování je znovu připojit, což způsobí selhání šifrování. 
  - Po restartování počítače bude trvat dobu procesu Azure Disk Encryption pro připojení nově šifrovanými disky. Bude okamžitě k dispozici po restartu. Proces potřebuje čas ke spuštění, odemčení a pak připojte šifrované jednotky, teprve pak ji bude k dispozici pro jiné procesy pro přístup k. Tento proces může trvat déle než minutu po restartování počítače v závislosti na charakteristikách systému.

Příklad příkazy, které je možné připojit datové disky a vytvořte nezbytné/etc/fstab položky lze nalézt v [řádky 244 248 tohoto souboru skriptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a> Sítě a zásad skupiny

**Povolit Azure Disk Encryption funkce, virtuální počítače IaaS musí splňovat následující požadavky na konfiguraci koncového bodu sítě:**
  - Získá token pro připojení k vašemu trezoru klíčů, musí být schopný se připojit k Azure Active Directory koncový bod, virtuálních počítačů IaaS \[login.microsoftonline.com\].
  - Zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS může připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit k koncový bod služby Azure storage, který je hostitelem úložiště rozšíření Azure a účet úložiště Azure, který je hostitelem souborů virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure na Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo pro povolení odchozích připojení k IP adres. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../key-vault/key-vault-access-behind-firewall.md).    


**Zásady skupiny:**
 - Řešení Azure Disk Encryption používá ochrana externí klíče nástroje BitLocker pro virtuální počítače IaaS s Windows. Pro virtuální počítače připojené k doméně, push nemáte žádné zásady skupiny, které vynucují ochrany pomocí čipu TPM. Informace o zásadách skupiny na "Povolit BitLocker bez kompatibilního čipu TPM" najdete v tématu [odkaz zásad skupiny Bitlockeru](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastními zásadami skupiny musí zahrnovat toto nastavení: [Konfigurace uživatelského úložiště informací pro obnovení BitLockeru – > povoluje 256 obnovovací klíč](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro BitLocker nejsou kompatibilní. Na počítačích, které nebyly k dispozici nastavení správné zásady, použijí nové zásady vynutí nové zásady aktualizace (gpupdate.exe/Force) a následného restartování může být nutné.

- Azure Disk Encryption dojde k chybě, pokud zásady skupiny na úrovni domény blokují algoritmus AES-CBC, který je používán nástrojem BitLocker.


## <a name="bkmk_PSH"></a> Prostředí Azure PowerShell
[Prostředí Azure PowerShell](/powershell/azure/overview) poskytuje sadu rutin, které používá [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) modelu pro správu vašich prostředků Azure. Můžete ho použít ve svém prohlížeči prostřednictvím [Azure Cloud Shell](../cloud-shell/overview.md), nebo ho můžete nainstalovat na místním počítači použít v jakékoli relaci Powershellu pomocí následujícího postupu. Pokud už ho máte nainstalovaný místně, ujistěte se, že používáte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [prostředí Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instalace Azure Powershellu pro použití v místním počítači (volitelné): 
1. Postupujte podle pokynů v odkazech pro váš operační systém, pokračujte ale zbytek postupu níže.      
   - [Instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps). 
     - Nainstalujte PowerShellGet, Azure PowerShell a načtěte modul AZ Module. 

2. Ověřte nainstalované verze modulu AZ Module. V případě potřeby [aktualizace modulu Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
    Doporučuje se použít nejnovější verzi modulu AZ Module verze.

     ```powershell
     Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
     ```

3. Přihlaste se k Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
     
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
Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption Key Vault a Azure AD, můžete použít [skript prostředí PowerShell pro Azure Disk Encryption požadované součásti](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Další informace o používání skriptu požadavky najdete v článku [šifrování virtuálního počítače Quickstart](azure-disk-encryption-linux-powershell-quickstart.md) a [Azure Disk Encryption příloha](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

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

Trezor klíčů můžete vytvořit s Azure PowerShell pomocí rutiny [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Další rutiny pro Key Vault najdete v tématu [AZ. klíčů trezor](/powershell/module/az.keyvault/). 

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. V případě potřeby vytvořte novou skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  K vypsání umístění datového centra použijte [příkaz Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Vytvoření nového trezoru klíčů pomocí [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
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
 Pomocí rutiny PowerShellu pro Trezor klíčů [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) Povolte šifrování disku pro Trezor klíčů.

  - **Povolit Key Vault pro šifrování disku:** EnabledForDiskEncryption se vyžaduje pro Azure Disk Encryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **V případě potřeby povolte Key Vault pro nasazení:** Povolí poskytovateli prostředků Microsoft. COMPUTE načíst tajné kódy z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **V případě potřeby povolte Key Vault pro nasazení šablony:** Umožňuje Azure Resource Manager získávat tajné klíče z tohoto trezoru klíčů, když se tento trezor klíčů odkazuje v nasazení šablony.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Nastavení trezoru klíčů pokročilé zásady přístupu pomocí Azure CLI
Použití [az keyvault update](/cli/azure/keyvault#az-keyvault-update) povolit šifrování disku pro trezor klíčů. 

 - **Povolit Key Vault pro šifrování disku:** Je nutné povolit šifrování disku. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **V případě potřeby povolte Key Vault pro nasazení:** Povolí poskytovateli prostředků Microsoft. COMPUTE načíst tajné kódy z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **V případě potřeby povolte Key Vault pro nasazení šablony:** Povolí Správce prostředků načtení tajných kódů z trezoru.
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
Pokud chcete použít šifrovací klíč klíče (KEK) pro další úroveň zabezpečení pro šifrovací klíče, přidejte do trezoru klíčů KEK. Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte šifrovací klíč klíče v trezoru klíčů. Můžete také importovat KEK z vaší místní správy k klíče HSM. Další informace najdete v tématu [dokumentace ke službě Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault.

* Při generování klíčů použijte typ klíče RSA. Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

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
      
 #Step 3: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'HSM';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
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

