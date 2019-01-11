---
title: Azure Disk Encryption pomocí Azure AD App požadavky (předchozí verze)
description: Tento článek obsahuje požadavky pro používání Microsoft Azure Disk Encryption pro virtuální počítače IaaS.
author: mestew
ms.service: security
ms.subservice: Azure Disk Encryption
ms.topic: article
ms.author: mstewart
ms.date: 01/09/2019
ms.custom: seodec18
ms.openlocfilehash: cadd78102eab5c057458b3f7705a531e762f2f89
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54199268"
---
# <a name="azure-disk-encryption-prerequisites-previous-release"></a>Požadavky Azure Disk Encryption (předchozí verze)

**Novou verzi sady Azure Disk Encryption eliminuje požadavek na poskytnutí parametrem aplikace Azure AD povolit šifrování disku virtuálního počítače. Nové verze se už nevyžadují zadejte přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrován bez parametrů aplikace Azure AD pomocí nové verze. Chcete-li zobrazit pokyny, jak povolit šifrování disku virtuálního počítače pomocí nové verze, najdete v článku [požadavky Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md). Virtuální počítače, které již byly šifrované pomocí aplikace Azure AD, parametry jsou stále podporovány a má pokračovat udržovat syntaxí AAD.**

 Tento článek, požadované součásti služby Azure Disk Encryption, vysvětluje položky, které musí být na místě, abyste mohli používat Azure Disk Encryption. Spolu s obecné požadavky Azure Disk Encryption je integrovaná s [Azure Key Vault](https://docs.microsoft.com/azure/key-vault/) a použije aplikaci Azure AD se poskytovat ověřování, abyste mohli spravovat šifrovací klíče ve službě key vault. Možná budete také chtít používat [prostředí Azure PowerShell](/powershell/azure/overview) nebo [rozhraní příkazového řádku Azure](/cli/azure/) nastavení nebo konfigurace služby Key Vault a aplikace Azure AD.

Dříve než povolíte pro podporované scénáře, které byly zmíněny v Azure Disk Encryption ve virtuálních počítačích Azure IaaS [přehledu Azure Disk Encryption](azure-security-disk-encryption-overview.md) článek, je nutné mít požadavky na místě. 

> [!WARNING]
> - Některá doporučení může zvýšit dat, sítě nebo výpočetní využití prostředků, což vede k další náklady na licence nebo předplatné. Musíte mít aktivní předplatné Azure platnou k vytváření prostředků v Azure v podporovaných oblastech.
> - Pokud jste už dřív použili [Azure Disk Encryption pomocí Azure AD app](azure-security-disk-encryption-prerequisites-aad.md) pro šifrování tento virtuální počítač, budete muset pokračovat tuto možnost použijte k šifrování virtuálního počítače. Nemůžete použít [Azure Disk Encryption](azure-security-disk-encryption-prerequisites.md) na tento šifrovaný virtuální počítač jako tato akce není podporovaný scénář význam přepnutí mimo aplikaci AAD pro tento šifrovaný virtuální počítač se zatím nepodporuje. 


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
- Ujistěte se, že nastavení /etc/fstab jsou správně nakonfigurovány pro připojení. Tato nastavení nakonfigurujete, spusťte připojení – příkaz nebo restartujte virtuální počítač a aktivuje tímto způsobem opětovné připojení. Jakmile, která se dokončí, zkontrolujte výstup příkazu lsblk k ověření, že požadovaný disk je pořád připojený. 
    - Pokud soubor /etc/fstab nebude připojit jednotku správně před povolením šifrování, Azure Disk Encryption nebude možné ji správně připojit.
    - Azure Disk Encryption proces přesune informace připojení mimo /etc/fstab a do jeho vlastní konfigurační soubor jako součást procesu šifrování. Není možné dospod vidět, že položka chybí /etc/fstab po data šifrování jednotky dokončí.
    -  Po restartování počítače bude trvat dobu procesu Azure Disk Encryption pro připojení nově šifrovanými disky. Bude okamžitě k dispozici po restartu. Proces potřebuje čas ke spuštění, odemčení a pak připojte šifrovaných disků před jejich nebudou dostupné pro jiné procesy pro přístup k. Tento proces může trvat déle než minutu po restartování počítače v závislosti na charakteristikách systému.

Příklad příkazy, které je možné připojit datové disky a vytvořte nezbytné/etc/fstab položky lze nalézt v [řádky 197 205 tohoto souboru skriptu](https://github.com/ejarvi/ade-cli-getting-started/blob/master/validate.sh#L197-L205). 


## <a name="bkmk_GPO"></a> Sítě a zásad skupiny

**K povolení této funkce Azure Disk Encryption pomocí AAD starší syntaxe parametru, virtuální počítače IaaS, musí splňovat následující požadavky na konfiguraci koncového bodu sítě:** 
  - Získá token pro připojení k vašemu trezoru klíčů, musí být schopný se připojit k Azure Active Directory koncový bod, virtuálních počítačů IaaS \[login.microsoftonline.com\].
  - Zapsat šifrovací klíče do trezoru klíčů, musí být virtuální počítač IaaS může připojit ke koncovému bodu trezoru klíčů.
  - Virtuální počítač IaaS musí být schopný se připojit k koncový bod služby Azure storage, který je hostitelem úložiště rozšíření Azure a účet úložiště Azure, který je hostitelem souborů virtuálního pevného disku.
  -  Pokud vaše zásady zabezpečení omezuje přístup z virtuálních počítačů Azure na Internetu, můžete vyřešit předchozí identifikátor URI a nakonfigurovat konkrétní pravidlo pro povolení odchozích připojení k IP adres. Další informace najdete v tématu [Azure Key Vault za bránou firewall](../key-vault/key-vault-access-behind-firewall.md).
  - Na Windows Pokud byl explicitně zakázán protokol TLS 1.0 a verze rozhraní .NET se neaktualizoval na 4.6 nebo novější, následující změny registru povolí ADE vybrat novější verze TLS:
    
        [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001

        [HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\Microsoft\.NETFramework\v4.0.30319]
        "SystemDefaultTlsVersions"=dword:00000001
        "SchUseStrongCrypto"=dword:00000001` 
     

 


**Zásady skupiny:**
 - Řešení Azure Disk Encryption používá ochrana externí klíče nástroje BitLocker pro virtuální počítače IaaS s Windows. Pro virtuální počítače připojené k doméně, push nemáte žádné zásady skupiny, které vynucují ochrany pomocí čipu TPM. Informace o zásadách skupiny na "Povolit BitLocker bez kompatibilního čipu TPM" najdete v tématu [odkaz zásad skupiny Bitlockeru](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings#a-href-idbkmk-unlockpol1arequire-additional-authentication-at-startup).

-  Zásady nástroje BitLocker na virtuálních počítačích připojených k doméně pomocí zásad vlastní skupiny, musí obsahovat následující nastavení: [Konfigurace úložiště uživatele bitlockeru informace recovery -> Povolit 256bitový obnovovací klíč](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-group-policy-settings). Azure Disk Encryption selže, když jsou nekompatibilní nastavení zásad vlastní skupiny pro Bitlocker. Na počítačích, které nebyly k dispozici nastavení správné zásady, použijí nové zásady vynutí nové zásady aktualizace (gpupdate.exe/Force) a následného restartování může být nutné.  


## <a name="bkmk_PSH"></a> Prostředí Azure PowerShell
[Prostředí Azure PowerShell](/powershell/azure/overview) poskytuje sadu rutin, které používá [Azure Resource Manageru](../azure-resource-manager/resource-group-overview.md) modelu pro správu vašich prostředků Azure. Můžete ho použít ve svém prohlížeči prostřednictvím [Azure Cloud Shell](../cloud-shell/overview.md), nebo ho můžete nainstalovat na místním počítači použít v jakékoli relaci Powershellu pomocí následujícího postupu. Pokud už ho máte nainstalovaný místně, ujistěte se, že používáte nejnovější verzi sady SDK Azure Powershellu ke konfiguraci Azure Disk Encryption. Stáhněte si nejnovější verzi [prostředí Azure PowerShell verze](https://github.com/Azure/azure-powershell/releases).

### <a name="install-azure-powershell-for-use-on-your-local-machine-optional"></a>Instalace Azure Powershellu pro použití v místním počítači (volitelné):  
1. Postupujte podle pokynů v odkazech pro váš operační systém, pokračujte ale zbytek postupu níže.
    - [Instalace a konfigurace Azure Powershellu pro Windows](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.13.0). 
        - Instalace modulu PowerShellGet, prostředí Azure PowerShell a načtení modulu AzureRM. 

2. Nainstalujte [modul Powershellu pro Azure Active Directory](/powershell/azure/active-directory/install-adv2#installing-the-azure-ad-module). 

     ```powershell
     Install-Module AzureAD
     ```

3. Ověřte nainstalovaných verzí modulů.
      ```powershell
      Get-Module AzureRM -ListAvailable | Select-Object -Property Name,Version,Path
      Get-Module AzureAD -ListAvailable | Select-Object -Property Name,Version,Path
      ```
4. Přihlaste se k Azure s využitím [Connect-AzureRmAccount](/powershell/module/azurerm.profile/connect-azurermaccount) rutiny.
     
     ```powershell
     Connect-AzureRmAccount
     # For specific instances of Azure, use the -Environment parameter.
     Connect-AzureRmAccount –Environment (Get-AzureRmEnvironment –Name AzureUSGovernment)
    
     <# If you have multiple subscriptions and want to specify a specific one, 
     get your subscription list with Get-AzureRmSubscription and 
     specify it with Set-AzureRmContext.  #>
     Get-AzureRmSubscription
     Set-AzureRmContext -SubscriptionId "xxxx-xxxx-xxxx-xxxx"
     ```

5.  Připojení k Azure AD [Connect-AzureAD](/powershell/module/azuread/connect-azuread).
     
     ```powershell
     Connect-AzureAD
     ```

6. Kontrola [Začínáme s Azure Powershellem](/powershell/azure/get-started-azureps) a [AzureAD](/powershell/module/azuread), v případě potřeby.

## <a name="bkmk_CLI"></a> Rozhraní příkazového řádku Azure

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


## <a name="prerequisite-workflow-for-key-vault-and-the-azure-ad-app"></a>Požadované pracovní postup pro Key Vault a aplikace Azure AD

Pokud jste již obeznámeni s požadavky pro Azure Disk Encryption Key Vault a Azure AD, můžete použít [skript prostředí PowerShell pro Azure Disk Encryption požadované součásti](https://raw.githubusercontent.com/Azure/azure-powershell/master/src/ResourceManager/Compute/Commands.Compute/Extension/AzureDiskEncryption/Scripts/AzureDiskEncryptionPreRequisiteSetup.ps1 ). Další informace o používání skriptu požadavky najdete v článku [šifrování virtuálního počítače Quickstart](quick-encrypt-vm-powershell.md) a [Azure Disk Encryption příloha](azure-security-disk-encryption-appendix.md#bkmk_prereq-script). 

1. Vytvoření trezoru klíčů 
2. Nastavení aplikace Azure AD a instanční objekt služby.
3. Nastavte zásady přístupu trezoru klíčů pro aplikace Azure AD.
4. Nastavení služby key vault pokročilé zásady přístupu.
 
## <a name="bkmk_KeyVault"></a> Vytvoření trezoru klíčů 
Azure Disk Encryption je integrovaná s [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) umožňují řídit a spravovat šifrování disku klíče a tajné kódy ve vašem předplatném služby key vault. Můžete vytvořit trezor klíčů nebo použijte již existující pro Azure Disk Encryption. Další informace o trezorů klíčů najdete v tématu [Začínáme s Azure Key Vault](../key-vault/key-vault-get-started.md) a [zabezpečení trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md). Šablony Resource Manageru, Azure Powershellu nebo rozhraní příkazového řádku Azure CLI slouží k vytvoření služby key vault. 


>[!WARNING]
>Pokud chcete mít jistotu, že šifrování tajných kódů není překračují hranice regionální, Azure Disk Encryption musí být umístěné ve stejné oblasti služby Key Vault a virtuální počítače. Vytvoření a použití služby Key Vault, která je ve stejné oblasti jako virtuální počítač k šifrování. 


### <a name="bkmk_KVPSH"></a> Vytvoření služby key vault pomocí Powershellu

Můžete vytvořit trezor klíčů s použitím prostředí Azure PowerShell [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault) rutiny. Další rutiny pro Key Vault najdete v části [AzureRM.KeyVault](/powershell/module/azurerm.keyvault/). 

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH). 
2. V případě potřeby vytvořte novou skupinu prostředků s [New-AzureRmResourceGroup](/powershell/module/AzureRM.Resources/New-AzureRmResourceGroup).  Seznam datacentrum v umístění, použijte [Get-AzureRmLocation](/powershell/module/azurerm.resources/get-azurermlocation). 
     
     ```azurepowershell-interactive
     # Get-AzureRmLocation 
     New-AzureRmResourceGroup –Name 'MySecureRG' –Location 'East US'
     ```

3. Vytvoření nové služby key vault pomocí [New-AzureRmKeyVault](/powershell/module/azurerm.keyvault/New-AzureRmKeyVault)
    
      ```azurepowershell-interactive
     New-AzureRmKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -Location 'East US'
     ```

4. Poznámka: **název trezoru**, **název skupiny prostředků**, **ID prostředku**, **identifikátor URI trezoru**a **ID objektu** pro pozdější použití, která se vrátí, když šifrování disků. 


### <a name="bkmk_KVCLI"></a> Vytvoření služby key vault pomocí Azure CLI
Váš trezor klíčů s použitím rozhraní příkazového řádku Azure můžete spravovat [az keyvault](/cli/azure/keyvault#commands) příkazy. Chcete-li vytvořit trezor klíčů, použijte [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create).

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. V případě potřeby vytvořte novou skupinu prostředků s [vytvořit skupiny az](/cli/azure/group#az-group-create). Chcete-li vypsat umístění, použijte [az účet list-locations](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MySecureRG" -l "East US"
     ```

3. Vytvoření nové služby key vault pomocí [az keyvault vytvořit](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MySecureRG" --location "East US"
     ```

4. Poznámka: **Název_trezoru** (název), **název skupiny prostředků**, **ID prostředku** (ID), **identifikátor URI trezoru**a **ID objektu** , která se vrátí k použití později. 

### <a name="bkmk_KVRM"></a> Vytvoření trezoru klíčů pomocí šablony Resource Manageru

Můžete vytvořit trezor klíčů pomocí [šablony Resource Manageru](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. Na šablony Azure pro rychlý start, klikněte na tlačítko **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, služby Key Vault název, ID objektu, právní podmínky a smlouvy a klikněte na **nákupní**. 


## <a name="bkmk_ADapp"></a> Nastavení aplikace Azure AD a služby instančního objektu 
Pokud je třeba šifrování, aby byla povolená na spuštěný virtuální počítač v Azure, Azure Disk Encryption generuje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů se vyžaduje ověřování Azure AD. Vytvořte aplikaci Azure AD pro tento účel. Pro účely ověřování, můžete použít buď ověřování na základě tajný kód klienta nebo [ověřování klienta na základě certifikátů Azure AD](../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="bkmk_ADappPSH"></a> Nastavení aplikace Azure AD a služby instančního objektu pomocí Azure Powershellu 
Spusťte následující příkazy, získat a používat [modulu Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Použití [New-AzureRmADApplication](/powershell/module/azurerm.resources/new-azurermadapplication) rutiny prostředí PowerShell k vytvoření aplikace Azure AD. MyApplicationHomePage a MyApplicationUri můžou být všechny hodnoty, kterou požadujete.

     ```azurepowershell-interactive
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzureRmADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication.ApplicationId je ID klienta Azure AD a $aadClientSecret je tajný kód klienta, který můžete později použít k povolení Azure Disk Encryption. Tajný klíč klienta Azure AD chrání odpovídajícím způsobem. Spuštění `$azureAdApplication.ApplicationId` zobrazí ApplicationID.


### <a name="bkmk_ADappCLI"></a> Nastavení aplikace Azure AD a služby instančního objektu pomocí Azure CLI

Můžete spravovat vaše instančních objektů s použitím rozhraní příkazového řádku Azure [az ad sp](/cli/azure/ad/sp) příkazy. Další informace najdete v tématu [vytvoření instančního objektu Azure ](/cli/azure/create-an-azure-service-principal-azure-cli).

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Vytvořte nový instanční objekt.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  AppId vrátila ID klienta Azure AD se používá v dalších příkazů. Je také hlavní název služby, kterou budete používat pro az keyvault set-policy. Heslo je tajný kód klienta, který jste měli použít později, aby Azure Disk Encryption. Tajný klíč klienta Azure AD chrání odpovídajícím způsobem.
 
### <a name="bkmk_ADappRM"></a> Nastavení aplikace Azure AD a služby objektu zabezpečení i když na webu Azure portal
Pomocí postupu z [použití portálu k vytvoření aplikace Azure Active Directory a instančního objektu, který má přístup k prostředkům](../active-directory/develop/howto-create-service-principal-portal.md) článku vytvořte aplikaci Azure AD. Jednotlivé kroky uvedené níže přejdete přímo do oddílu článku dokončete. 

1. [Ověření požadovaných oprávnění](../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Vytvoření aplikace Azure Active Directory](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Můžete použít libovolný název a adresu URL, které chcete při vytváření aplikace přihlašování.
3. [Získání ID aplikace a ověřovací klíč](../active-directory/develop/howto-create-service-principal-portal.md#get-application-id-and-authentication-key). 
     - Ověřovací klíč je tajný kód klienta a slouží jako AadClientSecret pro Set-AzureRmVMDiskEncryptionExtension. 
        - Ověřovací klíč se používá aplikace jako přihlašovací údaje pro přihlášení k Azure AD. Na webu Azure Portal tento tajný kód se nazývá klíče, ale nemá žádný vztah k trezorům klíčů. Zabezpečte tento tajný kód správně. 
     - ID aplikace se později použije jako AadClientId pro Set-AzureRmVMDiskEncryptionExtension a atribut ServicePrincipalName pro Set-AzureRmKeyVaultAccessPolicy. 

## <a name="bkmk_KVAP"></a> Nastavit zásady přístupu trezoru klíčů pro aplikace Azure AD
K šifrování tajných kódů zápisu do zadané služby Key Vault, musí Azure Disk Encryption ID klienta a tajný kód klienta aplikace Azure Active Directory, který má oprávnění k zápisu tajných klíčů do služby Key Vault. 

> [!NOTE]
> Azure Disk Encryption je potřeba nakonfigurovat následující zásady přístupu pro klientské aplikace Azure AD: _WrapKey_ a _nastavit_ oprávnění.

### <a name="bkmk_KVAPPSH"></a> Nastavit zásady přístupu trezoru klíčů pro aplikace Azure AD pomocí Azure Powershellu
Vaše aplikace Azure AD potřebuje oprávnění pro přístup k klíčů nebo tajných klíčů v trezoru. Použití [Set-AzureKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) rutiny k udělení oprávnění k aplikaci pomocí ID klienta (který se vygeneroval při aplikace byl zaregistrován) jako _– ServicePrincipalName_ Hodnota parametru. Další informace najdete v blogovém příspěvku [Azure Key Vault – krok za krokem](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectPSH).
2. Nastavte zásady přístupu trezoru klíčů pro aplikaci AD pomocí prostředí PowerShell.

     ```azurepowershell-interactive
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $rgname = 'MySecureRG'
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
     ```

### <a name="bkmk_KVAPCLI"></a> Nastavit zásady přístupu trezoru klíčů pro aplikace Azure AD pomocí Azure CLI
Použití [az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) nastavení zásad přístupu. Další informace najdete v tématu [Správa služby Key Vault pomocí CLI 2.0](../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

1. V případě potřeby [připojit ke svému předplatnému Azure](azure-security-disk-encryption-appendix.md#bkmk_ConnectCLI).
2. Poskytněte instanční objekt vytvoříte pomocí rozhraní příkazového řádku Azure přístup k získání tajných kódů a zalamování klíče pomocí následujícího příkazu:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="bkmk_KVAPRM"></a> Nastavit zásady přístupu trezoru klíčů pro aplikace Azure AD pomocí portálu

1. Otevřete skupinu prostředků s využitím key vaultu.
2. Vyberte svůj trezor klíčů, přejděte na **zásady přístupu**, pak klikněte na tlačítko **přidat nový**.
3. V části **vybrat objekt zabezpečení**, vyhledejte aplikaci Azure AD, kterou jste vytvořili a vyberte ho. 
4. Pro **oprávnění klíče**, zkontrolujte **zabalit klíč** pod **kryptografické operace**.
5. Pro **oprávnění tajného klíče**, zkontrolujte **nastavit** pod **operace správy tajný klíč**.
6. Klikněte na tlačítko **OK** uložte zásadu přístupu. 

![Azure Key Vault kryptografické operace/v – zabalit klíč](./media/azure-security-disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault tajný klíč oprávnění – nastavení ](./media/azure-security-disk-encryption/keyvault-portal-fig3b.png)

## <a name="bkmk_KVper"></a> Nastavení trezoru klíčů pokročilé zásady přístupu
Platforma Azure potřebuje přístup k šifrování klíčů nebo tajných klíčů v trezoru klíčů, aby byly k dispozici pro virtuální počítač pro spuštění a dešifrování svazky. Povolit šifrování disku v trezoru klíčů nebo nasazení se nezdaří.  

### <a name="bkmk_KVperPSH"></a> Nastavení trezoru klíčů pokročilé zásady přístupu pomocí Azure Powershellu
 Použijte rutinu prostředí PowerShell služby key vault [Set-AzureRmKeyVaultAccessPolicy](/powershell/module/azurerm.keyvault/set-azurermkeyvaultaccesspolicy) povolit šifrování disku pro trezor klíčů.

  - **Povolení služby Key Vault pro šifrování disku:** EnabledForDiskEncryption je vyžadován pro Azure Disk encryption.
      
     ```azurepowershell-interactive 
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDiskEncryption
     ```

  - **Povolení služby Key Vault pro nasazení, v případě potřeby:** Umožňuje zprostředkovateli prostředků Microsoft.Compute. k načítání tajných kódů z trezoru klíčů, když tento trezor klíčů se odkazuje v vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurepowershell-interactive
      Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForDeployment
     ```

  - **Povolení služby Key Vault pro šablonu nasazení, v případě potřeby:** Umožňuje získat tajné kódy z trezoru klíčů při tento trezor klíčů se odkazuje v nasazení šablony Azure Resource Manageru.

     ```azurepowershell-interactive             
     Set-AzureRmKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MySecureRG' -EnabledForTemplateDeployment
     ```

### <a name="bkmk_KVperCLI"></a> Nastavení trezoru klíčů pokročilé zásady přístupu pomocí Azure CLI
Použití [az keyvault update](/cli/azure/keyvault#az-keyvault-update) povolit šifrování disku pro trezor klíčů. 

 - **Povolení služby Key Vault pro šifrování disku:** Povolené pro šifrování disků je povinný. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-disk-encryption "true"
     ```  

 - **Povolení služby Key Vault pro nasazení, v případě potřeby:** Povolit virtuální počítače k načítání certifikátů uložených jako tajné kódy z trezoru.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-deployment "true"
     ``` 

 - **Povolení služby Key Vault pro šablonu nasazení, v případě potřeby:** Povolit správce prostředků k načítání tajných kódů z trezoru.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MySecureRG" --enabled-for-template-deployment "true"
     ```


### <a name="bkmk_KVperrm"></a> Nastavení trezoru klíčů pokročilé zásady přístupu k webu Azure portal

1. Vyberte váš trezor klíčů, přejděte na **zásady přístupu**, a **kliknutím zobrazíte pokročilé zásady přístupu**.
2. Vyberte políčko **povolení přístupu k Azure Disk Encryption pro šifrování svazku**.
3. Vyberte **povolit přístup k Azure Virtual Machines pro nasazení** a/nebo **povolit přístup k Azure Resource Manageru pro nasazení šablony**, v případě potřeby. 
4. Klikněte na **Uložit**.

![Azure key vaultu pokročilé zásady přístupu](./media/azure-security-disk-encryption/keyvault-portal-fig4.png)


## <a name="bkmk_KEK"></a> Nastavit šifrovací klíč klíče (volitelné)
Pokud chcete použít šifrovací klíč klíče (KEK) pro další úroveň zabezpečení pro šifrovací klíče, přidejte do trezoru klíčů KEK. Použití [Add-AzureKeyVaultKey](/powershell/module/azurerm.keyvault/add-azurekeyvaultkey) rutina pro vytvoření šifrovací klíč klíče v trezoru klíčů. Můžete také importovat KEK z vaší místní správy k klíče HSM. Další informace najdete v tématu [dokumentace ke službě Key Vault](../key-vault/key-vault-hsm-protected-keys.md). Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault. 

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
     # Fill in 'MyLocation', 'MySecureRG', and 'MySecureVault' with your values.
     # Use Get-AzureRmLocation to get available locations and use the DisplayName.
     # To use an existing resource group, comment out the line for New-AzureRmResourceGroup
     
     $Loc = 'MyLocation';
     $rgname = 'MySecureRG';
     $KeyVaultName = 'MySecureVault'; 
     New-AzureRmResourceGroup –Name $rgname –Location $Loc;
     New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc;
     $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname;
     $KeyVaultResourceId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId;
     $diskEncryptionKeyVaultUrl = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).VaultUri;
     
 # Step 2: Create the AD application and service principal.
     # Fill in 'MyAADClientSecret', "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
     # MyApplicationHomePage and the MyApplicationUri can be any values you wish.
     
     $aadClientSecret =  'MyAADClientSecret';
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force;
     $azureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -Password $aadClientSecretSec
     $servicePrincipal = New-AzureRmADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId;
     $aadClientID = $azureAdApplication.ApplicationId;
     
 #Step 3: Enable the vault for disk encryption and set the access policy for the Azure AD application.
     
     Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption;
     Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname;
     
 #Step 4: Create a new key in the key vault with the Add-AzureKeyVaultKey cmdlet.
     # Fill in 'MyKeyEncryptionKey' with your value.
     
     $keyEncryptionKeyName = 'MyKeyEncryptionKey';
     Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName -Destination 'Software';
     $keyEncryptionKeyUrl = (Get-AzureKeyVaultKey -VaultName $KeyVaultName -Name $keyEncryptionKeyName).Key.kid;
     
 #Step 5: Encrypt the disks of an existing IaaS VM
     # Fill in 'MySecureVM' with your value. 
     
     $VMName = 'MySecureVM';
     Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $vmName -AadClientID $aadClientID -AadClientSecret $aadClientSecret -DiskEncryptionKeyVaultUrl $diskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId;
```

## <a name="bkmk_Cert"></a> Ověřování pomocí certifikátu (volitelné)
Pokud chcete použít ověřování pomocí certifikátu, můžete nahrát jej do trezoru klíčů a nasadit ho do klienta. Před použitím skriptu prostředí PowerShell, měli byste se seznámit s požadavky Azure Disk Encryption pochopit postup ve skriptu. Ukázkový skript může být nutné změny pro vaše prostředí.

     
 ```powershell

 # Fill in "MySecureRG", "MySecureVault", and 'MyLocation' ('My location' only if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption. No need to set 'My location' in this case.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   #Setting some variables with the key vault information 
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId

   # Create the Azure AD application and associate the certificate with it. 
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname
   
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
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId
 ```

## <a name="bkmk_CertKEK"></a> Ověřování pomocí certifikátů a KEK (volitelné)

Pokud chcete používat ověřování certifikátů a zabalit klíč šifrování s KEK, můžete použít níže uvedený skript jako příklad. Před použitím skriptu prostředí PowerShell, měli byste se seznámit se všemi předchozími požadavky Azure Disk Encryption pochopit postup ve skriptu. Ukázkový skript může být nutné změny pro vaše prostředí.

> [!IMPORTANT]
> Ověřování na základě certifikátů Azure AD není aktuálně podporována u virtuálních počítačů s Linuxem.



     
 ```powershell
# Fill in 'MySecureRG', 'MySecureVault', and 'MyLocation' (if needed)

   $rgname = 'MySecureRG'
   $KeyVaultName= 'MySecureVault'

   # Create a key vault and set enabledForDiskEncryption property on it. 
   # Comment out the next three lines if you already have an existing key vault enabled for encryption.

   $Loc = 'MyLocation'
   New-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname -Location $Loc
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDiskEncryption

   # Create the Azure AD application and associate the certificate with it.  
   # Fill in "C:\certificates\mycert.pfx", "Password", "<My Application Display Name>", "<https://MyApplicationHomePage>", and "<https://MyApplicationUri>" with your values.
   # MyApplicationHomePage and the MyApplicationUri can be any values you wish

   $CertPath = "C:\certificates\mycert.pfx"
   $CertPassword = "Password"
   $Cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($CertPath, $CertPassword)
   $CertValue = [System.Convert]::ToBase64String($cert.GetRawCertData())

   $AzureAdApplication = New-AzureRmADApplication -DisplayName "<My Application Display Name>" -HomePage "<https://MyApplicationHomePage>" -IdentifierUris "<https://MyApplicationUri>" -CertValue $CertValue 
   $ServicePrincipal = New-AzureRmADServicePrincipal -ApplicationId $AzureAdApplication.ApplicationId

   $AADClientID = $AzureAdApplication.ApplicationId
   $aadClientCertThumbprint= $cert.Thumbprint

   ## Give access for setting secrets and wraping keys
   Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $rgname

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
   Set-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName -SecretValue $Secret
   Set-AzureRmKeyVaultAccessPolicy -VaultName $KeyVaultName -ResourceGroupName $rgname -EnabledForDeployment

   #Setting some variables with the key vault information and generating a KEK 
   # FIll in 'KEKName'
   
   $KEKName ='KEKName'
   $KeyVault = Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname
   $DiskEncryptionKeyVaultUrl = $KeyVault.VaultUri
   $KeyVaultResourceId = $KeyVault.ResourceId
   $KEK = Add-AzureKeyVaultKey -VaultName $KeyVaultName -Name $KEKName -Destination "Software"
   $KeyEncryptionKeyUrl = $KEK.Key.kid



   # Deploy the certificate to the VM
   # Fill in 'MySecureVM' with your value.

   $VMName = 'MySecureVM'
   $CertUrl = (Get-AzureKeyVaultSecret -VaultName $KeyVaultName -Name $KeyVaultSecretName).Id
   $SourceVaultId = (Get-AzureRmKeyVault -VaultName $KeyVaultName -ResourceGroupName $rgname).ResourceId
   $VM = Get-AzureRmVM -ResourceGroupName $rgname -Name $VMName 
   $VM = Add-AzureRmVMSecret -VM $VM -SourceVaultId $SourceVaultId -CertificateStore "My" -CertificateUrl $CertUrl
   Update-AzureRmVM -VM $VM -ResourceGroupName $rgname 

   #Enable encryption on the VM using Azure AD client ID and the client certificate thumbprint

   Set-AzureRmVMDiskEncryptionExtension -ResourceGroupName $rgname -VMName $VMName -AadClientID $AADClientID -AadClientCertThumbprint $AADClientCertThumbprint -DiskEncryptionKeyVaultUrl $DiskEncryptionKeyVaultUrl -DiskEncryptionKeyVaultId $KeyVaultResourceId -KeyEncryptionKeyUrl $keyEncryptionKeyUrl -KeyEncryptionKeyVaultId $KeyVaultResourceId
```

 
## <a name="next-steps"></a>Další postup
> [!div class="nextstepaction"]
> [Aktivace Azure Disk Encryption pro Windows](azure-security-disk-encryption-windows-aad.md)

> [!div class="nextstepaction"]
> [Aktivace Azure Disk Encryption pro Linux](azure-security-disk-encryption-linux-aad.md)
