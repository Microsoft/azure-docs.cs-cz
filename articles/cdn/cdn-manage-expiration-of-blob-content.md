---
title: Správa vypršení platnosti úložiště objektů blob Azure
titleSuffix: Azure Content Delivery Network
description: Další informace o možnostech řízení time-to-live pro objekty BLOB v azure cdn ukládání do mezipaměti.
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
ms.topic: article
ms.date: 02/1/2018
ms.author: mazha
ms.openlocfilehash: f28282a802e4b38fadc05c7090fa2a2af154de54
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74083158"
---
# <a name="manage-expiration-of-azure-blob-storage-in-azure-cdn"></a>Správa vypršení platnosti úložiště objektů blob Azure v Azure CDN
> [!div class="op_single_selector"]
> * [Webový obsah Azure](cdn-manage-expiration-of-cloud-service-content.md)
> * [Úložiště objektů blob Azure](cdn-manage-expiration-of-blob-content.md)
> 
> 

[Služba úložiště objektů blob](../storage/common/storage-introduction.md#blob-storage) ve službě Azure Storage je jedním z několika původů založených na Azure integrovaných se sítí Azure Content Delivery Network (CDN). Jakýkoli veřejně přístupný obsah objektu blob lze uložit do mezipaměti v Azure CDN, dokud jeho čas ověnčený (TTL) uplyne. TTL je určena `Cache-Control` hlavičkou v odpovědi HTTP z původního serveru. Tento článek popisuje několik způsobů, `Cache-Control` jak můžete nastavit záhlaví na objekt blob ve službě Azure Storage.

Nastavení mezipaměti můžete řídit také z portálu Azure nastavením pravidel ukládání do mezipaměti CDN. Pokud vytvoříte pravidlo ukládání do mezipaměti a nastavíte jeho chování ukládání do mezipaměti na **Přepsat** nebo **Převrátit mezipaměť**, nastavení mezipaměti poskytované původem popsané v tomto článku budou ignorována. Informace o obecných konceptech ukládání do mezipaměti naleznete v tématu [Jak funguje ukládání do mezipaměti](cdn-how-caching-works.md).

> [!TIP]
> Můžete nastavit žádné TTL na objekt blob. V takovém případě Azure CDN automaticky použije výchozí TTL sedm dní, pokud jste nastavili pravidla ukládání do mezipaměti na webu Azure Portal. Tento výchozí hodnota TTL se vztahuje pouze na obecné optimalizace webového doručování. Pro velké optimalizace souborů je výchozí Hodnota TTL jeden den a pro optimalizace streamování médií je výchozí hodnota TTL jeden rok.
> 
> Další informace o tom, jak Azure CDN funguje pro urychlení přístupu k objektům BLOB a dalším souborům, najdete [v tématu Přehled sítě pro doručování obsahu Azure](cdn-overview.md).
> 
> Další informace o úložišti objektů Blob Azure najdete [v tématu Úvod do úložiště objektů BLOB](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction).
 

## <a name="setting-cache-control-headers-by-using-cdn-caching-rules"></a>Nastavení záhlaví řízení mezipaměti pomocí pravidel ukládání do mezipaměti CDN
Upřednostňovanou metodou pro nastavení `Cache-Control` záhlaví objektu blob je použití pravidel ukládání do mezipaměti na webu Azure Portal. Další informace o pravidlech ukládání do mezipaměti CDN naleznete v [tématu Řízení chování mezipaměti Azure CDN pomocí pravidel ukládání do mezipaměti](cdn-caching-rules.md).

> [!NOTE] 
> Pravidla ukládání do mezipaměti jsou k dispozici jenom pro **Azure CDN Standard od Verizonu** a Azure CDN Standard z profilů **Akamai.** Pro **profily Azure CDN Premium od Verizonu** je nutné použít [modul pravidel Azure CDN](cdn-rules-engine.md) na portálu Pro **správu** pro podobné funkce.

**Chcete-li přejít na stránku pravidel ukládání do mezipaměti sítě CDN**,

1. Na webu Azure Portal vyberte profil CDN a pak vyberte koncový bod pro objekt blob.

2. V levém podokně v části Nastavení vyberte **Pravidla ukládání do mezipaměti**.

   ![Tlačítko cdn caching rules](./media/cdn-manage-expiration-of-blob-content/cdn-caching-rules-btn.png)

   Zobrazí se stránka **Pravidla ukládání do mezipaměti**.

   ![Stránka cdn ukládání do mezipaměti](./media/cdn-manage-expiration-of-blob-content/cdn-caching-page.png)


**Nastavení záhlaví správce mezipaměti služby úložiště objektů BLOB pomocí globálních pravidel ukládání do mezipaměti:**

1. V části **Globální pravidla ukládání do mezipaměti**nastavte chování ukládání řetězce dotazu do **mezipaměti** na **Ignorovat řetězce dotazu** a nastavte **chování ukládání do mezipaměti** na **Přepsat**.
      
2. V **poli Doba vypršení platnosti mezipaměti**zadejte 3600 do pole **Sekundy** nebo 1 do pole **Hodiny.** 

   ![Příklad globálních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-global-caching-rules-example.png)

   Toto pravidlo globálního ukládání do mezipaměti nastaví dobu trvání mezipaměti na jednu hodinu a ovlivní všechny požadavky na koncový bod. Přepíše všechny `Cache-Control` `Expires` nebo HTTP záhlaví, které jsou odesílány zdrojovým serverem určeným koncovým bodem.   

3. Vyberte **Uložit**.
 
**Nastavení záhlaví správce mezipaměti souboru objektů blob pomocí vlastních pravidel ukládání do mezipaměti:**

1. V části **Vlastní pravidla ukládání do mezipaměti**vytvořte dvě podmínky shody:

     A. Pro první podmínku shody nastavte **podmínku shoda** s **cestou** a zadejte `/blobcontainer1/*` **hodnotu Shoda**. Nastavte **chování ukládání do mezipaměti** na **Přepsat** a zadejte 4 do pole **Hodiny.**

    B. Pro druhou podmínku shody nastavte **podmínku shoda** s **cestou** a zadejte `/blobcontainer1/blob1.txt` **hodnotu Shoda**. Nastavte **chování ukládání do mezipaměti** na **Přepsat** a zadejte 2 do pole **Hodiny.**

    ![Příklad vlastních pravidel ukládání do mezipaměti CDN](./media/cdn-manage-expiration-of-blob-content/cdn-custom-caching-rules-example.png)

    První vlastní pravidlo ukládání do mezipaměti nastaví dobu trvání mezipaměti čtyři hodiny pro všechny soubory objektů blob ve `/blobcontainer1` složce na původní server určený koncovým bodem. Druhé pravidlo přepíše první pravidlo `blob1.txt` pouze pro soubor objektů blob a nastaví dobu trvání mezipaměti dvě hodiny pro něj.

2. Vyberte **Uložit**.


## <a name="setting-cache-control-headers-by-using-azure-powershell"></a>Nastavení záhlaví řízení mezipaměti pomocí Azure PowerShellu

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/overview) je jeden z nejrychlejších a nejvýkonnějších způsobů správy služeb Azure. Pomocí `Get-AzStorageBlob` rutiny získat odkaz na objekt blob, `.ICloudBlob.Properties.CacheControl` nastavte vlastnost. 

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
> PowerShell můžete také použít ke [správě profilů CDN a koncových bodů](cdn-manage-powershell.md).
> 
>

