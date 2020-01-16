---
title: Konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB
description: Naučte se konfigurovat klíče spravované zákazníkem pro účet Azure Cosmos DB s využitím Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/14/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: c77e89c509f10155ddc27e92f09465959b629f67
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979203"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos pomocí Azure Key Vault

> [!NOTE]
> V tuto chvíli musíte požádat o přístup k používání této možnosti. Pokud to chcete udělat, kontaktujte prosím [cosmosdbpm@microsoft.com](mailto:cosmosdbpm@microsoft.com).

Data uložená ve vašem účtu Azure Cosmos se automaticky a hladce zašifrují. Azure Cosmos DB nabízí dvě možnosti správy klíčů používaných k šifrování neaktivních dat:

- **Klíče spravované službou** – ve výchozím nastavení spravuje Microsoft klíče, které slouží k šifrování dat v účtu Azure Cosmos.

- **Klíče spravované zákazníkem (CMK)** – Volitelně můžete zvolit, že se má přidat druhá vrstva šifrování s vlastními klíči.

Klíče spravované zákazníkem musíte uložit do [Azure Key Vault](../key-vault/key-vault-overview.md) a zadat klíč pro každý účet Azure Cosmos, který je povolený pomocí klíčů spravovaných zákazníkem. Tento klíč se používá k šifrování všech dat uložených v daném účtu.

> [!NOTE]
> Klíče spravované zákazníkem jsou v současné době dostupné jenom pro nové účty Azure Cosmos a při vytváření účtu byste je měli nakonfigurovat.

## <a id="register-resource-provider"></a>Registrace poskytovatele prostředků Azure Cosmos DB pro předplatné Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com/), do svého předplatného Azure a na kartě **Nastavení** vyberte **poskytovatelé prostředků** :

   ![Položka poskytovatelé prostředků z levé nabídky](./media/how-to-setup-cmk/portal-rp.png)

