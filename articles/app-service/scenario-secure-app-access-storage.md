---
title: Kurz – webová aplikace přistupuje k úložišti pomocí spravovaných identit | Azure
description: V tomto kurzu se naučíte získat přístup k Azure Storage pro aplikaci pomocí spravovaných identit.
services: storage, app-service-web
author: rwike77
manager: CelesteDG
ms.service: app-service-web
ms.topic: tutorial
ms.workload: identity
ms.date: 11/30/2020
ms.author: ryanwi
ms.reviewer: stsoneff
ms.custom: azureday1
ms.openlocfilehash: 72b1d4fe864c23c0ac065e47d96ab0c78866defa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96435837"
---
# <a name="tutorial-access-azure-storage-from-a-web-app"></a>Kurz: přístup k Azure Storage z webové aplikace

Naučte se, jak získat přístup k Azure Storage webové aplikace (ne k přihlášenému uživateli) běžícímu na Azure App Service pomocí spravovaných identit.

:::image type="content" alt-text="Diagram, který ukazuje, jak získat přístup k úložišti" source="./media/scenario-secure-app-access-storage/web-app-access-storage.svg" border="false":::

Z webové aplikace chcete přidat přístup k rovině dat Azure (Azure Storage, Azure SQL Database, Azure Key Vault nebo jiným službám). Můžete použít sdílený klíč, ale pak se musíte starat o provozní zabezpečení, kteří můžou vytvořit, nasadit a spravovat tajný klíč. Je také možné, že se klíč rezervoval do GitHubu, který hackeři ví, jak hledat. Bezpečnější způsob, jak poskytnout přístup k datům vaší webové aplikaci, je použití [spravovaných identit](../active-directory/managed-identities-azure-resources/overview.md).

Spravovaná identita z Azure Active Directory (Azure AD) umožňuje App Service přistupovat k prostředkům prostřednictvím řízení přístupu na základě role (RBAC), aniž by museli vyžadovat přihlašovací údaje aplikace. Po přiřazení spravované identity k webové aplikaci se Azure postará o vytvoření a distribuci certifikátu. Lidé si nemuseli dělat starosti se správou tajných klíčů nebo přihlašovacích údajů aplikací.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte spravovanou identitu přiřazenou systémem na webové aplikaci.
> * Vytvořte účet úložiště a kontejner Azure Blob Storage.
> * Přístup k úložišti z webové aplikace pomocí spravovaných identit.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

* Webová aplikace spuštěná v Azure App Service s [povoleným App Servicem ověřováním/autorizačním modulem](scenario-secure-app-authentication-app-service.md).

## <a name="enable-managed-identity-on-an-app"></a>Povolení spravované identity v aplikaci

Pokud webovou aplikaci vytváříte a publikujete prostřednictvím sady Visual Studio, ve vaší aplikaci je povolená spravovaná identita za vás. Ve službě App Service v levém podokně vyberte **Identita** a pak vyberte **přiřazený systém**. Ověřte, zda je **stav** nastaven na hodnotu **zapnuto**. Pokud ne, vyberte **Uložit** a vyberte **Ano** , pokud chcete povolit spravovanou identitu přiřazenou systémem. Je-li povolena spravovaná identita, je stav nastaven na hodnotu **zapnuto** a je k dispozici ID objektu.

:::image type="content" alt-text="Snímek obrazovky zobrazující možnost identity přiřazené systémem" source="./media/scenario-secure-app-access-storage/create-system-assigned-identity.png":::

Tento krok vytvoří nové ID objektu, které se liší od ID aplikace vytvořeného v podokně **ověřování/autorizace** . Zkopírujte ID objektu spravované identity přiřazené systémem. Budete ho potřebovat později.

## <a name="create-a-storage-account-and-blob-storage-container"></a>Vytvoření účtu úložiště a Blob Storage kontejneru

Teď jste připraveni vytvořit účet úložiště a kontejner Blob Storage.

