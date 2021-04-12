---
title: Konfigurace klíčů spravovaných zákazníkem pro účet Azure Cosmos DB
description: Naučte se konfigurovat klíče spravované zákazníkem pro účet Azure Cosmos DB s využitím Azure Key Vault
author: ThomasWeiss
ms.service: cosmos-db
ms.topic: how-to
ms.date: 04/01/2021
ms.author: thweiss
ms.openlocfilehash: 1b1fc0b51c1cd2a99ec97bec9f588699a893ceca
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222618"
---
# <a name="configure-customer-managed-keys-for-your-azure-cosmos-account-with-azure-key-vault"></a>Konfigurace klíčů spravovaných zákazníkem pro váš účet služby Azure Cosmos DB s využitím služby Azure Key Vault
[!INCLUDE[appliesto-all-apis](includes/appliesto-all-apis.md)]

Data uložená v účtu Azure Cosmos se automaticky a hladce šifrují pomocí klíčů spravovaných Microsoftem (**klíče spravované službou**). Volitelně můžete zvolit, že se má přidat druhá vrstva šifrování s klíči, které spravujete (**klíče spravované zákazníkem**).

:::image type="content" source="./media/how-to-setup-cmk/cmk-intro.png" alt-text="Vrstvy šifrování kolem zákaznických dat":::

Klíče spravované zákazníkem musíte uložit v [Azure Key Vault](../key-vault/general/overview.md) a zadat klíč pro každý účet Azure Cosmos, který je povolený pomocí klíčů spravovaných zákazníkem. Tento klíč se používá k šifrování všech dat uložených v daném účtu.

> [!NOTE]
> Klíče spravované zákazníkem jsou v současné době dostupné jenom pro nové účty Azure Cosmos. Při vytváření účtu byste je měli nakonfigurovat.

## <a name="register-the-azure-cosmos-db-resource-provider-for-your-azure-subscription"></a><a id="register-resource-provider"></a> Registrace poskytovatele prostředků Azure Cosmos DB pro předplatné Azure

