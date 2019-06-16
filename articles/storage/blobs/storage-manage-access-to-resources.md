---
title: Povolit veřejné oprávnění ke čtení pro kontejnery a objekty BLOB v úložišti objektů Blob v Azure | Dokumentace Microsoftu
description: Zjistěte, jak zpřístupnit kontejnerům a objektům blob pro anonymní přístup a jak k nim přistupovat prostřednictvím kódu programu.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 04/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.openlocfilehash: e0f93b0a95a228b26fae266129aea4b595b05e0f
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65148359"
---
# <a name="manage-anonymous-read-access-to-containers-and-blobs"></a>Správa anonymního přístupu pro čtení ke kontejnerům a objektům blob

Můžete povolit anonymní, veřejný přístup pro čtení kontejneru a jeho objekty BLOB v úložišti objektů Blob v Azure. Díky tomu můžete udělit přístup jen pro čtení k těmto prostředkům bez sdílení klíč účtu a bez nutnosti sdíleného přístupového podpisu (SAS).

Veřejné oprávnění ke čtení je nejvhodnější pro scénáře, ve kterém chcete určité přes bloby až po mít vždycky k dispozici pro anonymní přístup pro čtení. Chcete detailnější kontrolu může vytvořit sdílený přístupový podpis. Sdílené přístupové podpisy umožňují zajistit omezený přístup pomocí různá oprávnění pro konkrétní časové období. Další informace o vytváření sdílené přístupové podpisy, naleznete v tématu [použití sdílených přístupových podpisů (SAS) ve službě Azure Storage](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="grant-anonymous-users-permissions-to-containers-and-blobs"></a>Anonymním uživatelům uděluje oprávnění ke kontejnerům a objektům BLOB

Ve výchozím kontejneru a všech objektů BLOB v něm může být přístupné pouze uživatele, která byla udělena příslušná oprávnění. Pokud chcete udělit přístup pro čtení anonymním uživatelům ke kontejneru a jeho objektům BLOB, můžete nastavit úroveň veřejného přístupu kontejneru. Když udělujete veřejný přístup ke kontejneru, pak anonymní uživatelé mohou číst objektů BLOB v kontejneru veřejně přístupné bez autorizace žádosti.

Konfigurovat kontejner s následujícími oprávněními:

* **Žádné veřejné oprávnění ke čtení:** Kontejner a jeho objekty BLOB je přístupný pouze vlastník účtu úložiště. Toto je výchozí pro všechny nové kontejnery.
* **Veřejné oprávnění ke čtení pro objekty BLOB pouze:** Objekty BLOB v kontejneru můžete číst anonymní žádosti, ale k datům kontejneru není k dispozici. Anonymní klienty nelze vytvořit výčet objektů BLOB v kontejneru.
* **Veřejný přístup pro kontejner a jeho objekty BLOB pro čtení:** Všech kontejnerů a data objektů blob můžete číst anonymní žádosti. Klienty můžete zobrazit výčet objektů BLOB v kontejneru pomocí anonymní žádosti, ale nelze vytvořit výčet kontejnery v rámci účtu úložiště.

Nastavení kontejneru oprávnění můžete použít následující:

* [Azure Portal](https://portal.azure.com)
* [Azure PowerShell](../common/storage-powershell-guide-full.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)
* [Azure CLI](../common/storage-azure-cli.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json#create-and-manage-blobs)
* Programově pomocí jednoho z klientských knihoven pro úložiště nebo rozhraní REST API

### <a name="set-container-public-access-level-in-the-azure-portal"></a>Nastavte úroveň veřejného přístupu kontejneru na webu Azure Portal

Z [webu Azure portal](https://portal.azure.com), můžete aktualizovat úroveň veřejného přístupu pro jeden nebo více kontejnerů:

1. Na webu Azure Portal přejděte na svůj účet úložiště.
1. V části **službu Blob service** v okně s nabídkou vyberte **objekty BLOB**.
1. Vyberte kontejnery, pro které chcete nastavit úroveň veřejného přístupu.
1. Použití **změnit úroveň přístupu** tlačítko a zobrazte nastavení veřejného přístupu.
1. Vyberte úroveň požadované veřejného přístupu z **úroveň veřejného přístupu** rozevíracího seznamu a klikněte na tlačítko OK na použití změny na vybrané kontejnery.

Následující snímek obrazovky ukazuje, jak změnit úroveň veřejného přístupu pro vybrané kontejnery.

![Snímek obrazovky ukazující, jak nastavit úroveň veřejného přístupu na portálu](./media/storage-manage-access-to-resources/storage-manage-access-to-resources-0.png)

> [!NOTE]
> Nelze změnit úroveň veřejného přístupu pro jednotlivé objekty blob. Úroveň veřejného přístupu nastavený pouze na úrovni kontejneru.

### <a name="set-container-public-access-level-with-net"></a>Nastavte úroveň veřejného přístupu kontejneru s využitím .NET

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

Konstruktory, které nevyžadují přihlašovací údaje můžete použít klienta, který přistupuje ke kontejnerům a objektům BLOB anonymně. Následující příklady ukazují různé způsoby odkazovat kontejnerům a objektům BLOB anonymně.

### <a name="create-an-anonymous-client-object"></a>Vytvoření objektu anonymním klientem

Můžete vytvořit nový objekt klienta služby pro anonymní přístup tím, že poskytuje koncový bod úložiště objektů Blob pro účet. Název kontejneru v příslušném účtu, který je dostupný pro anonymní přístup, musíte taky vědět.

```csharp
public static void CreateAnonymousBlobClient()
{
    // Create the client object using the Blob storage endpoint.
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

Následující tabulka uvádí operace, které může být volána anonymně při konfiguraci kontejneru pro veřejný přístup.

| Operace REST | Veřejné oprávnění ke čtení pro kontejner | Veřejné oprávnění ke čtení jenom pro objekty blob |
| --- | --- | --- |
| Seznam kontejnerů | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Vytvoření kontejneru | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Získat vlastnosti kontejneru | Anonymních požadavků | Jenom autorizované požadavky |
| Získání metadat kontejneru | Anonymních požadavků | Jenom autorizované požadavky |
| Nastavte Metadata kontejneru | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Získání seznamu ACL kontejneru | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Nastavení ACL kontejneru | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Odstranění kontejneru | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Výpis objektů BLOB | Anonymních požadavků | Jenom autorizované požadavky |
| Put Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Získání objektu Blob | Anonymních požadavků | Anonymních požadavků |
| Získání vlastností objektu Blob | Anonymních požadavků | Anonymních požadavků |
| Nastavit vlastnosti objektu Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Získat Metadata objektu Blob | Anonymních požadavků | Anonymních požadavků |
| Nastavte Metadata objektu Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Vložit blok | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Získat seznam blokovaných položek (pouze potvrzené bloků) | Anonymních požadavků | Anonymních požadavků |
| Získat seznam blokovaných (pouze nepotvrzené bloků nebo všechny bloky) | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Vložit blokovaných webů. | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Odstranit objekt Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Zkopírování objektu Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Vytvoření snímku objektu Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Zapůjčení objektu Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Vložit stránky | Jenom autorizované požadavky | Jenom autorizované požadavky |
| Získání rozsahů stránek | Anonymních požadavků | Anonymních požadavků |
| Doplňovací objekt Blob | Jenom autorizované požadavky | Jenom autorizované požadavky |

## <a name="next-steps"></a>Další postup

* [Autorizace pro služby Azure Storage](https://docs.microsoft.com/rest/api/storageservices/authorization-for-the-azure-storage-services)
* [Použití sdílených přístupových podpisů (SAS)](../common/storage-dotnet-shared-access-signature-part-1.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)