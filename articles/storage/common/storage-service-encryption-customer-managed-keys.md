---
title: Šifrování služby Azure Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault | Dokumentace Microsoftu
description: Šifrovat úložiště objektů Blob v Azure a službou soubory Azure na straně služby při ukládání dat pomocí funkce služby Azure Storage a šifrování a dešifrování se při načítání dat pomocí klíčů spravovaných zákazníkem.
services: storage
author: lakasa
ms.service: storage
ms.topic: article
ms.date: 10/11/2018
ms.author: lakasa
ms.subservice: common
ms.openlocfilehash: c749a9dedef3970002c4f0672ffcc67aeaea422a
ms.sourcegitcommit: 698a3d3c7e0cc48f784a7e8f081928888712f34b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/31/2019
ms.locfileid: "55457424"
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování služby Storage pomocí klíčů spravovaných zákazníkem ve službě Azure Key Vault

Microsoft Azure platí závazek pomáhá zabezpečit a chránit vaše data pro splnění vaší organizace na zabezpečení a závazky dodržování předpisů. Jedním ze způsobů, aby Platforma Azure úložiště chrání vaše data je pomocí šifrování služby Storage (SSE), která šifruje vaše data při zápisu do úložiště a dešifruje data při jeho načítání. Šifrování a dešifrování je automatické, transparentnost a používá oborového 256bitového [šifrování AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard), jednu z nejsilnějších bloku šifer k dispozici.

Spravovaná Microsoftem šifrovací klíče můžete použít s SSE nebo můžete použít vlastní šifrovací klíče. Tento článek popisuje, jak pomocí vlastních šifrovacích klíčů. Další informace o používání klíčů spravovaných microsoftem, nebo o SSE obecně najdete v tématu [šifrování služby Storage pro neaktivní uložená data](storage-service-encryption.md).

SSE pro úložiště objektů Blob v Azure a službou soubory Azure je integrovaná se službou Azure Key Vault tak, aby služby key vault můžete použít ke správě šifrovacích klíčů. Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo rozhraní API služby Azure Key Vault můžete použít ke generování šifrovací klíče. S Azure Key Vault můžete spravovat a řídit vaše klíče a auditovat využití klíčů také.

> [!Note]  
> Šifrování služby Storage pomocí klíčů spravovaných zákazníkem není k dispozici pro [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md). [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md) používá standardní [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) na Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt) v Linuxu a poskytovat řešení šifrování integrované se službou KeyVault.

Proč vytvářet vlastní klíče? Vlastní klíče získáte větší flexibilitu, takže můžete vytvářet, otáčet, zakázat a definovat řízení přístupu. Vlastní klíče umožňují šifrovací klíče použité k ochraně vašich dat auditu.

## <a name="get-started-with-customer-managed-keys"></a>Začínáme s klíče spravované zákazníkem

Používat klíče spravované zákazníkem SSE, můžete vytvořit nový trezor klíčů a klíč, nebo můžete použít existujícího trezoru klíčů a klíč. Účet úložiště a trezoru klíčů musí být ve stejné oblasti, ale mohou být v různých předplatných.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

### <a name="step-1-create-a-storage-account"></a>Krok 1: vytvořit účet úložiště

