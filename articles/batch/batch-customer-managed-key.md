---
title: Konfigurace klíčů spravovaných zákazníkem pro účet Azure Batch s využitím Azure Key Vault a spravované identity
description: Naučte se šifrovat data Batch pomocí klíčů.
author: pkshultz
ms.topic: how-to
ms.date: 07/17/2020
ms.author: peshultz
ms.openlocfilehash: 2ed19846209d098d9eba8dba991e08d1fc57f185
ms.sourcegitcommit: b39cf769ce8e2eb7ea74cfdac6759a17a048b331
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2021
ms.locfileid: "98678005"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Konfigurace klíčů spravovaných zákazníkem pro účet Azure Batch s využitím Azure Key Vault a spravované identity

Ve výchozím nastavení Azure Batch používá klíče spravované platformou k šifrování všech zákaznických dat uložených ve službě Azure Batch, jako jsou certifikáty, metadata úlohy nebo úlohy. Volitelně můžete k šifrování dat uložených v Azure Batch použít vlastní klíče, například klíče spravované zákazníkem.

Klíče, které zadáte, musí být vygenerované v [Azure Key Vault](../key-vault/general/basic-concepts.md)a účty Batch, které chcete nakonfigurovat pomocí klíčů spravovaných zákazníkem, musí být povolené pomocí [spravované identity Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!IMPORTANT]
> Podpora klíčů spravovaných zákazníkem v Azure Batch je v současnosti ve verzi Public Preview pro oblasti Západní Evropa, Severní Evropa, Švýcarsko – sever, Střed USA, Střed USA – jih, Středozápadní USA, Východní USA, Východní USA 2, Západní USA 2, US Gov – Virginie a US Gov – Arizona.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti.
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Vytvoření účtu Batch se spravovanou identitou přiřazenou systémem

### <a name="azure-portal"></a>portál Azure

