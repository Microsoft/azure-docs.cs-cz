---
title: Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)
description: V tomto článku se dozvíte, jak vytvořit a nakonfigurovat Trezor klíčů pro Azure Disk Encryption s Azure AD.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.topic: how-to
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 878828a40f7a14fe8bb5728ac06585e6aca2eec8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102555478"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Vytvoření a konfigurace trezoru klíčů pro Azure Disk Encryption s využitím Azure AD (předchozí verze)

**Nová vydaná verze Azure Disk Encryption eliminuje požadavek na poskytnutí parametru aplikace Azure AD, aby bylo možné povolit šifrování disku virtuálního počítače. V nové verzi už nebudete muset zadávat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD, které používají novou verzi. Pokyny k povolení šifrování disků virtuálního počítače pomocí nové vydané verze najdete v tématu [Azure Disk Encryption](disk-encryption-overview.md). Virtuální počítače, které jsou už šifrované pomocí parametrů aplikace Azure AD, se pořád podporují a měly by se udržovat dál se syntaxí AAD.**

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných klíčů disku.  Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/general/overview.md) a [zabezpečení trezoru klíčů](../../key-vault/general/secure-your-key-vault.md). 

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption s Azure AD (předchozí verze) zahrnuje tři kroky:

1. Vytvořte trezor klíčů. 
2. Nastavení aplikace a instančního objektu služby Azure AD.
3. Nastavte pro aplikaci Azure AD zásady přístupu k trezoru klíčů.
4. Nastavte pokročilé zásady přístupu k trezoru klíčů.
 
Můžete také, pokud chcete, vygenerovat nebo importovat klíč šifrování klíče (KEK).

Postup [Instalace nástrojů a připojení k Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)najdete v tématu hlavní informace o [Vytvoření a konfiguraci trezoru klíčů pro Azure Disk Encryption](disk-encryption-key-vault.md) článek.

