---
title: Povolit veřejné oprávnění ke čtení pro kontejnery a objekty BLOB v úložišti objektů Blob v Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpřístupnit kontejnerům a objektům blob pro anonymní přístup a jak k nim přistupovat prostřednictvím kódu programu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: bf7dabc1c3765d86e7a0f87acaa6f06a68d3d530
ms.sourcegitcommit: e2348a7a40dc352677ae0d7e4096540b47704374
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/05/2018
ms.locfileid: "43782004"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob
Můžete povolit anonymní, veřejný přístup pro čtení kontejneru a jeho objekty BLOB v úložišti objektů Blob v Azure. Díky tomu můžete udělit přístup jen pro čtení k těmto prostředkům bez sdílení klíč účtu a bez nutnosti sdíleného přístupového podpisu (SAS).

Veřejné oprávnění ke čtení je nejvhodnější pro scénáře, ve kterém chcete určité přes bloby až po mít vždycky k dispozici pro anonymní přístup pro čtení. Chcete detailnější kontrolu může vytvořit sdílený přístupový podpis. Sdílené přístupové podpisy umožňují zajistit omezený přístup pomocí různá oprávnění pro konkrétní časové období. Další informace o vytváření sdílené přístupové podpisy, naleznete v tématu [použití sdílených přístupových podpisů (SAS) ve službě Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Anonymním uživatelům uděluje oprávnění ke kontejnerům a objektům BLOB
Ve výchozím kontejneru a všech objektů BLOB v něm přístupná jenom vlastník účtu úložiště. Pokud chcete dát anonymním uživatelům oprávnění ke čtení pro kontejner a jeho objekty BLOB, můžete nastavit oprávnění kontejner umožní veřejný přístup. Anonymní uživatelé mohou číst objektů BLOB v kontejneru veřejně přístupné bez ověření požadavku.

Konfigurovat kontejner s následujícími oprávněními:

* **Žádné veřejné přístup pro čtení:** kontejneru a jeho objekty BLOB je přístupný pouze vlastník účtu úložiště. Toto je výchozí pro všechny nové kontejnery.
* **Veřejný přístup pro objekty BLOB pouze pro čtení:** objektů BLOB v kontejneru můžete číst anonymní žádosti, ale kontejnerů dat není k dispozici. Anonymní klienty nelze vytvořit výčet objektů BLOB v kontejneru.
* **Úplné veřejné oprávnění ke čtení:** všech kontejnerů a objektů blob data je možné načíst pomocí anonymní žádosti. Klienty můžete zobrazit výčet objektů BLOB v kontejneru pomocí anonymní žádosti, ale nelze vytvořit výčet kontejnery v rámci účtu úložiště.

Nastavení kontejneru oprávnění můžete použít následující:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programově pomocí jednoho z klientských knihoven pro úložiště nebo rozhraní REST API

### <a name="set-container-permissions-in-the-azure-portal"></a>Nastavte oprávnění kontejneru na webu Azure Portal
Nastavení kontejneru oprávnění ve [webu Azure portal](https://portal.azure.com), postupujte podle těchto kroků:

1. Otevřete váš **účtu úložiště** okno na portálu. Váš účet úložiště můžete najít tak, že vyberete **účty úložiště** v okně portálu hlavní nabídky.
1. V části **služby BLOB SERVICE** v okně s nabídkou vyberte **objekty BLOB**.
1. Klikněte pravým tlačítkem na řádek kontejneru, nebo vyberte tři tečky a otevřete kontejneru **kontextovou nabídku**.
1. Vyberte **zásada přístupu** v místní nabídce.
1. Vyberte **získat přístup k typu** z rozevírací nabídky.

    ![Metadata kontejneru dialogové okno Upravit](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Nastavit oprávnění kontejneru s využitím .NET
Pokud chcete nastavit oprávnění pro kontejner pomocí jazyka C# a klientskou knihovnu pro úložiště pro .NET, nejdřív načtěte stávající oprávnění kontejneru voláním **GetPermissions** metody. Nastavte **PublicAccess** vlastnost **BlobContainerPermissions** objekt, který je vrácený **GetPermissions** metoda. Nakonec proveďte volání **měli** metoda s aktualizovanými oprávněními.

Následující příklad nastaví kontejneru oprávnění pro úplné veřejné oprávnění ke čtení. Chcete-li nastavit oprávnění, která veřejné oprávnění ke čtení pro objekty BLOB pouze, nastavte **PublicAccess** vlastnost **BlobContainerPublicAccessType.Blob**. Pokud chcete odebrat všechna oprávnění pro anonymní uživatele, nastavte vlastnost na **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Anonymní přístup ke kontejnerům a objektům BLOB
Konstruktory, které nevyžadují přihlašovací údaje můžete použít klienta, který přistupuje ke kontejnerům a objektům BLOB anonymně. Následující příklady ukazují různé způsoby anonymně odkazovat na prostředky služby objektů Blob.

### <a name="create-an-anonymous-client-object"></a>Vytvoření objektu anonymním klientem
Můžete vytvořit nový objekt klienta služby pro anonymní přístup tím, že poskytuje koncový bod služby Blob service pro účet. Název kontejneru v příslušném účtu, který je dostupný pro anonymní přístup, musíte taky vědět.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob service endpoint.
    CloudBlobClient blobClient = new CloudBlobClient(new Uri(@"https://storagesample.blob.core.windows.net"));

    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = blobClient.GetContainerReference("sample-container");

    // Read the container's properties. Note this is only possible when the container supports full public read access.
    container.FetchAttributes();
    Console.WriteLine(container.Properties.LastModified);
    Console.WriteLine(container.Properties.ETag);
}
```

### <a name="reference-a-container-anonymously"></a>Odkaz kontejner anonymně
Pokud máte adresu URL do kontejneru, které jsou dostupné anonymně, můžete do kontejneru odkazovat přímo.

```csharp
public static void ListBlobsAnonymously()
{
    // Get a reference to a container that's available for anonymous access.
    CloudBlobContainer container = new CloudBlobContainer(new Uri(@"https://storagesample.blob.core.windows.net/sample-container"));

    // List blobs in the container.
    foreach (IListBlobItem blobItem in container.ListBlobs())
    {
        Console.WriteLine(blobItem.Uri);
    }
}
```

### <a name="reference-a-blob-anonymously"></a>Anonymně odkazovat na objekt blob
Pokud máte adresu URL do objektu blob, který je dostupný pro anonymní přístup, můžete odkazovat přímo pomocí adresy URL objektu blob:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funkce dostupné pro anonymní uživatele
Následující tabulka uvádí operace, které může být volána anonymním uživatelům při ACL kontejneru je nastavená na veřejný přístup.

| Operace REST | Oprávnění s úplné veřejné oprávnění ke čtení | Oprávnění s veřejné oprávnění ke čtení pro objekty BLOB pouze |
| --- | --- | --- |
| Seznam kontejnerů |Pouze vlastník |Pouze vlastník |
| Vytvoření kontejneru |Pouze vlastník |Pouze vlastník |
| Získat vlastnosti kontejneru |Vše |Pouze vlastník |
| Získání metadat kontejneru |Vše |Pouze vlastník |
| Nastavte Metadata kontejneru |Pouze vlastník |Pouze vlastník |
| Získání seznamu ACL kontejneru |Pouze vlastník |Pouze vlastník |
| Nastavení ACL kontejneru |Pouze vlastník |Pouze vlastník |
| Odstranění kontejneru |Pouze vlastník |Pouze vlastník |
| Výpis objektů BLOB |Vše |Pouze vlastník |
| Vložení objektu Blob |Pouze vlastník |Pouze vlastník |
| Získání objektu Blob |Vše |Vše |
| Získání vlastností objektu Blob |Vše |Vše |
| Nastavit vlastnosti objektu Blob |Pouze vlastník |Pouze vlastník |
| Získat metadata objektu blob |Vše |Vše |
| Nastavte Metadata objektu Blob |Pouze vlastník |Pouze vlastník |
| Vložit blok |Pouze vlastník |Pouze vlastník |
| Získat seznam blokovaných položek (pouze potvrzené bloků) |Vše |Vše |
| Získat seznam blokovaných (pouze nepotvrzené bloků nebo všechny bloky) |Pouze vlastník |Pouze vlastník |
| Vložit blokovaných webů. |Pouze vlastník |Pouze vlastník |
| Odstranit objekt Blob |Pouze vlastník |Pouze vlastník |
| Zkopírování objektu Blob |Pouze vlastník |Pouze vlastník |
| Vytvoření snímku objektu Blob |Pouze vlastník |Pouze vlastník |
| Zapůjčení objektu Blob |Pouze vlastník |Pouze vlastník |
| Vložit stránky |Pouze vlastník |Pouze vlastník |
| Získání rozsahů stránek |Vše |Vše |
| Doplňovací objekt Blob |Pouze vlastník |Pouze vlastník |

## <a name="next-steps"></a>Další postup

* [Ověřování pro služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Použití sdílených přístupových podpisů (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegování přístupu se sdíleným přístupovým podpisem](https://msdn.microsoft.com/library/azure/ee395415.aspx)
