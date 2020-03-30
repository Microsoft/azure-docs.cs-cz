---
title: Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)
description: Tento článek obsahuje předpoklady pro použití microsoft azure diskšifrování pro virtuální počítače IaaS.
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 03/15/2019
ms.custom: seodec18
ms.openlocfilehash: 5a42b1e0dc82b3340bbebe176c71cb1754d00664
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243377"
---
# <a name="creating-and-configuring-a-key-vault-for-azure-disk-encryption-with-azure-ad-previous-release"></a>Vytvoření a konfigurace trezoru klíčů pro šifrování disku Azure pomocí Azure AD (předchozí verze)

**Nová verze Azure Disk Encryption eliminuje požadavek na poskytování parametru aplikace Azure AD pro povolení šifrování disku virtuálního počítače. S novou verzí už nemusíte poskytovat přihlašovací údaje Azure AD během kroku povolení šifrování. Všechny nové virtuální počítače musí být zašifrované bez parametrů aplikace Azure AD pomocí nové verze. Pokyny k povolení šifrování disku virtuálního počítače pomocí nové verze najdete v [tématu Azure Disk Encryption](disk-encryption-overview.md). Virtuální počítače, které už byly zašifrované pomocí parametrů aplikace Azure AD, jsou stále podporované a měly by být nadále udržovány pomocí syntaxe AAD.**

Azure Disk Encryption používá Azure Key Vault k řízení a správě šifrovacích klíčů a tajných kódů disku.  Další informace o trezorech klíčů najdete [v tématu Začínáme s trezorem klíčů Azure](../../key-vault/key-vault-get-started.md) a [Zabezpečte trezor klíčů](../../key-vault/key-vault-secure-your-key-vault.md). 

Vytvoření a konfigurace trezoru klíčů pro použití s Azure Disk Encryption s Azure AD (předchozí verze) zahrnuje tři kroky:

1. Vytvoření trezoru klíčů 
2. Nastavte instanční objekt aplikace a služby Azure AD.
3. Nastavte pro aplikaci Azure AD zásady přístupu k trezoru klíčů.
4. Nastavte pokročilé zásady přístupu k trezoru klíčů.
 
Pokud chcete, můžete také vygenerovat nebo importovat šifrovací klíč (KEK).

