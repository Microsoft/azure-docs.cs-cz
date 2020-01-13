---
title: Konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB
description: Naučte se konfigurovat klíče spravované zákazníkem pro váš Azure Cosmos DB účet.
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/11/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 964c3e4e2de43e6bcae353f0b525eb62e6613361
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/12/2020
ms.locfileid: "75911866"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-db-account"></a>Konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB

> [!NOTE]
> V tuto chvíli musíte požádat o přístup k používání této možnosti. Pokud to chcete udělat, kontaktujte prosím [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Data uložená ve vašem účtu Azure Cosmos DBa se automaticky a hladce zašifrují. Azure Cosmos DB nabízí dvě možnosti správy klíčů používaných k zašifrování vašich dat v klidovém prostředí:
- **Klíče spravované službou**. Ve výchozím nastavení spravuje společnost Microsoft klíče používané k šifrování účtu Azure Cosmos DB.
- **Klíče spravované zákazníkem (CMK)** . Volitelně můžete zvolit přidání druhé vrstvy šifrování pomocí klíčů, které spravujete.

Klíče spravované zákazníkem musí být uloženy v [Azure Key Vault](../key-vault/key-vault-overview.md). Pro každý účet s povoleným CMK je nutné zadat klíč a použít ho k šifrování všech dat uložených v tomto účtu.

## <a name="setup"></a>Nastavení

Klíče spravované zákazníkem jsou v současné době dostupné jenom pro nové účty a je potřeba je nastavit během vytváření účtu.

### <a name="1-make-sure-the-azure-cosmos-db-resource-provider-is-registered-for-your-azure-subscription"></a>1. Zajistěte, aby byl pro vaše předplatné Azure registrovaný poskytovatel prostředků Azure Cosmos DB.

V Azure Portal přejděte do svého předplatného Azure a v nabídce vlevo vyberte **poskytovatelé prostředků** :

![Položka poskytovatelé prostředků z levé nabídky](./media/how-to-setup-cmk/portal-rp.png)

Vyhledejte poskytovatele prostředků **Microsoft. DocumentDB** .
- Pokud je poskytovatel prostředků už označený jako registrovaný, není potřeba nic dělat.
- Pokud ne, vyberte ji a klikněte na **zaregistrovat**:

    ![Registrace poskytovatele prostředků Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

### <a name="2-configure-your-azure-key-vault-instance"></a>2. konfigurace instance Azure Key Vault

Použití klíčů spravovaných zákazníkem s Azure Cosmos DB vyžaduje, aby se v Azure Key Vault instanci, kterou plánujete použít k hostování šifrovacích klíčů, nastavily dvě vlastnosti: **obnovitelné odstranění** a **nemazatelné**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, ale dají se povolit buď pomocí PowerShellu, nebo přes rozhraní příkazového řádku Azure.

Další informace o tom, jak tyto vlastnosti povolit u existující instance Azure Key Vault, najdete v částech s názvem povolení obnovitelného odstranění a povolení funkce vyprázdnit ochranu v jednom z následujících článků:
- [Použití obnovitelného odstranění s využitím PowerShellu](../key-vault/key-vault-soft-delete-powershell.md)
- [Použití obnovitelného odstranění pomocí Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

### <a name="3-add-an-access-policy-to-your-azure-key-vault-instance"></a>3. Přidejte do své instance Azure Key Vault zásady přístupu.

V Azure Portal přejdete do instance Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. Pak v nabídce vlevo vyberte **zásady přístupu** :

!["Zásady přístupu" v levé nabídce](./media/how-to-setup-cmk/portal-akv-ap.png)

- Vybrat **+ Přidat zásady přístupu**
- V rozevírací nabídce **oprávnění klíče** vyberte **získat**, **rozbalení klíče** a **zabalit klíč**:

    ![Výběr správných oprávnění](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

- V části **Vybrat objekt zabezpečení**vyberte možnost **není vybráno**. Pak vyhledejte a vyberte objekt zabezpečení **Azure Cosmos DB** . Nakonec klikněte na **Vybrat** v dolní části (Pokud se nepovedlo najít objekt zabezpečení **Azure Cosmos DB** , možná budete muset znovu zaregistrovat poskytovatele prostředků **Microsoft. DocumentDB** v kroku 1):

    ![Výběr objektu zabezpečení Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

- Vyberte **Přidat** a přidejte nové zásady přístupu.

### <a name="4-generate-a-key-in-azure-key-vault"></a>4. vygenerujte klíč v Azure Key Vault

Z Azure Portal použijte instanci Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. Pak v nabídce vlevo vyberte **klíče** :

![Položka Keys v levé nabídce](./media/how-to-setup-cmk/portal-akv-keys.png)

- Vybrat **vygenerovat/importovat**
- Zadejte název nového klíče, vyberte velikost klíče RSA (pro nejlepší zabezpečení doporučujeme minimálně 3072) a vyberte **vytvořit**:

    ![Vytváří se nový klíč.](./media/how-to-setup-cmk/portal-akv-gen.png)

- Po vytvoření klíče klikněte na nově vytvořený klíč a pak na jeho aktuální verzi.
- Zkopírujte **identifikátor klíče** klíče s výjimkou části za posledním lomítkem:

    ![Kopíruje se identifikátor klíče klíče.](./media/how-to-setup-cmk/portal-akv-keyid.png)

### <a name="5-create-a-new-azure-cosmos-db-account"></a>5. vytvoření nového účtu Azure Cosmos DB

#### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Při vytváření nového účtu Azure Cosmos DB z Azure Portal v kroku **šifrování** vyberte **klíč spravovaný zákazníkem** . V poli **identifikátor URI klíče** předejte identifikátor URI Azure Key Vault Key zkopírovaný z kroku 4:

![Nastavení parametrů CMK v Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

#### <a name="using-powershell"></a>Použití PowerShellu

Při vytváření nového účtu Azure Cosmos DB pomocí prostředí PowerShell,
- předejte identifikátor URI Azure Key Vaultho klíče zkopírovaného z kroku 4 pod vlastností **keyVaultKeyUri** v **PropertyObject**,
- Ujistěte se, že jako verzi rozhraní API používáte "2019-12-12".

> [!IMPORTANT]
> Parametr `Location` musí být explicitně nastaven pro účet, který má být úspěšně vytvořen pomocí CMK.

```powershell
$resourceGroupName = "myResourceGroup"
$accountLocation = "West US 2"
$accountName = "mycosmosaccount"

$failoverLocations = @(
    @{ "locationName"="West US 2"; "failoverPriority"=0 }
)

$CosmosDBProperties = @{
    "databaseAccountOfferType"="Standard";
    "locations"=$failoverLocations;
    "keyVaultKeyUri" = "https://<my-vault>.vault.azure.net/keys/<my-key>";
}

New-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    -ApiVersion "2019-12-12" -ResourceGroupName $resourceGroupName `
    -Location $accountLocation -Name $accountName -PropertyObject $CosmosDBProperties
```

#### <a name="using-azure-resource-manager-templates"></a>Používání šablon Azure Resource Manager

Při vytváření nového účtu Azure Cosmos DB prostřednictvím šablony Azure Resource Manager:
- předejte identifikátor URI Azure Key Vaultho klíče zkopírovaného z kroku 4 pod vlastností **keyVaultKeyUri** v objektu **Properties (vlastnosti** ).
- Ujistěte se, že jako verzi rozhraní API používáte "2019-12-12".

> [!IMPORTANT]
> Parametr `location` musí být explicitně nastaven pro účet, který má být úspěšně vytvořen pomocí CMK.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "keyVaultKeyUri": {
            "type": "string"
        }
    },
    "resources": 
    [
        {
            "type": "Microsoft.DocumentDB/databaseAccounts",
            "name": "[parameters('accountName')]",
            "apiVersion": "2019-12-12",
            "kind": "GlobalDocumentDB",
            "location": "[parameters('location')]",
            "properties": {
                "locations": [ 
                    {
                        "locationName": "[parameters('location')]",
                        "failoverPriority": 0,
                        "isZoneRedundant": false
                    }
                ],
                "databaseAccountOfferType": "Standard",
                "keyVaultKeyUri": "[parameters('keyVaultKeyUri')]"
            }
        }
    ]
}

```

```powershell
$resourceGroupName = "myResourceGroup"
$accountName = "mycosmosaccount"
$accountLocation = "West US 2"
$keyVaultKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-key>"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile "deploy.json" `
    -accountName $accountName `
    -location $accountLocation `
    -keyVaultKeyUri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-there-any-additional-charge-when-using-customer-managed-keys"></a>Platí se při použití klíčů spravovaných zákazníkem nějaké další poplatky?

Ano. Aby se zohlednilo dodatečné výpočetní zatížení, které je nutné ke správě šifrování a dešifrování dat pomocí klíčů spravovaných zákazníkem, budou všechny operace spouštěné s účtem Azure Cosmos DB získat 25% zvýšení počtu spotřebovaných [jednotek žádosti](./request-units.md) .

### <a name="what-data-gets-encrypted-with-the-cmk"></a>Jaká data se šifrují pomocí CMK?

Všechna data uložená ve vašem účtu Azure Cosmos DB se šifrují pomocí CMK, s výjimkou následujících metadat:
- názvy [účtů Azure Cosmos DB, databází a kontejnerů](./account-overview.md#elements-in-an-azure-cosmos-account),
- názvy [uložených procedur](./stored-procedures-triggers-udfs.md),
- cesty k vlastnostem deklarované v [zásadách indexování](./index-policy.md)
- hodnoty [klíče oddílu](./partitioning-overview.md)kontejneru.

### <a name="will-customer-managed-keys-be-supported-for-existing-accounts"></a>Budou se pro existující účty podporovat zákazníky spravované klíče?

Tato funkce je momentálně dostupná jenom pro nové účty.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existuje plán, který podporuje jemnější členitost než klíče na úrovni účtu?

V současné době se však považují klíče na úrovni kontejneru.

### <a name="how-does-customer-managed-keys-affect-backups"></a>Jaký vliv mají klíče spravované zákazníkem na zálohy?

Azure Cosmos DB provádí [pravidelné a automatické zálohování](./online-backup-and-restore.md) dat uložených ve vašem účtu. Tato operace zálohuje šifrovaná data. Aby bylo možné použít obnovenou zálohu, musí být šifrovací klíč používaný v době zálohování stále k dispozici. To znamená, že se neudělalo žádné odvolání a verze klíče, který se použil v době zálohování, se pořád povolí.

### <a name="how-do-i-revoke-an-encryption-key"></a>Návody odvolat šifrovací klíč?

Odvolání klíče se provádí zakázáním nejnovější verze klíče:

![Zakázání verze klíče](./media/how-to-setup-cmk/portal-akv-rev2.png)

Pokud chcete odebrat všechny klíče z Azure Key Vault instance, můžete taky odstranit zásady přístupu udělené objektu zabezpečení Azure Cosmos DB:

![Odstraňují se zásady přístupu pro objekt zabezpečení Azure Cosmos DB.](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jaké operace jsou k dispozici po odvolání klíče spravovaného zákazníkem?

Jediná operace, kterou je možné použít při odvolání šifrovacího klíče, je odstranění účtu.

## <a name="next-steps"></a>Další kroky

- Další informace o [šifrování dat v Azure Cosmos DB](./database-encryption-at-rest.md)
- Přehled [zabezpečeného přístupu k datům v Cosmos DB](secure-access-to-data.md)