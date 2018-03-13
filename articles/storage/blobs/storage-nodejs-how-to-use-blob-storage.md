---
title: "Používání úložiště Blob z Node.js | Microsoft Docs"
description: "Ukládejte nestrukturovaná data v cloudu pomocí Azure Blob Storage (úložiště objektů)."
services: storage
documentationcenter: nodejs
author: tamram
manager: timlt
editor: tysonn
ms.assetid: 8b0df222-1ca8-4967-8248-6d6d720947b8
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 12/08/2016
ms.author: tamram
ms.openlocfilehash: 52f1f1543fe0ef15cf71d2cf1f9a8bfeaae8933f
ms.sourcegitcommit: 059dae3d8a0e716adc95ad2296843a45745a415d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/09/2018
---
# <a name="how-to-use-blob-storage-from-nodejs"></a>Používání úložiště Blob z Node.js
[!INCLUDE [storage-selector-blob-include](../../../includes/storage-selector-blob-include.md)]

[!INCLUDE [storage-check-out-samples-all](../../../includes/storage-check-out-samples-all.md)]

## <a name="overview"></a>Přehled
Tento článek ukazuje, jak provádět běžné scénáře s využitím úložiště objektů Blob. Ukázky jsou zapsány pomocí rozhraní API Node.js. Pokryté scénáře zahrnují nahrát, seznam, stažení a odstranění objektů BLOB.

[!INCLUDE [storage-blob-concepts-include](../../../includes/storage-blob-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../../includes/storage-create-account-include.md)]

