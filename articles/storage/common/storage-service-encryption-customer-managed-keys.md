---
title: Azure šifrování služby úložiště v Azure Key Vault klíče spravovaného zákazníkem | Microsoft Docs
description: Pomocí funkce šifrování služby úložiště Azure pro šifrování Azure Blob Storage na straně služby při ukládání dat a při získávání data pomocí klíče spravovaného zákazníkem ho dešifrovat.
services: storage
author: lakasa
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 03/07/2018
ms.author: lakasa
ms.openlocfilehash: 04688f943ac9eba27ca193aa2054c69b6a94547d
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2018
---
# <a name="storage-service-encryption-using-customer-managed-keys-in-azure-key-vault"></a>Šifrování služby úložiště v Azure Key Vault klíče spravovaného zákazníkem

Microsoft Azure se zaměřuje na pomáhá chránit a ochranu dat, aby splňovaly vaše organizace zabezpečení a dodržování předpisů závazky. Jedním ze způsobů, že Azure Storage chrání vaše data je prostřednictvím úložiště služby šifrování (SSE), která data šifruje při zápisu do úložiště a při získávání ji dešifruje data. Šifrování a dešifrování je automatická, transparentní a používá 256 bitů [šifrování AES](https://wikipedia.org/wiki/Advanced_Encryption_Standard), jednu z nejsilnějších bloku šifer k dispozici.

Spravované společností Microsoft šifrovací klíče můžete použít s SSE nebo můžete použít vlastní šifrovací klíče. Tento článek popisuje postup použití vlastních šifrovacích klíčů. Další informace o používání klíčů spravovaný společností Microsoft nebo o SSE v obecné najdete v tématu [šifrování služby úložiště pro Data v klidovém stavu](storage-service-encryption.md).

SSE pro úložiště objektů Blob a soubor je integrovaná s Azure Key Vault, tak, aby trezoru klíčů můžete použít ke správě šifrovacích klíčů. Můžete vytvořit vlastní šifrovací klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API pro Azure Key Vault generovat šifrovací klíče. S Azure Key Vault můžete spravovat a řídit klíče a také auditovat vaše použití klíče.

Proč vytvořit vlastní klíče? Vlastní klíče poskytují větší flexibilitu, takže můžete vytvářet, otáčení, zakázat a definovat řízení přístupu. Vlastní klíče také umožňují audit šifrovací klíče umožňuje chránit vaše data.

## <a name="get-started-with-customer-managed-keys"></a>Začínáme s klíče spravovaného zákazníkem

Používat spravované zákazníkem klíče SSE, můžete buď vytvořit nový trezor klíčů a klíč nebo můžete použít existující trezor klíčů a klíč. Účet úložiště a trezoru klíčů musí být ve stejné oblasti, ale mohou být v různých předplatných. 

### <a name="step-1-create-a-storage-account"></a>Krok 1: Vytvoření účtu úložiště

Nejprve vytvořte účet úložiště, pokud již nemáte. Další informace najdete v tématu [vytvořit nový účet úložiště](storage-quickstart-create-account.md).

### <a name="step-2-enable-sse-for-blob-and-file-storage"></a>Krok 2: Povolení SSE pro úložiště Blob a souborů

Použitím klíče spravovaného zákazníkem SSE vyžaduje, aby byla povolena dvě funkce ochrany klíčů logicky odstranit a provést není mazání. Tato nastavení Ujistěte se, že klíče nelze omylem nebo záměrně odstraněn. Maximální doba uchování období klíčů je nastaveno na 90 dnů, ochrana uživatele na základě nebezpečného actors nebo Ransomware, který útokům.

Pokud chcete prostřednictvím kódu programu povolit spravované zákazníkem klíče pro SSE, můžete použít [REST API služby Azure Storage prostředků zprostředkovatele](https://docs.microsoft.com/rest/api/storagerp), [prostředků zprostředkovatele Klientská knihovna pro úložiště pro .NET](https://docs.microsoft.com/dotnet/api), [ Prostředí Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview), nebo [rozhraní příkazového řádku Azure](https://docs.microsoft.com/azure/storage/storage-azure-cli).

Používat spravované zákazníkem klíče SSE, je nutné přiřadit identitu účtu úložiště k účtu úložiště. Identita můžete nastavit tak, že spustíte následující příkaz Powershellu:

```powershell
Set-AzureRmStorageAccount -ResourceGroupName \$resourceGroup -Name \$accountName -AssignIdentity
```

Můžete povolit logicky odstranit a provést není vyprázdnit spuštěním následujících příkazů prostředí PowerShell:

```powershell
($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enableSoftDelete -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties

($resource = Get-AzureRmResource -ResourceId (Get-AzureRmKeyVault -VaultName
$vaultName).ResourceId).Properties | Add-Member -MemberType NoteProperty -Name
enablePurgeProtection -Value 'True'

Set-AzureRmResource -resourceid $resource.ResourceId -Properties
$resource.Properties
```

### <a name="step-3-enable-encryption-with-customer-managed-keys"></a>Krok 3: Povolit šifrování pomocí klíče spravovaného zákazníkem

Ve výchozím nastavení používá SSE klíče spravované Microsoftem. Můžete povolit SSE spravované zákazníkem klíče pro účet úložiště pomocí [portál Azure](https://portal.azure.com/). Na **nastavení** pro účet úložiště, klikněte na **šifrování**. Vyberte **použít vlastní klíč** možnost, jak je znázorněno na následujícím obrázku.

![Možnost šifrování portálu snímek obrazovky zobrazující](./media/storage-service-encryption-customer-managed-keys/ssecmk1.png)

### <a name="step-4-select-your-key"></a>Krok 4: Vyberte klíč

Klíč můžete zadat buď jako identifikátor URI, nebo výběrem klíče z trezoru klíčů.

#### <a name="specify-a-key-as-a-uri"></a>Zadejte klíč jako identifikátor URI

Pokud chcete zadat klíč z identifikátoru URI, postupujte takto:

1. Vyberte **klávesy Enter URI** možnost.  
2. V **identifikátor URI klíčů** pole, zadejte identifikátor URI.

    ![Portál snímek obrazovky s šifrování s, zadejte možnost klíče uri](./media/storage-service-encryption-customer-managed-keys/ssecmk2.png)


#### <a name="specify-a-key-from-a-key-vault"></a>Zadejte klíč z trezoru klíčů 

Pokud chcete zadat klíče z trezoru klíčů, postupujte takto:

1. Vyberte **vyberte Key Vault** možnost.  
2. Vyberte trezor klíčů, který obsahuje klíč, který chcete použít.
3. Vyberte klíč z trezoru klíčů.

    ![Portálu snímek obrazovky zobrazující šifrování použijte možnost vlastní klíče](./media/storage-service-encryption-customer-managed-keys/ssecmk3.png)

Pokud účet úložiště nemá přístup k trezoru klíčů, můžete spustit příkaz prostředí Azure PowerShell, který je znázorněno na následujícím obrázku k udělení přístupu.

![Portál snímek obrazovky ukazující, přístup k trezoru klíčů odepřen](./media/storage-service-encryption-customer-managed-keys/ssecmk4.png)

Můžete také udělit přístup prostřednictvím portálu Azure tak, že přejdete do Azure Key Vault na portálu Azure a udělení přístupu k účtu úložiště.


Výše uvedený klíč můžete přidružit stávající účet úložiště pomocí následujících příkazů prostředí PowerShell:
```powershell
$storageAccount = Get-AzureRmStorageAccount -ResourceGroupName "myresourcegroup" -AccountName "mystorageaccount"
$keyVault = Get-AzureRmKeyVault -VaultName "mykeyvault"
$key = Get-AzureKeyVaultKey -VaultName $keyVault.VaultName -Name "keytoencrypt"
Set-AzureRmKeyVaultAccessPolicy -VaultName $keyVault.VaultName -ObjectId $storageAccount.Identity.PrincipalId -PermissionsToKeys wrapkey,unwrapkey,get
Set-AzureRmStorageAccount -ResourceGroupName $storageAccount.ResourceGroupName -AccountName $storageAccount.StorageAccountName -EnableEncryptionService "Blob" -KeyvaultEncryption -KeyName $key.Name -KeyVersion $key.Version -KeyVaultUri $keyVault.VaultUri
```


### <a name="step-5-copy-data-to-storage-account"></a>Krok 5: Kopírování dat do účtu úložiště

K přenosu dat do nového účtu úložiště tak, aby je zašifrovaná. Další informace najdete v části [– nejčastější dotazy pro šifrování služby úložiště](storage-service-encryption.md#faq-for-storage-service-encryption).

### <a name="step-6-query-the-status-of-the-encrypted-data"></a>Krok 6: Dotaz na stav šifrovaná data

Dotaz na stav šifrovaná data.

## <a name="faq-for-sse-with-customer-managed-keys"></a>Nejčastější dotazy týkající se SSE zákazníka spravované klíče

**Otázka: používám storage úrovně Premium; můžete použít spravovaný zákazníkem klíče s SSE?**

Odpověď: Ano SSE spravovaný společností Microsoft a spravované zákazníkem klíče je podporována na úložiště úrovně Standard a Premium storage.

**Otázka: je možné vytvořit nové účty úložiště s SSE klíče spravovaného zákazníkem povolit pomocí Azure PowerShell a rozhraní příkazového řádku Azure?**

Odpověď: Ano.

**Otázka: jak mnohem víc Azure Storage náklady Pokud se používá klíče spravovaného zákazníkem s SSE?**

Odpověď: je s náklady související pro použití Azure Key Vault. Další podrobnosti najdete na adrese [klíč trezoru ceny](https://azure.microsoft.com/pricing/details/key-vault/). Není k dispozici bez dalších nákladů pro SSE, která je povolena pro všechny účty úložiště.

**Otázka: je možné odvolat přístup k šifrovacím klíčům?**

A: Ano, můžete kdykoli odvolat přístup. Existuje několik způsobů odvolat přístup k klíče. Odkazovat na [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [příkazového řádku Azure Key Vault](https://docs.microsoft.com/cli/azure/keyvault) další podrobnosti. Odvolání přístupu bude efektivně blokovat přístup k všech objektů BLOB v účtu úložiště, protože účet šifrovací klíč je pravděpodobně nepřístupný úložiště Azure.

**Otázka: je možné vytvořit účet úložiště a klíč v jiné oblasti?**

A: účet úložiště a Azure Key Vault a klíč ne, musí být ve stejné oblasti.

**Otázka: lze povolit spravované zákazníkem klíče pro SSE při vytváření účtu úložiště?**

Odpověď: Ne. Při prvním vytvoření účtu úložiště, jsou k dispozici pro SSE pouze klíče spravované Microsoftem. Pokud chcete používat klíče spravovaného zákazníkem, je nutné aktualizovat vlastnosti účtu úložiště. Můžete použít REST nebo jeden z knihovny klienta úložiště prostřednictvím kódu programu aktualizace účtu úložiště nebo aktualizovat vlastnosti účtu úložiště pomocí portálu Azure po vytvoření účtu.

**Otázka: je možné zakázat šifrování při použití spravovaného zákazníkem klíče s SSE?**

A: šifrování Ne, nelze zakázat. Ve výchozím nastavení pro všechny služby – objekt Blob, soubor, tabulky a fronty úložiště je povolené šifrování. Volitelně můžete přepínat pomocí spravovaných společností Microsoft klíče pomocí klíče spravovaného zákazníkem a naopak.

**Otázka: je SSE ve výchozím nastavení povolené, po vytvoření nového účtu úložiště?**

Odpověď: SSE je povoleno ve výchozím nastavení pro všechny účty úložiště a pro všechny služby – objekt Blob, soubor, Table a Queue storage.

**Otázka: I nelze povolit SSE pomocí klíče spravovaného zákazníkem na svůj účet úložiště.**

Odpověď: je účet úložiště Azure Resource Manager? Spravované zákazníkem klíče nepodporuje klasické účty úložiště. SSE klíče spravovaného zákazníkem lze povolit pouze na účty úložiště Resource Manager.

**Otázka: co je logicky odstranit a provést není vymazat? Je potřeba povolit toto nastavení použít SSE klíče spravovaného zákazníkem?**

Odpověď: soft odstraňte a provést není vyprázdnění musí být povoleno používat SSE klíče spravovaného zákazníkem. Tato nastavení zajistěte, aby byl váš klíč není omylem nebo záměrně odstraněn. Maximální doba uchování období klíčů je nastaveno na 90 dnů, ochrana uživatele na základě nebezpečného actors a Ransomware, který útokům. Toto nastavení nelze zakázat.

**Otázka: je povolena pouze v určitých oblastí klíče spravovaného zákazníkem SSE?**

Odpověď: SSE spravované zákazníkem klíče je k dispozici ve všech oblastech pro úložiště Blob a souboru.

**Otázka: jak I contact někdo po jakýchkoli problémů nebo chcete poskytnout zpětnou vazbu?**

Odpověď: kontaktní [ ssediscussions@microsoft.com ](mailto:ssediscussions@microsoft.com) pro veškeré problémy související s šifrování služby úložiště.

## <a name="next-steps"></a>Další postup

-   Další informace o komplexní sadu zabezpečení možnosti, které pomáhají vývojářům vytvářet aplikace, zabezpečení naleznete v tématu [Průvodce zabezpečením úložiště](storage-security-guide.md).

-   Souhrnné informace o Azure Key Vault naleznete v tématu [co je Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-whatis)?

-   Začínáme v Azure Key Vault, najdete v části [Začínáme s Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-get-started).
