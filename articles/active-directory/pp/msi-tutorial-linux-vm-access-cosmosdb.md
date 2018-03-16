---
title: "Používat přiřazený uživatelem spravované služby Identity (MSI) na virtuální počítač s Linuxem pro přístup k databázi Azure Cosmos"
description: "Kurz vás provede procesem pomocí User-Assigned spravované služby Identity (MSI) na virtuální počítač s Linuxem, přístup k databázi Azure Cosmos."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/14/2018
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: dbb5e9e8f9accd618599010ab2bbb4a8760e534f
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="use-a-user-assigned-managed-service-identity-msi-on-a-linux-vm-to-access-azure-cosmos-db"></a>Používat přiřazený uživatelem spravované služby Identity (MSI) na virtuální počítač s Linuxem pro přístup k databázi Azure Cosmos 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Tento kurz ukazuje, jak vytvořit a použít přiřazený uživatelem spravované služby Identity (MSI) z virtuální počítač s Linuxem a pak ji používat pro přístup k databázi Azure Cosmos. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatele přiřazené identita spravované služby (MSI)
> * Přiřadit MSI přiřazené pro virtuální počítač s Linuxem
> * Udělení přístupu MSI k instanci Azure Cosmos DB
> * Získání přístupového tokenu pomocí Instalační služby MSI identity přiřazený uživatelem a používat ho pro přístup k databázi Azure Cosmos

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

V tomto kurzu spustit příklady skriptu rozhraní příkazového řádku, máte dvě možnosti:

