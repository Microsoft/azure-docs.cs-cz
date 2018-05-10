---
title: Spravovat konec platnosti úložiště objektů Azure Blob v Azure Content Delivery Network | Microsoft Docs
description: Informace o možnostech řízení time to live pro objekty BLOB v Azure CDN ukládání do mezipaměti.
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
ms.openlocfilehash: a0f89a272fa300f6acced2de02ba5465ab282079
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/07/2018
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Spravovat konec platnosti úložiště objektů Azure Blob v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Azure Blob Storage](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Služba úložiště objektů Blob](../storage/common/storage-introduction.md#blob-storage) ve službě Azure Storage je jedním z několika Azure na základě původu integrované s Azure Content Delivery Network (CDN). Veškerý obsah, veřejně přístupná objektů blob můžete v Azure CDN do mezipaměti, dokud uplynutí jeho time to live (TTL). Hodnota TTL je dáno `Cache-Control` hlavičku HTTP odpovědi ze zdrojového serveru. Tento článek popisuje několik způsobů, které můžete nastavit `Cache-Control` záhlaví u objektu blob ve službě Azure Storage.

Nastavení mezipaměti na portálu Azure můžete také ovládat nastavením [CDN ukládání do mezipaměti pravidla](#setting-cache-control-headers-by-using-caching-rules). Pokud jste vytvořit pravidlo pro ukládání do mezipaměti a nastavte své chování ukládání do mezipaměti **přepsat** nebo **vynechat mezipaměti**, zadaný počátečního nastavení ukládání do mezipaměti popsané v tomto článku jsou ignorovány. Informace o obecné koncepty ukládání do mezipaměti najdete v tématu [průběh ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete nastavit žádné TTL pro objekt blob. V takovém případě Azure CDN automaticky použije výchozí hodnotu TTL sedm dní, pokud jste nastavili ukládání do mezipaměti pravidla na portálu Azure. Toto výchozí nastavení TTL se vztahuje pouze na obecné webové doručení optimalizace. Pro optimalizace na velkých souborů je výchozí hodnota TTL je jeden den a pro streamování optimalizace médií, je výchozí hodnota TTL je jeden rok.
> 
> Další informace o tom, jak funguje Azure CDN pro urychlení přístupu k objektům BLOB a další soubory, najdete v části [přehled Azure Content Delivery Network](cdn-overview.md).
> 
> Další informace o Azure Blob storage najdete v tématu [Úvod do úložiště objektů Blob](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení hlavičky Cache-Control pomocí ukládání do mezipaměti pravidla CDN
Upřednostňovanou metodou pro nastavení objekt blob `Cache-Control` záhlaví je používat ukládání do mezipaměti pravidla na portálu Azure. Další informace o ukládání do mezipaměti pravidla CDN najdete v tématu [Azure CDN ovládacího prvku s ukládáním do mezipaměti pravidla chování ukládání do mezipaměti](cdn-caching-rules.md).

> [!NOTE] 
> Jsou k dispozici pouze pro ukládání do mezipaměti pravidla **Azure CDN Standard od společnosti Verizon** a **Azure CDN Standard od společnosti Akamai** profily. Pro **Azure CDN Premium od společnosti Verizon** profily, je nutné použít [stroj pravidel Azure CDN](cdn-rules-engine.md) v **spravovat** portálu pro podobné funkce.

**Přejděte na stránku pravidla ukládání do mezipaměti pro CDN**:

1. Na portálu Azure vyberte profil CDN a potom vyberte koncového bodu pro tento objekt blob.

2. V levém podokně v části nastavení, vyberte **ukládání do mezipaměti pravidla**.

   ![Ukládání do mezipaměti tlačítko pravidla CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   **Ukládání do mezipaměti pravidla** se zobrazí stránka.

   ![Ukládání do mezipaměti stránky CDN](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Nastavení hlavičky Cache-Control služby objektů Blob storage pomocí globální pravidla pro ukládání do mezipaměti:**

1. V části **globální ukládání do mezipaměti pravidla**, nastavte **chování ukládání řetězců s dotazy** k **ignorovat řetězce dotazů** a nastavte **chování ukládání do mezipaměti** k  **Přepsání**.
      
2. Pro **mezipaměti Doba vypršení platnosti**, zadejte 3600 v **sekund** pole nebo 1 v **hodin** pole. 

   ![Příklad pravidla globální ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Toto pravidlo globální ukládání do mezipaměti nastaví dobu trvání mezipaměti jednu hodinu a ovlivňuje všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` nebo `Expires` hlavičky HTTP, které odesílá na zdrojový server zadané v koncovém bodě.   

3. Vyberte **Uložit**.
 
**Nastavení objektu blob hlavičky Cache-Control souboru pomocí vlastní pravidla pro ukládání do mezipaměti:**

1. V části **vlastní ukládání do mezipaměti pravidla**, vytvořte dvě podmínky shody:

     A. Pro první podmínku shodu nastavit **vyhovují podmínce** k **cesta** a zadejte `/blobcontainer1/*` pro **odpovídají hodnotě**. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte 4 v **hodin** pole.

    B. Pro druhou podmínku shody, nastavte **vyhovují podmínce** k **cesta** a zadejte `/blobcontainer1/blob1.txt` pro **odpovídají hodnotě**. Nastavit **chování ukládání do mezipaměti** k **přepsat** a zadejte 2 v **hodin** pole.

    ![Příklad vlastní pravidla ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    Nastaví dobu trvání čtyři hodiny pro objekt blob soubory v mezipaměti na první vlastní pravidlo pro ukládání do mezipaměti `/blobcontainer1` složky na původním serveru určeného váš koncový bod. První pravidlo přepíše druhé pravidlo `blob1.txt` pouze soubor blob a nastaví dobu trvání v mezipaměti pro něj dvě hodiny.

2. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Nastavení hlavičky Cache-Control pomocí prostředí Azure PowerShell
[Prostředí Azure PowerShell](/powershell/azure/overview) je jedním z nejrychlejší a nejúčinnějších způsobů, jak spravovat služeb Azure. Použití `Get-AzureStorageBlob` rutiny odkazovat na objekt blob, nastavte `.ICloudBlob.Properties.CacheControl` vlastnost. 

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
> Můžete taky použít PowerShell k [spravovat koncové body a profilů CDN](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Nastavení hlavičky Cache-Control pomocí rozhraní .NET
K určení objekt blob `Cache-Control` záhlaví pomocí rozhraní .NET kódu, použijte [Klientská knihovna pro úložiště Azure pro .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md) nastavit [CloudBlob.Properties.CacheControl](https://msdn.microsoft.com/library/microsoft.windowsazure.storage.blob.blobproperties.cachecontrol.aspx) vlastnost.

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
> Další ukázky kódu .NET jsou k dispozici v [ukázky úložiště objektů Blob Azure pro .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Nastavení hlavičky Cache-Control pomocí jiných metod

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
S [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/), můžete zobrazit a upravit vaše prostředky úložiště objektů blob, včetně vlastnosti, jako *CacheControl* vlastnost. 

Chcete-li aktualizovat *CacheControl* vlastnost objektu blob s Azure Storage Explorer:
   1. Vyberte objekt blob a potom vyberte **vlastnosti** v místní nabídce. 
   2. Přejděte dolů k položce *CacheControl* vlastnost.
   3. Zadejte hodnotu a pak vyberte **Uložit**.


![Azure Storage Explorer vlastnosti](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Rozhraní příkazového řádku Azure
Pomocí [rozhraní příkazového řádku Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) (CLI), můžete spravovat prostředky Azure blob z příkazového řádku. Hlavička cache-control při nahrání objektu blob pomocí Azure CLI, nastavit *cacheControl* vlastnost pomocí `-p` přepínače. Následující příklad ukazuje, jak nastavit hodnotu TTL na jednu hodinu (3600 sekund):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Azure storage services REST API
Můžete použít [Azure storage services REST API](https://msdn.microsoft.com/library/azure/dd179355.aspx) explicitně nastavit *x-ms-blob-cache-control* vlastnost pomocí následující operace na vyžádání:
  
   - [Uveďte objektů Blob](https://msdn.microsoft.com/library/azure/dd179451.aspx)
   - [Uveďte seznam blokovaných položek](https://msdn.microsoft.com/library/azure/dd179467.aspx)
   - [Nastavit vlastnosti objektů Blob](https://msdn.microsoft.com/library/azure/ee691966.aspx)

## <a name="testing-the-cache-control-header"></a>Testování hlavička Cache-Control
Snadno můžete ověřit nastavení TTL objektů BLOB. V prohlížeči [nástroje pro vývojáře](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/), test, který zahrnuje objektu blob služby `Cache-Control` hlavičky odpovědi. Můžete také použít nástroj jako [Wget](https://www.gnu.org/software/wget/), [Postman](https://www.getpostman.com/), nebo [Fiddler](http://www.telerik.com/fiddler) a prověří hlavičky odpovědi.

## <a name="next-steps"></a>Další kroky
* [Zjistěte, jak spravovat platnost obsahu cloudové služby v Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Další informace o ukládání do mezipaměti koncepty](cdn-how-caching-works.md)

