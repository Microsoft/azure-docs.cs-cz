---
title: Povolit veřejný přístup pro čtení pro kontejnery a objekty BLOB v Azure Blob storage | Microsoft Docs
description: Zjistěte, jak zpřístupnit kontejnery a objekty BLOB pro anonymní přístup a jak přistupovat k nim prostřednictvím kódu programu.
services: storage
author: tamram
manager: jeconnoc
ms.service: storage
ms.topic: article
ms.date: 04/26/2017
ms.author: tamram
ms.openlocfilehash: 4ddafb095816b5be82a18faa9c60869094e5e4c6
ms.sourcegitcommit: fbba5027fa76674b64294f47baef85b669de04b7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/24/2018
ms.locfileid: "29557057"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob
Můžete povolit anonymní, veřejný přístup pro čtení kontejner a jeho objekty BLOB v Azure Blob storage. Díky tomu můžete udělit přístup jen pro čtení k těmto prostředkům bez sdílení klíč účtu a bez nutnosti sdílený přístupový podpis (SAS).

Veřejný přístup pro čtení je nejvhodnější pro scénáře, kde chcete určité objekty BLOB vždy k dispozici pro anonymní přístup pro čtení. Pro větší kontrolu můžete vytvořit sdílený přístupový podpis. Sdílené přístupové podpisy umožňují zadat omezený přístup pomocí různých oprávnění pro konkrétní časové období. Další informace o vytváření sdílený přístup podpisy, najdete v části [pomocí sdílené přístupové podpisy (SAS) ve službě Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Udělte anonymním uživatelům oprávnění ke kontejnerům a objektům BLOB
Ve výchozím kontejneru a všechny objekty BLOB v něm může být přístupné jenom vlastník účtu úložiště. Umožnit anonymní uživatelé oprávnění ke čtení pro kontejner a jeho objekty BLOB, můžete nastavit kontejner oprávnění, která umožní veřejný přístup. Anonymní uživatelé mohou číst objekty BLOB do kontejneru, veřejně přístupný bez ověřování žádosti.

Kontejner můžete nakonfigurovat s následujícími oprávněními:

* **Žádný veřejný přístup pro čtení:** kontejner a jeho objekty BLOB je přístupný jenom vlastník účtu úložiště. Toto je výchozí nastavení pro všechny nové kontejnery.
* **Veřejný přístup pro objekty BLOB pouze pro čtení:** anonymní žádosti může číst objekty BLOB v kontejneru, ale kontejneru data nejsou k dispozici. Anonymní klienti nemůže vytvořit výčet objektů BLOB v kontejneru.
* **Úplný veřejný přístup pro čtení:** všechna data kontejnerů a objektů blob mohou být přečteny anonymní žádost. Klienti mohou anonymní žádosti o výčet objektů BLOB v kontejneru, ale nemůže vytvořit výčet kontejnery v rámci účtu úložiště.

Nastavit oprávnění kontejneru, můžete použít následující:

* [portál Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI 2.0](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programově pomocí jedné z knihovny klienta úložiště nebo REST API

### <a name="set-container-permissions-in-the-azure-portal"></a>Nastavte oprávnění kontejneru na portálu Azure
Nastavit oprávnění kontejneru [portál Azure](https://portal.azure.com), postupujte takto:

1. Otevřete váš **účet úložiště** okno na portálu. Váš účet úložiště můžete najít tak, že vyberete **účty úložiště** v okně portálu přejděte z hlavní nabídky.
1. V části **služby objektů BLOB** v okně nabídky vyberte **kontejnery**.
1. Klikněte pravým tlačítkem na kontejner řádek nebo vyberte se třemi tečkami otevřete kontejneru **kontextovou nabídku**.
1. Vyberte **zásada přístupu** v místní nabídce.
1. Vyberte **přistupovat typu** z rozevírací nabídky.

    ![Metadata kontejneru dialogové okno Upravit](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

### <a name="set-container-permissions-with-net"></a>Nastavte oprávnění kontejner s rozhraním .NET
Nastavení oprávnění pro kontejner pomocí C# a knihovnu klienta služby Storage pro .NET, nejdřív načtěte stávající oprávnění kontejneru voláním **GetPermissions** metoda. Nastavte **PublicAccess** vlastnost **BlobContainerPermissions** objekt, který je vrácen **GetPermissions** metoda. Nakonec zavolejte **měli** metoda aktualizované oprávnění.

Následující příklad nastaví kontejneru oprávnění pro úplné veřejný přístup pro čtení. Chcete-li nastavit oprávnění pro veřejný přístup pro čtení pro pouze objekty BLOB, nastavte **PublicAccess** vlastnost **BlobContainerPublicAccessType.Blob**. Chcete-li odebrat všechna oprávnění pro anonymní uživatele, nastavte vlastnost na **BlobContainerPublicAccessType.Off**.

```csharp
public static void SetPublicContainerPermissions(CloudBlobContainer container)
{
    BlobContainerPermissions permissions = container.GetPermissions();
    permissions.PublicAccess = BlobContainerPublicAccessType.Container;
    container.SetPermissions(permissions);
}
```

## <a name="access-containers-and-blobs-anonymously"></a>Přístup k kontejnery a objekty BLOB anonymně
Konstruktory, které nevyžadují přihlašovací údaje můžete použít klienta, který přistupuje ke kontejnerům a objektům BLOB anonymně. Následující příklady ukazují několik různých způsobů anonymně odkazovat na prostředky služby objektů Blob.

### <a name="create-an-anonymous-client-object"></a>Vytvoření objektu anonymním klientem
Můžete vytvořit nový objekt klienta služby pro anonymní přístup tím, že poskytuje koncový bod služby objektů Blob pro účet. Můžete však také musíte znát název kontejneru v daném účtu, který je k dispozici pro anonymní přístup.

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
Pokud máte adresu URL do kontejneru, který je anonymně k dispozici, můžete tak, aby odkazovaly kontejneru přímo.

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

### <a name="reference-a-blob-anonymously"></a>Referenční objekt blob anonymně
Pokud máte adresu URL do objektu blob, které jsou dostupné pro anonymní přístup, můžete odkazovat přímo pomocí této adresy URL objektu blob:

```csharp
public static void DownloadBlobAnonymously()
{
    CloudBlockBlob blob = new CloudBlockBlob(new Uri(@"https://storagesample.blob.core.windows.net/sample-container/logfile.txt"));
    blob.DownloadToFile(@"C:\Temp\logfile.txt", System.IO.FileMode.Create);
}
```

## <a name="features-available-to-anonymous-users"></a>Funkce, které jsou dostupné pro anonymní uživatele
Následující tabulka uvádí operací, které může být volána anonymní uživatelé při ACL kontejneru je nastavená na veřejný přístup.

| Operace REST | Oprávnění Úplné veřejný přístup pro čtení | Oprávnění s veřejný přístup pro čtení pro pouze objekty BLOB |
| --- | --- | --- |
| Seznam kontejnery |Pouze vlastník |Pouze vlastník |
| Vytvoření kontejneru |Pouze vlastník |Pouze vlastník |
| Získat vlastnosti kontejneru |Vše |Pouze vlastník |
| Získat Metadata kontejneru |Vše |Pouze vlastník |
| Nastavit Metadata kontejneru |Pouze vlastník |Pouze vlastník |
| Získání kontejneru seznamu ACL |Pouze vlastník |Pouze vlastník |
| Nastavit kontejneru seznamu ACL |Pouze vlastník |Pouze vlastník |
| Odstranit kontejner |Pouze vlastník |Pouze vlastník |
| Seznam objektů BLOB |Vše |Pouze vlastník |
| Uveďte objektů Blob |Pouze vlastník |Pouze vlastník |
| Získání objektu Blob |Vše |Vše |
| Získat vlastnosti objektu Blob |Vše |Vše |
| Nastavit vlastnosti objektů Blob |Pouze vlastník |Pouze vlastník |
| Získat Metadata objektu Blob |Vše |Vše |
| Nastavit Metadata objektu Blob |Pouze vlastník |Pouze vlastník |
| Uveďte bloku |Pouze vlastník |Pouze vlastník |
| Získat seznam blokovaných položek (pouze potvrdit bloky) |Vše |Vše |
| Získat seznam blokovaných (pouze nepotvrzené bloky nebo všechny bloky) |Pouze vlastník |Pouze vlastník |
| Uveďte seznam blokovaných položek |Pouze vlastník |Pouze vlastník |
| Odstranit objekt Blob |Pouze vlastník |Pouze vlastník |
| Zkopírování objektu Blob |Pouze vlastník |Pouze vlastník |
| Objekt Blob snímku |Pouze vlastník |Pouze vlastník |
| Objekt Blob zapůjčení |Pouze vlastník |Pouze vlastník |
| Uveďte stránky |Pouze vlastník |Pouze vlastník |
| Get rozsahů stránek |Vše |Vše |
| Append – objekt Blob |Pouze vlastník |Pouze vlastník |

## <a name="next-steps"></a>Další postup

* [Ověřování služby Azure Storage](https://msdn.microsoft.com/library/azure/dd179428.aspx)
* [Použití sdílených přístupových podpisů (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Delegování přístupu se sdíleným přístupovým podpisem](https://msdn.microsoft.com/library/azure/ee395415.aspx)
