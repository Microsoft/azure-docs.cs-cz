---
title: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Cosmos DB
description: Tento kurz vás provede procesem použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2018
ms.author: daveba
ms.openlocfilehash: c2b4f048785c60a54f40c50bbec4a2f12373efe2
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/31/2018
ms.locfileid: "43336958"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Cosmos DB

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem pro virtuální počítač s Windows získat přístup ke Cosmos DB. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření účtu služby Cosmos DB
> * Udělení přístupu k přístupovým klíčům účtu Cosmos DB spravované identitě přiřazené systémem na virtuálním počítači s Windows
> * Získání přístupového tokenu pomocí spravované identity přiřazené systémem na virtuálním počítači s Windows k volání Azure Resource Manageru
> * Získání přístupových klíčů z Azure Resource Manageru kvůli volání služby Cosmos DB

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Přihlášení k webu Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Windows](/azure/virtual-machines/windows/quick-create-portal)

- [Povolení spravované identity přiřazené systémem na vašem virtuálním počítači](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB 

Vytvořte si účet služby Cosmos DB (pokud ho ještě nemáte). Tento krok můžete přeskočit a můžete použít stávající účet služby Cosmos DB. 

1. V levém horním rohu na webu Azure Portal klikněte na tlačítko pro **vytvoření nové služby**.
2. Klikněte na **Databáze**, pak na **Azure Cosmos DB** a zobrazí se nový panel Nový účet.
3. Zadejte **ID** pro účet služby Cosmos DB, který použijete později.  
4. **API** musí být nastaveno na SQL. Přístup popsaný v tomto kurzu je možné použít s ostatními dostupnými typy rozhraní API. Kroky tohoto kurzu jsou ale určené pro rozhraní API SQL.
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.  Vyberte **Umístění**, ve kterém je Cosmos DB k dispozici.
6. Klikněte na možnost **Vytvořit**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Vytvoření kolekce v účtu služby Cosmos DB

Potom přidejte shromažďování dat v účtu služby Cosmos DB, kterého se můžete v dalších krocích dotazovat.

1. Přejděte na nově vytvořený účet Cosmos DB.
2. Na kartě **Přehled** klikněte na tlačítko **pro přidání kolekce** a vysune se panel Přidat kolekci.
3. Pro kolekci zadejte ID databáze, ID kolekce, vyberte kapacitu úložiště, zadejte klíč oddílu, zadejte hodnotu propustnosti a potom klikněte na **OK**.  Pro účely tohoto kurzu stačí, když použijete „Test“ jako ID databáze a ID kolekce, vyberete kapacitu pevného úložiště a nejnižší propustnost (400 RU/s).  

## <a name="grant-windows-vm-system-assigned-managed-identity-access-to-the-cosmos-db-account-access-keys"></a>Udělení přístupu k přístupovým klíčům účtu Cosmos DB spravované identitě přiřazené systémem na virtuálním počítači s Windows

Cosmos DB nativně nepodporuje ověřování Azure AD. Spravovanou identitu přiřazenou systémem ale můžete použít k načtení přístupového klíče ke Cosmos DB z Resource Manageru a tento klíč použít pro přístup ke Cosmos DB. V tomto kroku udělíte spravované identitě přiřazené systémem na virtuálním počítači s Windows přístup ke klíčům k účtu Cosmos DB.

Pokud chcete spravované identitě přiřazené systémem na počítači s Windows udělit v Azure Resource Manageru pomocí PowerShellu přístup k účtu Cosmos DB, aktualizujte hodnoty `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` a `<COSMOS DB ACCOUNT NAME>` pro svoje prostředí. `<PRINCIPALID>` nahraďte vlastností `principalId` vrácenou příkazem `az resource show` v části [Načtení vlastnosti principalID spravované identity přiřazené systémem na virtuálním počítači s Linuxem](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Služba Cosmos DB podporuje při použití přístupových klíčů dvě úrovně: přístup k účtu pro čtení/zápis a přístup k účtu jen pro čtení.  Roli `DocumentDB Account Contributor` přiřaďte, pokud chcete k účtu získat klíče pro přístup pro čtení/zápis. Pokud chcete k účtu získat klíče pro přístup jen pro čtení, přiřaďte roli `Cosmos DB Account Reader Role`:

```azurepowershell
$spID = (Get-AzureRMVM -ResourceGroupName myRG -Name myVM).identity.principalid
New-AzureRmRoleAssignment -ObjectId $spID -RoleDefinitionName "Reader" -Scope "/subscriptions/<mySubscriptionID>/resourceGroups/<myResourceGroup>/providers/Microsoft.Storage/storageAccounts/<myStorageAcct>"
```

## <a name="get-an-access-token-using-the-windows-vm-system-assigned-managed-identity-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí spravované identity přiřazené systémem na virtuálním počítači s Windows k volání Azure Resource Manageru

Ve zbývající části kurzu použijeme k práci dříve vytvořený virtuální počítač. 

V této části budete muset používat rutiny prostředí PowerShell pro Azure Resource Manager.  Než budete pokračovat, [stáhněte si nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview) (v případě, že ho nemáte nainstalovaný).

Na virtuální počítač s Windows budete také muset nainstalovat nejnovější verzi [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli).

1. Na webu Azure Portal přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a potom nahoře na stránce **Přehled** klikněte na **Připojit**. 
2. Zadejte své **uživatelské jméno** a **heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
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

## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Získání přístupových klíčů z Azure Resource Manageru kvůli volání služby Cosmos DB

Teď použijte PowerShell k volání Resource Manageru. Použijte přístupový token, který jste načetli v předchozí části, a načtěte přístupový klíč k účtu služby Cosmos DB. Jakmile budeme mít přístupový klíč, můžeme zadat dotaz na službu Cosmos DB. Nezapomeňte nahradit hodnoty parametrů `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` a `<COSMOS DB ACCOUNT NAME>` vlastními hodnotami. Hodnotu `<ACCESS TOKEN>` nahraďte dříve získaným přístupovým tokenem.  Pokud chcete načíst klíče pro čtení/zápis, použijte typ operace klíče `listKeys`.  Pokud chcete načíst klíče jen pro čtení, použijte typ operace klíče `readonlykeys`:

```powershell
Invoke-WebRequest -Uri https://management.azure.com/subscriptions/<SUBSCRIPTION-ID>/resourceGroups/<RESOURCE-GROUP>/providers/Microsoft.DocumentDb/databaseAccounts/<COSMOS DB ACCOUNT NAME>/listKeys/?api-version=2016-12-01 -Method POST -Headers @{Authorization="Bearer $ARMToken"}
```
V odpovědi získáte seznam klíčů.  Pokud například získáte klíče jen pro čtení:

```powershell
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```
Když teď máte přístupový klíč pro účet služby Cosmos DB, můžete ho předat sadě SDK služby Cosmos DB a provádět volání za účelem přístupu k účtu.  Přístupový klíč můžete například předat rozhraní příkazového řádku Azure CLI.  V okně účtu Cosmos DB na webu Azure Portal můžete získat <COSMOS DB CONNECTION URL> z karty **Přehled**.  <ACCESS KEY> nahraďte hodnotou, kterou jste získali výše:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Tento příkaz rozhraní příkazového řádku vrátí podrobnosti o kolekci:

```bash
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

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak použít identitu přiřazenou systémem na virtuálním počítači s Windows pro přístup ke Cosmos DB.  Další informace o službě Cosmos DB najdete v:

> [!div class="nextstepaction"]
>[Přehledu databáze Azure Cosmos DB](/azure/cosmos-db/introduction)