Nejprve vytvořte účet úložiště, pokud již nemáte. Další informace najdete v článku o [vytvoření účtu úložiště](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Krok 2: Povolit SSE pro úložiště objektů Blob a souborů

Pokud chcete povolit SSE pomocí klíčů spravovaných zákazníkem, musí být dvě funkce ochrany klíčů obnovitelného odstranění a proveďte není vyprázdnit, povolená i ve službě Azure Key Vault. Tato nastavení Ujistěte se, že klíče nelze neúmyslně nebo úmyslně odstraněnými. Maximální doba uchování klíčů je nastavena na 90 dnů, ochraně uživatelů před útočníky nebo útoky ransomwaru.

Pokud chcete prostřednictvím kódu programu pro SSE povolit klíče spravované zákazníkem, můžete použít [Azure Storage Resource Provider REST API](https://docs.microsoft.com/rest/api/storagerp), [Storage Resource Provider Klientská knihovna pro .NET](https://docs.microsoft.com/dotnet/api), [ Prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Používat klíče spravované zákazníkem SSE, musíte přiřadit identitu účtu úložiště do účtu úložiště. Spuštěním následujícího příkazu Powershellu nebo Azure CLI můžete nastavit identitu:

```powershell
Set-AzStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

```azurecli-interactive
az storage account \
    --account-name <account_name> \
    --resource-group <resource_group> \
    --assign-identity
```

Spuštěním následujících příkazů Powershellu nebo rozhraní příkazového řádku Azure můžete povolit obnovitelné odstranění a proveďte není odstranění:

```powershell
($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzResource -ResourceId (Get-AzKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

```azurecli-interactive
az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enableSoftDelete=true

az resource update \
    --id $(az keyvault show --name <vault_name> -o tsv | awk '{print $1}') \
    --set properties.enablePurgeProtection=true
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Krok 3: Povolit šifrování pomocí klíčů spravovaných zákazníkem

Ve výchozím nastavení používá SSE klíčů spravovaných microsoftem. SSE můžete povolit pomocí klíčů spravovaných zákazníkem pro použití účtu úložiště [webu Azure portal](https://portal.azure.com/). Na **nastavení** okno pro účet úložiště, klikněte na tlačítko **šifrování**. Vyberte **použít vlastní klíč** možnosti, jak je znázorněno na následujícím obrázku.

![Portálu snímek obrazovky zobrazující možnost šifrování](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Krok 4: Vyberte svůj klíč

Váš klíč můžete zadat buď jako identifikátor URI, nebo výběrem klíč ze služby key vault.

#### <a name="specify-a-key-as-a-uri"></a>Zadejte klíč jako identifikátor URI

Chcete-li určit váš klíč z identifikátoru URI, postupujte takto:

1. Zvolte **zadejte identifikátor URI klíče** možnost.
2. V **URI klíče** zadejte identifikátor URI.

   ![Portálu snímek obrazovky zobrazující šifrování s, zadejte možnost identifikátor uri klíče](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)

#### <a name="specify-a-key-from-a-key-vault"></a>Zadejte klíč ze služby key vault

Chcete-li určit klíč ze služby key vault, postupujte takto:

1. Zvolte **vyberte ze služby Key Vault** možnost.
2. Vyberte trezor klíčů, který obsahuje klíč, který chcete použít.
3. Zvolte klíče z trezoru klíčů.

   ![Použití portálu bez snímek obrazovky zobrazující možnost vlastní klíče](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Pokud účet úložiště nemá přístup k trezoru klíčů, je můžete spustit příkaz prostředí Azure PowerShell k udělení přístupu je znázorněno na následujícím obrázku.

![Portálu snímek obrazovky zobrazující přístup byl odepřen pro key vault](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Můžete také udělit přístup prostřednictvím webu Azure portal přejděte do služby Azure Key Vault na webu Azure Portal a udělení přístupu k účtu úložiště.

Výše uvedený klíč můžete přidružit existující účet úložiště pomocí následujících příkazů prostředí PowerShell:

```powershell
$storageAccount = Get-AzStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```

### <a name="step-5-copy-data-to-storage-account"></a>Krok 5: Kopírování dat do účtu úložiště

Přenos dat do nového účtu úložiště tak, aby je šifrovaný. Další informace najdete v části [nejčastější dotazy k šifrování služby Storage](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Krok 6: Dotaz na stav šifrovaných dat

Dotaz na stav šifrovaná data.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Nejčastější dotazy týkající se SSE s zákazníka spravovat klíče

**Používám storage úrovně Premium; můžete použít klíče spravované zákazníkem pomocí SSE?**  
Ano, SSE pomocí klíčů spravovaných microsoftem a spravované zákazníkem je podporováno na úložiště úrovně Standard a Premium storage.

**Můžu vytvořit nové účty úložiště s SSE pomocí klíčů spravovaných zákazníkem povolit pomocí Azure Powershellu a rozhraní příkazového řádku Azure?**  
Ano.

**Jak Azure Storage stát když budu používat klíče spravované zákazníkem pomocí SSE?**  
Existuje stojí pro používání služby Azure Key Vault. Další podrobnosti najdete na webu [cenách služby Key Vault](https://azure.microsoft.com/pricing/details/key-vault/). Se neúčtují žádné další poplatky pro SSE, který je povolený pro všechny účty úložiště.

**Šifrování služby Storage je dostupné na Azure Managed Disks?**  
Šifrování služby Storage je dostupné pro Azure Managed Disks a klíčů spravovaných microsoftem, ale ne s zákazníkem spravované klíče. Namísto Managed Disks podpora SSE pomocí klíčů spravovaných zákazníkem, doporučujeme [Azure Disk Encryption](../../security/azure-security-disk-encryption-overview.md), který používá standardní [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) na Windows a [DM-Crypt](https://en.wikipedia.org/wiki/Dm-crypt)v Linuxu poskytují integrovaná šifrování služby KeyVault.

**Čím se liší od Azure Disk Encryption šifrování služby Storage?**  
Azure Disk Encryption poskytuje integraci řešení na základě operačního systému, jako je BitLocker a DM-Crypt a Azure Key Vaultu. Šifrování služby Storage poskytuje šifrování nativně ve vrstvě platformy Azure storage, níže virtuálního počítače.

**Můžete odvolat přístup k šifrovacím klíčům?**
Ano, můžete kdykoli odvolat přístup. Odvolání přístupu ke klíčům několika způsoby. Odkazovat na [Azure Key Vault prostředí PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault/) a [příkazového řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) další podrobnosti. Odvolání přístupu zablokuje efektivní oprávnění pro všechny objekty BLOB v účtu úložiště jako nedostupný šifrovací klíč účtu Azure Storage.

**Můžete vytvořit účet úložiště a klíč v jiné oblasti?**  
Účet úložiště a služby Azure Key Vault a klíč ne, musí být ve stejné oblasti.

**Můžete povolit klíče spravované zákazníkem pro SSE při vytváření účtu úložiště?**  
Ne. Při prvním vytvoření účtu úložiště, jsou k dispozici pro SSE pouze klíčů spravovaných microsoftem. Pokud chcete používat klíče spravované zákazníkem, je nutné aktualizovat vlastnosti účtu úložiště. Můžete programově aktualizovat váš účet úložiště pomocí REST nebo některé z klientských knihoven pro úložiště nebo aktualizovat vlastnosti účtu úložiště pomocí webu Azure portal po vytvoření účtu služby.

**Můžete zakázat šifrování pomocí klíčů spravovaných zákazníkem pomocí SSE?**  
Ne, nelze zakázat šifrování. Ve výchozím nastavení pro úložiště objektů Blob v Azure, Azure soubory, fronty Azure a Azure Table storage je povolené šifrování. Volitelně můžete přepnout od užívání klíčů spravovaných microsoftem, pomocí klíčů spravovaných zákazníkem a naopak.

**SSE je povoleno při vytváření nového účtu úložiště?**  
SSE je povolená pro všechny účty úložiště a pro úložiště objektů Blob v Azure, soubory Azure, Azure Queue storage a Azure Table storage.

**Nelze povolit SSE pomocí klíčů spravovaných zákazníkem v účtu úložiště.**  
Je to účet úložiště Azure Resource Manageru? Klíče spravované zákazníkem nepodporují účty klasického úložiště. SSE pomocí klíčů spravovaných zákazníkem lze povolit pouze pro účty úložiště Resource Manageru.

**Co je obnovitelné odstranění a nejsou provést odstranění? Je potřeba povolit toto nastavení používat klíče spravované zákazníkem SSE?**  
Obnovitelné odstranění a proveďte není vyprázdnění musí být povoleno používat klíče spravované zákazníkem SSE. Tato nastavení Ujistěte se, že váš klíč není neúmyslně nebo úmyslně odstraněnými. Maximální doba uchování klíčů je nastavena na 90 dnů, ochraně uživatelů před útočníky a útoky ransomwaru. Toto nastavení nejde vypnout.

**Je SSE pomocí klíčů spravovaných zákazníkem povolen pouze v konkrétní oblasti?**  
SSE pomocí klíčů spravovaných zákazníkem je k dispozici ve všech oblastech pro úložiště objektů Blob v Azure a službou soubory Azure.

**Jak se mám obrátit uživatel můžu mít nějaké problémy nebo chcete poskytnout zpětnou vazbu?**  
Kontakt [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) pro všechny problémy související s šifrování služby Storage.

## <a name="next-steps"></a>Další postup

- Další informace o komplexní sadu zabezpečení možnosti, které pomáhají vývojářům vytváření zabezpečených aplikací, najdete v článku [Příručka zabezpečení úložiště](storage-security-guide.md).
- Další informace o službě Azure Key Vault najdete v článku [co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?
- Začínáme v Azure Key Vault najdete v části [Začínáme se službou Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