Postup instalace nástrojů a [připojení k Azure](disk-encryption-key-vault.md#install-tools-and-connect-to-azure)najdete v hlavním článku Vytvoření a konfigurace [trezoru klíčů pro Azure Disk Encryption.](disk-encryption-key-vault.md)

> [!Note]
> Kroky v tomto článku jsou automatizované v [předpokladech Azure Disk Encryption CLI skript](https://github.com/ejarvi/ade-cli-getting-started) a [Azure Disk Encryption předpoklady PowerShell skript](https://github.com/Azure/azure-powershell/tree/master/src/Compute/Compute/Extension/AzureDiskEncryption/Scripts).


## <a name="create-a-key-vault"></a>Vytvořte trezor klíčů 
Azure Disk Encryption je integrovaný s [Azure Key Vault,](https://azure.microsoft.com/documentation/services/key-vault/) který vám pomůže řídit a spravovat klíče šifrování disku a tajné klíče v předplatném trezoru klíčů. Můžete vytvořit trezor klíčů nebo použít existující trezor pro Azure Disk Encryption. Další informace o trezorech klíčů najdete [v tématu Začínáme s trezorem klíčů Azure](../../key-vault/key-vault-get-started.md) a [Zabezpečte trezor klíčů](../../key-vault/key-vault-secure-your-key-vault.md). K vytvoření trezoru klíčů můžete použít šablonu Správce prostředků, Azure PowerShell nebo Azure CLI. 


>[!WARNING]
>Chcete-li se ujistit, že tajné kódy šifrování nepřekračují místní hranice, azure disk encryption potřebuje trezor klíčů a virtuální počítače, které mají být umístěny společně ve stejné oblasti. Vytvořte a použijte trezor klíčů, který je ve stejné oblasti jako virtuální hod, který má být šifrován. 


### <a name="create-a-key-vault-with-powershell"></a>Vytvoření trezoru klíčů pomocí PowerShellu

Pomocí prostředí Azure PowerShell můžete vytvořit trezor klíčů pomocí rutiny [New-AzKeyVault.](/powershell/module/az.keyvault/New-azKeyVault) Další rutiny pro trezor klíčů naleznete v tématu [Az.KeyVault](/powershell/module/az.keyvault/). 

1. V případě potřeby vytvořte novou skupinu prostředků pomocí [skupiny New-AzResourceGroup](/powershell/module/az.Resources/New-azResourceGroup).  Chcete-li vypsat umístění datových center, použijte [get-azlocation](/powershell/module/az.resources/get-azlocation). 
     
     ```azurepowershell-interactive
     # Get-AzLocation 
     New-AzResourceGroup –Name 'MyKeyVaultResourceGroup' –Location 'East US'
     ```

1. Vytvoření nového trezoru klíčů pomocí [aplikace New-AzKeyVault](/powershell/module/az.keyvault/New-azKeyVault)
    
      ```azurepowershell-interactive
     New-AzKeyVault -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -Location 'East US'
     ```

4. Všimněte si **názvu úložiště**, **názvu skupiny prostředků**, **ID prostředku**, **identifikátoru URI**a **ID objektu,** které jsou vráceny pro pozdější použití při šifrování disků. 


### <a name="create-a-key-vault-with-azure-cli"></a>Vytvoření trezoru klíčů pomocí azure CLI
Trezor klíčů můžete spravovat pomocí azure CLI pomocí příkazů [az keyvault.](/cli/azure/keyvault#commands) Chcete-li vytvořit trezor klíčů, použijte [az keyvault create](/cli/azure/keyvault#az-keyvault-create).

1. V případě potřeby vytvořte novou skupinu prostředků s [vytvořením skupiny az](/cli/azure/group#az-group-create). Chcete-li seznam umístění, použijte [seznam az seznam](/cli/azure/account#az-account-list) 
     
     ```azurecli-interactive
     # To list locations: az account list-locations --output table
     az group create -n "MyKeyVaultResourceGroup" -l "East US"
     ```

3. Vytvořte nový trezor klíčů pomocí [az keyvault create](/cli/azure/keyvault#az-keyvault-create).
    
     ```azurecli-interactive
     az keyvault create --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --location "East US"
     ```

4. Všimněte si **názvu (názvu) úložiště,** **názvu skupiny prostředků**, **ID prostředku** (ID), **identifikátoru URI úložiště**a **ID objektu,** které jsou vráceny pro pozdější použití. 

### <a name="create-a-key-vault-with-a-resource-manager-template"></a>Vytvoření trezoru klíčů pomocí šablony Správce prostředků

Trezor klíčů můžete vytvořit pomocí [šablony Správce prostředků](https://github.com/Azure/azure-quickstart-templates/tree/master/101-key-vault-create).

1. V šabloně Azure quickstart klikněte na **Deploy to Azure**.
2. Vyberte předplatné, skupinu prostředků, umístění skupiny prostředků, název trezoru klíčů, ID objektu, právní podmínky a smlouvu a klikněte na **Koupit**. 


## <a name="set-up-an-azure-ad-app-and-service-principal"></a>Nastavení aplikace a hlavního povinného poskytování služeb Azure AD 
Když potřebujete šifrování, které má být povoleno na spuštěném virtuálním počítači v Azure, Azure Disk Encryption generuje a zapisuje šifrovací klíče do trezoru klíčů. Správa šifrovacích klíčů v trezoru klíčů vyžaduje ověřování Azure AD. Vytvořte aplikaci Azure AD pro tento účel. Pro účely ověřování můžete použít ověřování pomocí tajných klíče klienta nebo [ověřování Azure AD založené na klientských certifikátech](../../active-directory/authentication/active-directory-certificate-based-authentication-get-started.md).


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-powershell"></a>Nastavení aplikace Azure AD a hlavního mocního služby pomocí Azure PowerShellu 
Chcete-li provést následující příkazy, získejte a použijte [modul Azure AD PowerShell](/powershell/azure/active-directory/install-adv2). 

1. Pomocí rutiny Prostředí PowerShell [nové azadaplikace](/powershell/module/az.resources/new-azadapplication) vytvořte aplikaci Azure AD. MyApplicationHomePage a MyApplicationUri může být libovolné hodnoty, které chcete.

     ```azurepowershell
     $aadClientSecret = "My AAD client secret"
     $aadClientSecretSec = ConvertTo-SecureString -String $aadClientSecret -AsPlainText -Force
     $azureAdApplication = New-AzADApplication -DisplayName "My Application Display Name" -HomePage "https://MyApplicationHomePage" -IdentifierUris "https://MyApplicationUri" -Password $aadClientSecretSec
     $servicePrincipal = New-AzADServicePrincipal –ApplicationId $azureAdApplication.ApplicationId
     ```

3. $azureAdApplication.ApplicationId je Azure AD ClientID a $aadClientSecret je tajný klíč klienta, který později použijete k povolení azure diskového šifrování. Chraňte tajný klíč klienta Azure AD odpovídajícím způsobem. Spuštění `$azureAdApplication.ApplicationId` vám ukáže ApplicationID.


### <a name="set-up-an-azure-ad-app-and-service-principal-with-azure-cli"></a>Nastavení aplikace Azure AD a hlavního mocního objektu služeb pomocí azure cli

Pomocí azure cli můžete spravovat své instanční objekty pomocí příkazů [az ad sp.](/cli/azure/ad/sp) Další informace najdete [v tématu Vytvoření instančního objektu Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

1. Vytvořte nový instanční objekt.
     
     ```azurecli-interactive
     az ad sp create-for-rbac --name "ServicePrincipalName" --password "My-AAD-client-secret" --skip-assignment 
     ```
3.  AppId vrácena je Azure AD ClientID používá v jiných příkazech. Je to také hlavní aktualizace služeb na dále jen pro zásady nastavení az keyvault. Heslo je tajný klíč klienta, který byste měli později použít k povolení azure disk encryption. Chraňte tajný klíč klienta Azure AD odpovídajícím způsobem.
 
### <a name="set-up-an-azure-ad-app-and-service-principal-though-the-azure-portal"></a>Nastavení aplikace Azure AD a instančního objektu služby přes portál Azure
Pomocí kroků z [portálu Use vytvořte instanční objekt a instancí Azure Active Directory, který má přístup](../../active-directory/develop/howto-create-service-principal-portal.md) k článku o prostředcích k vytvoření aplikace Azure AD. Každý krok uvedený níže vás přenese přímo do článku části k dokončení. 

1. [Ověření požadovaných oprávnění](../../active-directory/develop/howto-create-service-principal-portal.md#required-permissions)
2. [Vytvoření aplikace Azure Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application) 
     - Při vytváření aplikace můžete použít libovolné jméno a přihlašovací adresu URL.
3. [Získejte ID aplikace a ověřovací klíč](../../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in). 
     - Ověřovací klíč je tajný klíč klienta a používá se jako AadClientSecret pro Set-AzVMDiskEncryptionExtension. 
        - Ověřovací klíč používá aplikace jako přihlašovací údaje pro přihlášení k Azure AD. Na webu Azure Portal se tento tajný klíč nazývá klíče, ale nemá žádný vztah k trezorům klíčů. Zabezpečte toto tajemství odpovídajícím způsobem. 
     - ID aplikace bude později použito jako AadClientId pro Set-AzVMDiskEncryptionExtension a jako ServicePrincipalName pro Set-AzKeyVaultAccessPolicy. 

## <a name="set-the-key-vault-access-policy-for-the-azure-ad-app"></a>Nastavte zásady přístupu trezoru klíčů pro aplikaci Azure AD.
K zápisu tajných kódů šifrování do určeného trezoru klíčů potřebuje šifrování disku Azure ID a tajný klíč klienta aplikace Azure Active Directory, která má oprávnění k zápisu tajných kódů do trezoru klíčů. 

> [!NOTE]
> Azure Disk Encryption vyžaduje konfiguraci následujících zásad přístupu pro klientskou aplikaci Azure AD: _Oprávnění WrapKey_ a _Set._

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-powershell"></a>Nastavení zásad přístupu k trezoru klíčů pro aplikaci Azure AD pomocí Azure PowerShellu
Vaše aplikace Azure AD potřebuje práva pro přístup ke klíčům nebo tajným klíčům v trezoru. Pomocí rutiny [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) udělte oprávnění aplikaci pomocí ID klienta (které bylo generováno při registraci aplikace) jako hodnoty parametru _–ServicePrincipalName._ Další informace najdete v příspěvku blogu [Azure Key Vault – krok za krokem](https://blogs.technet.com/b/kv/archive/2015/06/02/azure-key-vault-step-by-step.aspx). 

1. Nastavte zásady přístupu k trezoru klíčů pro aplikaci Služby AD pomocí prostředí PowerShell.

     ```azurepowershell
     $keyVaultName = 'MySecureVault'
     $aadClientID = 'MyAadAppClientID'
     $KVRGname = 'MyKeyVaultResourceGroup'
     Set-AzKeyVaultAccessPolicy -VaultName $keyVaultName -ServicePrincipalName $aadClientID -PermissionsToKeys 'WrapKey' -PermissionsToSecrets 'Set' -ResourceGroupName $KVRGname
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-azure-cli"></a>Nastavení zásad přístupu k trezoru klíčů pro aplikaci Azure AD pomocí azure cli
Pomocí [zásady nastavení az keyvault](/cli/azure/keyvault#az-keyvault-set-policy) nastavte zásady přístupu. Další informace naleznete v [tématu Správa trezoru klíčů pomocí funkce CLI 2.0](../../key-vault/key-vault-manage-with-cli2.md#authorizing-an-application-to-use-a-key-or-secret).

Podejte instanční objekt, který jste vytvořili prostřednictvím přístupu k nastavení příkazu Azure CLI, abyste získali tajné klíče a zabalili klíče pomocí následujícího příkazu:
 
     ```azurecli-interactive
     az keyvault set-policy --name "MySecureVault" --spn "<spn created with CLI/the Azure AD ClientID>" --key-permissions wrapKey --secret-permissions set
     ```

### <a name="set-the-key-vault-access-policy-for-the-azure-ad-app-with-the-portal"></a>Nastavení zásad přístupu trezoru klíčů pro aplikaci Azure AD pomocí portálu

1. Otevřete skupinu prostředků pomocí trezoru klíčů.
2. Vyberte trezor klíčů, přejděte na **Přístupové zásady**a klikněte na **Přidat nový**.
3. V **části Vybrat hlavní objekt**vyhledejte aplikaci Azure AD, kterou jste vytvořili, a vyberte ji. 
4. U **oprávnění klíče**zaškrtněte **políčko Zalamovat klíč** v části **Kryptografické operace**.
5. U **tajných oprávnění**zaškrtněte **políčko Nastavit** v části Operace tajné **hospo- operace**.
6. Klepnutím na **tlačítko OK** uložte zásady přístupu. 

![Kontifikrygrafické operace služby Azure Key Vault – zalamovací klíč](../media/disk-encryption/keyvault-portal-fig3.png)

![Tajná oprávnění úložiště klíčů Azure – sada](../media/disk-encryption/keyvault-portal-fig3b.png)

## <a name="set-key-vault-advanced-access-policies"></a>Nastavte pokročilé zásady přístupu trezoru klíčů.
Platforma Azure potřebuje přístup k šifrovacím klíčům nebo tajným klíčům ve vašem trezoru klíčů, aby byly k dispozici virtuálnímu počítači pro zavádění a dešifrování svazků. Povolení šifrování disku v trezoru klíčů nebo nasazení se nezdaří.  

### <a name="set-key-vault-advanced-access-policies-with-azure-powershell"></a>Nastavení pokročilých zásad přístupu trezoru klíčů pomocí Azure PowerShellu
 Pomocí rutiny trezoru klíčů PowerShell [Set-AzKeyVaultAccessPolicy](/powershell/module/az.keyvault/set-azkeyvaultaccesspolicy) povolte šifrování disku pro trezor klíčů.

  - **Povolit trezor klíčů pro šifrování disku:** Pro šifrování disku Azure je vyžadováno technologie EnabledForDiskEncryption.
      
     ```azurepowershell-interactive 
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDiskEncryption
     ```

  - **V případě potřeby povolte trezor klíčů pro nasazení:** Umožňuje poskytovateli prostředků Microsoft.Compute načíst tajné klíče z tohoto trezoru klíčů, když se na tento trezor klíčů odkazuje při vytváření prostředků, například při vytváření virtuálního počítače.

     ```azurepowershell-interactive
      Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForDeployment
     ```

  - **V případě potřeby povolte trezor klíčů pro nasazení šablony:** Umožňuje Azure Resource Manager získat tajné klíče z tohoto trezoru klíčů, když tento trezor klíčů odkazuje v nasazení šablony.

     ```azurepowershell-interactive             
     Set-AzKeyVaultAccessPolicy -VaultName 'MySecureVault' -ResourceGroupName 'MyKeyVaultResourceGroup' -EnabledForTemplateDeployment
     ```

### <a name="set-key-vault-advanced-access-policies-using-the-azure-cli"></a>Nastavení pokročilých zásad přístupu trezoru klíčů pomocí příkazového příkazového příkazu Azure
Pomocí [aktualizace az keyvault](/cli/azure/keyvault#az-keyvault-update) povolte šifrování disku pro trezor klíčů. 

 - **Povolit trezor klíčů pro šifrování disku:** Je vyžadováno šifrování pro disk. 

     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-disk-encryption "true"
     ```  

 - **V případě potřeby povolte trezor klíčů pro nasazení:** Povolit virtuálním počítačům načítat certifikáty uložené jako tajné klíče z trezoru.
     ```azurecli-interactive
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-deployment "true"
     ``` 

 - **V případě potřeby povolte trezor klíčů pro nasazení šablony:** Povolte Správci prostředků načíst tajné klíče z úložiště.
     ```azurecli-interactive  
     az keyvault update --name "MySecureVault" --resource-group "MyKeyVaultResourceGroup" --enabled-for-template-deployment "true"
     ```


### <a name="set-key-vault-advanced-access-policies-through-the-azure-portal"></a>Nastavení pokročilých zásad přístupu trezoru klíčů prostřednictvím portálu Azure

1. Vyberte trezor klíčů, přejděte na **zásady přístupu**a **klepnutím zobrazte pokročilé zásady přístupu**.
2. Zaškrtněte políčko **Povolit přístup k šifrování disku Azure pro šifrování svazku**.
3. Vyberte **Povolit přístup k virtuálním počítačům Azure pro nasazení** a/nebo **Povolit přístup ke Správci prostředků Azure pro nasazení šablony**, v případě potřeby. 
4. Klikněte na **Uložit**.

![Rozšířené zásady přístupu trezoru klíčů Azure](../media/disk-encryption/keyvault-portal-fig4.png)


## <a name="set-up-a-key-encryption-key-optional"></a>Nastavení šifrovacího klíče (volitelné)
Pokud chcete použít šifrovací klíč klíče (KEK) pro další vrstvu zabezpečení šifrovacích klíčů, přidejte k trezoru klíčů kek. Pomocí rutiny [Add-AzKeyVaultKey](/powershell/module/az.keyvault/add-azkeyvaultkey) vytvořte v trezoru klíčů šifrovací klíč. KEK můžete také importovat z místního klíče správy hsm. Další informace naleznete v [tématu Dokumentace trezoru klíčů](../../key-vault/key-vault-hsm-protected-keys.md). Když je zadán klíč šifrovací klíč, Azure Disk Encryption používá tento klíč k zabalení tajných kódů šifrování před zápisem do trezoru klíčů. 

* Při generování klíčů použijte typ klíče RSA. Azure Disk Encryption ještě nepodporuje použití klíčů elliptic Curve.

* Tajný klíč trezoru klíčů a adresy URL KEK musí být verzí. Azure vynucuje toto omezení správy verzí. Platné adresy URL tajných kódů a KEK najdete v následujících příkladech:

  * Příklad platné tajné adresy URL:*https://contosovault.vault.azure.net/secrets/EncryptionSecretWithKek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Příklad platné adresy URL KEK:*https://contosovault.vault.azure.net/keys/diskencryptionkek/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

* Azure Disk Encryption nepodporuje určení čísla portů jako součást tajných kódů trezoru klíčů a adresy URL KEK. Příklady nepodporovaných a podporovaných adres URL trezoru klíčů naleznete v následujících příkladech:

  * Nepřijatelná adresa URL trezoru klíčů*https://contosovault.vault.azure.net:443/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*
  * Adresa URL přijatelného trezoru klíčů:*https://contosovault.vault.azure.net/secrets/contososecret/xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx*

### <a name="set-up-a-key-encryption-key-with-azure-powershell"></a>Nastavení šifrovacího klíče pomocí Azure PowerShellu 
Před použitím skriptu Prostředí PowerShell byste měli být obeznámeni s předpoklady azure disk šifrování pochopit kroky ve skriptu. Ukázkový skript může vyžadovat změny pro vaše prostředí. Tento skript vytvoří všechny předpoklady pro šifrování disku Azure a zašifruje existující virtuální počítač IaaS a zakončuje šifrovací klíč disku pomocí šifrovacího klíče klíče. 

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
Pokud chcete použít ověřování certifikátu, můžete jej odeslat do trezoru klíčů a nasadit do klienta. Před použitím skriptu Prostředí PowerShell byste měli být obeznámeni s předpoklady azure disk šifrování pochopit kroky ve skriptu. Ukázkový skript může vyžadovat změny pro vaše prostředí.

     
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

## <a name="certificate-based-authentication-and-a-kek-optional"></a>Ověřování na základě certifikátu a KEK (volitelné)

Pokud chcete použít ověřování certifikátu a zabalit šifrovací klíč s KEK, můžete použít níže uvedený skript jako příklad. Před použitím skriptu Prostředí PowerShell byste měli být obeznámeni se všemi předchozími předpoklady azure disk encryption pochopit kroky ve skriptu. Ukázkový skript může vyžadovat změny pro vaše prostředí.

     
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

[Povolení šifrování disku Azure pomocí Azure AD na virtuálních počítačích s Windows (předchozí verze)](disk-encryption-windows-aad.md)
