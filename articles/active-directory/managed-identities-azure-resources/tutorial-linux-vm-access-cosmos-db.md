---
title: Použití spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Cosmos DB
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Cosmos DB.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: priyamo
ms.openlocfilehash: d107e8283e68043a49c080fd1b021b29b917c6f7
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55812449"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-cosmos-db"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Cosmos DB 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem na virtuálním počítači s Linuxem získat přístup ke službě Azure Cosmos DB. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření účtu služby Cosmos DB
> * Vytvoření kolekce v účtu služby Cosmos DB
> * Udělení přístupu k instanci Azure Cosmos DB spravované identitě přiřazené systémem
> * Načtení `principalID` spravované identity přiřazené systémem virtuálního počítače s Linuxem
> * Získání přístupového tokenu a jeho použití k volání Azure Resource Manageru
> * Získání přístupových klíčů z Azure Resource Manageru kvůli volání služby Cosmos DB

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Ukázkové skripty rozhraní příkazového řádku v tomto kurzu můžete spustit dvěma způsoby:

- Použijte [Azure Cloud Shell](~/articles/cloud-shell/overview.md) buď přímo z webu Azure Portal, nebo přes tlačítko **Vyzkoušet** umístěné v pravém horním rohu každého bloku kódu.
- Pokud upřednostňujete práci v místní konzole rozhraní příkazového řádku, [nainstalujte nejnovější verzi CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější).

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

## <a name="retrieve-the-principalid-of-the-linux-vms-system-assigned-managed-identity"></a>Načtení `principalID` spravované identity přiřazené systémem virtuálního počítače s Linuxem

Jelikož budete v následující části potřebovat získat přístup z Resource Manageru k přístupovým klíčům účtu Cosmos DB, je potřeba načíst `principalID` spravované identity přiřazené systémem virtuálního počítače s Linuxem.  Nezapomeňte nahradit hodnoty parametrů `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (skupina prostředků obsahující váš virtuální počítač) a `<VM NAME>` vlastními hodnotami.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Odpověď bude obsahovat podrobnosti o spravované identitě přiřazené systémem (poznamenejte si hodnotu principalID, protože ji použijete v další části):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vms-system-assigned-identity-access-to-the-cosmos-db-account-access-keys"></a>Udělení přístupu k přístupovým klíčům účtu Cosmos DB spravované identitě zřizované systémem virtuálního počítače s Linuxem

Cosmos DB nativně nepodporuje ověřování Azure AD. Spravovanou identitu ale můžete použít k načtení přístupového klíče ke Cosmos DB z Resource Manageru a tento klíč pak použít pro přístup ke Cosmos DB. V tomto kroku udělíte spravované identitě přiřazené systémem přístup ke klíčům k účtu Cosmos DB.

Pokud chcete spravované identitě přiřazené systémem udělit v Azure Resource Manageru pomocí Azure CLI přístup k účtu Cosmos DB, aktualizujte hodnoty `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` a `<COSMOS DB ACCOUNT NAME>` pro svoje prostředí. Nahraďte `<MI PRINCIPALID>` s `principalId` vlastnosti vrácené `az resource show` příkaz v načtení principalID MI virtuálních počítačích s Linuxem.  Služba Cosmos DB podporuje při použití přístupových klíčů dvě úrovně: přístup k účtu pro čtení/zápis a přístup k účtu jen pro čtení.  Roli `DocumentDB Account Contributor` přiřaďte, pokud chcete k účtu získat klíče pro přístup pro čtení a zápis. Pokud chcete k účtu získat klíče pro přístup jen pro čtení, přiřaďte roli `Cosmos DB Account Reader Role`:

```azurecli-interactive
az role assignment create --assignee <MI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Odpověď bude obsahovat podrobnosti a vytvořeném přiřazení role:

```
{
  "id": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>/providers/Microsoft.Authorization/roleAssignments/5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "name": "5b44e628-394e-4e7b-bbc3-d6cd4f28f15b",
  "properties": {
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "roleDefinitionId": "/subscriptions/<SUBSCRIPTION ID>/providers/Microsoft.Authorization/roleDefinitions/fbdf93bf-df7d-467e-a4d2-9458aa1360c8",
    "scope": "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT>"
  },
  "resourceGroup": "<RESOURCE GROUP>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="get-an-access-token-using-the-linux-vms-system-assigned-managed-identity-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí spravované identity přiřazené systémem virtuálního počítače s Linuxem a jeho použití k volání Azure Resource Manageru

Ve zbývající části kurzu použijte k práci dříve vytvořený virtuální počítač.

K dokončení tohoto postupu potřebujete klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](../../virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na webu Azure Portal přejděte na **Virtuální počítače**, přejděte ke svému linuxovému virtuálnímu počítači a potom nahoře na stránce **Přehled** klikněte na **Připojit**. Zkopírujte řetězec pro připojení k vašemu virtuálnímu počítači. 
2. Použijte klienta SSH a připojte se ke svému virtuálnímu počítači.  
3. Dále se zobrazí výzva k zadání **hesla**, které jste přidali při vytváření **virtuálního počítače s Linuxem**. Pak byste se měli úspěšně přihlásit.  
4. Pomocí CURL získejte přístupový token pro Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
    ```
 
    > [!NOTE]
    > V předchozím požadavku platí, že hodnota parametru „resource“ musí přesně odpovídat hodnotě, kterou očekává Azure AD. Při použití ID prostředku Azure Resource Manageru musí být v identifikátoru URI koncové lomítko.
    > V následující odpovědi byl kvůli stručnosti prvek access_token zkrácen.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Získání přístupových klíčů z Azure Resource Manageru kvůli volání služby Cosmos DB  

Teď použijte CURL k volání Resource Manageru. Použijte přístupový token, který jste načetli v předchozí části, a načtěte přístupový klíč k účtu služby Cosmos DB. Jakmile budeme mít přístupový klíč, můžeme zadat dotaz na službu Cosmos DB. Nezapomeňte nahradit hodnoty parametrů `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` a `<COSMOS DB ACCOUNT NAME>` vlastními hodnotami. Hodnotu `<ACCESS TOKEN>` nahraďte dříve získaným přístupovým tokenem.  Pokud chcete načíst klíče pro čtení/zápis, použijte typ operace klíče `listKeys`.  Pokud chcete načíst klíče jen pro čtení, použijte typ operace klíče `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> V textu předchozí adresy URL se rozlišují velká a malá písmena. Proto zkontrolujte, že jste je u svých skupin prostředků zadali správně. Je také důležité vědět, že se jedná o požadavek POST, a nikoli o požadavek GET. Ujistěte se, že parametrem -d předáte hodnotu, která zachycuje limit délky. Tato hodnota může být NULL.  

V odpovědi CURL získáte seznam klíčů.  Pokud například získáte klíče jen pro čtení:  

```bash 
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

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak použít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup ke službě Cosmos DB.  Další informace o službě Cosmos DB najdete v:

> [!div class="nextstepaction"]
>[Přehledu databáze Azure Cosmos DB](/azure/cosmos-db/introduction)

