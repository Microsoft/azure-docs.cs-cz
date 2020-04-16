---
title: Konfigurace klíčů spravovaných zákazníky pro váš účet Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat klíče spravované zákazníky pro váš účet Azure Cosmos DB pomocí Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 03/19/2020
ms.author: thweiss
ROBOTS: noindex, nofollow
ms.openlocfilehash: 733a85e492185e7e83922a3cc91d53c848b939a1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81411144"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurace klíčů spravovaných zákazníky pro váš účet Azure Cosmos pomocí azure key vaultu

> [!NOTE]
> V tomto okamžiku je nutné požádat o přístup k použití této funkce. Chcete-li tak [azurecosmosdbcmk@service.microsoft.com](mailto:azurecosmosdbcmk@service.microsoft.com)učinit, obraťte se na společnost .

Data uložená ve vašem účtu Azure Cosmos se automaticky a bezproblémově šifrují pomocí klíčů spravovaných společností Microsoft **(klíče spravované službou).** Volitelně můžete přidat druhou vrstvu šifrování s klíči, které spravujete **(klíče spravované zákazníkem).**

![Vrstvy šifrování kolem zákaznických dat](./media/how-to-setup-cmk/cmk-intro.png)

Klíče spravované zákazníkem je nutné uložit do [služby Azure Key Vault](../key-vault/key-vault-overview.md) a poskytnout klíč pro každý účet Azure Cosmos, který je povolený pomocí klíčů spravovaných zákazníky. Tento klíč se používá k šifrování všech dat uložených v tomto účtu.

> [!NOTE]
> V současné době jsou klíče spravované zákazníkem k dispozici jenom pro nové účty Azure Cosmos. Měli byste je nakonfigurovat během vytváření účtu.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a>Registrace poskytovatele prostředků Azure Cosmos DB pro vaše předplatné Azure