Každý účet úložiště musí patřit do nějaké skupiny prostředků Azure. Skupina prostředků je logický kontejner pro seskupení služeb Azure. Když vytváříte účet úložiště, máte možnost vytvořit novou skupinu prostředků nebo použít existující skupinu prostředků. V tomto článku se dozvíte, jak vytvořit novou skupinu prostředků.

Účet úložiště Univerzální v2 poskytuje přístup ke všem službám Azure Storage: objektům blob, souborům, frontám, tabulkám a diskům. Zde popsané kroky vytvoří účet úložiště pro obecné účely v2, ale postup vytvoření libovolného typu účtu úložiště je podobný.

Objekty BLOB v Azure Storage jsou uspořádány do kontejnerů. Než budete moct nahrát objekt BLOB později v tomto kurzu, musíte nejdřív vytvořit kontejner.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete vytvořit účet úložiště pro obecné účely V2 v Azure Portal, postupujte podle těchto kroků.

1. V nabídce webu Azure Portal vyberte **Všechny služby**. V seznamu prostředků zadejte **účty úložiště**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **účty úložiště**.

1. V okně **účty úložiště** , které se zobrazí, vyberte **Přidat**.

1. Vyberte předplatné, ve kterém chcete vytvořit účet úložiště.

1. V poli **Skupina prostředků** vyberte skupinu prostředků, která obsahuje vaši webovou aplikaci z rozevírací nabídky.

1. Dále zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název musí mít také délku 3 až 24 znaků a může obsahovat pouze číslice a malá písmena.

1. Vyberte pro svůj účet úložiště nějaké umístění nebo použijte výchozí umístění.

1. Tato pole nechte nastavená na výchozí hodnoty:

    |Pole|Hodnota|
    |--|--|
    |Model nasazení|Resource Manager|
    |Výkon|Standard|
    |Druh účtu|StorageV2 (obecné účely v2)|
    |Replikace|Geograficky redundantní úložiště s přístupem pro čtení (RA-GRS)|
    |Úroveň přístupu|Horká|

1. Vyberte **Zkontrolovat a vytvořit**, zkontrolujte nastavení účtu úložiště a vytvořte účet.

1. Vyberte **Vytvořit**.

Pokud chcete vytvořit kontejner Blob Storage v Azure Storage, postupujte podle těchto kroků.

1. V Azure Portal přejít na svůj nový účet úložiště.

1. V levé nabídce účtu úložiště přejděte do části **BLOB Service** a pak vyberte **kontejnery**.

1. Vyberte tlačítko **+ Kontejner**.

1. Zadejte název nového kontejneru. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-).

1. Nastavte úroveň veřejného přístupu ke kontejneru. Výchozí úroveň je **Privátní (bez anonymního přístupu)**.

1. Kliknutím na **OK** kontejner vytvoříte.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Pokud chcete vytvořit účet úložiště pro obecné účely v2 a kontejner Blob Storage, spusťte následující skript. Zadejte název skupiny prostředků, která obsahuje vaši webovou aplikaci. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název musí mít také délku 3 až 24 znaků a může obsahovat pouze číslice a malá písmena.

Zadejte umístění účtu úložiště. Pokud chcete zobrazit seznam umístění platných pro vaše předplatné, spusťte příkaz ```Get-AzLocation | select Location``` . Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-).

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami.

```powershell
Connect-AzAccount

$resourceGroup = "securewebappresourcegroup"
$location = "<location>"
$storageName="securewebappstorage"
$containerName = "securewebappblobcontainer"

$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroup `
  -Name $storageName `
  -Location $location `
  -SkuName Standard_RAGRS `
  -Kind StorageV2

$ctx = $storageAccount.Context

New-AzStorageContainer -Name $containerName -Context $ctx -Permission blob
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Pokud chcete vytvořit účet úložiště pro obecné účely v2 a kontejner Blob Storage, spusťte následující skript. Zadejte název skupiny prostředků, která obsahuje vaši webovou aplikaci. Zadejte název účtu úložiště. Zvolený název musí být jedinečný v rámci Azure. Název musí mít také délku 3 až 24 znaků a může obsahovat pouze číslice a malá písmena. 

Zadejte umístění účtu úložiště. Název kontejneru musí obsahovat malá písmena, musí začínat písmenem nebo číslicí a smí obsahovat jenom písmena, číslice a spojovníky (-).

Následující příklad používá účet Azure AD k autorizaci operace vytvoření kontejneru. Než vytvoříte kontejner, přiřaďte roli Přispěvatel dat objektů BLOB úložiště sami sobě. I když jste vlastníkem účtu, potřebujete explicitní oprávnění k provádění operací s daty v účtu úložiště.

Nezapomeňte nahradit zástupné hodnoty v lomených závorkách vlastními hodnotami.

```azurecli-interactive
az login

