---
title: Správa platnosti objektů Blob v Azure storage v Azure Content Delivery Network | Dokumentace Microsoftu
description: Další informace o možnostech řízení time-to-live pro objekty BLOB v Azure CDN ukládání do mezipaměti.
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: ad4801e9-d09a-49bf-b35c-efdc4e6034e8
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: 29e9bee5f7712252d95b9416ad5523b4dfdd4b94
ms.sourcegitcommit: 359b0b75470ca110d27d641433c197398ec1db38
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/07/2019
ms.locfileid: "55814312"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Správa platnosti objektů Blob v Azure storage v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Objektů Blob služby storage](../storage/common/storage-introduction.md#blob-storage) ve službě Azure Storage je jeden z několika míst původu založené na Azure integrované s Azure Content Delivery Network (CDN). Obsah jakékoli veřejně přístupné objektu blob můžete v Azure CDN do mezipaměti, až uplyne time to live (TTL). Hodnota TTL je určeno `Cache-Control` hlaviček v odpovědi HTTP ze zdrojového serveru. Tento článek popisuje několik způsobů, kterými můžete nastavit `Cache-Control` záhlaví na objekt blob ve službě Azure Storage.

Nastavení mezipaměti na webu Azure Portal můžete také řídit nastavení pravidla ukládání do mezipaměti CDN. Pokud vytvoříte pravidlo ukládání do mezipaměti a nastavte její chování ukládání do mezipaměti na **přepsat** nebo **Nepoužívat mezipaměť**, zadaný původ ukládání do mezipaměti nastavení popisovaná v tomto článku jsou ignorovány. Informace o získání všeobecného přehledu ukládání do mezipaměti najdete v tématu [funguje jak ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete nastavit žádná hodnota TTL pro objekt blob. V tomto případě Azure CDN automaticky použije výchozí hodnota TTL sedm dní, pokud jste nastavili na portálu Azure portal pravidla ukládání do mezipaměti. Toto výchozí nastavení TTL platí jenom pro optimalizace doručení obecné web. Optimalizace velkých souborů je výchozí hodnota TTL je jeden den a pro optimalizace streamování médií, výchozí hodnota TTL je 1 rok.
> 
> Další informace o tom, jak funguje Azure CDN k urychlení přístupu k objektům BLOB a další soubory, naleznete v tématu [Přehled služby Azure Content Delivery Network](cdn-overview.md).
> 
> Další informace o službě Azure Blob storage najdete v tématu [seznámení se službou Blob storage](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení hlavičky Cache-Control s využitím pravidel ukládání do mezipaměti CDN
Upřednostňovanou metodou pro nastavení objekt blob `Cache-Control` záhlaví, je použít pravidla ukládání do mezipaměti na webu Azure Portal. Další informace o pravidla ukládání do mezipaměti CDN najdete v tématu [řízení Azure CDN s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici pouze pro **Azure CDN Standard od Verizonu** a **Azure CDN Standard od Akamai** profily. Pro **Azure CDN Premium od Verizonu** profily, je nutné použít [stroj pravidel Azure CDN](cdn-rules-engine.md) v **spravovat** portálu pro podobné funkce.

**Přejděte na stránku pravidla ukládání do mezipaměti CDN**:

1. Na webu Azure Portal vyberte profil CDN a pak vyberte koncový bod objektu BLOB.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Chcete-li nastavit hlavičky Cache-Control služby Blob storage s využitím globální pravidla ukládání do mezipaměti:**

1. V části **globální pravidla ukládání do mezipaměti**, nastavte **chování ukládání řetězců dotazů** k **ignorovat řetězce dotazů** a nastavte **chování ukládání do mezipaměti** k  **Přepsat**.
      
2. Pro **doba vypršení platnosti mezipaměti**, zadejte 3600 v **sekund** pole nebo 1 **hodin** pole. 

   ![Příklad globální pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Tento globální pravidla ukládání do mezipaměti nastaví dobu trvání mezipaměti jednu hodinu a má vliv na všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` nebo `Expires` hlavičky HTTP, které se odesílá na zdrojový server určeném na koncový bod.   

3. Vyberte **Uložit**.
 
**Nastavení hlavičky Cache-Control souboru objektu blob s použitím vlastní pravidla ukládání do mezipaměti:**

1. V části **vlastní pravidla ukládání do mezipaměti**, vytvořte dvě podmínky shody:

     A. Pro první podmínky shody, nastavte **odpovídají podmínce** k **cesta** a zadejte `/blobcontainer1/*` pro **odpovídají hodnotě**. Nastavte **chování ukládání do mezipaměti** k **přepsat** a zadejte 4 **hodin** pole.

    B. Druhá podmínka shody, nastavte **odpovídají podmínce** k **cesta** a zadejte `/blobcontainer1/blob1.txt` pro **odpovídají hodnotě**. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte v 2 **hodin** pole.

    ![Příklad vlastní pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    První vlastní pravidlo ukládání do mezipaměti nastaví dobu trvání čtyři hodiny pro všechny soubory objektů blob v mezipaměti `/blobcontainer1` složky na původním serveru určeného váš koncový bod. První pravidlo pro přepíše druhé pravidlo `blob1.txt` pouze soubor objektu blob a nastaví mezipaměti trvání dvě hodiny.

2. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Nastavení hlavičky Cache-Control pomocí Azure Powershellu
[Prostředí Azure PowerShell](/powershell/azure/overview) je jedním ze způsobů nejrychlejší a procesorově nejvýkonnější ke správě služeb Azure. Použití `Get-AzureStorageBlob` rutiny pro získání odkazu na objekt blob, pak nastavte `.ICloudBlob.Properties.CacheControl` vlastnost. 

Příklad:

```powershell
# Create a storage context
$context = New-AzureStorageContext -StorageAccountName "<storage account name>" -StorageAccountKey "<storage account key>"

# Get a reference to the blob
$blob = Get-AzureStorageBlob -Context $context -Container "<container name>" -Blob "<blob name>"

# Set the CacheControl property to expire in 1 hour (3600 seconds)
$blob.ICloudBlob.Properties.CacheControl = "max-age=3600"

# Send the update to the cloud
$blob.ICloudBlob.SetProperties()
```

> [!TIP]
> Můžete také použít PowerShell k [spravovat profily CDN a koncové body](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Nastavení hlavičky Cache-Control s použitím rozhraní .NET
Chcete-li určit objekt blob `Cache-Control` záhlaví pomocí kódu .NET, použijte [Klientská knihovna Azure Storage pro .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) nastavit [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) vlastnost.

Příklad:

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
> Další ukázky kódu rozhraní .NET jsou k dispozici v [ukázky pro úložiště objektů Blob v Azure pro .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Nastavení hlavičky Cache-Control pomocí jiných metod

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
S [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/), můžete zobrazit a upravit prostředky úložiště objektů blob, včetně vlastnosti, jako *CacheControl* vlastnost. 

Chcete-li aktualizovat *CacheControl* vlastností objektu blob pomocí Průzkumníka služby Azure Storage:
   1. Vyberte objekt blob a pak vyberte **vlastnosti** v místní nabídce. 
   2. Přejděte dolů k položce *CacheControl* vlastnost.
   3. Zadejte hodnotu a pak vyberte **Uložit**.


![Vlastnosti služby Azure Storage Exploreru](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Rozhraní příkazového řádku Azure
S [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) (CLI), můžete spravovat prostředků Azure blob Storage z příkazového řádku. Chcete-li nastavit hlavičku cache-control, při nahrání objektu blob pomocí Azure CLI, nastavte *cacheControl* vlastnost s použitím `-p` přepnout. Následující příklad ukazuje, jak nastavit interval TTL, ZÍSKÁ na jednu hodinu (3 600 sekund):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Rozhraní REST API služby Azure storage
Můžete použít [rozhraní REST API služby Azure storage](https://msdn.microsoft.com/library/azure/dd179355.aspx) explicitně nastavit *x-ms-blob-cache-control* vlastnost s použitím následujících operací na vyžádání:
  
   - [Vložení objektu Blob](https://msdn.microsoft.com/library/azure/dd179451.aspx)
   - [Vložit blokovaných webů.](https://msdn.microsoft.com/library/azure/dd179467.aspx)
   - [Nastavit vlastnosti objektu Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Testování hlavičku Cache-Control
Můžete snadno ověřit nastavení TTL objektů BLOB. V prohlížeči [vývojářské nástroje](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, který obsahuje objekt blob `Cache-Control` hlavičky odpovědi. Můžete také použít nástroje jako [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), nebo [Fiddler](http://www.telerik.com/fiddler) a prověří hlavičky odpovědi.

## <a name="next-steps"></a>Další kroky
* [Zjistěte, jak spravovat platnost obsahu cloudové služby v Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Další informace o ukládání do mezipaměti koncepty](cdn-how-caching-works.md)