1. Přihlaste se k [Azure Portal](https://portal.azure.com/), pokračujte na své předplatné Azure a na kartě **Nastavení** vyberte **poskytovatelé prostředků** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp.png" alt-text="Položky poskytovatelé prostředků z levé nabídky":::

1. Vyhledejte poskytovatele prostředků **Microsoft.DocumentDB** . Ověřte, jestli je poskytovatel prostředků už označený jako registrovaný. Pokud ne, zvolte poskytovatele prostředků a vyberte **Registrovat**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-rp-register.png" alt-text="Registrace poskytovatele prostředků Microsoft.DocumentDB":::

## <a name="configure-your-azure-key-vault-instance"></a>Konfigurace instance Azure Key Vault

Použití klíčů spravovaných zákazníkem s Azure Cosmos DB vyžaduje, abyste v instanci Azure Key Vault nastavili dvě vlastnosti, které chcete použít k hostování šifrovacích klíčů: **obnovitelné odstranění** a **vyprázdnit ochranu**.

Pokud vytvoříte novou instanci Azure Key Vault, povolte tyto vlastnosti během vytváření:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-prop.png" alt-text="Povolení obnovitelné ochrany při odstranění a vyprázdnění pro novou instanci Azure Key Vault":::

Pokud používáte existující instanci Azure Key Vault, můžete ověřit, že jsou tyto vlastnosti povolené, a to tak, že si prohlížíte oddíl **Properties (vlastnosti** ) na Azure Portal. Pokud některá z těchto vlastností není povolená, přečtěte si část povolení obnovitelného odstranění a povolení ochrany vyprázdnit v jednom z následujících článků:

- [Použití obnovitelného odstranění s využitím PowerShellu](../key-vault/general/key-vault-recovery.md)
- [Použití obnovitelného odstranění pomocí Azure CLI](../key-vault/general/key-vault-recovery.md)

## <a name="add-an-access-policy-to-your-azure-key-vault-instance"></a><a id="add-access-policy"></a> Přidání zásady přístupu do instance Azure Key Vault

1. V Azure Portal přejdete do instance Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. V nabídce vlevo vyberte **zásady přístupu** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-ap.png" alt-text="Zásady přístupu z nabídky vlevo":::

1. Vyberte **+ Přidat zásady přístupu**.

1. V rozevírací **nabídce oprávnění ke klíči** vyberte **získat**, **Rozbalit klíč** a **zabalit klíč** oprávnění:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap-perm2.png" alt-text="Výběr správných oprávnění":::

1. V části **Vybrat objekt zabezpečení** vyberte možnost **není vybráno**.

1. Vyhledejte objekt zabezpečení **Azure Cosmos DB** a vyberte ho (aby bylo snazší najít, můžete také vyhledat ID objektu zabezpečení: `a232010e-820c-4083-83bb-3ace5fc29d0b` pro libovolnou oblast Azure s výjimkou oblastí Azure Government, kde je hlavní ID `57506a73-e302-42a9-b869-6f12d9ec29e9` ). Pokud objekt zabezpečení **Azure Cosmos DB** v seznamu není, bude pravděpodobně nutné znovu zaregistrovat poskytovatele prostředků **Microsoft.DocumentDB** , jak je popsáno v části [registrace poskytovatele prostředků](#register-resource-provider) v tomto článku.

   > [!NOTE]
   > Tím se v zásadách přístupu Azure Key Vault zaregistruje Azure Cosmos DB identity First-straně. Pokud chcete tuto identitu první strany nahradit pomocí spravované identity účtu Azure Cosmos DB, přečtěte si téma [použití spravované identity v zásadách Azure Key Vault přístupu](#using-managed-identity).

1. Zvolte **Vybrat** v dolní části. 

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-add-ap.png" alt-text="Vyberte objekt zabezpečení Azure Cosmos DB.":::

1. Vyberte **Přidat** a přidejte nové zásady přístupu.

1. Pokud chcete uložit všechny změny, vyberte **Uložit** na instanci Key Vault.

## <a name="generate-a-key-in-azure-key-vault"></a>Vygenerovat klíč v Azure Key Vault

1. V Azure Portal navštivte instanci Azure Key Vault, kterou plánujete použít k hostování šifrovacích klíčů. Pak v nabídce vlevo vyberte **klíče** :

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keys.png" alt-text="Položka klíče z levé nabídky":::

1. Vyberte **Generovat/importovat**, zadejte název nového klíče a vyberte velikost klíče RSA. Pro zajištění nejlepšího zabezpečení se doporučuje minimálně 3072. Pak vyberte **vytvořit**:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-gen.png" alt-text="Vytvořit nový klíč":::

1. Po vytvoření klíče vyberte nově vytvořený klíč a jeho aktuální verzi.

1. Zkopírujte **identifikátor klíče** klíče, s výjimkou části za posledním lomítkem:

   :::image type="content" source="./media/how-to-setup-cmk/portal-akv-keyid.png" alt-text="Kopíruje se identifikátor klíče klíče.":::

## <a name="create-a-new-azure-cosmos-account"></a>Vytvořit nový účet Azure Cosmos

### <a name="using-the-azure-portal"></a>Použití webu Azure Portal

Když v Azure Portal vytvoříte nový účet Azure Cosmos DB, v kroku **šifrování** vyberte **klíč spravovaný zákazníkem** . Do pole **identifikátor URI klíče** vložte identifikátor URI nebo klíče Azure Key Vaultho klíče, který jste zkopírovali z předchozího kroku:

:::image type="content" source="./media/how-to-setup-cmk/portal-cosmos-enc.png" alt-text="Nastavení parametrů CMK v Azure Portal":::

### <a name="using-azure-powershell"></a><a id="using-powershell"></a> Použití Azure PowerShell

Když vytváříte nový účet Azure Cosmos DB pomocí prostředí PowerShell:

- Předejte identifikátor URI Azure Key Vaultho klíče, který jste zkopírovali dříve pod vlastností **keyVaultKeyUri** v **PropertyObject**.

- Jako verzi rozhraní API použijte **2019-12-12** nebo novější.

> [!IMPORTANT]
> Vlastnost je nutné nastavit `locations` explicitně, aby byl účet úspěšně vytvořen pomocí klíčů spravovaných zákazníkem.

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

Po vytvoření účtu můžete ověřit, že klíče spravované zákazníkem byly povoleny načtením identifikátoru URI Azure Key Vaultho klíče:

```powershell
Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
    -ResourceType "Microsoft.DocumentDb/databaseAccounts" `
    | Select-Object -ExpandProperty Properties `
    | Select-Object -ExpandProperty keyVaultKeyUri
```

### <a name="using-an-azure-resource-manager-template"></a>Použití šablony Azure Resource Manager

Když vytváříte nový účet Azure Cosmos pomocí šablony Azure Resource Manager:

- Předejte identifikátor URI Azure Key Vaultho klíče, který jste zkopírovali dříve pod vlastností **keyVaultKeyUri** v objektu **Properties** .

- Jako verzi rozhraní API použijte **2019-12-12** nebo novější.

> [!IMPORTANT]
> Vlastnost je nutné nastavit `locations` explicitně, aby byl účet úspěšně vytvořen pomocí klíčů spravovaných zákazníkem.

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

### <a name="using-the-azure-cli"></a><a id="using-azure-cli"></a> Použití rozhraní příkazového řádku Azure

Když vytvoříte nový účet Azure Cosmos pomocí Azure CLI, předejte identifikátor URI Azure Key Vaultho klíče, který jste zkopírovali dříve pod `--key-uri` parametrem.

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

Po vytvoření účtu můžete ověřit, že klíče spravované zákazníkem byly povoleny načtením identifikátoru URI Azure Key Vaultho klíče:

```azurecli-interactive
az cosmosdb show \
    -n $accountName \
    -g $resourceGroupName \
    --query keyVaultKeyUri
```

## <a name="using-a-managed-identity-in-the-azure-key-vault-access-policy"></a><a id="using-managed-identity"></a> Použití spravované identity v zásadách přístupu Azure Key Vault

Tato zásada přístupu zajišťuje přístup k šifrovacím klíčům, ke kterým má účet Azure Cosmos DB přístup. K tomu je potřeba udělit přístup ke konkrétní identitě Azure Active Directory (AD). Podporují se dva typy identit:

- Identitu první strany Azure Cosmos DB lze použít k udělení přístupu ke službě Azure Cosmos DB.
- [Spravované identity](how-to-setup-managed-identity.md) účtu Azure Cosmos DB můžete použít k udělení přístupu ke svému účtu konkrétně.

Vzhledem k tomu, že se spravovaná identita přiřazená systémem může načíst jenom po vytvoření vašeho účtu, musíte nejdřív vytvořit účet pomocí identity první strany, jak je popsáno [výše](#add-access-policy). Potom:

1. Pokud jste to ještě neudělali během vytváření účtu, povolte na svém účtu [spravovanou identitu přiřazenou systémem](how-to-setup-managed-identity.md) a zkopírujte `principalId` přiřazenou identitu.

1. Přidejte k účtu Azure Key Vault nové zásady přístupu, jak je popsáno [výše](#add-access-policy), ale použijte `principalId` místo toho, co jste zkopírovali v předchozím kroku, místo identity první strany Azure Cosmos DB.

1. Aktualizujte účet Azure Cosmos DB, abyste určili, že při přístupu k šifrovacím klíčům v Azure Key Vault chcete použít spravovanou identitu přiřazenou systémem. To můžete provést zadáním této vlastnosti v šabloně Azure Resource Manager účtu:

   ```json
   {
       "type": " Microsoft.DocumentDB/databaseAccounts",
       "properties": {
           "defaultIdentity": "SystemAssignedIdentity",
           // ...
       },
       // ...
   }
   ```

1. Volitelně můžete odebrat Azure Cosmos DB identitu od první strany z vaší zásady přístupu Azure Key Vault.

## <a name="key-rotation"></a>Obměna klíčů

Rotace klíče spravovaného zákazníkem používaného vaším účtem Azure Cosmos se dá provádět dvěma způsoby.

- Vytvoří novou verzi klíče, která se v tuto chvíli používá Azure Key Vault:

  :::image type="content" source="./media/how-to-setup-cmk/portal-akv-rot.png" alt-text="Vytvořit novou verzi klíče":::

- Zaměňte klíč, který se aktuálně používá, úplně jiný, a to tak, že na svém účtu aktualizujete identifikátor URI klíče. V Azure Portal přejděte na účet Azure Cosmos a v nabídce vlevo vyberte **šifrování dat** :

    :::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Položka nabídky šifrování dat":::

    Potom nahraďte **identifikátor URI klíče** novým klíčem, který chcete použít, a vyberte **Uložit**:

    :::image type="content" source="./media/how-to-setup-cmk/portal-key-swap.png" alt-text="Aktualizovat identifikátor URI klíče":::

    V prostředí PowerShell můžete dosáhnout stejného výsledku:

    ```powershell
    $resourceGroupName = "myResourceGroup"
    $accountName = "mycosmosaccount"
    $newKeyUri = "https://<my-vault>.vault.azure.net/keys/<my-new-key>"
    
    $account = Get-AzResource -ResourceGroupName $resourceGroupName -Name $accountName `
        -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    
    $account.Properties.keyVaultKeyUri = $newKeyUri
    
    $account | Set-AzResource -Force
    ```

Předchozí klíč nebo verzi klíče je možné zakázat za 24 hodin, nebo po [Azure Key Vault protokoly auditu](../key-vault/general/logging.md) nezobrazuje aktivitu z Azure Cosmos DB v tomto klíči nebo verzi klíče.
    
## <a name="error-handling"></a>Zpracování chyb

Při použití Customer-Managedch klíčů (CMK) v Azure Cosmos DB, pokud dojde k chybám, Azure Cosmos DB vrátí podrobnosti chyby spolu s podstavovým kódem HTTP v odpovědi. Tento druhotný stavový kód můžete použít k ladění hlavní příčiny problému. Seznam podporovaných kódů dílčího stavu HTTP najdete v článku [kódy stavu HTTP pro Azure Cosmos DB](/rest/api/cosmos-db/http-status-codes-for-cosmosdb) .

## <a name="frequently-asked-questions"></a>Nejčastější dotazy

### <a name="is-there-an-additional-charge-to-enable-customer-managed-keys"></a>Existují další poplatky za povolení klíčů spravovaných zákazníkem?

Ne, žádné poplatky za povolení této funkce.

### <a name="how-do-customer-managed-keys-impact-capacity-planning"></a>Jak se zákazníci, kteří spravují klíče, ovlivňují plánování kapacity?

Při použití klíčů spravovaných zákazníkem se na [jednotky žádosti](./request-units.md) spotřebované v rámci databázových operací zobrazuje nárůst, který odráží dodatečné zpracování potřebné k šifrování a dešifrování vašich dat. To může vést k mírně většímu využití zřízené kapacity. Následující tabulku použijte pro následující pokyny:

| Typ operace | Zvýšení jednotky žádosti |
|---|---|
| Čtení z bodu (načtení položek podle jejich ID) | + 5% na operaci |
| Libovolná operace zápisu | + 6% na operaci<br/>přibližně + 0,06 RU na indexovaná vlastnost |
| Dotazy, čtení kanálu změn nebo kanál konfliktů | + 15% na operaci |

### <a name="what-data-gets-encrypted-with-the-customer-managed-keys"></a>Jaká data se šifrují pomocí klíčů spravovaných zákazníkem?

Všechna data uložená ve vašem účtu Azure Cosmos se šifrují pomocí klíčů spravovaných zákazníkem s výjimkou následujících metadat:

- Názvy [účtů Azure Cosmos DB, databází a kontejnerů](./account-databases-containers-items.md#elements-in-an-azure-cosmos-account)

- Názvy [uložených procedur](./stored-procedures-triggers-udfs.md)

- Cesty k vlastnostem deklarované v [zásadách indexování](./index-policy.md)

- Hodnoty [klíčů oddílů](./partitioning-overview.md) v kontejnerech

### <a name="are-customer-managed-keys-supported-for-existing-azure-cosmos-accounts"></a>Podporují se pro existující účty Azure Cosmos klíče spravované zákazníky?

Tato funkce je momentálně dostupná jenom pro nové účty.

### <a name="is-it-possible-to-use-customer-managed-keys-in-conjunction-with-the-azure-cosmos-db-analytical-store"></a>Je možné používat klíče spravované zákazníkem ve spojení s Azure Cosmos DB [analytického úložiště](analytical-store-introduction.md)?

Ano, ale před povolením analytického úložiště musíte v zásadách Azure Key Vault přístupu [použít spravovanou identitu svého účtu Azure Cosmos DB](#using-managed-identity) .

### <a name="is-there-a-plan-to-support-finer-granularity-than-account-level-keys"></a>Existuje plán, který podporuje jemnější členitost než klíče na úrovni účtu?

V současné době se neberou klíče na úrovni kontejneru.

### <a name="how-can-i-tell-if-customer-managed-keys-are-enabled-on-my-azure-cosmos-account"></a>Jak poznám, jestli jsou na mém účtu Azure Cosmos povolené klíče spravované zákazníkem?

V Azure Portal přejděte na účet Azure Cosmos a sledujte položku **šifrování dat** v nabídce vlevo. Pokud tato položka existuje, ve vašem účtu jsou povolené klíče spravované zákazníkem:

:::image type="content" source="./media/how-to-setup-cmk/portal-data-encryption.png" alt-text="Položka nabídky šifrování dat":::

Můžete také programově načíst podrobnosti účtu Azure Cosmos a vyhledat přítomnost této `keyVaultKeyUri` Vlastnosti. Další způsoby, jak to provést [v prostředí PowerShell](#using-powershell) a [používání Azure CLI](#using-azure-cli), najdete výše.

### <a name="how-do-customer-managed-keys-affect-a-backup"></a>Jaký vliv mají klíče spravované zákazníkem na zálohu?

Azure Cosmos DB provádí [pravidelné a automatické zálohování](./online-backup-and-restore.md) dat uložených ve vašem účtu. Tato operace zálohuje šifrovaná data. Chcete-li použít obnovenou zálohu, je vyžadován šifrovací klíč, který jste použili v době zálohování. To znamená, že se neudělalo žádné odvolání a verze klíče, která se použila v době zálohování, bude pořád povolená.

### <a name="how-do-i-revoke-an-encryption-key"></a>Návody odvolat šifrovací klíč?

Odvolání klíče se provádí zakázáním nejnovější verze klíče:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev2.png" alt-text="Zakázání verze klíče":::

Pokud chcete odebrat všechny klíče z Azure Key Vault instance, můžete taky odstranit zásady přístupu udělené objektu zabezpečení Azure Cosmos DB:

:::image type="content" source="./media/how-to-setup-cmk/portal-akv-rev.png" alt-text="Odstraňují se zásady přístupu pro objekt zabezpečení Azure Cosmos DB.":::

### <a name="what-operations-are-available-after-a-customer-managed-key-is-revoked"></a>Jaké operace jsou k dispozici po odvolání klíče spravovaného zákazníkem?

Jediná operace, kterou je možné použít při odvolání šifrovacího klíče, je odstranění účtu.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [šifrování dat v Azure Cosmos DB](./database-encryption-at-rest.md).
- Získejte přehled o [zabezpečení přístupu k datům v Cosmos DB](secure-access-to-data.md).
