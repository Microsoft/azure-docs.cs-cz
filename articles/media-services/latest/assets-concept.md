---
title: Prostředky ve službě Azure Media Services | Dokumentace Microsoftu
description: Tento článek obsahuje vysvětlení, co jsou prostředky, a jak se používají Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 10/15/2018
ms.author: juliako
ms.openlocfilehash: 62cc4634a0f76b0562d5b3c1355a7442fc5cf989
ms.sourcegitcommit: c2c279cb2cbc0bc268b38fbd900f1bac2fd0e88f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/24/2018
ms.locfileid: "49985229"
---
# <a name="assets"></a>Prostředky

**Asset** obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory s titulky) a metadata o těchto souborech. Jakmile jsou digitální soubory nahrát do assetu, může použít ve službě Media Services, kódování a streamování pracovních postupů.

Prostředek je namapována na kontejner objektů blob ve službě [účtu služby Azure Storage](storage-account-concept.md) a soubory v prostředku se ukládají jako objekty BLOB bloku v tomto kontejneru. Můžete pracovat se soubory prostředků v kontejnerech pomocí sady SDK služby Storage klientů.

Azure Media Services podporuje úrovně objektu Blob, pokud používá účet pro obecné účely v2 (GPv2) úložiště. S účty GPv2 můžete přesunout soubory na studenou, nebo pohotovostní úložiště. Studeného úložiště je vhodný pro archivaci zdrojové soubory, pokud už nepotřebujete (například po jejich jsou kódovány.)

V Media Services v3 vstup úlohy vytvářet z prostředků nebo z adresy URL http (s). Pouze vytvořit asset, který slouží jako vstup pro úlohu, naleznete v tématu [vytvořit vstup úlohy z místního souboru](job-input-from-local-file-how-to.md).

Také, přečtěte si informace o [účty úložiště ve službě Media Services](storage-account-concept.md) a [transformace a úlohy](transform-concept.md).

## <a name="asset-definition"></a>Definice prostředků

Následující tabulka uvádí vlastnosti prostředku a umožňuje jejich definice.

|Název|Typ|Popis|
|---|---|---|
|id|řetězec|Plně kvalifikované ID prostředku pro prostředek.|
|jméno|řetězec|Název prostředku.|
|properties.alternateId |řetězec|Alternativní ID prostředku.|
|properties.assetId |řetězec|ID prostředku.|
|Properties.Container |řetězec|Název kontejneru objektů blob asset.|
|Properties.Created |řetězec|Datum vytvoření prostředku.|
|Properties.Description |řetězec|Popis prostředku.|
|properties.lastModified |řetězec|Datum majetku poslední změny.|
|properties.storageAccountName |řetězec|Název účtu úložiště.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Formát šifrování Asset. Jeden z žádné nebo MediaStorageEncryption.|
|type|řetězec|Typ prostředku.|

Kompletní definici, naleznete v tématu [prostředky](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Služba Media Services podporuje následující možnosti dotazu OData pro prostředky: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

Popis operátoru:

* EQ = rovno
* Ne = není rovno
* Ge = větší než nebo rovno
* Le = menší než nebo rovno
* Gt = je větší než
* Lt = menší než

### <a name="filteringordering"></a>Filtrování a řazení

Následující tabulka ukazuje, jak tyto možnosti může použít u vlastnosti prostředku: 

|Název|Filtr|Objednání|
|---|---|---|
|id|||
|jméno|Podporované platformy: Eq, Gt, Lt|Podporuje: vzestupným a sestupným|
|properties.alternateId |Podporuje: Eq||
|properties.assetId |Podporuje: Eq||
|Properties.Container |||
|Properties.Created|Podporované platformy: Eq, Gt, Lt| Podporované platformy: Vzestupným a sestupným|
|Properties.Description |||
|properties.lastModified |||
|properties.storageAccountName |||
|properties.storageEncryptionFormat | ||
|type|||

Následující příklad jazyka C# filtry na datum vytvoření:

```csharp
var odataQuery = new ODataQuery<Asset>("properties/created lt 2018-05-11T17:39:08.387Z");
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName, odataQuery);
```

### <a name="pagination"></a>Stránkování

Pro každý ze čtyř povoleno řazení je podporováno stránkování. V současné době je velikost stránky je 1000.

> [!TIP]
> Odkaz na další vždy používejte k vytvoření výčtu kolekce a není závislý na konkrétní stránce velikost.

Pokud odpovědi na dotaz obsahuje mnoho položek, tato služba vrátí "\@odata.nextLink" k získání další stránky výsledků. Tímto lze na stránku prostřednictvím úplná sada výsledků. Nelze konfigurovat velikost stránky. 

Pokud prostředky jsou vytvořeny nebo odstranili stránkování prostřednictvím kolekce, změny se projeví v navrácených výsledcích (pokud tyto změny jsou součástí kolekce, která se nestáhla.) 

Následující příklad jazyka C# ukazuje, jak zobrazit výčet prostřednictvím všechny prostředky v účtu.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

ZBÝVAJÍCÍ příklady naleznete v tématu [prostředky – seznam](https://docs.microsoft.com/rest/api/media/assets/list)

## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

K ochraně vašich prostředků v klidovém stavu, prostředky by se měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services:

|Možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services|Šifrování AES-256, klíče spravované službou Media Services|Podporované<sup>(1)</sup>|Nepodporuje<sup>(2)</sup>|
|[Šifrování služby Storage pro neaktivní uložená Data](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru nabízených službou Azure Storage, klíče spravované službou Azure nebo podle zákazníka|Podporováno|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené službou Azure storage, klíče spravované zákazníkem ve službě Key Vault|Nepodporuje se|Nepodporuje se|

<sup>1</sup> při Media Services podporuje zpracování obsahu v nezašifrované podobě nebo bez jakéhokoli typu šifrování, učiníte tak se nedoporučuje.

<sup>2</sup> v Media Services v3 šifrování úložiště (šifrování AES-256) je pouze podporována pro zpětné kompatibility při vaše prostředky se vytvořily pomocí Media Services v2. To znamená v3 spolupracuje s existující úložiště šifrované prostředky, ale nebude umožňovat vytváření nových.

## <a name="next-steps"></a>Další postup

[Streamování souboru](stream-files-dotnet-quickstart.md)
