---
title: Azure Disk Encryption s požadavky Aplikace Azure AD (předchozí verze)
description: Tento článek popisuje předpoklady pro použití Microsoft Azureho šifrování disku pro virtuální počítače s IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 09/30/2019
ms.custom: seodec18
ms.openlocfilehash: 07db6c5cecf2b8dec803e73d5775edef3f8906fa
ms.sourcegitcommit: 7c2dba9bd9ef700b1ea4799260f0ad7ee919ff3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71828631"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Požadavky na Azure Disk Encryption (předchozí verze)

**Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure AD, aby bylo možné povolit šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD, které používají novou verzi. Pokyny k povolení šifrování disků virtuálního počítače pomocí nové verze najdete v tématu [Azure Disk Encryption požadavky](azure-security-disk-encryption-prerequisites.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.**

 Tento článek, Azure Disk Encryption předpoklady, vysvětluje položky, které je potřeba splnit, než bude možné používat Azure Disk Encryption. Společně s obecnými požadavky je Azure Disk Encryption integrována s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) a používá aplikaci Azure AD k poskytování ověřování za účelem správy šifrovacích klíčů v trezoru klíčů. Můžete také použít [Azure PowerShell](/powershell/azure/overview) nebo rozhraní příkazového [řádku Azure CLI](/cli/azure/) k nastavení nebo konfiguraci Key Vault a aplikace Azure AD.

Než povolíte Azure Disk Encryption na virtuálních počítačích Azure IaaS pro podporované scénáře, které byly popsány v článku [Azure Disk Encryption Overview](azure-security-disk-encryption-overview.md) , ujistěte se, že jsou splněné požadavky. 

> [!WARNING]
> - Některá doporučení můžou zvýšit využití dat, sítě nebo výpočetních prostředků, což má za následek další licence nebo náklady na předplatné. Abyste mohli vytvářet prostředky v Azure v podporovaných oblastech, musíte mít platné aktivní předplatné Azure.
> - Pokud jste už dříve použili [Azure Disk Encryption se službou Azure AD](azure-security-disk-encryption-prerequisites-aad.md) k šifrování tohoto virtuálního počítače, budete muset tuto možnost použít k ZAšifrování virtuálního počítače. V tomto šifrovaném virtuálním počítači nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) , protože se nejedná o podporovaný scénář, což znamená, že pro tento zašifrovaný virtuální počítač se zatím nepodporuje přepínání z aplikace AAD. 

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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
| RHEL | 7.3 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 7.2 | Operační systém a datový disk (viz poznámka níže) |
| RHEL | 6.8 | Datový disk (viz poznámka níže) |
| RHEL | 6.7 | Datový disk (viz poznámka níže) |
| CentOS | 7,7 | Operační systém a datový disk |
| CentOS | 7,6 | Operační systém a datový disk |
| CentOS | 7,5 | Operační systém a datový disk |
| CentOS | 7,4 | Operační systém a datový disk |
| CentOS | 7.3 | Operační systém a datový disk |
| CentOS | 7.2 n | Operační systém a datový disk |
| CentOS | 6.8 | Datový disk |
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

