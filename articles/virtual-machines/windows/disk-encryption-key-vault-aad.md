---
title: Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)
description: Tento článek obsahuje požadavky pro používání Microsoft Azure Disk Encryption pro virtuální počítače IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79243377"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)

**Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure AD, aby bylo možné povolit šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD, které používají novou verzi. Pokyny k povolení šifrování disků virtuálního počítače pomocí nové vydané verze najdete v tématu [Azure Disk Encryption](disk-encryption-overview.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.**

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku.  Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md) a [zabezpečení trezoru klíčů](../../key-vault/key-vault-secure-your-key-vault.md). 

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption s Azure AD (předchozí verze) zahrnuje tři kroky:

1. Vytvoření trezoru klíčů 
2. Nastavení aplikace Azure AD a instanční objekt služby.
3. Nastavte zásady přístupu trezoru klíčů pro aplikace Azure AD.
4. Nastavení služby key vault pokročilé zásady přístupu.
 
Můžete také, pokud chcete, vygenerovat nebo importovat klíč šifrování klíče (KEK).

Postup [Instalace nástrojů a připojení k Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)najdete v tématu hlavní informace o [Vytvoření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md) článek.

> [!Note]
> Kroky v tomto článku jsou automatizovány ve skriptu rozhraní příkazového [řádku Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) a v tématu [Azure Disk Encryption předpoklady PowerShellu](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů 
Azure Disk Encryption je integrovaná do [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , která vám pomůžou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů. Můžete vytvořit trezor klíčů nebo použijte již existující pro Azure Disk Encryption. Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/key-vault-get-started.md) a [zabezpečení trezoru klíčů](../../key-vault/key-vault-secure-your-key-vault.md). Šablony Resource Manageru, Azure Powershellu nebo rozhraní příkazového řádku Azure CLI slouží k vytvoření služby key vault. 


>[!WARNING]
>Pokud chcete mít jistotu, že šifrování tajných kódů není překračují hranice regionální, Azure Disk Encryption musí být umístěné ve stejné oblasti služby Key Vault a virtuální počítače. Vytvoření a použití služby Key Vault, která je ve stejné oblasti jako virtuální počítač k šifrování. 


### <a name="create-a-key-vault-with-powershell"></a>Vytvoření trezoru klíčů pomocí PowerShellu

Trezor klíčů můžete vytvořit s Azure PowerShell pomocí rutiny [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault) . Další rutiny pro Key Vault najdete v tématu [AZ. klíčů trezor](/powershell/module/az.keyvault/). 

1. V případě potřeby vytvořte novou skupinu prostředků s [New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  K vypsání umístění datového centra použijte [příkaz Get-AzLocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Vytvoření nového trezoru klíčů pomocí [New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Poznamenejte si **název trezoru**, **název skupiny prostředků**, **ID prostředku**, **identifikátor URI trezoru**a **ID objektu** , které se vrátí pro pozdější použití při šifrování disků. 


### <a name="create-a-key-vault-with-azure-cli"></a>Vytvoření trezoru klíčů pomocí Azure CLI
Trezor klíčů můžete spravovat pomocí Azure CLI pomocí příkazů AZ klíč [trezoru](/cli/azure/keyvault#commands) . Chcete-li vytvořit Trezor klíčů, použijte příkaz [AZ Key trezor Create](/cli/azure/keyvault#az-keyvault-create).

1. V případě potřeby vytvořte novou skupinu prostředků pomocí [AZ Group Create](/cli/azure/group#az-group-create). Pokud chcete zobrazit seznam umístění, použijte [příkaz AZ Account list-Locations](/cli/azure/account#az-account-list) . 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Vytvořte nový trezor klíčů pomocí [AZ klíčů Create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Poznamenejte si **název trezoru** (název), **název skupiny prostředků**, **ID prostředku** (ID), **identifikátor URI trezoru**a **ID objektu** , které se vrátí pro pozdější použití. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Vytvoření trezoru klíčů s Správce prostředků šablonou

Trezor klíčů můžete vytvořit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, Key Vault název, ID objektu, právních podmínek a smlouvy, a pak klikněte na **koupit**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Nastavení aplikace a instančního objektu služby Azure AD 
Pokud je třeba šifrování, aby byla povolená na spuštěný virtuální počítač v Azure, Azure Disk Encryption generuje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů se vyžaduje ověřování Azure AD. Vytvořte aplikaci Azure AD pro tento účel. Pro účely ověřování můžete použít buď ověřování na základě tajného klíče klienta, nebo [ověřování Azure AD založené na certifikátech klienta](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Nastavení aplikace a instančního objektu služby Azure AD pomocí Azure PowerShell 
Pokud chcete spustit následující příkazy, Získejte a použijte [modul Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. K vytvoření aplikace Azure AD použijte rutinu [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) prostředí PowerShell. MyApplicationHomePage a MyApplicationUri můžou být všechny hodnoty, kterou požadujete.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication.ApplicationId je ID klienta Azure AD a $aadClientSecret je tajný kód klienta, který můžete později použít k povolení Azure Disk Encryption. Tajný klíč klienta Azure AD chrání odpovídajícím způsobem. Po spuštění `$azureAdApplication.ApplicationId` se zobrazí ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Nastavení aplikace služby Azure AD a instančního objektu pomocí Azure CLI

Objekty služby můžete spravovat pomocí Azure CLI pomocí příkazů [AZ AD SP](/cli/azure/ad/sp) . Další informace najdete v tématu [Vytvoření instančního objektu Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Vytvořte nový instanční objekt.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  AppId vrátila ID klienta Azure AD se používá v dalších příkazů. Je také hlavní název služby, kterou budete používat pro az keyvault set-policy. Heslo je tajný kód klienta, který jste měli použít později, aby Azure Disk Encryption. Tajný klíč klienta Azure AD chrání odpovídajícím způsobem.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Nastavení aplikace a instančního objektu služby Azure AD, přestože Azure Portal
Pomocí kroků z [portálu use vytvořte aplikaci Azure Active Directory a instanční objekt, který má přístup k prostředkům,](../../active-directory/develop/howto-create-service-principal-portal.md) a vytvořte tak aplikaci Azure AD. Jednotlivé kroky uvedené níže přejdete přímo do oddílu článku dokončete. 

1. [Ověřit požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Vytvoření aplikace Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Můžete použít libovolný název a adresu URL, které chcete při vytváření aplikace přihlašování.
3. [Získejte ID aplikace a ověřovací klíč](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Ověřovací klíč je tajný klíč klienta a používá se jako AadClientSecret pro set-AzVMDiskEncryptionExtension. 
        - Ověřovací klíč se používá aplikace jako přihlašovací údaje pro přihlášení k Azure AD. Na webu Azure Portal tento tajný kód se nazývá klíče, ale nemá žádný vztah k trezorům klíčů. Zabezpečte tento tajný kód správně. 
     - ID aplikace bude použito později jako AadClientId pro set-AzVMDiskEncryptionExtension a jako ServicePrincipalName pro set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD
K šifrování tajných kódů zápisu do zadané služby Key Vault, musí Azure Disk Encryption ID klienta a tajný kód klienta aplikace Azure Active Directory, který má oprávnění k zápisu tajných klíčů do služby Key Vault. 

> [!NOTE]
> Azure Disk Encryption vyžaduje, abyste v klientské aplikaci Azure AD nakonfigurovali následující zásady přístupu: _WrapKey_ a _nastavte_ oprávnění.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí Azure PowerShell
Vaše aplikace Azure AD potřebuje oprávnění pro přístup k klíčů nebo tajných klíčů v trezoru. Pomocí rutiny [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte aplikaci oprávnění pomocí ID klienta (které bylo vygenerováno při registraci aplikace) jako hodnoty parametru _– servicePrincipalName_ . Další informace najdete v blogovém příspěvku [Azure Key Vault – krok za](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx)krokem. 

1. Nastavte zásady přístupu trezoru klíčů pro aplikaci AD pomocí prostředí PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí Azure CLI
Pomocí [AZ webtrezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) nastavte zásady přístupu. Další informace najdete v tématu [správa Key Vault pomocí CLI 2,0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Poskytněte instanční objekt vytvoříte pomocí rozhraní příkazového řádku Azure přístup k získání tajných kódů a zalamování klíče pomocí následujícího příkazu:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí portálu

1. Otevřete skupinu prostředků s využitím key vaultu.
2. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu**a pak klikněte na **Přidat nový**.
3. V části **Vybrat objekt zabezpečení**vyhledejte aplikaci Azure AD, kterou jste vytvořili, a vyberte ji. 
4. U **klíčových oprávnění**zaškrtněte v části **kryptografické operace** **klíč pro zabalení** .
5. V případě **oprávnění tajného klíče**zaškrtněte v části **operace správy tajných klíčů** **nastaveno** .
6. Zásady přístupu uložíte kliknutím na **OK** . 

![Azure Key Vault kryptografické operace/v – zabalit klíč](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault tajný klíč oprávnění – nastavení](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů
Platforma Azure potřebuje přístup k šifrování klíčů nebo tajných klíčů v trezoru klíčů, aby byly k dispozici pro virtuální počítač pro spuštění a dešifrování svazky. Povolit šifrování disku v trezoru klíčů nebo nasazení se nezdaří.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů pomocí Azure PowerShell
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

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů pomocí Azure CLI
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


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Nastavení zásad rozšířeného přístupu trezoru klíčů pomocí Azure Portal

1. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu**a **kliknutím zobrazte zásady pokročilého přístupu**.
2. Zaškrtněte políčko s názvem **Povolit přístup k Azure Disk Encryption pro šifrování svazku**.
3. V případě potřeby vyberte **Povolit přístup k Azure Virtual Machines pro nasazení** nebo **povolit přístup k Azure Resource Manager pro nasazení šablony**. 
4. Klikněte na **Uložit**.

![Azure key vaultu pokročilé zásady přístupu](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Nastavení klíčového šifrovacího klíče (volitelné)
Pokud chcete použít šifrovací klíč klíče (KEK) pro další úroveň zabezpečení pro šifrovací klíče, přidejte do trezoru klíčů KEK. Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte šifrovací klíč klíče v trezoru klíčů. Můžete také importovat KEK z vaší místní správy k klíče HSM. Další informace najdete v [dokumentaci Key Vault](../../key-vault/key-vault-hsm-protected-keys.md). Pokud je zadaný šifrovací klíč klíče, Azure Disk Encryption používá tento klíč k šifrování tajných kódů zabalení před zápisem do služby Key Vault. 

* Při generování klíčů použijte typ klíče RSA. Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

* Tajný kód trezoru klíčů a adres URL KEK musí být označené verzí. Azure vynucuje toto omezení správy verzí. Platný tajný kód a adresy URL KEK viz následující příklady:

  * Příklad platné tajné adresy URL: *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příklad platné adresy URL KEK: *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů služby key vault a KEK adresy URL. Příkladem adresy URL není podporováno a podporované služby key vault najdete v následující příklady:

  * Nepřijatelná adresa URL trezoru klíčů *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Přijatelná adresa URL trezoru klíčů: *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Nastavení šifrovacího klíče klíče pomocí Azure PowerShell 
Před použitím skriptu prostředí PowerShell, měli byste se seznámit s požadavky Azure Disk Encryption pochopit postup ve skriptu. Ukázkový skript může být nutné změny pro vaše prostředí. Tento skript vytvoří všechny požadavky Azure Disk Encryption a zašifruje stávající virtuální počítače IaaS obtékání šifrovací klíč disku s použitím šifrovací klíč klíče. 

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

## <a name="certificate-based-authentication-optional"></a>Ověřování na základě certifikátu (volitelné)
Pokud chcete použít ověřování pomocí certifikátu, můžete nahrát jej do trezoru klíčů a nasadit ho do klienta. Před použitím skriptu prostředí PowerShell, měli byste se seznámit s požadavky Azure Disk Encryption pochopit postup ve skriptu. Ukázkový skript může být nutné změny pro vaše prostředí.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Ověřování založené na certifikátech a KEK (volitelné)

Pokud chcete používat ověřování certifikátů a zabalit klíč šifrování s KEK, můžete použít níže uvedený skript jako příklad. Před použitím skriptu prostředí PowerShell, měli byste se seznámit se všemi předchozími požadavky Azure Disk Encryption pochopit postup ve skriptu. Ukázkový skript může být nutné změny pro vaše prostředí.

     
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

[Povolení Azure Disk Encryption s Azure AD na virtuálních počítačích s Windows (předchozí verze)](disk-encryption-windows-aad.md)
