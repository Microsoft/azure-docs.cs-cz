---
title: Prostředky ve službě Azure Media Services | Microsoft Docs
description: Tento článek vysvětluje, co jsou prostředky, a jak se používají službou Azure Media Services.
services: media-services
documentationcenter: ''
author: Juliako
manager: cfowler
editor: ''
ms.service: media-services
ms.workload: ''
ms.topic: article
ms.date: 03/19/2018
ms.author: juliako
ms.openlocfilehash: 61555eb6cca6995215ce43051abbda9aa43539ec
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284834"
---
# <a name="assets"></a>Prostředky

**Asset** obsahuje digitální soubory (včetně video, zvuk, obrázky, kolekci miniatur, textové stopy a soubory titulků) a metadata o těchto souborech. Po digitální soubory jsou odeslány do assetu, mohou být využívána ve službě Media Services kódování a vysílání datového proudu pracovních postupů.

Prostředek se mapuje na kontejner objektů blob ve [účet úložiště Azure](storage-account-concept.md) a soubory v prostředku jsou uloženy jako objekty BLOB bloku v tomto kontejneru. Můžete pracovat se soubory Asset v kontejnerech pomocí klienti SDK úložiště.

Azure Media Services podporuje Blob vrstev, pokud účet používá pro obecné účely v2 (GPv2) úložiště. S GPv2 můžete přesunout soubory do cool nebo studené úložiště. Studené úložiště je vhodný pro archivaci zdrojové soubory, když už nepotřebují (například po jejich byly zakódovány.)

Ve službě Media Services v3 lze vytvořit úlohu vstup z prostředků nebo z adresy URL http (s). Vytvoření asset, který lze použít jako vstup pro úlohu, naleznete v části [vytvořit úlohu vstupní z místního souboru](job-input-from-local-file-how-to.md).

Také, přečtěte si informace o [účty úložiště ve službě Media Services](storage-account-concept.md) a [úlohy a transformace](transform-concept.md).

## <a name="asset-definition"></a>Definování majetku.

V následující tabulce se zobrazují vlastnosti Assetu a dává jejich definice.

|Název|Typ|Popis|
|---|---|---|
|ID|řetězec|ID plně kvalifikovaný prostředků pro prostředek.|
|jméno|řetězec|Název prostředku.|
|properties.alternateId |řetězec|Alternativní ID prostředku.|
|properties.assetId |řetězec|ID prostředku.|
|Properties.Container |řetězec|Název kontejneru objektů blob asset.|
|Properties.Created |řetězec|Datum vytvoření prostředku.|
|Properties.Description |řetězec|Popis Asset.|
|properties.lastModified |řetězec|Datum prostředku poslední úpravy.|
|properties.storageAccountName |řetězec|Název účtu úložiště.|
|properties.storageEncryptionFormat |AssetStorageEncryptionFormat |Formát šifrování Asset. Jeden None nebo MediaStorageEncryption.|
|type|řetězec|Typ prostředku.|

Úplné definici naleznete v tématu [prostředky](https://docs.microsoft.com/rest/api/media/assets).

## <a name="filtering-ordering-paging"></a>Filtrování, řazení, stránkování

Služba Media Services podporuje následující možnosti dotazu OData pro prostředky: 

* $filter 
* $orderby 
* $top 
* $skiptoken 

### <a name="filteringordering"></a>Filtrování řazení

Následující tabulka ukazuje, jak tyto možnosti mohou být použity u vlastnosti prostředku: 

|Název|Filtr|Pořadí|
|---|---|---|
|ID|Podporuje:<br/>Rovná se<br/>Více než<br/>Menší než|Podporuje:<br/>Vzestupně<br/>Sestupně|
|jméno|||
|properties.alternateId |Podporuje:<br/>Rovná se||
|properties.assetId |Podporuje:<br/>Rovná se||
|Properties.Container |||
|Properties.Created|Podporuje:<br/>Rovná se<br/>Více než<br/>Menší než|Podporuje:<br/>Vzestupně<br/>Sestupně|
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

Stránkování se podporuje pro každé čtyři povoleno řazení. 

Pokud odpověď na dotaz obsahuje mnoho (aktuálně přes 1000) položky, službu vrátí "\@odata.nextLink" vlastnost k získání další stránky výsledků. Tímto lze na stránku prostřednictvím celou sadu výsledků. Velikost stránky není konfigurovatelná uživatelem. 

Pokud prostředky jsou vytvořené nebo odstraněné při stránkování prostřednictvím kolekce, změny se projeví do vrácených výsledků (pokud jsou takové změny v součástí kolekce, která nebyla stažena.) 

Následující příklad jazyka C# ukazuje, jak vytvořit výčet prostřednictvím všechny prostředky v účtu.

```csharp
var firstPage = await MediaServicesArmClient.Assets.ListAsync(CustomerResourceGroup, CustomerAccountName);

var currentPage = firstPage;
while (currentPage.NextPageLink != null)
{
    currentPage = await MediaServicesArmClient.Assets.ListNextAsync(currentPage.NextPageLink);
}
```

Příklady REST naleznete v tématu [prostředky – seznam](https://docs.microsoft.com/rest/api/media/assets/list)


## <a name="storage-side-encryption"></a>Šifrování na straně úložiště

Pokud chcete ochránit vaše prostředky v klidovém stavu, prostředky by měla šifrovat pomocí šifrování na straně úložiště. Následující tabulka ukazuje, jak funguje šifrování na straně úložiště ve službě Media Services:

|možnost šifrování|Popis|Media Services v2|Media Services v3|
|---|---|---|---|
|Šifrování úložiště služby Media Services|AES 256 šifrování klíče spravované službou Media Services|Podporované<sup>(1)</sup>|Nepodporuje<sup>(2)</sup>|
|[Šifrování služby úložiště pro Data v klidovém stavu](https://docs.microsoft.com/azure/storage/common/storage-service-encryption)|Šifrování na straně serveru, které nabízí Azure Storage, klíč spravovat Azure nebo zákazníka|Podporováno|Podporováno|
|[Šifrování na straně klienta úložiště](https://docs.microsoft.com/azure/storage/common/storage-client-side-encryption)|Šifrování na straně klienta nabízené úložiště Azure, klíč spravovaný zákazníkem v Key Vault|Nepodporuje se|Nepodporuje se|

<sup>1</sup> při Media Services podporuje zpracování obsahu v nešifrované podobě/bez jakoukoli formu šifrování, to tak se nedoporučuje.

<sup>2</sup> v3 Media Services, úložiště šifrování (AES 256 šifrování) je pouze podporována z důvodů zpětné kompatibility při vaše prostředky, které se vytvořily s v2 Media Services. Znamená v3 funguje s existující úložiště šifrovaný prostředky, ale neumožní vytváření nových databází.

## <a name="next-steps"></a>Další postup

> [!div class="nextstepaction"]
> [Streamování souboru](stream-files-dotnet-quickstart.md)