1. Vyhledejte poskytovatele prostředků **Microsoft. DocumentDB** . Ověřte, jestli je poskytovatel prostředků už označený jako registrovaný. Pokud ne, zvolte poskytovatele prostředků a vyberte **Registrovat**:

   ![Registrace poskytovatele prostředků Microsoft. DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurace instance Azure Key Vault

Použití klíčů spravovaných zákazníkem s Azure Cosmos DB vyžaduje, abyste nastavili dvě vlastnosti instance Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. Tyto vlastnosti zahrnují **obnovitelné odstranění** a **nemazání**. Tyto vlastnosti nejsou ve výchozím nastavení povolené, můžete je povolit buď pomocí PowerShellu, nebo pomocí Azure CLI.

Další informace o tom, jak tyto vlastnosti povolit u existující instance Azure Key Vault, najdete v části povolení obnovitelného odstranění a povolení ochrany vyprázdnit v jednom z následujících článků:

- [Použití obnovitelného odstranění s využitím PowerShellu](../key-vault/key-vault-soft-delete-powershell.md)
- [Použití obnovitelného odstranění pomocí Azure CLI](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Přidání zásady přístupu do instance Azure Key Vault

1. V Azure Portal přejdete do instance Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. V nabídce vlevo vyberte **zásady přístupu** :

   !["Zásady přístupu" v levé nabídce](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Vybrat **+ Přidat zásady přístupu**

1. V rozevírací nabídce **oprávnění klíče** vyberte **získat**, **Rozbalit klíč** a **zabalit klíč** :

   ![Výběr správných oprávnění](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. V části **Vybrat objekt zabezpečení**vyberte možnost **není vybráno**. Pak vyhledejte objekt zabezpečení **Azure Cosmos DB** a vyberte ho. Nakonec klikněte na tlačítko **Vybrat** v dolní části (Pokud není v seznamu **Azure Cosmos DB** hlavní objekt zabezpečení, bude pravděpodobně nutné znovu zaregistrovat poskytovatele prostředků **Microsoft. DocumentDB** , jak je popsáno v části [registrace poskytovatele prostředků](#register-resource-provider) v tomto článku):

   ![Vyberte objekt zabezpečení Azure Cosmos DB.](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Vyberte **Přidat** a přidejte nové zásady přístupu.

## <a name="generate-a-key-in-azure-key-vault"></a>Vygenerovat klíč v Azure Key Vault

1. V Azure Portal navštivte instanci Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. Pak v nabídce vlevo vyberte **klíče** :

   ![Položka Keys v levé nabídce](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Vyberte **Generovat/importovat**, zadejte název nového klíče, vyberte velikost klíče RSA (pro nejlepší zabezpečení doporučujeme minimálně 3072) a pak vyberte **vytvořit**:

   ![Vytvořit nový klíč](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Po vytvoření klíče vyberte nově vytvořený klíč a pak na jeho aktuální verzi.

1. Zkopírujte **identifikátor klíče** klíče s výjimkou části za posledním lomítkem:

   ![Kopíruje se identifikátor klíče klíče.](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Vytvořit nový účet Azure Cosmos

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Při vytváření nového účtu Azure Cosmos DB z Azure Portal v kroku **šifrování** vyberte **klíč spravovaný zákazníkem** . Do pole **identifikátor URI klíče** vložte identifikátor URI nebo klíče Azure Key Vaultho klíče, který jste zkopírovali z předchozího kroku:

![Nastavení parametrů CMK v Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Při vytváření nového účtu Azure Cosmos DB pomocí prostředí PowerShell,

- Předejte identifikátor URI Azure Key Vaultho klíče zkopírovaného dříve pod vlastností **keyVaultKeyUri** v **PropertyObject**

- Jako verzi rozhraní API použijte **2019-12-12** .

> [!IMPORTANT]
> Je nutné nastavit parametr `Location` explicitně pro účet, který má být úspěšně vytvořen pomocí klíčů spravovaných zákazníkem.

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

### <a name="using-azure-resource-manager-template"></a>Pomocí šablony Azure Resource Manageru

Při vytváření nového účtu Azure Cosmos pomocí šablony Azure Resource Manager:

- Předejte identifikátor URI Azure Key Vaultho klíče, který jste zkopírovali dříve pod vlastností **keyVaultKeyUri** v objektu **Properties** .

- Jako verzi rozhraní API použijte **2019-12-12** .

> [!IMPORTANT]
> Je nutné nastavit parametr `Location` explicitně pro účet, který má být úspěšně vytvořen pomocí klíčů spravovaných zákazníkem.

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

Nasaďte šablonu pomocí následujícího skriptu prostředí PowerShell:

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

Ano. Aby se zohlednilo dodatečné výpočetní zatížení, které je nutné ke správě šifrování a dešifrování dat pomocí klíčů spravovaných zákazníkem, všechny operace spouštěné s účtem Azure Cosmos spotřebují 25% nárůstu počtu [jednotek žádosti](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Jaká data se šifrují pomocí klíčů spravovaných zákazníkem?

Všechna data uložená ve vašem účtu Azure Cosmos se šifrují pomocí klíčů spravovaných zákazníkem, s výjimkou těchto meta dat:

- Názvy [účtů Azure Cosmos DB, databází a kontejnerů](./account-overview.md#elements-in-an-azure-cosmos-account)

- Názvy [uložených procedur](./stored-procedures-triggers-udfs.md)

- Cesty k vlastnostem deklarované v [zásadách indexování](./index-policy.md)

- Hodnoty [klíče oddílu](./partitioning-overview.md) v kontejnerech

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Podporují se pro existující účty Azure Cosmos klíče spravované zákazníky?

Tato funkce je momentálně dostupná jenom pro nové účty.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existuje plán, který podporuje jemnější členitost než klíče na úrovni účtu?

V současné době se však považují klíče na úrovni kontejneru.

### <a name="how-does-customer-managed-keys-affect-a-backup"></a>Jaký vliv mají klíče spravované zákazníkem na zálohu?

Azure Cosmos DB provádí [pravidelné a automatické zálohování](./online-backup-and-restore.md) dat uložených ve vašem účtu. Tato operace zálohuje šifrovaná data. Chcete-li použít obnovenou zálohu, je vyžadován šifrovací klíč, který jste použili v době zálohování. To znamená, že se neudělalo žádné odvolání a verze klíče, který se použil v době zálohování, se pořád povolí.

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