az storage account create \
    --name securewebappstorage \
    --resource-group securewebappresourcegroup \
    --location <location> \
    --sku Standard_ZRS \
    --encryption-services blob

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az ad signed-in-user show --query objectId -o tsv | az role assignment create \
    --role "Storage Blob Data Contributor" \
    --assignee @- \
    --scope $storageId

az storage container create \
    --account-name securewebappstorage \
    --name securewebappblobcontainer \
    --auth-mode login
```

---

## <a name="grant-access-to-the-storage-account"></a>Udělení přístupu k účtu úložiště

Abyste mohli vytvářet, číst a odstraňovat objekty blob, musíte své webové aplikaci udělit k účtu úložiště. V předchozím kroku jste nakonfigurovali webovou aplikaci běžící na App Service se spravovanou identitou. Pomocí Azure RBAC můžete spravované identitě udělit přístup k jinému prostředku, stejně jako jakýkoli objekt zabezpečení. Role Přispěvatel dat objektu BLOB služby Storage poskytuje webové aplikaci (reprezentované spravovanou identitou přiřazenou systémem) přístup ke čtení, zápisu a odstranění kontejneru objektů BLOB a dat.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

V [Azure Portal](https://portal.azure.com)přejděte do svého účtu úložiště, abyste udělili přístup k webové aplikaci. V levém podokně vyberte **řízení přístupu (IAM)** a pak vyberte **přiřazení rolí**. Zobrazí se seznam uživatelů, kteří mají přístup k účtu úložiště. Nyní chcete přidat přiřazení role k robotovi, službě App Service, která potřebuje přístup k účtu úložiště. Vyberte **Přidat**  >  **přiřazení role přidat**.

V **roli** vyberte **Přispěvatel dat objektů BLOB úložiště** a poskytněte vaší webové aplikaci přístup ke čtení objektů BLOB úložiště. V **přiřaďte přístup k** vyberte **App Service**. V **předplatném** vyberte své předplatné. Pak vyberte službu App Service, ke které chcete poskytnout přístup. Vyberte **Uložit**.

:::image type="content" alt-text="Snímek obrazovky, na kterém se zobrazuje obrazovka Přidat přiřazení role" source="./media/scenario-secure-app-access-storage/add-role-assignment.png":::

Vaše webová aplikace má teď přístup k vašemu účtu úložiště.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Spuštěním následujícího skriptu přiřaďte svou webovou aplikaci (reprezentovanou spravovanou identitou přiřazenou systémem) roli Přispěvatel dat objektů BLOB úložiště v účtu úložiště.

```powershell
$resourceGroup = "securewebappresourcegroup"
$webAppName="SecureWebApp20201102125811"
$storageName="securewebappstorage"

$spID = (Get-AzWebApp -ResourceGroupName $resourceGroup -Name $webAppName).identity.principalid
$storageId= (Get-AzStorageAccount -ResourceGroupName $resourceGroup -Name $storageName).Id
New-AzRoleAssignment -ObjectId $spID -RoleDefinitionName "Storage Blob Data Contributor" -Scope $storageId
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Spuštěním následujícího skriptu přiřaďte svou webovou aplikaci (reprezentovanou spravovanou identitou přiřazenou systémem) roli Přispěvatel dat objektů BLOB úložiště v účtu úložiště.

```azurecli-interactive
spID=$(az resource list -n SecureWebApp20201102125811 --query [*].identity.principalId --out tsv)

storageId=$(az storage account show -n securewebappstorage -g securewebappresourcegroup --query id --out tsv)

az role assignment create --assignee $spID --role 'Storage Blob Data Contributor' --scope $storageId
```

