---
title: Používat pro přístup k databázi Azure Cosmos MSI virtuálních počítačů Linux
description: Kurz vás provede procesem pomocí System-Assigned spravované služby Identity (MSI) na virtuální počítač s Linuxem, přístup k databázi Azure Cosmos.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: skwan
ms.openlocfilehash: b84426ad28dbc2264bd9b28fe0697a88390bb58d
ms.sourcegitcommit: 688a394c4901590bbcf5351f9afdf9e8f0c89505
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/17/2018
---
# <a name="tutorial-use-a-linux-vm-msi-to-access-azure-cosmos-db"></a>Kurz: Použijte pro přístup k databázi Azure Cosmos MSI virtuálních počítačů Linux 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


V tomto kurzu se dozvíte, jak vytvořit a použít MSI virtuálních počítačů Linux. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Linuxem pomocí Instalační služby MSI povoleno
> * Vytvoření účtu služby Cosmos DB
> * Vytvoření kolekce v účtu Cosmos DB
> * Udělení přístupu MSI k instanci Azure Cosmos DB
> * Načtení `principalID` z virtuální počítač Linux MSI
> * Získání přístupového tokenu a použít jej k vyvolání Azure Resource Manager
> * Získat přístupové klíče z Azure Resource Manager volání Cosmos DB

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte účet Azure [si zaregistrovat bezplatný účet](https://azure.microsoft.com) než budete pokračovat.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

V tomto kurzu spustit příklady skriptu rozhraní příkazového řádku, máte dvě možnosti:

- Použití [prostředí cloudu Azure](~/articles/cloud-shell/overview.md) z portálu Azure nebo prostřednictvím **zkuste ho** tlačítko, které se nachází v pravém horním rohu každé blok kódu.
- [Nainstalujte nejnovější verzi 2.0 rozhraní příkazového řádku](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější) Pokud byste radši chtěli použít místní konzoly rozhraní příkazového řádku.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu, vytvořte nový virtuální počítač s Linuxem povoleno MSI.

Pro vytvoření virtuálního počítače povoleným MSI:

1. Pokud používáte Azure CLI v místní konzole, nejdřív přihlásit k Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Používáte účet, který je přidružen k předplatnému Azure, pod kterou chcete nasadit virtuální počítač:

   ```azurecli-interactive
   az login
   ```

2. Vytvoření [skupiny prostředků](../../azure-resource-manager/resource-group-overview.md#terminology) pro členství ve skupině a nasazení virtuálního počítače a jeho souvisejících prostředků pomocí [vytvořit skupinu az](/cli/azure/group/#az_group_create). Pokud už máte skupinu prostředků, které chcete použít místo toho můžete tento krok přeskočit:

   ```azurecli-interactive 
   az group create --name myResourceGroup --location westus
   ```

3. Vytvoření virtuálního počítače pomocí [vytvořit virtuální počítač az](/cli/azure/vm/#az_vm_create). Následující příklad vytvoří virtuální počítač s názvem *Můjvp* s MSI, jak vyžádala `--assign-identity` parametr. `--admin-username` a `--admin-password` parametry zadejte účet správce jméno a heslo pro přihlášení k virtuálnímu počítači. Aktualizujte tyto hodnoty jako vhodné pro vaše prostředí: 

   ```azurecli-interactive 
   az vm create --resource-group myResourceGroup --name myVM --image win2016datacenter --generate-ssh-keys --assign-identity --admin-username azureuser --admin-password myPassword12
   ```

## <a name="create-a-cosmos-db-account"></a>Vytvoření účtu služby Cosmos DB 

Pokud jste již nemáte, vytvořte účet Cosmos DB. Můžete tento krok přeskočit a použít existující účet Cosmos DB. 

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

## <a name="retrieve-the-principalid-of-the-linux-vms-msi"></a>Načtení `principalID` MSI Linux Virtuálního počítače

Chcete-li získat přístup k přístupové klíče účtu Cosmos databáze ze Správce prostředků v následující části, je potřeba načíst `principalID` MSI Linux Virtuálního počítače.  Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>` (skupina prostředků je virtuální počítač nachází), a `<VM NAME>` hodnoty parametrů s vlastními hodnotami.

```azurecli-interactive
az resource show --id /subscriptions/<SUBSCRIPTION ID>/resourceGroups/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe> --api-version 2017-12-01
```
Odpověď obsahuje podrobnosti o MSI přiřazené systému (Poznámka principalID, protože se používá v další části):

```bash  
{
    "id": "/subscriptions/<SUBSCRIPTION ID>/<RESOURCE GROUP>/providers/Microsoft.Compute/virtualMachines/<VM NAMe>",
  "identity": {
    "principalId": "6891c322-314a-4e85-b129-52cf2daf47bd",
    "tenantId": "733a8f0e-ec41-4e69-8ad8-971fc4b533f8",
    "type": "SystemAssigned"
 }

```
## <a name="grant-your-linux-vm-msi-access-to-the-cosmos-db-account-access-keys"></a>Udělit přístup MSI virtuálních počítačů Linux vaše přístupové klíče účtu Cosmos DB

Cosmos DB nenabízí nativní podporu ověřování Azure AD. Můžete však použít MSI k načtení přístupový klíč Cosmos databáze ze Správce prostředků a pak pomocí klíče pro přístup k databázi Cosmos. V tomto kroku udělíte přístup MSI klíče k účtu Cosmos DB.

K udělení přístupu identity MSI k účtu Cosmos DB v správce Azure Resource Manager pomocí rozhraní příkazového řádku Azure, aktualizujte hodnoty pro `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<COSMOS DB ACCOUNT NAME>` pro vaše prostředí. Nahraďte `<MSI PRINCIPALID>` s `principalId` vlastnost vrácený `az resource show` v [načíst principalID virtuálních počítačů Linux MSI](#retrieve-the-principalID-of-the-linux-VM's-MSI).  Cosmos DB podporuje dvě úrovně podrobnosti při použití přístupových klíčů: pro čtení a zápis k účtu a přístup jen pro čtení k účtu.  Přiřazení `DocumentDB Account Contributor` role, pokud chcete získat klíče pro čtení a zápis pro účet, nebo přiřadit `Cosmos DB Account Reader Role` role, pokud chcete získat klíče jen pro čtení pro účet:

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

## <a name="get-an-access-token-using-the-linux-vms-msi-and-use-it-to-call-azure-resource-manager"></a>Získání přístupového tokenu pomocí Instalační služby MSI Linux Virtuálního počítače a použít jej k vyvolání Azure Resource Manager

Pro zbývající část tohoto kurzu pracovat z virtuálního počítače vytvořili dříve.

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/install_guide). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu Azure přejděte do **virtuální počítače**, přejděte na Linux virtuálního počítače, pak z **přehled** klikněte na stránce **Connect** v horní části. Zkopírujte řetězce pro připojení k virtuálnímu počítači. 
2. Připojte k virtuálnímu počítači pomocí vašeho klienta SSH.  
3. Potom zobrazí výzva k zadání ve vaší **heslo** jste přidali při vytváření **virtuálního počítače s Linuxem**. Můžete by pak se úspěšně přihlášeni.  
4. Použijte CURL k získání přístupu tokenu pro Azure Resource Manager: 
     
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fmanagement.azure.com%2F' -H Metadata:true   
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

Nyní pomocí CURL volání Resource Manager pomocí přístupového tokenu získali v předchozí části načíst přístupový klíč účtu Cosmos DB. Jakmile jsme přístupový klíč, jsme dotaz Cosmos DB. Nezapomeňte nahradit `<SUBSCRIPTION ID>`, `<RESOURCE GROUP>`, a `<COSMOS DB ACCOUNT NAME>` hodnoty parametrů s vlastními hodnotami. Nahraďte `<ACCESS TOKEN>` hodnotu s tímto tokenem přístupu, který jste získali dříve.  Pokud chcete získat klíče pro čtení a zápis, použijte typ klíče operace `listKeys`.  Pokud chcete načíst klíče jen pro čtení, použijte typ operace klíče `readonlykeys`:

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

- Přehled MSI najdete v tématu [spravované služby Identity (MSI) pro prostředky Azure](overview.md).

