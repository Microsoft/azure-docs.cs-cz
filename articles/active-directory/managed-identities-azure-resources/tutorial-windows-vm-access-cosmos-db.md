---
title: 'Kurz: použití spravované identity pro přístup k Azure Cosmos DB-Windows-Azure AD'
description: Tento kurz vás provede procesem použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 04508f1aa8ee9d6b4f730f57c60d959fab209122
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "101093789"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Cosmos DB

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem pro virtuální počítač s Windows získat přístup ke Cosmos DB. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření účtu služby Cosmos DB
> * Udělení přístupu k přístupovým klíčům účtu Cosmos DB spravované identitě přiřazené systémem na virtuálním počítači s Windows
> * Získání přístupového tokenu pomocí spravované identity přiřazené systémem na virtuálním počítači s Windows k volání Azure Resource Manageru
> * Získání přístupových klíčů z Azure Resource Manageru kvůli volání služby Cosmos DB

## <a name="prerequisites"></a>Předpoklady

- Pokud ještě neznáte funkci spravovaných identit pro prostředky Azure, podívejte se na tento [přehled](overview.md). 
- Pokud nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/) před tím, než budete pokračovat.
- K provedení potřebných kroků k vytvoření prostředku a správě rolí potřebuje váš účet oprávnění vlastníka v odpovídajícím oboru (vaše předplatné nebo skupina prostředků). Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).
- Nainstalovat nejnovější verzi [Azure PowerShell](/powershell/azure/install-az-ps)
- Budete také potřebovat virtuální počítač s Windows, který má povolené spravované identity přiřazené systémem.
  - Pokud pro tento kurz potřebujete vytvořit virtuální počítač, můžete postupovat podle článku [s názvem vytvořit virtuální počítač s povolenou identitou přiřazenou systémem](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) .

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB 

Vytvořte si účet služby Cosmos DB (pokud ho ještě nemáte). Tento krok můžete přeskočit a můžete použít stávající účet služby Cosmos DB. 

1. Na webu Azure Portal klikněte v levém horním rohu na tlačítko **+ Vytvořit prostředek**.
2. Klikněte na **Databáze**, pak na **Azure Cosmos DB** a zobrazí se nový panel Nový účet.
3. Zadejte **ID** pro účet služby Cosmos DB, který použijete později.  
4. **API** musí být nastaveno na SQL. Přístup popsaný v tomto kurzu je možné použít s ostatními dostupnými typy rozhraní API. Kroky tohoto kurzu jsou ale určené pro rozhraní API SQL.
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.  Vyberte **Umístění**, ve kterém je Cosmos DB k dispozici.
6. Klikněte na **Vytvořit**.

### <a name="create-a-collection"></a>Vytvoření kolekce 

Potom přidejte shromažďování dat v účtu služby Cosmos DB, kterého se můžete v dalších krocích dotazovat.

1. Přejděte na nově vytvořený účet Cosmos DB.
2. Na kartě **Přehled** klikněte na tlačítko **pro přidání kolekce** a vysune se panel Přidat kolekci.
3. Pro kolekci zadejte ID databáze, ID kolekce, vyberte kapacitu úložiště, zadejte klíč oddílu, zadejte hodnotu propustnosti a potom klikněte na **OK**.  Pro účely tohoto kurzu stačí, když použijete „Test“ jako ID databáze a ID kolekce, vyberete kapacitu pevného úložiště a nejnižší propustnost (400 RU/s).  


## <a name="grant-access"></a>Udělení přístupu

V této části se dozvíte, jak udělit přístup spravované identitě přiřazený systémem Windows VM k přístupovým klíčům Cosmos DB účtu. Cosmos DB nativně nepodporuje ověřování Azure AD. Spravovanou identitu přiřazenou systémem ale můžete použít k načtení Cosmos DB přístupového klíče z Správce prostředků a k přístupu k Cosmos DB použít klíč. V tomto kroku udělíte spravované identitě přiřazené systémem na virtuálním počítači s Windows přístup ke klíčům k účtu Cosmos DB.