> [!Note]
> Kroky v tomto článku jsou automatizovány ve skriptu rozhraní příkazového [řádku Azure Disk Encryption předpoklady](https://github.com/ejarvi/ade-cli-getting-started) a v tématu [Azure Disk Encryption předpoklady PowerShellu](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů. 
Azure Disk Encryption je integrovaná do [Azure Key Vault](https://azure.microsoft.com/documentation/services/key-vault/) , která vám pomůžou řídit a spravovat šifrovací klíče a tajné klíče disku v předplatném trezoru klíčů. Můžete vytvořit Trezor klíčů nebo použít existující pro Azure Disk Encryption. Další informace o trezorech klíčů najdete v tématu [Začínáme s Azure Key Vault](../../key-vault/general/overview.md) a [zabezpečení trezoru klíčů](../../key-vault/general/secure-your-key-vault.md). K vytvoření trezoru klíčů můžete použít šablonu Správce prostředků, Azure PowerShell nebo rozhraní příkazového řádku Azure. 


>[!WARNING]
>Abyste se ujistili, že šifrovací tajná klíče nejsou mezi regionálními hranicemi, Azure Disk Encryption potřebuje Key Vault a virtuální počítače, které se mají umístit do stejné oblasti. Vytvořte a použijte Key Vault, který je ve stejné oblasti jako virtuální počítač, který chcete zašifrovat. 


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

4. Poznamenejte si **název trezoru**, **název skupiny prostředků**, **ID prostředku**, **identifikátor URI trezoru** a **ID objektu** , které se vrátí pro pozdější použití při šifrování disků. 


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

4. Poznamenejte si **název trezoru** (název), **název skupiny prostředků**, **ID prostředku** (ID), **identifikátor URI trezoru** a **ID objektu** , které se vrátí pro pozdější použití. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Vytvoření trezoru klíčů s Správce prostředků šablonou

Trezor klíčů můžete vytvořit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně pro rychlý Start Azure klikněte na **nasadit do Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, Key Vault název, ID objektu, právních podmínek a smlouvy, a pak klikněte na **koupit**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Nastavení aplikace a instančního objektu služby Azure AD 
Pokud potřebujete povolit šifrování na běžícím virtuálním počítači v Azure, Azure Disk Encryption vygeneruje a zapíše šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů vyžaduje ověřování Azure AD. Vytvořte pro tento účel aplikaci Azure AD. Pro účely ověřování můžete použít buď ověřování na základě tajného klíče klienta, nebo [ověřování Azure AD založené na certifikátech klienta](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Nastavení aplikace a instančního objektu služby Azure AD pomocí Azure PowerShell 
Pokud chcete spustit následující příkazy, Získejte a použijte [modul Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. K vytvoření aplikace Azure AD použijte rutinu [New-AzADApplication](/powershell/module/az.resources/new-azadapplication) prostředí PowerShell. MyApplicationHomePage a MyApplicationUri mohou být libovolné hodnoty, které chcete.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $AzureAdApplication. ApplicationId je Azure AD ClientID a $aadClientSecret je tajný klíč klienta, který později použijete k povolení Azure Disk Encryption. Zabezpečte si tajný klíč klienta Azure AD správně. `$azureAdApplication.ApplicationId`Po spuštění se zobrazí ApplicationId.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Nastavení aplikace služby Azure AD a instančního objektu pomocí Azure CLI

Objekty služby můžete spravovat pomocí Azure CLI pomocí příkazů [AZ AD SP](/cli/azure/ad/sp) . Další informace najdete v tématu [Vytvoření instančního objektu Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Vytvoří nový instanční objekt.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  Vrácený identifikátor appId je Azure AD ClientID používané v dalších příkazech. Také je to hlavní název služby, který použijete pro AZ klíčů set-Policy. Heslo je tajný klíč klienta, který byste měli později použít k povolení Azure Disk Encryption. Zabezpečte si tajný klíč klienta Azure AD správně.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Nastavení aplikace a instančního objektu služby Azure AD, přestože Azure Portal
Pomocí kroků z [portálu use vytvořte aplikaci Azure Active Directory a instanční objekt, který má přístup k prostředkům,](../../active-directory/develop/howto-create-service-principal-portal.md) a vytvořte tak aplikaci Azure AD. Každý krok uvedený níže vás převezme přímo do oddílu článku, který se dokončí. 

1. [Ověřit požadovaná oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)
2. [Vytvoření aplikace Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal) 
     - Při vytváření aplikace můžete použít libovolný název a adresu URL pro přihlášení.
3. [Získejte ID aplikace a ověřovací klíč](../../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in). 
     - Ověřovací klíč je tajný klíč klienta a používá se jako AadClientSecret pro set-AzVMDiskEncryptionExtension. 
        - Ověřovací klíč používá aplikace jako přihlašovací údaje pro přihlášení ke službě Azure AD. V Azure Portal se tento tajný klíč nazývá klíče, ale nemá žádný vztah k trezorům klíčů. Zabezpečte tento tajný klíč správně. 
     - ID aplikace bude použito později jako AadClientId pro Set-AzVMDiskEncryptionExtension a jako ServicePrincipalName pro set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Nastavte zásady přístupu trezoru klíčů pro aplikaci Azure AD.
Pro zápis šifrovacích tajných klíčů do zadaného Key Vault Azure Disk Encryption potřebuje ID klienta a tajný klíč klienta Azure Active Directory aplikace, který má oprávnění k zápisu tajných kódů do Key Vault. 

> [!NOTE]
> Azure Disk Encryption vyžaduje, abyste v klientské aplikaci Azure AD nakonfigurovali následující zásady přístupu: _WrapKey_ a _nastavte_ oprávnění.

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí Azure PowerShell
Vaše aplikace Azure AD potřebuje oprávnění pro přístup ke klíčům nebo tajným klíčům v trezoru. Pomocí rutiny [set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte aplikaci oprávnění pomocí ID klienta (které bylo vygenerováno při registraci aplikace) jako hodnoty parametru _– servicePrincipalName_ . Další informace najdete v blogovém příspěvku [Azure Key Vault – krok za](/archive/blogs/kv/azure-key-vault-step-by-step)krokem. 

1. Nastavte zásady přístupu trezoru klíčů pro aplikaci AD pomocí PowerShellu.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí Azure CLI
Pomocí [AZ webtrezor set-Policy](/cli/azure/keyvault#az-keyvault-set-policy) nastavte zásady přístupu. Další informace najdete v tématu [správa Key Vault pomocí CLI 2,0](../../key-vault/general/manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Udělte instančnímu objektu, který jste vytvořili prostřednictvím Azure CLI přístup k získání tajných kódů a zabalte klíče pomocí následujícího příkazu:

```azurecli-interactive
az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí portálu

1. Otevřete skupinu prostředků ve vašem trezoru klíčů.
2. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu** a pak klikněte na **Přidat nový**.
3. V části **Vybrat objekt zabezpečení** vyhledejte aplikaci Azure AD, kterou jste vytvořili, a vyberte ji. 
4. U **klíčových oprávnění** zaškrtněte v části **kryptografické operace** **klíč pro zabalení** .
5. V případě **oprávnění tajného klíče** zaškrtněte v části **operace správy tajných klíčů** **nastaveno** .
6. Zásady přístupu uložíte kliknutím na **OK** . 

![Azure Key Vault kryptografické operace – zalomit klíč](../media/disk-encryption/keyvault-portal-fig3.png)

![Azure Key Vault oprávnění tajného kódu – sada](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Nastavte pokročilé zásady přístupu trezoru klíčů.
Platforma Azure potřebuje přístup k šifrovacím klíčům nebo tajným klíčům v trezoru klíčů, aby byly k dispozici pro virtuální počítač, aby bylo možné spouštět a dešifrovat svazky. V trezoru klíčů Povolte šifrování disku, jinak se nasazení nezdaří.  

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

1. Vyberte svůj Trezor klíčů, přejděte na **zásady přístupu** a **kliknutím zobrazte zásady pokročilého přístupu**.
2. Zaškrtněte políčko s názvem **Povolit přístup k Azure Disk Encryption pro šifrování svazku**.
3. V případě potřeby vyberte **Povolit přístup k Azure Virtual Machines pro nasazení** nebo **povolit přístup k Azure Resource Manager pro nasazení šablony**. 
4. Klikněte na **Uložit**.

![Zásady rozšířeného přístupu ke službě Azure Key trezor](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Nastavení klíčového šifrovacího klíče (volitelné)
Pokud chcete pro další vrstvu zabezpečení pro šifrovací klíče použít klíč šifrovacího klíče (KEK), přidejte do trezoru klíčů KEK. Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte šifrovací klíč klíče v trezoru klíčů. KEK můžete také importovat z místního modulu HSM správy klíčů. Další informace najdete v [dokumentaci Key Vault](../../key-vault/keys/hsm-protected-keys.md). Když je zadaný klíč šifrování klíče, Azure Disk Encryption používá tento klíč k zabalení šifrovacích tajných kódů před zápisem do Key Vault. 

* Při generování klíčů použijte typ klíče RSA. Azure Disk Encryption zatím nepodporuje používání klíčů eliptické křivky.

* Tajný kód trezoru klíčů a adresy URL KEK musí být ve verzi. Azure vynutilo toto omezení správy verzí. Platné tajné a KEK adresy URL najdete v následujících příkladech:

  * Příklad platné tajné adresy URL:   *https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příklad platné adresy URL KEK:   *https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje zadání čísel portů jako součást tajných kódů trezoru klíčů a adres URL KEK. Příklady nepodporovaných a podporovaných adres URL trezoru klíčů najdete v následujících příkladech:

  * Nepřijatelná adresa URL trezoru klíčů  *https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Přijatelná adresa URL trezoru klíčů:   *https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Nastavení šifrovacího klíče klíče pomocí Azure PowerShell 
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

## <a name="certificate-based-authentication-optional"></a>Ověřování na základě certifikátu (volitelné)
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Ověřování založené na certifikátech a KEK (volitelné)

Pokud chcete použít ověřování pomocí certifikátu a zabalit šifrovací klíč pomocí KEK, můžete jako příklad použít skript uvedený níže. Před použitím skriptu PowerShellu byste měli být obeznámeni se všemi předchozími Azure Disk Encryption nezbytnými pro pochopení kroků ve skriptu. Vzorový skript může pro vaše prostředí potřebovat změny.

     
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