- Použití [prostředí cloudu Azure](~/articles/cloud-shell/overview.md) z portálu Azure nebo prostřednictvím **zkuste ho** tlačítko, které se nachází v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte **+ vytvořit prostředek** najít v levém horním rohu portálu Azure.
2. Vyberte **výpočetní**a potom vyberte **virtuálního počítače s Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Virtuální počítač s Linuxem MSI](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. V části **skupiny prostředků**, zvolte **vytvořit nový** a zadejte název skupiny prostředků pro tento virtuální počítač. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. V **nastavení** okno, ponechejte výchozí hodnoty a klikněte na tlačítko **OK**.

## <a name="create-a-user-assigned-msi"></a>Vytvoření MSI se přiřazený uživatelem

1. Pokud používáte rozhraní příkazového řádku konzoly (ne relaci prostředí cloudu Azure), spusťte po přihlášení k Azure. Používáte účet, který je přidružen k předplatnému Azure, ve kterém chcete vytvořit nový soubor MSI:

    ```azurecli
    az login
    ```

2. Vytvoření přiřazený uživatelem MSI pomocí [vytvoření az identity](/cli/azure/identity#az_identity_create). `-g` Parametr určuje skupinu prostředků, kde se má vytvořit soubor MSI, a `-n` parametr určuje její název. Nezapomeňte nahradit `<RESOURCE GROUP>` a `<MSI NAME>` hodnoty parametrů s vlastními hodnotami:

    ```azurecli-interactive
    az identity create -g <RESOURCE GROUP> -n <MSI NAME>
    ```

    Odpověď obsahuje podrobnosti o MSI přiřazený uživatelem vytvořené, podobně jako v následujícím příkladu (Poznámka: `clientId` a `id` hodnoty MSI, jak se používají v následujících krocích):

    ```json
    {
    "clientId": "73444643-8088-4d70-9532-c3a0fdc190fz",
    "clientSecretUrl": "https://control-westcentralus.identity.azure.net/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>/credentials?tid=5678&oid=9012&aid=123444643-8088-4d70-9532-c3a0fdc190fz",
    "id": "/subscriptions/<SUBSCRIPTON ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>",
    "location": "westcentralus",
    "name": "<MSI NAME>",
    "principalId": "c0833082-6cc3-4a26-a1b1-c4b5f90a981f",
    "resourceGroup": "<RESOURCE GROUP>",
    "tags": {},
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533bl",
    "type": "Microsoft.ManagedIdentity/userAssignedIdentities"
    }
    ```

## <a name="assign-your-user-assigned-msi-to-your-linux-vm"></a>Přiřazený uživatelem Instalační služby MSI přiřadit virtuálním počítačům s Linuxem

Na rozdíl od MSI přiřadit systému MSI se přiřazený uživatelem lze klienty na několik prostředků Azure. V tomto kurzu přiřazení pro jeden virtuální počítač. Můžete je také přiřadit k více než jeden virtuální počítač.

Soubor MSI přiřazený uživatelem přiřadit virtuálním počítačům s Linuxem pomocí [az virtuálních počítačů přiřazení identity](/cli/azure/vm#az_vm_assign_identity). Nezapomeňte nahradit `<RESOURCE GROUP>` a `<VM NAME>` hodnoty parametrů s vlastními hodnotami. Použití `id` vrácena vlastnost v předchozím kroku `--identities` hodnota parametru:

```azurecli-interactive
az vm assign-identity -g <RESOURCE GROUP> -n <VM NAME> --identities "/subscriptions/<SUBSCRIPTION ID>/resourcegroups/<RESOURCE GROUP>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<MSI NAME>"
```

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB 

Pokud jste již nemáte, vytvořte nyní účet Cosmos DB. Také můžete tento krok přeskočit a použít existující účet Cosmos DB, pokud dáváte přednost. 

1. Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Klikněte na tlačítko **databáze**, pak **Azure Cosmos DB**a nové "nový účet" panelu zobrazí.
3. Zadejte **ID** pro Cosmos DB účet, který použijete později.  
4. **Rozhraní API** musí být nastavena na "SQL." Postup popsaný v tomto kurzu lze použít s jinými typy k dispozici rozhraní API, ale kroky v tomto kurzu jsou pro rozhraní SQL API.
5. Ujistěte se, **předplatné** a **skupiny prostředků** odpovídat jsou zadány při vytvoření virtuálního počítače v předchozím kroku.  Vyberte **umístění** kde je k dispozici Cosmos DB.
6. Klikněte na možnost **Vytvořit**.

## <a name="create-a-collection-in-the-cosmos-db-account"></a>Vytvoření kolekce v účtu Cosmos DB

Dál přidejte shromažďování dat v účtu Cosmos DB, který můžete dotazovat v dalších krocích.

1. Přejděte na svůj nově vytvořený účet Cosmos DB.
2. Na **přehled** klikněte na možnost **+/ přidat kolekce** tlačítko a "Přidat"kolekce panelu snímky out.
3. Poskytnout kolekce s výběrem ID, ID kolekce databáze kapacitu úložiště, zadejte klíč oddílu, zadejte hodnotu propustnosti a potom klikněte na tlačítko **OK**.  V tomto kurzu je dostačující použít "Test" jako ID databáze a ID kolekce, vyberte kapacitu dlouhodobého úložiště a nejnižší propustnost (400 RU/s).

## <a name="grant-your-user-assigned-msi-access-to-the-cosmos-db-account-access-keys"></a>Udělit přístup uživatele přiřazené MSI vaše přístupové klíče účtu Cosmos DB

Cosmos DB nenabízí nativní podporu ověřování Azure AD.  Můžete však použít MSI k načtení přístupový klíč Cosmos databáze ze Správce prostředků a pak pomocí klíče pro přístup k databázi Cosmos.  V tomto kroku udělíte, že uživatel přiřazenou MSI přístup klíče pro účet Cosmos DB.

Nejprve udělte přístup identity MSI k účtu Cosmos DB v správce Azure Resource Manager pomocí rozhraní příkazového řádku Azure. Aktualizujte hodnoty pro `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<COSMOS DB ACCOUNT NAME>` pro vaše prostředí. Nahraďte `<MSI PRINCIPALID>` s `principalId` vlastnost vrácený `az identity create` v [vytvořit instalační služby MSI přiřazený uživatelem](#create-a-user-assigned-msi).  Cosmos DB podporuje dvě úrovně podrobnosti při použití přístupových klíčů: pro čtení a zápis k účtu a přístup jen pro čtení k účtu.  Přiřazení `DocumentDB Account Contributor` role, pokud chcete získat klíče pro čtení a zápis pro účet, nebo přiřadit `Cosmos DB Account Reader Role` role, pokud chcete získat klíče jen pro čtení pro účet:

```azurecli-interactive
az role assignment create --assignee <MSI PRINCIPALID> --role '<ROLE NAME>' --scope "/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMODS DB ACCOUNT NAME>"
```

Odpověď obsahuje podrobnosti o vytvoření přiřazení role:

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

## <a name="get-an-access-token-using-the-user-assigned-msi-and-use-it-to-call-azure-resource-manager"></a>Získat token přístupu pomocí uživatele přiřazené MSI a použít jej k vyvolání Azure Resource Manager

Pro zbývající část tohoto kurzu pracovat z virtuálního počítače vytvořili dříve.

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu Azure přejděte do **virtuální počítače**, přejděte na Linux virtuálního počítače, pak z **přehled** klikněte na stránce **Connect** v horní části. Zkopírujte řetězce pro připojení k virtuálnímu počítači. 
2. Připojte k virtuálnímu počítači pomocí vašeho klienta SSH.  
3. Potom zobrazí výzva k zadání ve vaší **heslo** jste přidali při vytváření **virtuálního počítače s Linuxem**. Můžete by pak se úspěšně přihlášeni.  
4. Použijte CURL k získání přístupu tokenu pro Azure Resource Manager.  

    CURL žádosti a odpovědi pro přístupový token je níže.  Nahraďte <CLIENT ID> s clientId přiřazena hodnota vaše uživatele MSI: 
    
    ```bash
    curl -H Metadata:true "http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com/&client_id=<MSI CLIENT ID>" 
    ```
    
    > [!NOTE]
    > V předchozí požadavek musí být hodnota parametru "prostředek" přesnou shodu pro očekávané službou Azure AD. Pokud používáte ID prostředku Azure Resource Manager, je nutné zahrnout do adresy koncové lomítko, v identifikátoru URI.
    > V následující odpovědi, jako byla zkrácena jako stručný výtah element access_token.
    
    ```bash
    {"access_token":"eyJ0eXAiOi...",
     "expires_in":"3599",
     "expires_on":"1518503375",
     "not_before":"1518499475",
     "resource":"https://management.azure.com/",
     "token_type":"Bearer",
     "client_id":"1ef89848-e14b-465f-8780-bf541d325cd5"}
     ```
    
## <a name="get-access-keys-from-azure-resource-manager-to-make-cosmos-db-calls"></a>Získat přístupové klíče z Azure Resource Manager volání Cosmos DB  

Nyní pomocí CURL volání Resource Manager pomocí přístupového tokenu nemůžeme načíst v předchozí části, k načtení přístupový klíč účtu Cosmos DB. Jakmile jsme přístupový klíč, jsme dotaz Cosmos DB. Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<COSMOS DB ACCOUNT NAME>` hodnoty parametrů s vlastními hodnotami. Nahraďte `<ACCESS TOKEN>` hodnotu s tímto tokenem přístupu, který jste získali dříve.  Pokud chcete získat klíče pro čtení a zápis, použijte typ klíče operace `listKeys`.  Pokud chcete načíst klíče jen pro čtení, použijte typ operace klíče `readonlykeys`:

```bash 
curl 'https://management.azure.com/subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.DocumentDB/databaseAccounts/<COSMOS DB ACCOUNT NAME>/<KEY OPERATION TYPE>?api-version=2016-03-31' -X POST -d "" -H "Authorization: Bearer <ACCESS TOKEN>" 
```

> [!NOTE]
> Text v předchozí adresa URL je malá a velká písmena, zajistěte proto, pokud používáte horní malá pro vaší skupiny prostředků, aby odrážela ji odpovídajícím způsobem. Kromě toho je důležité vědět, že se jedná o požadavek POST požadavek GET a ujistěte se, že předat hodnotu pro zachycení limit délky s -d, který může mít hodnotu NULL.  

Odpověď CURL poskytuje seznam klíčů.  Například, pokud se klíče jen pro čtení:  

```bash 
{"primaryReadonlyMasterKey":"bWpDxS...dzQ==",
"secondaryReadonlyMasterKey":"38v5ns...7bA=="}
```

Teď, když máte přístupový klíč pro účet Cosmos DB můžete předat na sadu SDK DB Cosmos a volání přístup k účtu.  Rychlé příklad můžete předat přístupový klíč do příkazového řádku Azure CLI.  Můžete získat <COSMOS DB CONNECTION URL> z **přehled** karty v okně Cosmos DB účtu na portálu Azure.  Nahraďte <ACCESS KEY> s hodnotou jste získali výše:

```bash
az cosmosdb collection show -c <COLLECTION ID> -d <DATABASE ID> --url-connection "<COSMOS DB CONNECTION URL>" --key <ACCESS KEY>
```

Toto rozhraní příkazového řádku příkaz vrátí podrobnosti o kolekci:

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

- Přehled MSI najdete v tématu [spravované služby Identity (MSI) pro prostředky Azure](msi-overview.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.