---

## <a name="access-blob-storage-net"></a>Přístup Blob Storage (.NET)

Třída [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) slouží k získání přihlašovacích údajů tokenu pro váš kód k autorizaci požadavků na Azure Storage. Vytvořte instanci třídy [DefaultAzureCredential](/dotnet/api/azure.identity.defaultazurecredential) , která pomocí spravované identity načte tokeny a připojí je k klientovi služby. Následující příklad kódu získá pověření ověřeného tokenu a použije ho k vytvoření objektu klienta služby, který nahraje nový objekt BLOB.

Pokud chcete tento kód zobrazit jako součást ukázkové aplikace, podívejte se na [ukázku v GitHubu](https://github.com/Azure-Samples/ms-identity-easyauth-dotnet-storage-graphapi/tree/main/1-WebApp-storage-managed-identity).

### <a name="install-client-library-packages"></a>Nainstalovat balíčky klientské knihovny

Nainstalujte [balíček BLOB Storage NuGet](https://www.nuget.org/packages/Azure.Storage.Blobs/) pro práci s BLOB Storage a [balíček klientské knihovny Azure identity pro .NET NuGet pro](https://www.nuget.org/packages/Azure.Identity/) ověření pomocí přihlašovacích údajů Azure AD. Nainstalujte klientské knihovny pomocí rozhraní příkazového řádku .NET Core nebo konzoly Správce balíčků v aplikaci Visual Studio.

# <a name="command-line"></a>[Příkazový řádek](#tab/command-line)

Otevřete příkazový řádek a přejděte do adresáře, který obsahuje soubor projektu.

Spusťte příkazy install.

```dotnetcli
dotnet add package Azure.Storage.Blobs

dotnet add package Azure.Identity
```

# <a name="package-manager"></a>[Správce balíčků](#tab/package-manager)

Otevřete projekt nebo řešení v aplikaci Visual Studio a otevřete konzolu **nástroje** pomocí  >  příkazu **Správce balíčků NuGet správce**  >  **balíčků** .

Spusťte příkazy install.
```powershell
Install-Package Azure.Storage.Blobs

Install-Package Azure.Identity
```

---

### <a name="example"></a>Příklad

```csharp
using System;
using Azure.Storage.Blobs;
using Azure.Storage.Blobs.Models;
using System.Collections.Generic;
using System.Threading.Tasks;
using System.Text;
using System.IO;
using Azure.Identity;

// Some code omitted for brevity.

static public async Task UploadBlob(string accountName, string containerName, string blobName, string blobContents)
{
    // Construct the blob container endpoint from the arguments.
    string containerEndpoint = string.Format("https://{0}.blob.core.windows.net/{1}",
                                                accountName,
                                                containerName);

    // Get a credential and create a client object for the blob container.
    BlobContainerClient containerClient = new BlobContainerClient(new Uri(containerEndpoint),
                                                                    new DefaultAzureCredential());

    try
    {
        // Create the container if it does not exist.
        await containerClient.CreateIfNotExistsAsync();

        // Upload text to a new block blob.
        byte[] byteArray = Encoding.ASCII.GetBytes(blobContents);

        using (MemoryStream stream = new MemoryStream(byteArray))
        {
            await containerClient.UploadBlobAsync(blobName, stream);
        }
    }
    catch (Exception e)
    {
        throw e;
    }
}
```

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud jste s tímto kurzem hotovi a už nepotřebujete webovou aplikaci ani související prostředky, [vyčistěte prostředky, které jste vytvořili](scenario-secure-app-clean-up-resources.md).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
>
> * Vytvořte spravovanou identitu přiřazenou systémem.
> * Vytvořte účet úložiště a kontejner Blob Storage.
> * Přístup k úložišti z webové aplikace pomocí spravovaných identit.

> [!div class="nextstepaction"]
> [App Service přistupuje Microsoft Graph jménem uživatele.](scenario-secure-app-access-microsoft-graph-as-user.md)