**Pokud chcete funkci Azure Disk Encryption povolit pomocí starší syntaxe parametru AAD, virtuální počítače s IaaS musí splňovat následující požadavky konfigurace koncového bodu sítě:** 
  - K získání tokenu pro připojení k trezoru klíčů musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu Azure Active Directory @no__t -0login. microsoftonline. com @ no__t-1.
  - Aby bylo možné zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS schopný připojit se ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit ke koncovému bodu Azure Storage, který hostuje úložiště rozšíření Azure a účet úložiště Azure, který hostuje soubory VHD.
  -  Pokud vaše zásada zabezpečení omezuje přístup z virtuálních počítačů Azure na Internet, můžete přeložit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo tak, aby umožňovalo odchozí připojení k IP adresám. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../key-vault/key-vault-access-behind-firewall.md).
  - Pokud je v systému Windows explicitně zakázán protokol TLS 1,0 a verze rozhraní .NET nebyla aktualizována na 4,6 nebo vyšší, umožní tato změna registru vybrat novější verzi protokolu TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Zásady skupiny:**
 - Řešení Azure Disk Encryption využívá ochranu pomocí nástroje BitLocker pro virtuální počítače s Windows IaaS. Pro virtuální počítače připojené k doméně neinstalujte žádné zásady skupiny, které vynutily ochranu čipem TPM. Informace o zásadách skupiny pro "povolení nástroje BitLocker bez kompatibilního čipu TPM" najdete v tématu [přehled zásady skupiny BitLockeru](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#bkmk-unlockpol1).

-  Zásady BitLockeru na virtuálních počítačích připojených k doméně s vlastními zásadami skupiny musí zahrnovat následující nastavení: [Konfigurace úložiště informací pro obnovení BitLockeru v případě, > povolení 256 bitového obnovovacího klíče](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption se nezdaří, pokud vlastní nastavení zásad skupiny pro BitLocker nejsou kompatibilní. V počítačích, které nemají správné nastavení zásad, použijte nové zásady, vynutí aktualizaci nové zásady (gpupdate. exe/Force) a pak se může vyžadovat restartování.  


## <a name="bkmk_PSH"></a>Azure PowerShell
[Azure PowerShell](/powershell/azure/overview) poskytuje sadu rutin, které používají model [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) ke správě prostředků Azure. Můžete ji použít v prohlížeči pomocí [Azure Cloud Shell](../cloud-shell/overview.md), nebo ji můžete nainstalovat na místní počítač podle pokynů níže, aby se použila v jakékoli relaci PowerShellu. Pokud jste ho už nainstalovali místně, ujistěte se, že používáte nejnovější verzi Azure PowerShell ke konfiguraci Azure Disk Encryption.

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Nainstalovat Azure PowerShell pro použití na místním počítači (volitelné):  
1. [Instalace a konfigurace Azure PowerShell](/powershell/azure/install-az-ps). 

2. Nainstalujte [modul Azure Active Directory PowerShell](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Ověřte nainstalované verze modulů.
      ```powershell
      Get-Module Az -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Přihlaste se k Azure pomocí rutiny [Connect-AzAccount](/powershell/module/az.accounts/connect-azaccount) .
     
     ```powershell
     Connect-AzAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzAccount –Environment (Get-AzEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzSubscription and 
     specify it with Set-AzContext.  #>
     Get-AzSubscription
     Set-AzContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Připojte se k Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. V případě potřeby se podívejte na [téma Začínáme s Azure PowerShell](/powershell/azure/get-started-azureps) a [AzureAD](/powershell/module/azuread).

## <a name="bkmk_CLI"></a>Rozhraní příkazového řádku Azure

[Azure CLI 2,0](/cli/azure) je nástroj příkazového řádku pro správu prostředků Azure. Rozhraní příkazového řádku je navržené k flexibilnímu dotazování dat, podpoře dlouhotrvajících operací jako neblokujících procesů a snadného skriptování. Můžete ho používat v prohlížeči pomocí služby [Azure Cloud Shell](../cloud-shell/overview.md) nebo nainstalovat na místním počítači a používat ho v jakékoli relaci PowerShellu.

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


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Požadovaný pracovní postup pro Key Vault a aplikaci Azure AD

Pokud už jste obeznámeni s požadavky Key Vault a Azure AD pro Azure Disk Encryption, můžete použít [skript prostředí PowerShell pro Azure Disk Encryption požadavků](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Další informace o použití skriptu předpokladů najdete v části [Azure Disk Encryption dodatku](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Vytvoření trezoru klíčů 
2. Nastavení aplikace a instančního objektu služby Azure AD.
3. Nastavte zásady přístupu trezoru klíčů pro aplikaci Azure AD.
4. Nastavte zásady rozšířeného přístupu trezoru klíčů.
 
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


## <a name="bkmk_ADapp"></a>Nastavení aplikace a instančního objektu služby Azure AD 
Pokud potřebujete povolit šifrování na běžícím virtuálním počítači v Azure, Azure Disk Encryption vygeneruje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů vyžaduje ověřování Azure AD. Vytvořte pro tento účel aplikaci Azure AD. Pro účely ověřování můžete použít buď ověřování na základě tajného klíče klienta, nebo [ověřování Azure AD založené na certifikátech klienta](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a>Nastavení aplikace a instančního objektu služby Azure AD pomocí Azure PowerShell 
Pokud chcete spustit následující příkazy, Získejte a použijte [modul Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. V případě potřeby se [připojte k předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. K vytvoření aplikace Azure AD použijte rutinu [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) prostředí PowerShell. MyApplicationHomePage a MyApplicationUri mohou být libovolné hodnoty, které chcete.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication. ApplicationId je Azure AD ClientID a $aadClientSecret je tajný klíč klienta, který později použijete k povolení Azure Disk Encryption. Zabezpečte si tajný klíč klienta Azure AD správně. Spuštění `$azureAdApplication.ApplicationId` zobrazí hodnotu ApplicationID.


### <a name="bkmk_ADappCLI"></a>Nastavení aplikace služby Azure AD a instančního objektu pomocí Azure CLI

Objekty služby můžete spravovat pomocí Azure CLI pomocí příkazů [AZ AD SP](/cli/azure/ad/sp) . Další informace najdete v tématu [Vytvoření instančního objektu Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. V případě potřeby se [připojte k předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Vytvoří nový instanční objekt.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Vrácený identifikátor appId je Azure AD ClientID používané v dalších příkazech. Také je to hlavní název služby, který použijete pro AZ klíčů set-Policy. Heslo je tajný klíč klienta, který byste měli později použít k povolení Azure Disk Encryption. Zabezpečte si tajný klíč klienta Azure AD správně.
 
### <a name="bkmk_ADappRM"></a>Nastavení aplikace a instančního objektu služby Azure AD, přestože Azure Portal
Pomocí kroků z [portálu use vytvořte aplikaci Azure Active Directory a instanční objekt, který má přístup k prostředkům,](../active-directory/develop/howto-create-service-principal-portal.md) a vytvořte tak aplikaci Azure AD. Každý krok uvedený níže vás převezme přímo do oddílu článku, který se dokončí. 

1. [Ověřit požadovaná oprávnění](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Při vytváření aplikace můžete použít libovolný název a adresu URL pro přihlášení.
3. [Získejte ID aplikace a ověřovací klíč](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Ověřovací klíč je tajný klíč klienta a používá se jako AadClientSecret pro set-AzVMDiskEncryptionExtension. 
        - Ověřovací klíč používá aplikace jako přihlašovací údaje pro přihlášení ke službě Azure AD. V Azure Portal se tento tajný klíč nazývá klíče, ale nemá žádný vztah k trezorům klíčů. Zabezpečte tento tajný klíč správně. 
     - ID aplikace bude použito později jako AadClientId pro set-AzVMDiskEncryptionExtension a jako ServicePrincipalName pro set-AzKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD
Pro zápis šifrovacích tajných klíčů do zadaného Key Vault Azure Disk Encryption potřebuje ID klienta a tajný klíč klienta Azure Active Directory aplikace, který má oprávnění k zápisu tajných kódů do Key Vault. 

> [!NOTE]
> Azure Disk Encryption vyžaduje, abyste v klientské aplikaci Azure AD nakonfigurovali následující zásady přístupu: _WrapKey_ a _nastavte_ oprávnění.

### <a name="bkmk_KVAPPSH"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí Azure PowerShell
Vaše aplikace Azure AD potřebuje oprávnění pro přístup ke klíčům nebo tajným klíčům v trezoru. Pomocí rutiny [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte aplikaci oprávnění pomocí ID klienta (které bylo vygenerováno při registraci aplikace) jako hodnoty parametru _– servicePrincipalName_ . Další informace najdete v blogovém příspěvku [Azure Key Vault – krok za](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)krokem. 

1. V případě potřeby se [připojte k předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Nastavte zásady přístupu trezoru klíčů pro aplikaci AD pomocí PowerShellu.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="bkmk_KVAPCLI"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí Azure CLI
Pomocí [AZ webtrezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) nastavte zásady přístupu. Další informace najdete v tématu [správa Key Vault pomocí CLI 2,0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. V případě potřeby se [připojte k předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Udělte instančnímu objektu, který jste vytvořili prostřednictvím Azure CLI přístup k získání tajných kódů a zabalte klíče pomocí následujícího příkazu:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí portálu

1. Otevřete skupinu prostředků ve vašem trezoru klíčů.
2. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu**a pak klikněte na **Přidat nový**.
3. V části **Vybrat objekt zabezpečení**vyhledejte aplikaci Azure AD, kterou jste vytvořili, a vyberte ji. 
4. U **klíčových oprávnění**zaškrtněte v části **kryptografické operace** **klíč pro zabalení** .
5. V případě **oprávnění tajného klíče**zaškrtněte v části **operace správy tajných klíčů** **nastaveno** .
6. Zásady přístupu uložíte kliknutím na **OK** . 

![Azure Key Vault kryptografické operace – zalomit klíč](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault oprávnění tajného kódu – sada](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

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

 - **V případě potřeby povolte Key Vault pro nasazení:** Povolí Virtual Machines načtení certifikátů uložených jako tajných klíčů z trezoru.
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
     New-AzResourceGroup –Name  $KVRGname –Location $Loc;
     New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc;
     $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname;
     $KeyVaultResourceId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName  $KVRGname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption;
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName  $KVRGname;
     
 #Step 4: Create a new key in the key vault with the Add-AzKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values. 
     
     $VMName = 'MySecureVM';
      $VMRGName = 'MyVirtualMachineResourceGroup';
     Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a>Ověřování na základě certifikátu (volitelné)
Pokud chcete použít ověřování pomocí certifikátu, můžete ho nahrát do trezoru klíčů a nasadit ho do klienta. Před použitím skriptu PowerShellu byste měli být obeznámeni s Azure Disk Encryption předpoklady pro pochopení kroků ve skriptu. Vzorový skript může pro vaše prostředí potřebovat změny.

     
 ```powershell

 # Fill in "MyKeyVaultResourceGroup", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
   
   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert".  

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for -EnabledForDeployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM'
   $VMRGName = 'MyVirtualMachineResourceGroup'
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a>Ověřování založené na certifikátech a KEK (volitelné)

Pokud chcete použít ověřování pomocí certifikátu a zabalit šifrovací klíč pomocí KEK, můžete jako příklad použít skript uvedený níže. Před použitím skriptu PowerShellu byste měli být obeznámeni se všemi předchozími Azure Disk Encryption nezbytnými pro pochopení kroků ve skriptu. Vzorový skript může pro vaše prostředí potřebovat změny.

> [!IMPORTANT]
> Ověřování založené na certifikátech Azure AD se v tuto chvíli nepodporuje u virtuálních počítačů se systémem Linux.



     
 ```powershell
# Fill in 'MyKeyVaultResourceGroup', 'MySecureVault', and 'MyLocation' (if needed)

   $KVRGname = 'MyKeyVaultResourceGroup'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname -Location $Loc
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname

   # Upload the pfx file to the key vault. 
   # Fill in "MyAADCert". 

   $KeyVaultSecretName = "MyAADCert"
   $FileContentBytes = get-content $CertPath -Encoding Byte
   $FileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
           $JSONObject = @"
           { 
               "data" : "$filecontentencoded", 
               "dataType" : "pfx", 
               "password" : "$CertPassword" 
           } 
"@

   $JSONObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
   $JSONEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

   #Set the secret and set the key vault policy for deployment

   $Secret = ConvertTo-SecureString -String $JSONEncoded -AsPlainText -Force
   Set-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $KVRGname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' and 'MyVirtualMachineResourceGroup' with your values.

   $VMName = 'MySecureVM';
   $VMRGName = 'MyVirtualMachineResourceGroup';
   $CertUrl = (Get-AzKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzKeyVault -VaultName $KeyVaultName -ResourceGroupName $KVRGName).ResourceId
   $VM = Get-AzVM -ResourceGroupName $VMRGName -Name $VMName 
   $VM = Add-AzVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzVM -VM $VM -ResourceGroupName $VMRGName

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzVMDiskEncryptionExtension -ResourceGroupName $VMRGName -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Další kroky
> [!div class="nextstepaction"]
> [Povolit Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Povolit Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux-aad.md)