1. Přihlaste se na [portál Azure](https://portal.azure.com/), přejděte na předplatné Azure a na kartě **Nastavení** vyberte **zprostředkovatele prostředků:**

   ![Položka "Poskytovatelé prostředků" z levé nabídky](./media/how-to-setup-cmk/portal-rp.png)

1. Vyhledejte zprostředkovatele prostředků **Microsoft.DocumentDB.** Ověřte, zda je poskytovatel prostředků již označen jako registrovaný. Pokud ne, zvolte poskytovatele prostředků a vyberte **Registrovat**:

   ![Registrace zprostředkovatele prostředků Microsoft.DocumentDB](./media/how-to-setup-cmk/portal-rp-register.png)

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurace instance azure key vault

Použití klíčů spravovaných zákazníky s Azure Cosmos DB vyžaduje, abyste nastavili dvě vlastnosti instance Azure Key Vault, které chcete použít k hostování šifrovacích klíčů. Mezi tyto vlastnosti patří **obnovitelné odstranění** a **neodstraňovat**. Tyto vlastnosti nejsou ve výchozím nastavení povoleny. Můžete je povolit pomocí PowerShellu nebo Azure CLI.

Informace o povolení těchto vlastností v existující instanci služby Azure Key Vault naleznete v části Povolení obnovitelného odstranění a Povolení ochrany proti vymazání v jednom z následujících článků:

- [Jak používat obnovitelné odstranění s PowerShellem](../key-vault/key-vault-soft-delete-powershell.md)
- [Jak používat obnovitelné odstranění pomocí azure cli](../key-vault/key-vault-soft-delete-cli.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a>Přidání zásad přístupu k instanci služby Azure Key Vault

1. Z webu Azure najdete na instanci Azure Key Vault, kterou chcete použít k hostování šifrovacích klíčů. V levé nabídce vyberte **možnost Zásady přístupu:**

   !["Přístup k zásadám" z levé nabídky](./media/how-to-setup-cmk/portal-akv-ap.png)

1. Vyberte **+ Přidat zásady přístupu**.

1. V rozevírací nabídce **Klíčová oprávnění** vyberte **Získat**, **Rozbalit klíč**a **Zalamovat** klíčová oprávnění:

   ![Výběr správných oprávnění](./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png)

1. V části **Select principal**vyberte Možnost **Žádný vybraný**. Potom vyhledejte hlavní objekt **Azure Cosmos DB** a vyberte ho (pro usnadnění hledání `a232010e-820c-4083-83bb-3ace5fc29d0b` můžete také vyhledávat podle ID jistiny: pro všechny oblasti Azure s výjimkou oblastí Azure Government, kde je `57506a73-e302-42a9-b869-6f12d9ec29e9`hlavní ID). Nakonec zvolte **Vybrat** dole. Pokud hlavní povinný **Azure Cosmos DB** není v seznamu, možná budete muset znovu zaregistrovat poskytovatele prostředků **Microsoft.DocumentDB,** jak je popsáno v [části Registrovat poskytovatele prostředků](#register-resource-provider) v tomto článku.

   ![Vyberte hlavní objekt Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-add-ap.png)

1. Výběrem **možnosti Přidat** přidáte nové zásady přístupu.

## <a name="generate-a-key-in-azure-key-vault"></a>Generovat klíč v azure key vault

1. Z webu Azure Portal přejděte na instanci Azure Key Vault, kterou chcete použít k hostování šifrovacích klíčů. Potom vyberte **Klávesy** z levé nabídky:

   !["Klávesy" vstup z levé ho menu](./media/how-to-setup-cmk/portal-akv-keys.png)

1. Vyberte **Generovat nebo importovat**, zadejte název nového klíče a vyberte velikost klíče RSA. Pro nejlepší zabezpečení se doporučuje minimálně 3072. Pak vyberte **Vytvořit**:

   ![Vytvoření nového klíče](./media/how-to-setup-cmk/portal-akv-gen.png)

1. Po vytvoření klíče vyberte nově vytvořený klíč a jeho aktuální verzi.

1. Zkopírujte identifikátor **klíče**klíče , s výjimkou části za posledním lomítkem:

   ![Kopírování identifikátoru klíče klíče](./media/how-to-setup-cmk/portal-akv-keyid.png)

## <a name="create-a-new-azure-cosmos-account"></a>Vytvoření nového účtu Azure Cosmos

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Když vytvoříte nový účet Azure Cosmos DB z portálu Azure, zvolte **klíč spravovaný zákazníkem** v kroku **Šifrování.** In the **Key URI** field, paste the URI/key identifier of the Azure Key Vault key that you copied from the previous step:

![Nastavení parametrů CMK na webu Azure Portal](./media/how-to-setup-cmk/portal-cosmos-enc.png)

### <a name="using-azure-powershell"></a>Použití Azure Powershell

Když vytvoříte nový účet Azure Cosmos DB s PowerShellem:

- Předajuri, který určuje klíč Azure Key Vault zkopírovaný dříve pod vlastností **keyVaultKeyUri** v **propertyObject**.

- Jako verzi rozhraní API použijte **2019-12-12.**

> [!IMPORTANT]
> Parametr je `Location` nutné explicitně nastavit, aby byl účet úspěšně vytvořen pomocí klíčů spravovaných zákazníkem.

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

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manageru

Když vytvoříte nový účet Azure Cosmos prostřednictvím šablony Azure Resource Manageru:

- Předajuri, který určuje klíč Azure Key Vault, který jste zkopírovali dříve pod vlastností **keyVaultKeyUri** v objektu **vlastností.**

- Jako verzi rozhraní API použijte **2019-12-12.**

> [!IMPORTANT]
> Parametr je `Location` nutné explicitně nastavit, aby byl účet úspěšně vytvořen pomocí klíčů spravovaných zákazníkem.

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

Nasazení šablony pomocí následujícího skriptu Prostředí PowerShell:

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

### <a name="using-the-azure-cli"></a>Použití Azure CLI

Když vytvoříte nový účet Azure Cosmos prostřednictvím rozhraní příkazového řádku Azure, předajte identifikátor URI klíče Azure Key Vault, který jste zkopírovali dříve pod parametrem **--key-uri.**

```azurecli-interactive
resourceGroupName='myResourceGroup'
accountName='mycosmosaccount'
keyVaultKeyUri = 'https://<my-vault>.vault.azure.net/keys/<my-key>'

az cosmosdb create \
    -n $accountName \
    -g $resourceGroupName \
    --locations regionName='West US 2' failoverPriority=0 isZoneRedundant=False \
    --key-uri $keyVaultKeyUri
```

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-there-any-additional-charge-for-using-customer-managed-keys"></a>Existují nějaké další poplatky za použití klíčů spravovaných zákazníkem?

Ano. Chcete-li účet pro další výpočetní zatížení, které je nutné pro správu šifrování dat a dešifrování pomocí klíčů spravovaných zákazníkem, všechny operace provedené proti účtu Azure Cosmos spotřebovávají 25 procent zvýšení [jednotek požadavků](./request-units.md).

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Jaká data se zašifrují pomocí klíčů spravovaných zákazníkem?

Všechna data uložená ve vašem účtu Azure Cosmos jsou šifrovaná pomocí klíčů spravovaných zákazníkem, s výjimkou následujících metadat:

- Názvy vašich [účtů, databází a kontejnerů](./account-overview.md#elements-in-an-azure-cosmos-account) Azure Cosmos DB

- Názvy [uložených procedur](./stored-procedures-triggers-udfs.md)

- Cesty vlastností deklarované v [zásadách indexování](./index-policy.md)

- Hodnoty [klíčů oddílů](./partitioning-overview.md) kontejnerů

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Jsou klíče spravované zákazníkem podporované pro existující účty Azure Cosmos?

Tato funkce je v současné době k dispozici pouze pro nové účty.

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existuje plán na podporu jemnější rozlišovací schopnost než klíče na úrovni účtu?

Není v současné době, ale klíče na úrovni kontejneru jsou zvažovány.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Jak klíče spravované zákazníkem ovlivní zálohu?

Azure Cosmos DB provádí [pravidelné a automatické zálohování](./online-backup-and-restore.md) dat uložených ve vašem účtu. Tato operace zálohuje šifrovaná data. Chcete-li použít obnovenou zálohu, je vyžadován šifrovací klíč, který jste použili v době zálohování. To znamená, že nebylo provedeno žádné odvolání a verze klíče, která byla použita v době zálohování, bude stále povolena.

### <a name="how-do-i-revoke-an-encryption-key"></a>Jak lze odvolat šifrovací klíč?

Odvolání klíče se provádí zakázáním nejnovější verze klíče:

![Zakázání verze klíče](./media/how-to-setup-cmk/portal-akv-rev2.png)

Případně můžete odvolat všechny klíče z instance Azure Key Vault, můžete odstranit zásady přístupu udělené jistině Azure Cosmos DB:

![Odstranění zásad přístupu pro hlavní objekt Azure Cosmos DB](./media/how-to-setup-cmk/portal-akv-rev.png)

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jaké operace jsou k dispozici po odvolání klíče spravovaného zákazníkem?

Jedinou možnou operací, pokud byl šifrovací klíč odvolán, je odstranění účtu.

## <a name="next-steps"></a>Další kroky

- Další informace o [šifrování dat v Azure Cosmos DB](./database-encryption-at-rest.md).
- Získejte přehled o [zabezpečeném přístupu k datům v Cosmos DB](secure-access-to-data.md).
