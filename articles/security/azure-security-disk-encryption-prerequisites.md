---
title: Požadavky – Azure Disk Encryption pro virtuální počítače s IaaS | Microsoft Docs
description: Tento článek popisuje předpoklady pro použití Microsoft Azureho šifrování disku pro virtuální počítače s IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 551918373f8292d798980600d6e0d43add55bd18
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828273"
---
# <a name="azure-disk-encryption-prerequisites"></a>Azure Disk Encryption předpoklady

Tento článek, Azure Disk Encryption předpoklady, vysvětluje položky, které je potřeba splnit, než bude možné používat Azure Disk Encryption. Azure Disk Encryption je integrována s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) , která vám pomůžou se správou šifrovacích klíčů. Azure Disk Encryption můžete nakonfigurovat pomocí [Azure PowerShell](/powershell/azure/overview), rozhraní příkazového [řádku Azure](/cli/azure/)nebo [Azure Portal](https://portal.azure.com) .

Než povolíte Azure Disk Encryption na virtuálních počítačích Azure IaaS pro podporované scénáře, které byly popsány v článku [Azure Disk Encryption Overview](azure-security-disk-encryption-overview.md) , ujistěte se, že jsou splněné požadavky. 

> [!WARNING]
> - Pokud jste už dříve použili [Azure Disk Encryption se službou Azure AD](azure-security-disk-encryption-prerequisites-aad.md) k šifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD.
> - Některá doporučení můžou zvýšit využití dat, sítě nebo výpočetních prostředků, což má za následek další licence nebo náklady na předplatné. Abyste mohli vytvářet prostředky v Azure v podporovaných oblastech, musíte mít platné aktivní předplatné Azure.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="supported-vm-sizes"></a>Podporované velikosti virtuálních počítačů

Azure Disk Encryption není k dispozici na [virtuálních počítačích Basic a-Series](https://azure.microsoft.com/pricing/details/virtual-machines/series/). Azure Disk Encryption je k dispozici na jiných virtuálních počítačích, které splňují tyto minimální požadavky na paměť:

| Virtuální počítač | Minimální požadavek na paměť |
|--|--|
| Virtuální počítače s Windows | 2 GB |
| Virtuální počítače se systémem Linux jenom při šifrování datových svazků| 2 GB |
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

| Distribuce systému Linux | Version | Typ svazku podporovaný pro šifrování|
| --- | --- |--- |
| Ubuntu | 18,04| Operační systém a datový disk |
| Ubuntu | 16,04| Operační systém a datový disk |
| Ubuntu | 14.04.5</br>[s vyladěným jádrem Azure se aktualizovala na 4,15 nebo novější.](azure-security-disk-encryption-tsg.md#bkmk_Ubuntu14) | Operační systém a datový disk |
| RHEL | 7,7 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,6 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,5 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,4 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,3 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7,2 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 6,8 | Datový disk (viz poznámka níže) |
| RHEL | 6,7 | Datový disk (viz poznámka níže) |
| CentOS | 7,7 | Operační systém a datový disk |
| CentOS | 7,6 | Operační systém a datový disk |
| CentOS | 7,5 | Operační systém a datový disk |
| CentOS | 7,4 | Operační systém a datový disk |
| CentOS | 7,3 | Operační systém a datový disk |
| CentOS | 7.2 n | Operační systém a datový disk |
| CentOS | 6,8 | Datový disk |
| openSUSE | 42,3 | Datový disk |
| SLES | 12. SP4 | Datový disk |
| SLES | 12. SP3 | Datový disk |

> [!NOTE]
> Nová implementace ADE je podporovaná pro RHEL operační systém a datový disk pro Image RHEL7 s průběžnými platbami. ADE se v současné době nepodporuje pro image RHEL s využitím vlastních předplatných (BYOS). Další informace najdete v tématu [Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md) .

- Azure Disk Encryption vyžaduje, aby se váš Trezor klíčů a virtuální počítače nacházely ve stejné oblasti a předplatném Azure. Konfigurace prostředků v samostatných oblastech způsobí selhání při povolování funkce Azure Disk Encryption.

#### <a name="additional-prerequisites-for-linux-iaas-vms"></a>Další požadavky pro virtuální počítače s IaaS Linux 

- Azure Disk Encryption vyžaduje, aby byly v systému přítomné moduly dm-crypt a vfat. Odebráním nebo zakázáním VFAT z výchozí image znemožníte systému číst klíč a získat klíč potřebný k odemknutí disků při dalším restartování. Kroky pro posílení zabezpečení systému, které odebírají modul VFAT ze systému, nejsou kompatibilní s Azure Disk Encryption. 
- Než povolíte šifrování, datové disky, které se mají šifrovat, musí být správně uvedené v adresáři/etc/fstab. Použijte pro tuto položku název trvalého blokování zařízení, protože u názvů zařízení ve formátu "/dev/sdX" nelze spoléhat na to, že se mají přidružit ke stejnému disku během restartování, zejména po použití šifrování. Další podrobnosti o tomto chování najdete v tématu [řešení potíží se změnami názvů zařízení virtuálních počítačů se systémem Linux](../virtual-machines/linux/troubleshoot-device-names-problems.md) .
- Ujistěte se, že nastavení/etc/fstab jsou správně nakonfigurovaná pro připojení. Chcete-li nakonfigurovat tato nastavení, spusťte příkaz Mount-a, restartujte virtuální počítač a aktivujte znovu připojení tímto způsobem. Až to bude hotové, zkontrolujte výstup příkazu lsblk a ověřte, jestli je jednotka pořád připojená. 
  - Pokud soubor/etc/fstab před povolením šifrování nepřipojí jednotku správně, Azure Disk Encryption nebude moci správně připojit.
  - Proces Azure Disk Encryption přesune informace o připojení z/etc/fstab a do vlastního konfiguračního souboru jako součást procesu šifrování. Nezobrazují se upozornění, aby se po dokončení šifrování datové jednotky v/etc/fstab zobrazila položka chybějící.
  - Před zahájením šifrování nezapomeňte zastavit všechny služby a procesy, které by mohly být zapsány do připojených datových disků, a zakázat je, aby se po restartování nerestartoval automaticky. V těchto oddílech můžou být soubory otevřené a brání tak postupu šifrování je znovu připojit, což způsobí selhání šifrování. 
  - Po restartování bude trvat čas, než proces Azure Disk Encryption připojí nově zašifrované disky. Po restartování počítače nebudou okamžitě k dispozici. Proces potřebuje čas ke spuštění, odemčení a připojení šifrovaných jednotek, než je k dispozici pro přístup k ostatním procesům. Tento proces může trvat déle než minutu po restartování v závislosti na charakteristikách systému.

Příklad příkazů, které lze použít k připojení datových disků a vytvoření potřebných položek/etc/fstab, najdete na [řádcích 244-248 tohoto souboru skriptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L244-L248). 

## <a name="bkmk_GPO"></a>Sítě a Zásady skupiny

**Aby bylo možné povolit funkci Azure Disk Encryption, musí virtuální počítače IaaS splňovat následující požadavky konfigurace koncového bodu sítě:**
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu Azure Active Directory @no__t -0login. microsoftonline. com @ no__t-1.
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit ke koncovému bodu Azure Storage, který hostuje úložiště rozšíření Azure a účet úložiště Azure, který hostuje soubory VHD.
  -  Pokud vaše zásada zabezpečení omezuje přístup z virtuálních počítačů Azure na Internet, můžete přeložit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo tak, aby umožňovalo odchozí připojení k IP adresám. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../key-vault/key-vault-access-behind-firewall.md).    


**Zásady skupiny:**
 - Řešení Azure Disk Encryption využívá ochranu pomocí nástroje BitLocker pro virtuální počítače s Windows IaaS. Pro virtuální počítače připojené k doméně neinstalujte žádné zásady skupiny, které vynutily ochranu čipem TPM. Informace o zásadách skupiny pro "povolení nástroje BitLocker bez kompatibilního čipu TPM" najdete v tématu [přehled zásady skupiny BitLockeru](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastními zásadami skupiny musí zahrnovat následující nastavení: [Konfigurace úložiště informací pro obnovení BitLockeru v případě, > povolení 256 bitového obnovovacího klíče](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro BitLocker nejsou kompatibilní. V počítačích, které nemají správné nastavení zásad, použijte nové zásady, vynutí aktualizaci nové zásady (gpupdate. exe/Force) a pak se může vyžadovat restartování.

- Azure Disk Encryption dojde k chybě, pokud zásady skupiny na úrovni domény blokují algoritmus AES-CBC, který je používán nástrojem BitLocker.


## <a name="bkmk_PSH"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) poskytuje sadu rutin, které používají model [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ke správě prostředků Azure. Můžete ji použít v prohlížeči pomocí [Azure Cloud Shell](../cloud-shell/overview.md), nebo ji můžete nainstalovat na místní počítač podle pokynů níže, aby se použila v jakékoli relaci PowerShellu. Pokud jste ho již nainstalovali místně, ujistěte se, že používáte nejnovější verzi sady Azure PowerShell SDK ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Nainstalovat Azure PowerShell pro použití na místním počítači (volitelné): 
1. Postupujte podle pokynů v odkazech pro váš operační systém a potom pokračujte dalšími kroky uvedenými níže.      
   - [Instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps). 
     - Nainstalujte PowerShellGet, Azure PowerShell a načtěte modul AZ Module. 

2. Ověřte nainstalované verze modulu AZ Module. V případě potřeby [aktualizujte modul Azure PowerShell](/powershell/azure/install-az-ps#update-the-azure-powershell-module).
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

4.  V případě potřeby si přečtěte [téma Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps).

## <a name="bkmk_CLI"></a>Instalace rozhraní příkazového řádku Azure CLI pro použití na místním počítači (volitelné)

[Azure CLI 2,0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. Rozhraní příkazového řádku je navržené k flexibilnímu dotazování dat, podpoře dlouhotrvajících operací jako neblokujících procesů a snadného skriptování. Můžete ji použít v prohlížeči s [Azure Cloud Shell](../cloud-shell/overview.md), nebo ji můžete nainstalovat na místní počítač a použít ji v jakékoli relaci PowerShellu.

1. Instalace rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) pro použití na místním počítači (volitelné):

2. Ověřte nainstalovanou verzi.
     
     ```azurecli-interactive
     az --version
     ``` 

3. Přihlaste se k Azure pomocí [AZ Login](/cli/azure/authenticate-azure-cli).
     
     ```azurecli
     az login
     
     # If you would like to select a tenant, use: 
     az login --tenant "<tenant>"

     # If you have multiple subscriptions, get your subscription list with az account list and specify with az account set.
     az account list
     az account set --subscription "<subscription name or ID>"
     ```

4. V případě potřeby si přečtěte téma [Začínáme s Azure CLI 2,0](/cli/azure/get-started-with-azure-cli) . 


## <a name="prerequisite-workflow-for-key-vault"></a>Požadovaný pracovní postup pro Key Vault
Pokud už jste obeznámeni s požadavky Key Vault a Azure AD pro Azure Disk Encryption, můžete použít [skript prostředí PowerShell pro Azure Disk Encryption požadavků](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Další informace o použití skriptu požadovaných součástí najdete v tématu [rychlý Start k virtuálnímu počítači](../virtual-machines/linux/disk-encryption-powershell-quickstart.md) a v [Azure Disk Encryption dodatku](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. V případě potřeby vytvořte skupinu prostředků.
2. Vytvořte Trezor klíčů. 
3. Nastavte zásady rozšířeného přístupu trezoru klíčů.

>[!WARNING]
>Před odstraněním trezoru klíčů se ujistěte, že jste s ním nešifroval žádné existující virtuální počítače. Pokud chcete trezor chránit před náhodným odstraněním, povolte v trezoru [obnovitelné odstranění](../key-vault/key-vault-soft-delete-powershell.md#enabling-soft-delete) a [Zámek prostředků](../azure-resource-manager/resource-group-lock-resources.md) . 
 
## <a name="bkmk_KeyVault"></a>Vytvoření trezoru klíčů 
Azure Disk Encryption je integrovaná do [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , která vám pomůžou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů. Můžete vytvořit Trezor klíčů nebo použít existující pro Azure Disk Encryption. Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md) a [zabezpečení trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md). K vytvoření trezoru klíčů můžete použít šablonu Správce prostředků, Azure PowerShell nebo rozhraní příkazového řádku Azure. 


>[!WARNING]
>Abyste se ujistili, že šifrovací tajná klíče nejsou mezi regionálními hranicemi, Azure Disk Encryption potřebuje Key Vault a virtuální počítače, které se mají umístit do stejné oblasti. Vytvořte a použijte Key Vault, který je ve stejné oblasti jako virtuální počítač, který chcete zašifrovat. 


### <a name="bkmk_KVPSH"></a>Vytvoření trezoru klíčů pomocí PowerShellu

Trezor klíčů můžete vytvořit s Azure PowerShell pomocí rutiny [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Další rutiny pro Key Vault najdete v tématu [AZ. klíčů trezor](/powershell/module/az.keyvault/). 

1. V případě potřeby se [připojte k předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. V případě potřeby vytvořte novou skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  K vypsání umístění datového centra použijte [příkaz Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

3. Vytvoření nového trezoru klíčů pomocí [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Poznamenejte si **název trezoru**, **název skupiny prostředků**, **ID prostředku**, **identifikátor URI trezoru**a **ID objektu** , které se vrátí pro pozdější použití při šifrování disků. 


### <a name="bkmk_KVCLI"></a>Vytvoření trezoru klíčů pomocí Azure CLI
Trezor klíčů můžete spravovat pomocí Azure CLI pomocí příkazů AZ klíč [trezoru](/cli/azure/keyvault#commands) . Chcete-li vytvořit Trezor klíčů, použijte příkaz [AZ Key trezor Create](/cli/azure/keyvault#az-keyvault-create).

1. V případě potřeby se [připojte k předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. V případě potřeby vytvořte novou skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Pokud chcete zobrazit seznam umístění, použijte [příkaz AZ Account list-Locations](/cli/azure/account#az-account-list) . 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Vytvořte nový trezor klíčů pomocí [AZ klíčů Create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Poznamenejte si **název trezoru** (název), **název skupiny prostředků**, **ID prostředku** (ID), **identifikátor URI trezoru**a **ID objektu** , které se vrátí pro pozdější použití. 

### <a name="bkmk_KVRM"></a>Vytvoření trezoru klíčů s Správce prostředků šablonou

Trezor klíčů můžete vytvořit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, Key Vault název, ID objektu, právních podmínek a smlouvy, a pak klikněte na **koupit**. 


## <a name="bkmk_KVper"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů
Platforma Azure potřebuje přístup k šifrovacím klíčům nebo tajným klíčům v trezoru klíčů, aby byly k dispozici pro virtuální počítač, aby bylo možné spouštět a dešifrovat svazky. V trezoru klíčů Povolte šifrování disku, jinak se nasazení nezdaří.  

### <a name="bkmk_KVperPSH"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů pomocí Azure PowerShell
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

### <a name="bkmk_KVperCLI"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů pomocí Azure CLI
K povolení šifrování disku pro Trezor klíčů použijte [AZ Key trezor Update](/cli/azure/keyvault#az-keyvault-update) . 

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


### <a name="bkmk_KVperrm"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů pomocí Azure Portal

1. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu**a **kliknutím zobrazte zásady pokročilého přístupu**.
2. Zaškrtněte políčko s názvem **Povolit přístup k Azure Disk Encryption pro šifrování svazku**.
3. V případě potřeby vyberte **Povolit přístup k Azure Virtual Machines pro nasazení** nebo **povolit přístup k Azure Resource Manager pro nasazení šablony**. 
4. Klikněte na **Uložit**.

    ![Zásady rozšířeného přístupu ke službě Azure Key trezor](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a>Nastavení klíčového šifrovacího klíče (volitelné)
Pokud chcete pro další vrstvu zabezpečení pro šifrovací klíče použít klíč šifrovacího klíče (KEK), přidejte do trezoru klíčů KEK. Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte šifrovací klíč klíče v trezoru klíčů. KEK můžete také importovat z místního modulu HSM správy klíčů. Další informace najdete v [dokumentaci Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault.

* Při generování klíčů použijte typ klíče RSA. Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

* Tajný kód trezoru klíčů a adresy URL KEK musí být ve verzi. Azure vynutilo toto omezení správy verzí. Platné tajné a KEK adresy URL najdete v následujících příkladech:

  * Příklad platné tajné adresy URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příklad platné adresy URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů trezoru klíčů a adres URL KEK. Příklady nepodporovaných a podporovaných adres URL trezoru klíčů najdete v následujících příkladech:

  * Nepřijatelná adresa URL trezoru klíčů *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Přijatelná adresa URL trezoru klíčů: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*


### <a name="bkmk_KEKPSH"></a>Nastavení šifrovacího klíče klíče pomocí Azure PowerShell 
Před použitím skriptu PowerShellu byste měli být obeznámeni s Azure Disk Encryption předpoklady pro pochopení kroků ve skriptu. Vzorový skript může pro vaše prostředí potřebovat změny. Tento skript vytvoří všechny požadavky Azure Disk Encryption a zašifruje stávající virtuální počítač s IaaS a zabalí šifrovací klíč disku pomocí klíčového šifrovacího klíče. 

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


 
## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Povolit Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows.md)

> [!div class="nextstepaction"]
> [Povolit Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux.md)