## <a name="create-a-nodejs-application"></a>Vytvoření aplikace Node.js
Pokyny o tom, jak vytvořit aplikaci Node.js najdete v tématu [vytvoření webové aplikace Node.js ve službě Azure App Service](../../app-service/app-service-web-get-started-nodejs.md), [sestavení a nasazení aplikace Node.js ve službě Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md) – pomocí prostředí Windows PowerShell nebo [sestavení a nasazení webové aplikace Node.js do Azure pomocí Web Matrix](https://www.microsoft.com/web/webmatrix/).

## <a name="configure-your-application-to-access-storage"></a>Konfigurace aplikace pro přístup k úložišti
Pokud chcete používat úložiště Azure, musíte sady SDK úložiště Azure pro platformu Node.js, která obsahuje sadu knihoven pohodlí, které komunikují s služby REST úložiště.

### <a name="use-node-package-manager-npm-to-obtain-the-package"></a>Získat balíček pomocí uzlu balíček správce (NPM)
1. Pomocí rozhraní příkazového řádku, jako například **prostředí PowerShell** (Windows), **Terminálové** (Mac), nebo **Bash** (Unix), přejděte na složku, kde jste vytvořili ukázkové aplikaci.
2. Typ **npm nainstalujte azure-storage** v příkazovém okně. Výstup z tohoto příkazu je podobná následující příklad kódu.

        azure-storage@0.5.0 node_modules\azure-storage
        +-- extend@1.2.1
        +-- xmlbuilder@0.4.3
        +-- mime@1.2.11
        +-- node-uuid@1.4.3
        +-- validator@3.22.2
        +-- underscore@1.4.4
        +-- readable-stream@1.0.33 (string_decoder@0.10.31, isarray@0.0.1, inherits@2.0.1, core-util-is@1.0.1)
        +-- xml2js@0.2.7 (sax@0.5.2)
        +-- request@2.57.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, oauth-sign@0.8.0, tunnel-agent@0.4.1, isstream@0.1.2, json-stringify-safe@5.0.1, bl@0.9.4, combined-stream@1.0.5, qs@3.1.0, mime-types@2.0.14, form-data@0.2.0, http-signature@0.11.0, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
3. Můžete ručně spustit **ls** příkazu ověřte, že **uzlu\_moduly** složka byla vytvořena. V této složce najít **azure-storage** balíček, který obsahuje knihovny, které potřebujete získat přístup k úložišti.

### <a name="import-the-package"></a>Import balíčku
Pomocí programu Poznámkový blok nebo jiném textovém editoru, přidejte následující do horní části **server.js** souboru aplikace, které máte v úmyslu používat úložiště:

```nodejs
var azure = require('azure-storage');
```

## <a name="set-up-an-azure-storage-connection"></a>Nastavit připojení k Azure Storage
Modul Azure, bude číst proměnné prostředí `AZURE_STORAGE_ACCOUNT` a `AZURE_STORAGE_ACCESS_KEY`, nebo `AZURE_STORAGE_CONNECTION_STRING`, informace požadované pro připojení k účtu úložiště Azure. Pokud nejsou nastavené těchto proměnných prostředí, musíte zadat informace o účtu při volání metody **createBlobService**.

## <a name="create-a-container"></a>Vytvoření kontejneru
**BlobService** objekt vám umožňuje spolupracovat s kontejnery a objekty BLOB. Následující kód vytvoří **BlobService** objektu. Přidejte následující v horní části **server.js**:

```nodejs
var blobSvc = azure.createBlobService();
```

> [!NOTE]
> Objekt blob může anonymně přístup pomocí **createBlobServiceAnonymous** a poskytnutím adresa hostitele. Například použít `var blobSvc = azure.createBlobServiceAnonymous('https://myblob.blob.core.windows.net/');`.
>
>

[!INCLUDE [storage-container-naming-rules-include](../../../includes/storage-container-naming-rules-include.md)]

Chcete-li vytvořit nový kontejner, použijte **createContainerIfNotExists**. Následující příklad kódu vytvoří nový kontejner s názvem 'můj_kontejner':

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', function(error, result, response){
    if(!error){
      // Container exists and is private
    }
});
```

Pokud je nově vytvořený kontejner, `result.created` hodnotu true. Pokud již existuje kontejner, `result.created` je false. `response`obsahuje informace o operaci, včetně informací o ETag pro příslušný kontejner.

### <a name="container-security"></a>Zabezpečení kontejneru
Ve výchozím nastavení nové kontejnery jsou soukromá a nemohou být získat anonymní přístup. Chcete-li zveřejnit kontejner, aby ho může anonymně přístup, můžete nastavit kontejneru přístup na úrovni **blob** nebo **kontejneru**.

* **objekt BLOB** -umožňuje anonymní přístup pro čtení obsahu objektu blob a metadat v tomto kontejneru, ale není metadata kontejneru například výpis všech objektů BLOB do kontejneru
* **kontejner** -umožňuje anonymní přístup pro čtení k obsah objektu blob a metadat, jakož i metadata kontejneru

Následující příklad kódu ukazuje nastavení přístupu na úrovni **blob**:

```nodejs
blobSvc.createContainerIfNotExists('mycontainer', {publicAccessLevel : 'blob'}, function(error, result, response){
    if(!error){
      // Container exists and allows
      // anonymous read access to blob
      // content and metadata within this container
    }
});
```

Alternativně můžete upravit úroveň přístupu tohoto kontejneru pomocí **setContainerAcl** k určení úrovně přístupu. Následující příklad kódu změní úroveň přístupu do kontejneru:

```nodejs
blobSvc.setContainerAcl('mycontainer', null /* signedIdentifiers */, {publicAccessLevel : 'container'} /* publicAccessLevel*/, function(error, result, response){
  if(!error){
    // Container access level set to 'container'
  }
});
```

Výsledek obsahuje informace o operaci, včetně aktuální **značka ETag** kontejneru.

### <a name="filters"></a>Filtry
Můžete provést volitelný filtrování operací u operace provedené pomocí **BlobService**. Filtrování operací může zahrnovat protokolování, automatickým opakovaným pokusem o atd. Filtry jsou objekty, které implementovat metodu s podpisem:

```nodejs
function handle (requestOptions, next)
```

Až to předzpracování na žádost o možnostech, metoda potřebuje volat tlačítko Další"předání zpětné volání podpisem následující:

```nodejs
function (returnObject, finalCallback, next)
```

V této zpětného volání a po zpracování returnObject (odpovědi požadavek na server) zpětné volání je potřeba buď vyvolat další, pokud existuje pokračovat ve zpracovávání ostatní filtry nebo jednoduše vyvolat finalCallback na konec volání služby.

Dva filtry, které implementují logiku opakovaných pokusů, které jsou součástí sady Azure SDK pro Node.js, **ExponentialRetryPolicyFilter** a **LinearRetryPolicyFilter**. Vytvoří následující **BlobService** objekt, který používá **ExponentialRetryPolicyFilter**:

```nodejs
var retryOperations = new azure.ExponentialRetryPolicyFilter();
var blobSvc = azure.createBlobService().withFilter(retryOperations);
```

## <a name="upload-a-blob-into-a-container"></a>Nahrání objektu blob do kontejneru
Existují tři typy objektů blob: objekty BLOB bloků, objekty BLOB stránky a doplňovacích objektů BLOB. Objekty BLOB bloku povolit, že vám umožní nahrát efektivně velkých objemů dat. Append – objekty BLOB jsou optimalizované pro doplňovací operace. Objekty BLOB stránky jsou optimalizované pro operace čtení a zápisu. Další informace najdete v tématu [Principy objekty BLOB bloku a doplňovacích objektů BLOB, objekty BLOB stránky](http://msdn.microsoft.com/library/azure/ee691964.aspx).

### <a name="block-blobs"></a>Objekty blob bloku
Odeslání dat do objekt blob bloku, použijte tento příkaz:

* **createBlockBlobFromLocalFile** – vytvoří nový objekt blob bloku a odesílá obsah souboru
* **createBlockBlobFromStream** – vytvoří nový objekt blob bloku a odesílá obsah datového proudu
* **createBlockBlobFromText** – vytvoří nový objekt blob bloku a odesílá obsah řetězce
* **createWriteStreamToBlockBlob** -poskytuje datový proud zápisu do objektu blob bloku

Následující příklad kódu odešle obsah **test.txt** soubor do **můj_objekt_blob**.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

`result` Vrácený tyto metody obsahuje informace o operaci, jako jsou například **značka ETag** objektu blob.

### <a name="append-blobs"></a>Doplňovací objekty BLOB
Odeslání dat do nový doplňovací objekt blob, použijte tento příkaz:

* **createAppendBlobFromLocalFile** – vytvoří nový doplňovací objekt blob a odesílá obsah souboru
* **createAppendBlobFromStream** – vytvoří nový doplňovací objekt blob a odesílá obsah datového proudu
* **createAppendBlobFromText** – vytvoří nový doplňovací objekt blob a odesílá obsah řetězce
* **createWriteStreamToNewAppendBlob** – vytvoří nový doplňovací objekt blob a pak poskytuje datového proudu pro zápis do

Následující příklad kódu odešle obsah **test.txt** soubor do **myappendblob**.

```nodejs
blobSvc.createAppendBlobFromLocalFile('mycontainer', 'myappendblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

Připojit k existující doplňovací objekt blob bloku, použijte tento příkaz:

* **appendFromLocalFile** -ke stávající doplňovací objekt blob připojit obsah souboru
* **appendFromStream** -ke stávající doplňovací objekt blob připojit obsah datového proudu
* **appendFromText** -ke stávající doplňovací objekt blob připojit obsah řetězce
* **appendBlockFromStream** -ke stávající doplňovací objekt blob připojit obsah datového proudu
* **appendBlockFromText** -ke stávající doplňovací objekt blob připojit obsah řetězce

> [!NOTE]
> appendFromXXX rozhraní API budou dělat některé selhání rychle, aby se zabránilo zbytečným serveru volání ověření klienta. appendBlockFromXXX nebude.
>
>

Následující příklad kódu odešle obsah **test.txt** soubor do **myappendblob**.

```nodejs
blobSvc.appendFromText('mycontainer', 'myappendblob', 'text to be appended', function(error, result, response){
  if(!error){
    // text appended
  }
});
```

### <a name="page-blobs"></a>Objekty blob stránky
Nahrát data do objektů blob stránky, použijte tento příkaz:

* **createPageBlob** -vytvoří nový objekt blob stránky konkrétní délky
* **createPageBlobFromLocalFile** – vytvoří nový objekt blob stránky a odesílá obsah souboru
* **createPageBlobFromStream** – vytvoří nový objekt blob stránky a odesílá obsah datového proudu
* **createWriteStreamToExistingPageBlob** -poskytuje zápisu datového proudu pro existující objekt blob stránky
* **createWriteStreamToNewPageBlob** – vytvoří nový objekt blob stránky a pak poskytuje datového proudu pro zápis do

Následující příklad kódu odešle obsah **test.txt** soubor do **mypageblob**.

```nodejs
blobSvc.createPageBlobFromLocalFile('mycontainer', 'mypageblob', 'test.txt', function(error, result, response){
  if(!error){
    // file uploaded
  }
});
```

> [!NOTE]
> Objekty BLOB stránky obsahovat 512 bajtů stránky. Obdržíte chybu při odesílání dat s velikostí, která není násobkem 512.
>
>

## <a name="list-the-blobs-in-a-container"></a>Zobrazí seznam objektů blob v kontejneru
K zobrazení seznamu objektů BLOB v kontejneru, použijte **listBlobsSegmented** metoda. Pokud chcete vrátit objektů BLOB s konkrétní předponou, použijte **listBlobsSegmentedWithPrefix**.

```nodejs
blobSvc.listBlobsSegmented('mycontainer', null, function(error, result, response){
  if(!error){
      // result.entries contains the entries
      // If not all blobs were returned, result.continuationToken has the continuation token.
  }
});
```

`result` Obsahuje `entries` kolekce, která je pole objektů, které popisují jednotlivých objektů blob. Pokud nemůže být vrácen všech objektů BLOB, `result` také poskytuje `continuationToken`, které můžete použít jako druhý parametr pro načtení další záznamy.

## <a name="download-blobs"></a>Stáhnout objekty blob
Chcete-li data z objektu blob, použijte tento příkaz:

* **getBlobToLocalFile** -zapíše obsahu objektu blob do souboru
* **getBlobToStream** -zapíše do datového proudu obsahu objektu blob
* **getBlobToText** -zapíše obsahu objektu blob na řetězec
* **createReadStream** -poskytuje datový proud čtení z objektu blob

Následující příklad kódu ukazuje, jak pomocí **getBlobToStream** stáhnout obsah **můj_objekt_blob** objektů blob a uložte ho do **výstup.txt** souboru pomocí datového proudu:

```nodejs
var fs = require('fs');
blobSvc.getBlobToStream('mycontainer', 'myblob', fs.createWriteStream('output.txt'), function(error, result, response){
  if(!error){
    // blob retrieved
  }
});
```

`result` Obsahuje informace o objektu blob, včetně **značka ETag** informace.

## <a name="delete-a-blob"></a>Odstranění objektu blob
Nakonec se odstranit objekt blob, zavolejte **deleteBlob**. Následující příklad kódu odstraní objektů blob s názvem **můj_objekt_blob**.

```nodejs
blobSvc.deleteBlob(containerName, 'myblob', function(error, response){
  if(!error){
    // Blob has been deleted
  }
});
```

## <a name="concurrent-access"></a>Souběžný přístup
Chcete-li podporovat souběžný přístup do objektu blob z více klientů nebo více instancí procesu, můžete použít **značky etag binárním rozsáhlým** nebo **zapůjčení**.

* **Značka Etag** -poskytuje způsob, jak zjistit, že objektu blob nebo kontejneru byl změněn jiným procesem
* **Zapůjčení** -poskytuje způsob, jak získat exkluzivní, obnovitelné, zápisu nebo odstranit přístupu do objektu blob pro v časovém intervalu

### <a name="etag"></a>ETag
Značky etag použití binárním rozsáhlým Pokud je potřeba povolit více klientů nebo instance k zápisu do bloku, objektů Blob nebo stránky Blob současně. Značky ETag umožňuje určit, pokud kontejner nebo objekt blob byla změněna od začátku čtení nebo vytvořili, která umožňuje zabránit přepsání změny potvrzeny jiný klienta nebo proces.

Můžete nastavit podmínky, značka ETag pomocí volitelného `options.accessConditions` parametr. Následující kód například pouze nahrávání **test.txt** soubor, pokud objekt blob již existuje a má hodnotu ETag obsahoval podle `etagToMatch`.

```nodejs
blobSvc.createBlockBlobFromLocalFile('mycontainer', 'myblob', 'test.txt', { accessConditions: { EtagMatch: etagToMatch} }, function(error, result, response){
    if(!error){
    // file uploaded
  }
});
```

Při použití značky etag binárním rozsáhlým, je obecný vzor:

1. Značky ETag získáte v důsledku create, seznamu nebo operaci get.
2. Provedení akce, kontrola, hodnota ETag nebyl změněn.

Pokud byla hodnota změněna, znamená to, že jiný klienta nebo instance upraveny objektů blob nebo kontejneru vzhledem k tomu, že jste získali hodnota značky ETag.

### <a name="lease"></a>Zapůjčení
Nové zapůjčení můžete získat pomocí **acquireLease** metodu s uvedením objektů blob nebo kontejner, kterou chcete získat zapůjčení na. Například následující kód získá zapůjčení na **můj_objekt_blob**.

```nodejs
blobSvc.acquireLease('mycontainer', 'myblob', function(error, result, response){
  if(!error) {
    console.log('leaseId: ' + result.id);
  }
});
```

Dalších operacích na **můj_objekt_blob** musíte zadat `options.leaseId` parametr. Zapůjčení ID se vrátí jako `result.id` z **acquireLease**.

> [!NOTE]
> Ve výchozím nastavení je neomezenou dobu trvání zapůjčení. Můžete určit jiný nekonečné dobu trvání (mezi 15 a 60 sekund) pomocí `options.leaseDuration` parametr.
>
>

Chcete-li odebrat zapůjčení, použijte **releaseLease**. Chcete-li rozdělit zapůjčení, ale jiným uživatelům zabránit v získání nového zapůjčení, dokud nevyprší platnost původního, použijte **breakLease**.

## <a name="work-with-shared-access-signatures"></a>Práce s podpisy sdíleného přístupu
Sdílené přístupové podpisy (SAS) jsou zabezpečené způsob, jak poskytnout podrobné přístup k objektům BLOB a kontejnerů, bez zadání názvu účtu úložiště nebo klíče. Sdílené přístupové podpisy jsou často používá k poskytování omezený přístup k datům, například povolení mobilních aplikací pro přístup k objektům BLOB.

> [!NOTE]
> Zatímco můžete také povolit anonymní přístup k objektům BLOB, sdílené přístupové podpisy umožňují zadejte více řízený přístup, protože musíte vygenerovat SAS.
>
>

Sdílené přístupové podpisy pomocí generuje důvěryhodné aplikace, například cloudové služby **generateSharedAccessSignature** z **BlobService**a poskytuje ji nedůvěryhodné nebo částečně důvěryhodné aplikaci, například mobilní aplikace. Sdílené přístupové podpisy se generují pomocí zásad, která popisuje spuštění a koncovým datem, během které sdílené přístupové podpisy jsou platné, a také udělená úroveň přístupu držitele podpisy sdíleného přístupu.

Následující příklad kódu vytvoří nová zásada sdíleného přístupu, která umožňuje držitele podpisy sdíleného přístupu k provedení operací čtení na **můj_objekt_blob** objektů blob a jeho platnost vyprší 100 minut po čase jeho vytvoření.

```nodejs
var startDate = new Date();
var expiryDate = new Date(startDate);
expiryDate.setMinutes(startDate.getMinutes() + 100);
startDate.setMinutes(startDate.getMinutes() - 100);

var sharedAccessPolicy = {
  AccessPolicy: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
};

var blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', 'myblob', sharedAccessPolicy);
var host = blobSvc.host;
```

Všimněte si, že informace o hostiteli je třeba zadat také, jako je povinný, pokud držitele podpisy sdíleného přístupu pokusí o přístup k kontejneru.

Klientská aplikace pak používá sdílené přístupové podpisy s **BlobServiceWithSAS** k provedení operace u objektu blob. Následující získá informace **můj_objekt_blob**.

```nodejs
var sharedBlobSvc = azure.createBlobServiceWithSas(host, blobSAS);
sharedBlobSvc.getBlobProperties('mycontainer', 'myblob', function (error, result, response) {
  if(!error) {
    // retrieved info
  }
});
```

Vzhledem k tomu, že sdílené přístupové podpisy byly vygenerovány s přístupem jen pro čtení, pokud je k pokusu změnit objekt blob, bude vrácena chyba.

### <a name="access-control-lists"></a>Seznamy řízení přístupu
Seznam řízení přístupu (ACL) můžete také nastavit zásady přístupu pro SAS. To je užitečné, pokud chcete povolit více klientům přístup k kontejner, ale poskytnutí zásad, jiný přístup pro každého klienta.

Seznam ACL je implementovaná pomocí pole zásady přístupu s ID spojené s každou zásadu. Následující příklad kódu definuje dvě zásady, jeden pro "uživatel1" a jeden pro, uživatel2":

```nodejs
var sharedAccessPolicy = {
  user1: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.READ,
    Start: startDate,
    Expiry: expiryDate
  },
  user2: {
    Permissions: azure.BlobUtilities.SharedAccessPermissions.WRITE,
    Start: startDate,
    Expiry: expiryDate
  }
};
```

Následující příklad kódu získá aktuální seznam ACL pro **můj_kontejner**a potom se přidají nové zásady pomocí **setBlobAcl**. Tento přístup umožňuje:

```nodejs
var extend = require('extend');
blobSvc.getBlobAcl('mycontainer', function(error, result, response) {
  if(!error){
    var newSignedIdentifiers = extend(true, result.signedIdentifiers, sharedAccessPolicy);
    blobSvc.setBlobAcl('mycontainer', newSignedIdentifiers, function(error, result, response){
      if(!error){
        // ACL set
      }
    });
  }
});
```

Jakmile je nastavená seznamu ACL, potom můžete vytvořit sdílené přístupové podpisy, na základě ID pro zásadu. Následující příklad kódu vytvoří nové sdílené přístupové podpisy pro, uživatel2":

```nodejs
blobSAS = blobSvc.generateSharedAccessSignature('mycontainer', { Id: 'user2' });
```

## <a name="next-steps"></a>Další postup
Další informace najdete v následujících zdrojích informací.

* [Úložiště azure SDK pro uzel referenční dokumentace rozhraní API] [Úložiště azure SDK pro uzel referenční dokumentace rozhraní API]  
* [Blog týmu azure Storage] [Blog týmu azure Storage]  
* [Azure SDK úložiště pro uzel] [ Azure Storage SDK for Node] úložišti na Githubu  
* [Středisko pro vývojáře Node.js](https://azure.microsoft.com/develop/nodejs/)  
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json)  

[Azure Storage SDK for Node]: https://github.com/Azure/azure-storage-node  

[Build and deploy a Node.js web app to Azure using Web Matrix]: https://www.microsoft.com/web/webmatrix/  
[Using the REST API]: http://msdn.microsoft.com/library/azure/hh264518.aspx  
[Azure portal]: https://portal.azure.com  
[Vytvoření a nasazení aplikace Node.js do Azure Cloud Service](../../cloud-services/cloud-services-nodejs-develop-deploy-app.md)  
[Blog týmu azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/  
[Úložiště azure SDK pro uzel referenční dokumentace rozhraní API]: http://dl.windowsazure.com/nodestoragedocs/index.html  
