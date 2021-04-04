---
title: Správa vypršení platnosti služby Azure Blob Storage
titleSuffix: Azure Content Delivery Network
description: Přečtěte si o možnostech řízení času na živé pro objekty BLOB v ukládání do mezipaměti Azure CDN.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: azure-cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: how-to
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 206ff6f888229356743bebb816cf03e4f7a7504b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "92778703"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Správa vypršení platnosti služby Azure Blob Storage v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Služba BLOB Storage](../storage/common/storage-introduction.md#blob-storage) v Azure Storage je jedním z několika zdrojů založených na platformě Azure integrovaných se službou Azure Content DELIVERY Network (CDN). Veškerý veřejně přístupný obsah objektu BLOB se dá v Azure CDN Uložit do mezipaměti, dokud neuplyne jeho hodnota TTL (Time to Live). Hodnota TTL je určena `Cache-Control` hlavičkou v odpovědi HTTP ze zdrojového serveru. Tento článek popisuje několik způsobů, jak můžete nastavit `Cache-Control` hlavičku objektu BLOB v Azure Storage.

Nastavení mezipaměti můžete také ovládat z Azure Portal nastavením pravidel ukládání do mezipaměti CDN. Pokud vytvoříte pravidlo ukládání do mezipaměti a nastavíte jeho chování ukládání do mezipaměti pro **přepsání** nebo **obejít mezipaměť**, budou nastavení ukládání do mezipaměti uvedená v tomto článku ignorována. Informace o obecných konceptech mezipaměti najdete v tématu [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> U objektu blob můžete nastavit možnost Nepoužívat hodnotu TTL. V takovém případě Azure CDN automaticky použije výchozí hodnotu TTL 7 dní, pokud jste nastavili pravidla ukládání do mezipaměti v Azure Portal. Výchozí hodnota TTL se vztahuje pouze na obecné optimalizace doručování webů. Pro optimalizace velkých souborů je výchozí hodnota TTL jeden den a optimalizace datových proudů médií je výchozí hodnota TTL jeden rok.
> 
> Další informace o tom, jak Azure CDN funguje pro urychlení přístupu k objektům blob a jiným souborům, najdete v tématu [Přehled Azure Content Delivery Network](cdn-overview.md).
> 
> Další informace o službě Azure Blob Storage najdete v tématu [Úvod do úložiště objektů BLOB](../storage/blobs/storage-blobs-introduction.md).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení hlaviček Cache-Control pomocí pravidel ukládání do mezipaměti CDN
Upřednostňovanou metodou pro nastavení záhlaví objektu BLOB `Cache-Control` je použití pravidel ukládání do mezipaměti v Azure Portal. Další informace o pravidlech ukládání do mezipaměti CDN najdete v tématu [Azure CDN řízení chování při ukládání do mezipaměti pomocí pravidel pro ukládání do](cdn-caching-rules.md)mezipaměti.

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici pouze pro **Azure CDN Standard od Verizon** a **Azure CDN Standard od profilů Akamai** . Pro **Azure CDN Premium ze profilů Verizon** je nutné použít [modul Azure CDN Rules](./cdn-verizon-premium-rules-engine.md) na portálu pro **správu** pro podobné funkce.

**Přejděte na stránku pravidla ukládání do mezipaměti CDN**:

1. V Azure Portal vyberte profil CDN a pak vyberte koncový bod pro objekt BLOB.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Nastavení hlaviček Cache-Control služby Blob Storage pomocí globálních pravidel ukládání do mezipaměti:**

1. V části **globální pravidla ukládání do** mezipaměti nastavte **chování při ukládání řetězce dotazu** na **Ignorovat řetězce dotazů** a nastavte **chování ukládání do mezipaměti** pro **přepsání**.
      
2. V poli **Doba vypršení platnosti mezipaměti** zadejte 3600 do pole **sekund** nebo 1 v poli **hodiny** . 

   ![Příklad globálních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Toto globální pravidlo ukládání do mezipaměti nastavuje dobu trvání mezipaměti jednu hodinu a ovlivňuje všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` `Expires` hlavičky protokolu HTTP, které jsou odesílány ze zdrojového serveru zadaného koncovým bodem.   

3. Vyberte **Uložit**.
 
**Nastavení hlaviček Cache-Control souboru BLOB pomocí vlastních pravidel ukládání do mezipaměti:**

1. V části **vlastní pravidla ukládání do mezipaměti** vytvořte dvě podmínky shody:

     A. U první podmínky shody nastavte **podmínku Match** na **path** a zadejte `/blobcontainer1/*` **hodnotu shody**. Nastavte **chování ukládání do mezipaměti** pro **přepsání** a do pole **hodiny** zadejte 4.

    B. U podmínky druhé shody nastavte **podmínku Match** na **path** a zadejte `/blobcontainer1/blob1.txt` **hodnotu shody**. Nastavte **chování ukládání do mezipaměti** pro **přepsání** a zadáním 2 do pole **hodiny** .

    ![Příklad vlastních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    První vlastní pravidlo ukládání do mezipaměti nastaví dobu trvání mezipaměti pro všechny soubory objektů BLOB ve `/blobcontainer1` složce na zdrojovém serveru určeném vaším koncovým bodem na čtyři hodiny. Druhé pravidlo přepíše první pravidlo `blob1.txt` pouze pro soubor objektu BLOB a nastaví pro něj dobu trvání mezipaměti dvě hodiny.

2. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Nastavení hlaviček Cache-Control pomocí Azure PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/) je jedním z nejrychlejších a nejúčinnějších způsobů správy služeb Azure. Pomocí `Get-AzStorageBlob` rutiny Získejte odkaz na objekt BLOB a pak nastavte `.ICloudBlob.Properties.CacheControl` vlastnost. 

Například:

```powershell
# Create a storage context
$context = New-AzStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Ke [správě profilů a koncových bodů CDN](cdn-manage-powershell.md)taky můžete použít PowerShell.
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Nastavení hlaviček Cache-Control pomocí .NET
Chcete-li zadat hlavičku objektu BLOB `Cache-Control` pomocí kódu .NET, nastavte vlastnost [CloudBlob. Properties. CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) pomocí [Azure Storage klientské knihovny pro rozhraní .NET](../storage/blobs/storage-quickstart-blobs-dotnet.md) .

Například:

```csharp
class Program
{
    const string connectionString = "<storage connection string>";
    static void Main()
    {
        // Retrieve storage account information from connection string
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(connectionString);

        // Create a blob client for interacting with the blob service.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Create a reference to the container
        CloudBlobContainer <container name> = blobClient.GetContainerReference("<container name>");

        // Create a reference to the blob
        CloudBlob <blob name> = container.GetBlobReference("<blob name>");

        // Set the CacheControl property to expire in 1 hour (3600 seconds)
        blob.Properties.CacheControl = "max-age=3600";

        // Update the blob's properties in the cloud
        blob.SetProperties();
    }
}
```

> [!TIP]
> V [Azure Blob Storage Samples pro .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/)jsou k dispozici více ukázek kódu .NET.
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Nastavení hlaviček Cache-Control pomocí jiných metod

### <a name="azure-storage-explorer"></a>Průzkumník služby Azure Storage
Pomocí [Průzkumník služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/)můžete zobrazit a upravit prostředky služby Blob Storage, včetně vlastností, jako je například vlastnost *CacheControl* . 

Aktualizace vlastnosti *CacheControl* objektu blob pomocí Průzkumník služby Azure Storage:
   1. Vyberte objekt BLOB a potom v místní nabídce vyberte možnost **vlastnosti** . 
   2. Přejděte dolů na vlastnost *CacheControl* .
   3. Zadejte hodnotu a pak vyberte **Uložit**.


![Vlastnosti Průzkumník služby Azure Storage](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Rozhraní příkazového řádku Azure
Pomocí rozhraní příkazového řádku (CLI) [azure Command-Line](/cli/azure) můžete spravovat prostředky objektů BLOB v Azure. Pokud chcete nastavit hlavičku Cache-Control při nahrávání objektu BLOB pomocí rozhraní příkazového řádku Azure, nastavte vlastnost *cacheControl* pomocí `-p` přepínače. Následující příklad ukazuje, jak nastavit hodnotu TTL na jednu hodinu (3600 sekund):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>REST API služby Azure Storage
[Služby Azure Storage](/rest/api/storageservices/) můžete použít REST API k explicitnímu nastavení vlastnosti *x-MS-BLOB-Cache-Control* pomocí následujících operací na žádost:
  
   - [Vložení objektu blob](/rest/api/storageservices/Put-Blob)
   - [Seznam blokovaných umístění](/rest/api/storageservices/Put-Block-List)
   - [Nastavení vlastností objektu BLOB](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testování hlavičky Cache-Control
Můžete snadno ověřit nastavení TTL objektů BLOB. Pomocí [vývojářských nástrojů](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)v prohlížeči otestujte, jestli váš objekt BLOB obsahuje `Cache-Control` hlavičku odpovědi. K prohlédnutí hlaviček odpovědi můžete použít také nástroj, jako je [wget](https://www.gnu.org/software/wget/), [post](https://www.getpostman.com/)nebo [Fiddler](https://www.telerik.com/fiddler) .

## <a name="next-steps"></a>Další kroky
* [Naučte se spravovat vypršení platnosti obsahu cloudové služby v Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Přečtěte si o konceptech ukládání do mezipaměti](cdn-how-caching-works.md)