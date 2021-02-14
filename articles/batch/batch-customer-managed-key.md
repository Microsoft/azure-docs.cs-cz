---
title: Konfigurace klíčů spravovaných zákazníkem pro účet Azure Batch s využitím Azure Key Vault a spravované identity
description: Naučte se šifrovat data Batch pomocí klíčů spravovaných zákazníkem.
author: pkshultz
ms.topic: how-to
ms.date: 02/11/2021
ms.author: peshultz
ms.openlocfilehash: d3f10436b95aaeb5eb35a873c2a3862c1492bd47
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100385060"
---
# <a name="configure-customer-managed-keys-for-your-azure-batch-account-with-azure-key-vault-and-managed-identity"></a>Konfigurace klíčů spravovaných zákazníkem pro účet Azure Batch s využitím Azure Key Vault a spravované identity

Ve výchozím nastavení Azure Batch používá klíče spravované platformou k šifrování všech zákaznických dat uložených ve službě Azure Batch, jako jsou certifikáty, metadata úlohy nebo úlohy. Volitelně můžete k šifrování dat uložených v Azure Batch použít vlastní klíče, například klíče spravované zákazníkem.

Klíče, které zadáte, musí být vygenerované v [Azure Key Vault](../key-vault/general/basic-concepts.md)a musí být dostupné se [spravovanými identitami pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

Existují dva typy spravovaných identit: [ *přiřazeno systémem* a *přiřazeno uživateli*](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types).

Můžete buď vytvořit účet Batch pomocí spravované identity přiřazené systémem, nebo vytvořit samostatnou spravovanou identitu přiřazenou uživatelem, která bude mít přístup k klíčům spravovaným zákazníkem. Projděte si [srovnávací tabulku](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) a pochopte rozdíly a zvažte, která možnost je pro vaše řešení nejvhodnější. Například pokud chcete použít stejnou spravovanou identitu pro přístup k více prostředkům Azure, bude nutná spravovaná identita přiřazená uživatelem. V takovém případě může stačit spravovaná identita přiřazená k účtu Batch. Pomocí spravované identity přiřazené uživatelem získáte také možnost vyhovět klíčům spravovaným zákazníkem při vytváření účtu Batch, jak je znázorněno [v následujícím příkladu](#create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys).

## <a name="create-a-batch-account-with-system-assigned-managed-identity"></a>Vytvoření účtu Batch se spravovanou identitou přiřazenou systémem

Pokud nepotřebujete samostatnou spravovanou identitu přiřazenou uživatelem, můžete při vytváření účtu Batch povolit spravovanou identitu přiřazenou systémem.

### <a name="azure-portal"></a>portál Azure

V [Azure Portal](https://portal.azure.com/)při vytváření účtů Batch vyberte v poli Typ identity na kartě **Upřesnit** příkaz **systém přiřazený** .

![Snímek obrazovky nového účtu Batch s typem identity přiřazený systémem](./media/batch-customer-managed-key/create-batch-account.png)

Po vytvoření účtu můžete v poli **ID objektu zabezpečení identity** v části **vlastnosti** najít jedinečný identifikátor GUID. **Typ identity** se zobrazí `System assigned` .

![Snímek obrazovky znázorňující jedinečný identifikátor GUID v poli ID objektu zabezpečení identity](./media/batch-customer-managed-key/linked-batch-principal.png)

Tuto hodnotu budete potřebovat, aby tento účet Batch mohl udělit přístup k Key Vault.

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
> Spravovaná identita přiřazená systémem, která je vytvořená v účtu Batch, se používá jenom pro načtení klíčů spravovaných zákazníkem z Key Vault. Tato identita není v fondech Batch k dispozici. Informace o použití spravované identity přiřazené uživatelem ve fondu najdete v tématu [Konfigurace spravovaných identit ve](managed-identity-pools.md)fondech Batch.

## <a name="create-a-user-assigned-managed-identity"></a>Vytvoření spravované identity přiřazené uživatelem

Pokud chcete, můžete [vytvořit spravovanou identitu přiřazenou uživatelem](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md#create-a-user-assigned-managed-identity) , která se dá použít pro přístup k klíčům spravovaným zákazníkem.

Pro přístup k Key Vault budete potřebovat hodnotu **ID klienta** této identity.

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurace instance Azure Key Vault

Azure Key Vault, ve kterých budou klíče vygenerovány, musí být vytvořeny ve stejném tenantovi jako váš účet Batch. Nemusí být ve stejné skupině prostředků nebo dokonce ani ve stejném předplatném.

### <a name="create-an-azure-key-vault"></a>Vytvoření služby Azure Key Vault

Při [vytváření instance Azure Key Vault](../key-vault/general/quick-create-portal.md) s použitím klíčů spravovaných zákazníkem pro Azure Batch se ujistěte,  že je povolená **Ochrana proti odstranění a vyprázdnění** .

![Obrazovka obrazovky pro vytváření Key Vault](./media/batch-customer-managed-key/create-key-vault.png)

### <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Přidání zásady přístupu do instance Azure Key Vault

V Azure Portal po vytvoření Key Vault přidejte v části **zásady přístupu** v části **Nastavení** přístup k účtu Batch pomocí spravované identity. V části **oprávnění klíče** vyberte **získat**, **zabalit klíč** a **Rozbalit klíč**.

![Snímek obrazovky s obrazovkou přidat zásady přístupu](./media/batch-customer-managed-key/key-permissions.png)

V poli **Vybrat** v části **hlavní objekty** zadejte jednu z následujících možností:

- Pro spravovanou identitu přiřazenou systémem: zadejte `principalId` , který jste dříve získali, nebo název účtu Batch.
- Pro uživatelem přiřazenou spravovanou identitu: zadejte **ID klienta** , které jste dříve načetli, nebo název spravované identity přiřazené uživatelem.

![Snímek obrazovky hlavní obrazovky](./media/batch-customer-managed-key/principal-id.png)

### <a name="generate-a-key-in-azure-key-vault"></a>Vygenerovat klíč v Azure Key Vault

V Azure Portal v části **klíč** klikněte na instance Key Vault a vyberte **Generovat/importovat**. Vyberte **typ klíče** , který má být `RSA` a **Velikost klíče RSA** , která má být alespoň `2048` bitů. `EC` typy klíčů nejsou aktuálně podporovány jako klíč spravovaný zákazníkem na účtu Batch.

![Vytvoření klíče](./media/batch-customer-managed-key/create-key.png)

Po vytvoření klíče klikněte na nově vytvořený klíč a aktuální verzi a zkopírujte **identifikátor klíče** v části **Properties (vlastnosti** ).  Ujistěte se, že v části **povolené operace** je zaškrtnuto políčko **zalomit klíč** a **Rozbalit klíč** .

## <a name="enable-customer-managed-keys-on-a-batch-account"></a>Povolení klíčů spravovaných zákazníkem na účtu Batch

Jakmile budete postupovat podle výše uvedených kroků, můžete na svém účtu Batch povolit klíče spravované zákazníkem.

### <a name="azure-portal"></a>portál Azure

V [Azure Portal](https://portal.azure.com/)přejdete na stránku účtu Batch. V části **šifrování** povolte **klíč spravovaný zákazníkem**. Můžete použít přímo identifikátor klíče, nebo můžete vybrat Trezor klíčů a potom kliknout na **Vybrat Trezor klíčů a klíč**.

![Snímek obrazovky znázorňující oddíl šifrování a možnost povolit klíč spravovaný zákazníkem](./media/batch-customer-managed-key/encryption-page.png)

### <a name="azure-cli"></a>Azure CLI

Po vytvoření účtu Batch pomocí spravované identity přiřazené systémem a přístup k Key Vault je potřeba aktualizovat účet Batch pomocí `{Key Identifier}` adresy URL pod `keyVaultProperties` parametrem. Nastavte také **encryption_key_source** jako `Microsoft.KeyVault` .

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_source Microsoft.KeyVault \
    --encryption_key_identifier {YourKeyIdentifier} 
```

## <a name="create-a-batch-account-with-user-assigned-managed-identity-and-customer-managed-keys"></a>Vytvoření účtu Batch pomocí uživatelsky přiřazené spravované identity a klíčů spravovaných zákazníkem

Pomocí klienta .NET Batch Management můžete vytvořit účet Batch, který bude mít uživatelem přiřazenou spravovanou identitu a klíče spravované zákazníkem.

```c#
EncryptionProperties encryptionProperties = new EncryptionProperties()
{
    KeySource = KeySource.MicrosoftKeyVault,
    KeyVaultProperties = new KeyVaultProperties()
    {
        KeyIdentifier = "Your Key Azure Resource Manager Resource ID"
    }
};

BatchAccountIdentity identity = new BatchAccountIdentity()
{
    Type = ResourceIdentityType.UserAssigned,
    UserAssignedIdentities = new Dictionary<string, BatchAccountIdentityUserAssignedIdentitiesValue>
    {
            ["Your Identity Azure Resource Manager ResourceId"] = new BatchAccountIdentityUserAssignedIdentitiesValue()
    }
};
var parameters = new BatchAccountCreateParameters(TestConfiguration.ManagementRegion, encryption:encryptionProperties, identity: identity);

var account = await batchManagementClient.Account.CreateAsync("MyResourceGroup",
    "mynewaccount", parameters); 
```

## <a name="update-the-customer-managed-key-version"></a>Aktualizace verze klíče spravovaného zákazníkem

Když vytváříte novou verzi klíče, aktualizujte účet Batch tak, aby používal novou verzi. Postupujte takto:

1. V Azure Portal přejděte na účet Batch a zobrazte nastavení šifrování.
2. Zadejte identifikátor URI pro novou verzi klíče. Alternativně můžete vybrat Key Vault a klíč znovu pro aktualizaci verze.
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
2. Zadejte identifikátor URI nového klíče. Alternativně můžete vybrat Key Vault a zvolit nový klíč.
3. Uložte provedené změny.

Pomocí rozhraní příkazového řádku Azure můžete také použít jiný klíč.

```azurecli
az batch account set \
    -n $accountName \
    -g $resourceGroupName \
    --encryption_key_identifier {YourNewKeyIdentifier} 
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

- **Jsou podporovány klíče spravované zákazníkem pro existující účty Batch?** No. Klíče spravované zákazníkem jsou podporovány pouze pro nové účty Batch.
- **Můžu vybrat velikosti klíčů RSA větší než 2048 bitů?** Ano, `3072` podporují se taky velikosti klíčů RSA a `4096` bitů.
- **Jaké operace jsou k dispozici po odvolání klíče spravovaného zákazníkem?** Jediná povolená operace je účet pro odstranění, pokud Batch ztratí přístup k klíči spravovanému zákazníkem.
- **Jak mám obnovit přístup k účtu Batch, pokud omylem odstraníte Key Vault klíč?** Vzhledem k tomu, že je povolená ochrana vyprázdnění a obnovitelné odstranění, můžete obnovit existující klíče. Další informace najdete v tématu [obnovení Azure Key Vault](../key-vault/general/key-vault-recovery.md).
- **Můžu zakázat klíče spravované zákazníkem?** Typ šifrování účtu Batch můžete kdykoli nastavit zpět na hodnotu spravovaný klíč společnosti Microsoft. Potom můžete tento klíč odstranit nebo změnit.
- **Jak můžu otočit své klíče?** Klíče spravované zákazníkem se automaticky neotočí. Pokud chcete klíč otočit, aktualizujte identifikátor klíče, ke kterému je účet přidružený.
- **Po obnovení přístupu, jak dlouho bude trvat, než bude účet Batch fungovat znovu?** Po obnovení přístupu může trvat až 10 minut, než se účet zpřístupní znovu.
- **Přestože účet Batch není k dispozici, co se stane s prostředky?** Dojde ke ztrátě všech fondů, které jsou spuštěny při přístupu k klíčům spravovaným zákazníkem, i nadále. Uzly se ale převedou do nedostupného stavu a úlohy se zastaví (a znovu se zařadí do fronty). Po obnovení přístupu budou uzly opět k dispozici a úlohy budou restartovány.
- **Vztahuje se tento mechanismus šifrování na disky virtuálních počítačů ve fondu Batch?** No. U fondů konfigurace cloudových služeb se pro operační systém a dočasný disk nepoužívá žádné šifrování. Pro fondy konfigurace virtuálních počítačů se operační systém a všechny zadané datové disky ve výchozím nastavení šifrují pomocí spravovaného klíče platformy Microsoft. V současné době nemůžete pro tyto disky zadat vlastní klíč. Pokud chcete zašifrovat dočasný disk virtuálních počítačů pro fond Batch pomocí spravovaného klíče platformy Microsoft, musíte ve svém fondu [Konfigurace virtuálního počítače](/rest/api/batchservice/pool/add#virtualmachineconfiguration) povolit vlastnost [diskEncryptionConfiguration](/rest/api/batchservice/pool/add#diskencryptionconfiguration) . U vysoce citlivých prostředí doporučujeme povolit dočasné šifrování disku a vyhnout se ukládání citlivých dat na disky s operačním systémem a daty. Další informace najdete v tématu [Vytvoření fondu s povoleným šifrováním disku](./disk-encryption.md) .
- **Je na účtu Batch k dispozici spravovaná identita přiřazená systémem na výpočetních uzlech?** No. Spravovaná identita přiřazená systémem se aktuálně používá jenom pro přístup k Azure Key Vault pro klíč spravovaný zákazníkem. Informace o použití spravované identity přiřazené uživatelem na výpočetních uzlech najdete v tématu [Konfigurace spravovaných identit ve fondech Batch](managed-identity-pools.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [osvědčených postupech zabezpečení v Azure Batch](security-best-practices.md).
- Přečtěte si další informace o[Azure Key Vault](../key-vault/general/basic-concepts.md).