V [Azure Portal](https://portal.azure.com/)při vytváření účtů Batch vyberte v poli Typ identity na kartě **Upřesnit** příkaz **systém přiřazený** .

![Nový účet Batch s typem identity přiřazený systémem](./media/batch-customer-managed-key/create-batch-account.png)

Po vytvoření účtu můžete v poli **ID objektu zabezpečení identity** v části **vlastnost** najít jedinečný identifikátor GUID. **Typ identity** se zobrazí `SystemAssigned` .

![Jedinečný identifikátor GUID v poli ID objektu zabezpečení identity](./media/batch-customer-managed-key/linked-batch-principal.png)
 
### <a name="azure-cli"></a>Azure CLI

Při vytváření nového účtu Batch zadejte `SystemAssigned` `--identity` parametr.

```azurecli
resourceGroupName='myResourceGroup'
accountName='mybatchaccount'

az batch account create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' \
    --identity 'SystemAssigned'
```

Po vytvoření účtu můžete ověřit, zda byla na tomto účtu povolena spravovaná identita přiřazená systémem. Nezapomeňte si uvědomit, že `PrincipalId` Tato hodnota bude potřeba k tomu, aby tento účet Batch udělil přístup k Key Vault.

```azurecli
az batch account show \
    -n $accountName \
    -g $resourceGroupName \
    --query identity
```

> [!NOTE]
> Spravovaná identita přiřazená systémem, která je vytvořená v účtu Batch, se používá jenom pro načtení klíčů spravovaných zákazníkem z Key Vault. Tato identita není v fondech Batch k dispozici.

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurace instance Azure Key Vault

### <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Při vytváření instance Azure Key Vault s použitím klíčů spravovaných zákazníkem pro Azure Batch se ujistěte, že je povolená ochrana proti **odstranění** a **vyprázdnění** .

![Obrazovka pro vytváření Key Vault](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Přidání zásady přístupu do instance Azure Key Vault

V Azure Portal po vytvoření Key Vault přidejte v části **zásady přístupu** v části **Nastavení** přístup k účtu Batch pomocí spravované identity. V části **oprávnění klíče** vyberte **získat**, **zabalit klíč** a **Rozbalit klíč**. 

![Přidat zásady přístupu](./media/batch-customer-managed-key/key-permissions.png)

V poli **Vybrat** v části **hlavní** název zadejte, `principalId` které jste dříve načetli, nebo název účtu Batch.

![Zadejte principalId](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Vygenerovat klíč v Azure Key Vault

V Azure Portal v části **klíč** klikněte na instance Key Vault a vyberte **Generovat/importovat**. Vyberte **typ klíče** , který má být `RSA` a **Velikost klíče RSA** , která má být alespoň `2048` bitů. `EC` typy klíčů nejsou aktuálně podporovány jako klíč spravovaný zákazníkem na účtu Batch.

![Vytvoření klíče](./media/batch-customer-managed-key/create-key.png)

Po vytvoření klíče klikněte na nově vytvořený klíč a aktuální verzi a zkopírujte **identifikátor klíče** v části **Properties (vlastnosti** ).  Ujistěte se, že v části **povolené operace** je zaškrtnuto políčko **zalomit klíč** a **Rozbalit klíč** .

## <a name="enable-customer-managed-keys-on-azure-batch-account"></a>Povolit pro účet Azure Batch klíče spravované zákazníkem

### <a name="azure-portal"></a>portál Azure

V [Azure Portal](https://portal.azure.com/)přejdete na stránku účtu Batch. V části **šifrování** povolte **klíč spravovaný zákazníkem**. Můžete použít přímo identifikátor klíče, nebo můžete vybrat Trezor klíčů a potom kliknout na **Vybrat Trezor klíčů a klíč**.

![V části šifrování povolte klíč spravovaný zákazníkem.](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

Po vytvoření účtu Batch pomocí spravované identity přiřazené systémem a přístup k Key Vault je potřeba aktualizovat účet Batch pomocí `{Key Identifier}` adresy URL pod `keyVaultProperties` parametrem. Nastavte také **encryption_key_source** jako `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="update-the-customer-managed-key-version"></a>Aktualizace verze klíče spravovaného zákazníkem

Když vytváříte novou verzi klíče, aktualizujte účet Batch tak, aby používal novou verzi. Postupujte takto:

1. V Azure Portal přejděte na účet Batch a zobrazte nastavení šifrování.
2. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat Trezor klíčů a klíč znovu pro aktualizaci verze.
3. Uložte provedené změny.

K aktualizaci verze můžete použít taky Azure CLI.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourKeyIdentifierWithNewVersion} 
```
## <a name="use-a-different-key-for-batch-encryption"></a>Použít pro dávkové šifrování jiný klíč

Chcete-li změnit klíč, který se používá pro šifrování pomocí dávkového šifrování, postupujte podle následujících kroků:

1. Přejděte na účet Batch a zobrazte nastavení šifrování.
2. Zadejte identifikátor URI nového klíče. Alternativně můžete vybrat Trezor klíčů a zvolit nový klíč.
3. Uložte provedené změny.

Pomocí rozhraní příkazového řádku Azure můžete také použít jiný klíč.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```
## <a name="frequently-asked-questions"></a>Nejčastější dotazy
  * **Jsou podporovány klíče spravované zákazníkem pro existující účty Batch?** No. Klíče spravované zákazníkem jsou podporovány pouze pro nové účty Batch.
  * **Můžu vybrat velikosti klíčů RSA větší než 2048 bitů?** Ano, `3072` podporují se taky velikosti klíčů RSA a `4096` bitů.
  * **Jaké operace jsou k dispozici po odvolání klíče spravovaného zákazníkem?** Jediná povolená operace je účet pro odstranění, pokud Batch ztratí přístup k klíči spravovanému zákazníkem.
  * **Jak mám obnovit přístup k účtu Batch, pokud omylem odstraníte Key Vault klíč?** Vzhledem k tomu, že je povolená ochrana vyprázdnění a obnovitelné odstranění, můžete obnovit existující klíče. Další informace najdete v tématu [obnovení Azure Key Vault](../key-vault/general/key-vault-recovery.md).
  * **Můžu zakázat klíče spravované zákazníkem?** Typ šifrování účtu Batch můžete kdykoli nastavit zpět na hodnotu spravovaný klíč společnosti Microsoft. Potom můžete tento klíč odstranit nebo změnit.
  * **Jak můžu otočit své klíče?** Klíče spravované zákazníkem se automaticky neotočí. Pokud chcete klíč otočit, aktualizujte identifikátor klíče, ke kterému je účet přidružený.
  * **Po obnovení přístupu, jak dlouho bude trvat, než bude účet Batch fungovat znovu?** Po obnovení přístupu může trvat až 10 minut, než se účet zpřístupní znovu.
  * **Přestože účet Batch není k dispozici, co se stane s prostředky?** Dojde ke ztrátě všech fondů, které jsou spuštěny při přístupu k klíčům spravovaným zákazníkem, i nadále. Uzly se ale převedou do nedostupného stavu a úlohy se zastaví (a znovu se zařadí do fronty). Po obnovení přístupu budou uzly opět k dispozici a úlohy budou restartovány.
  * **Vztahuje se tento mechanismus šifrování na disky virtuálních počítačů ve fondu Batch?** No. U fondů konfigurace cloudových služeb se pro operační systém a dočasný disk nepoužívá žádné šifrování. Pro fondy konfigurace virtuálních počítačů se operační systém a všechny zadané datové disky ve výchozím nastavení šifrují pomocí spravovaného klíče platformy Microsoft. V současné době nemůžete pro tyto disky zadat vlastní klíč. Pokud chcete zašifrovat dočasný disk virtuálních počítačů pro fond Batch pomocí spravovaného klíče platformy Microsoft, musíte ve svém fondu [Konfigurace virtuálního počítače](/rest/api/batchservice/pool/add#virtualmachineconfiguration) povolit vlastnost [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) . U vysoce citlivých prostředí doporučujeme povolit dočasné šifrování disku a vyhnout se ukládání citlivých dat na disky s operačním systémem a daty. Další informace najdete v tématu [Vytvoření fondu s povoleným šifrováním disku](./disk-encryption.md) .
  * **Je na účtu Batch k dispozici spravovaná identita přiřazená systémem na výpočetních uzlech?** No. Tato spravovaná identita se v tuto chvíli používá jenom pro přístup k Azure Key Vault pro klíč spravovaný zákazníkem.