## <a name="setting-cache-control-headers-by-using-net"></a>Nastavení záhlaví řízení mezipaměti pomocí rozhraní .NET
Chcete-li určit `Cache-Control` záhlaví objektu blob pomocí kódu .NET, nastavte vlastnost [CloudBlob.Properties.CacheControl](/dotnet/api/microsoft.azure.storage.blob.blobproperties.cachecontrol) pomocí [klientské knihovny úložiště Azure pro rozhraní .NET.](../storage/blobs/storage-dotnet-how-to-use-blobs.md)

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
> Další ukázky kódu .NET jsou k dispozici ve [vzorcích úložiště objektů blob Azure pro rozhraní .NET](https://azure.microsoft.com/documentation/samples/storage-blob-dotnet-getting-started/).
> 

## <a name="setting-cache-control-headers-by-using-other-methods"></a>Nastavení záhlaví cache-control pomocí jiných metod

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Pomocí [Průzkumníka úložiště Azure](https://azure.microsoft.com/features/storage-explorer/)můžete zobrazit a upravit prostředky úložiště objektů blob, včetně vlastností, jako je vlastnost *CacheControl.* 

Aktualizace vlastnosti *CacheControl* objektu blob pomocí Průzkumníka úložiště Azure:
   1. Vyberte objekt blob a pak z kontextové nabídky vyberte **Vlastnosti.** 
   2. Posuňte se dolů na vlastnost *CacheControl.*
   3. Zadejte hodnotu a pak vyberte **Uložit**.


![Vlastnosti Průzkumníka úložiště Azure](./media/cdn-manage-expiration-of-blob-content/cdn-storage-explorer-properties.png)

### <a name="azure-command-line-interface"></a>Rozhraní příkazového řádku Azure
Pomocí [rozhraní Příkazového řádku Azure](https://docs.microsoft.com/cli/azure) (CLI) můžete spravovat prostředky objektů blob Azure z příkazového řádku. Chcete-li nastavit záhlaví řízení mezipaměti při nahrávání objektu blob s azure `-p` CLI, nastavte vlastnost *cacheControl* pomocí přepínače. Následující příklad ukazuje, jak nastavit TTL na jednu hodinu (3600 sekund):
  
```azurecli
azure storage blob upload -c <connectionstring> -p cacheControl="max-age=3600" .\<blob name> <container name> <blob name>
```

### <a name="azure-storage-services-rest-api"></a>Rozhraní REST API služby Azure
Rozhraní REST [API služby Azure můžete](/rest/api/storageservices/) explicitně nastavit vlastnost *x-ms-blob-cache-control* pomocí následujících operací na základě požadavku:
  
   - [Vložení objektu blob](/rest/api/storageservices/Put-Blob)
   - [Seznam blokovaných položek](/rest/api/storageservices/Put-Block-List)
   - [Nastavení vlastností objektu blob](/rest/api/storageservices/Set-Blob-Properties)

## <a name="testing-the-cache-control-header"></a>Testování hlavičky Cache-Control
Můžete snadno ověřit nastavení TTL objektů BLOB. Pomocí [vývojářských nástrojů](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/)prohlížeče otestujte, že `Cache-Control` objekt blob obsahuje záhlaví odpovědi. Můžete také použít nástroj, jako je [Wget](https://www.gnu.org/software/wget/), [Pošťák](https://www.getpostman.com/)nebo [Šumař](https://www.telerik.com/fiddler) prozkoumat záhlaví odpovědí.

## <a name="next-steps"></a>Další kroky
* [Přečtěte si, jak spravovat vypršení platnosti obsahu cloudové služby v Azure CDN](cdn-manage-expiration-of-cloud-service-content.md)
* [Další informace o konceptech ukládání do mezipaměti](cdn-how-caching-works.md)