K udělení přístupu spravované identity systémem Windows VM k účtu Cosmos DB v Azure Resource Manager pomocí PowerShellu aktualizujte následující hodnoty:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>`

Služba Cosmos DB podporuje při použití přístupových klíčů dvě úrovně: přístup k účtu pro čtení/zápis a přístup k účtu jen pro čtení.  Roli `DocumentDB Account Contributor` přiřaďte, pokud chcete k účtu získat klíče pro přístup pro čtení a zápis. Pokud chcete k účtu získat klíče pro přístup jen pro čtení, přiřaďte roli `Cosmos DB Account Reader Role`.  Pro účely tohoto kurzu přiřaďte `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Cosmos DB Account Reader Role" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>"
```

>[!NOTE]
> Mějte na paměti, že pokud nemůžete provést operaci, možná nemáte správná oprávnění. Pokud chcete přístup pro zápis ke klíčům, potřebujete použít roli Azure, jako je například Přispěvatel účtu DocumentDB nebo vytvořit vlastní roli. Další informace najdete [v v Azure Cosmos DB řízení přístupu na základě role v Azure](../../cosmos-db/role-based-access-control.md) .

## <a name="access-data"></a>Přístup k datům

V této části se dozvíte, jak volat Azure Resource Manager pomocí přístupového tokenu pro spravovanou identitu přiřazenou systémem Windows VM. Ve zbývající části kurzu použijeme k práci dříve vytvořený virtuální počítač. 

Na virtuální počítač s Windows musíte nainstalovat nejnovější verzi rozhraní příkazového [řádku Azure CLI](/cli/azure/install-azure-cli) .

### <a name="get-an-access-token"></a>Získání přístupového tokenu

1. Na webu Azure Portal přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a potom nahoře na stránce **Přehled** klikněte na **Připojit**. 
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** virtuálního počítače, otevřete ve vzdálené relaci PowerShell.
4. Pomocí příkazu Invoke-WebRequest v PowerShellu požádejte místní spravované identity o koncový bod prostředků Azure k získání přístupového tokenu pro Azure Resource Manager.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -Method GET -Headers @{Metadata="true"}
   ```

   > [!NOTE]
   > Hodnota parametru „resource“ musí přesně odpovídat hodnotě, kterou očekává Azure AD. Při použití ID prostředku Azure Resource Manageru musí být v identifikátoru URI koncové lomítko.
    
   V dalším kroku extrahujte element Content, který je uložený jako formátovaný řetězec JSON (JavaScript Object Notation) v objektu $response. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```
   Potom z odpovědi extrahujte přístupový token.
    
   ```powershell
   $ArmToken = $content.access_token
   ```

### <a name="get-access-keys"></a>Získání přístupových klíčů 

V této části se dozvíte, jak získat přístupové klíče z Azure Resource Manager, aby bylo možné Cosmos DB volání. K volání Správce prostředků pomocí přístupového tokenu, který jsme dřív získali k načtení přístupového klíče Cosmos DB účtu, používáme PowerShell. Jakmile budeme mít přístupový klíč, můžeme zadat dotaz na službu Cosmos DB. Použijte vlastní hodnoty k nahrazení následujících položek:

- `<SUBSCRIPTION ID>`
- `<RESOURCE GROUP>`
- `<COSMOS DB ACCOUNT NAME>` 
- Hodnotu `<ACCESS TOKEN>` nahraďte dříve získaným přístupovým tokenem. 

>[!NOTE]
>Pokud chcete načíst klíče pro čtení/zápis, použijte typ operace klíče `listKeys`.  Pokud chcete načíst klíče jen pro čtení, použijte typ operace Key `readonlykeys` . Pokud nemůžete použít klíče listkey, ověřte, že jste k spravované identitě přiřadili [příslušnou roli](../../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role) .

```powershell
Invoke-WebRequest -Uri 'https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/readonlykeys/?api-version=2016-03-31' -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
Odpověď nabízí seznam klíčů.  Pokud například získáte klíče jen pro čtení:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Když teď máte přístupový klíč pro účet služby Cosmos DB, můžete ho předat sadě SDK služby Cosmos DB a provádět volání za účelem přístupu k účtu.  Přístupový klíč můžete například předat rozhraní příkazového řádku Azure CLI.  V okně účtu Cosmos DB na webu Azure Portal můžete získat `<COSMOS DB CONNECTION URL>` z karty **Přehled**.  `<ACCESS KEY>` nahraďte hodnotou, kterou jste získali výše:

```azurecli
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Tento příkaz rozhraní příkazového řádku vrátí podrobnosti o kolekci:

```output
{
  "collection": {
    "_conflicts": "conflicts/",
    "_docs": "docs/",
    "_etag": "\"00006700-0000-0000-0000-5a8271e90000\"",
    "_rid": "Es5SAM2FDwA=",
    "_self": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/",
    "_sprocs": "sprocs/",
    "_triggers": "triggers/",
    "_ts": 1518498281,
    "_udfs": "udfs/",
    "id": "Test",
    "indexingPolicy": {
      "automatic": true,
      "excludedPaths": [],
      "includedPaths": [
        {
          "indexes": [
            {
              "dataType": "Number",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "String",
              "kind": "Range",
              "precision": -1
            },
            {
              "dataType": "Point",
              "kind": "Spatial"
            }
          ],
          "path": "/*"
        }
      ],
      "indexingMode": "consistent"
    }
  },
  "offer": {
    "_etag": "\"00006800-0000-0000-0000-5a8271ea0000\"",
    "_rid": "f4V+",
    "_self": "offers/f4V+/",
    "_ts": 1518498282,
    "content": {
      "offerIsRUPerMinuteThroughputEnabled": false,
      "offerThroughput": 400
    },
    "id": "f4V+",
    "offerResourceId": "Es5SAM2FDwA=",
    "offerType": "Invalid",
    "offerVersion": "V2",
    "resource": "dbs/Es5SAA==/colls/Es5SAM2FDwA=/"
  }
}
```


## <a name="disable"></a>Zakázat

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak použít identitu přiřazenou systémem na virtuálním počítači s Windows pro přístup ke Cosmos DB.  Další informace o službě Cosmos DB najdete tady:

> [!div class="nextstepaction"]
>[Přehled Azure Cosmos DB](../../cosmos-db/introduction.md)