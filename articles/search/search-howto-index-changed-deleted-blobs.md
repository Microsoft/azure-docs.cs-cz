---
title: Změněné a odstraněné objekty blob
titleSuffix: Azure Cognitive Search
description: Po sestavení počátečního indexu vyhledávání, které importuje ze služby Azure Blob Storage, může následné indexování vyzvednutí jenom těch objektů blob, které se změnily nebo odstranily. Tento článek popisuje podrobnosti.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 01/29/2021
ms.openlocfilehash: 79d5583f8c9e562a0d21a91c210aa6259472661d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100383530"
---
# <a name="change-and-deletion-detection-in-blob-indexing-azure-cognitive-search"></a>Detekce změn a odstranění v indexování objektů BLOB (Azure Kognitivní hledání)

Po vytvoření indexu počátečního vyhledávání můžete chtít, aby další úlohy indexeru vybraly jenom nové a změněné dokumenty. Pro hledání obsahu, který pochází ze služby Azure Blob Storage, se detekce změny provede automaticky při použití plánu k aktivaci indexování. Ve výchozím nastavení služba přeindexuje jenom změněné objekty blob, jak Určuje `LastModified` časové razítko objektu BLOB. Na rozdíl od jiných zdrojů dat podporovaných indexery vyhledávání mají objekty blob vždycky časové razítko, což eliminuje nutnost ručního nastavení zásad zjišťování změn.

I když je zjišťování změn dané, zjišťování odstranění není. Pokud chcete zjistit odstraněné dokumenty, ujistěte se, že používáte přístup "obnovitelné odstranění". Pokud dojde k pravému odstranění objektů blob, odpovídající dokumenty nebudou odebrány z indexu vyhledávání.

Existují dva způsoby, jak implementovat postup obnovitelného odstranění:

+ Předběžné odstranění nativního objektu BLOB (Preview), popsané dál
+ [Obnovitelné odstranění pomocí vlastních metadat](#soft-delete-using-custom-metadata)

## <a name="native-blob-soft-delete-preview"></a>Obnovitelné odstranění nativního objektu BLOB (Preview)

Pro tento přístup k detekci odstranění Kognitivní hledání závisí na funkci [nativního odstranění objektů BLOB](../storage/blobs/soft-delete-blob-overview.md) v úložišti objektů BLOB v Azure, aby bylo možné zjistit, zda objekty blob přešly do nepodmíněného odstraněného stavu. Pokud jsou v tomto stavu objekty blob zjištěny, indexer vyhledávání tyto informace použije k odebrání odpovídajícího dokumentu z indexu.

> [!IMPORTANT]
> Podpora pro nativní odstranění objektu BLOB je ve verzi Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2020-06-30-Preview](./search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

### <a name="prerequisites"></a>Předpoklady

+ [Povolí obnovitelné odstranění pro objekty blob](../storage/blobs/soft-delete-blob-enable.md).
+ Objekty blob musí být v kontejneru úložiště objektů BLOB v Azure. Zásada podmíněného odstranění Kognitivní hledání Native BLOB není pro objekty BLOB z Azure Data Lake Storage Gen2 podporovaná.
+ Klíče dokumentů pro dokumenty v indexu musí být namapovány buď na vlastnost objektu blob, nebo na metadata objektu BLOB.
+ `api-version=2020-06-30-Preview`K nakonfigurování podpory pro obnovitelné odstranění je nutné použít verzi preview REST API ().

### <a name="how-to-configure-deletion-detection-using-native-soft-delete"></a>Jak nakonfigurovat detekci odstranění pomocí nativního obnovitelného odstranění

1. Když v BLOB Storage povolíte obnovitelné odstranění, nastavte zásady uchovávání na hodnotu, která je mnohem vyšší než plán intervalu indexeru. Tímto způsobem, pokud dojde k potížím s indexerem nebo pokud máte velký počet dokumentů k indexování, je dostatek času, aby indexer mohl nakonec zpracovat obnovitelné odstraněné objekty blob. Indexery Azure Kognitivní hledání odstraní jenom dokument z indexu, pokud je objekt BLOB zpracovává, když je ve stavu, kdy je odstraněný.

1. V Kognitivní hledání nastavte pro zdroj dat nativní zásady vyhledávání pro tiché odstranění objektů BLOB. Příklad najdete níže. Vzhledem k tomu, že je tato funkce ve verzi Preview, musíte použít REST API verze Preview.

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30-Preview
    Content-Type: application/json
    api-key: [admin key]
    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.NativeBlobSoftDeleteDeletionDetectionPolicy"
        }
    }
    ```

1. [Spusťte indexer](/rest/api/searchservice/run-indexer) nebo nastavte indexer tak, aby běžel [podle plánu](search-howto-schedule-indexers.md). Pokud indexer běží a zpracovává objekt BLOB se stavem obnovitelného odstranění, bude z indexu odebrán odpovídající hledaný dokument.

### <a name="reindexing-undeleted-blobs-using-native-soft-delete-policies"></a>Přeindexování neodstraněných objektů BLOB (pomocí nativních zásad obnovitelného odstranění)

Pokud obnovujete měkký odstraněný objekt BLOB v úložišti objektů blob, indexer ho nikdy znovu neindexuje. Důvodem je, že indexer používá `LastModified` časové razítko objektu BLOB k určení, zda je indexování nutné. Když se neodstraní neupravený objekt blob, jeho `LastModified` časové razítko se neaktualizuje, takže pokud indexer už zpracoval objekty BLOB s novějšími `LastModified` časovými razítky, nebude se znovu indexovat neodstraněného objektu BLOB. 

Chcete-li se ujistit, že je znovu indexován neodstraněný objekt blob, bude nutné aktualizovat `LastModified` časové razítko objektu BLOB. Jedním ze způsobů, jak to provést, je uložení metadat tohoto objektu BLOB. Nemusíte měnit metadata, ale pokud metadata znovu uložíte, aktualizuje se `LastModified` časové razítko objektu BLOB tak, aby indexer věděl, že ho má vybrat.

## <a name="soft-delete-using-custom-metadata"></a>Obnovitelné odstranění pomocí vlastních metadat

Tato metoda používá metadata objektu BLOB k určení, zda by měl být dokument hledání odebrán z indexu. Tato metoda vyžaduje dvě samostatné akce, odstranění vyhledávacího dokumentu z indexu a za ním i odstranění objektu BLOB v Azure Storage.

Existují kroky, které je potřeba provést v úložišti objektů BLOB a Kognitivní hledání, ale neexistují žádné další závislosti funkcí. Tato funkce je podporovaná ve všeobecně dostupných rozhraních API.

1. Přidejte do objektu BLOB vlastní dvojici klíč-hodnota metadat, abyste označili Azure Kognitivní hledání, že se logicky odstraní.

1. Nakonfigurujte zásady detekce sloupce tichého odstranění ve zdroji dat. Například následující zásady považují objekt blob, který se má odstranit, pokud má vlastnost metadata `IsDeleted` s hodnotou `true` :

    ```http
    PUT https://[service name].search.windows.net/datasources/blob-datasource?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
        "name" : "blob-datasource",
        "type" : "azureblob",
        "credentials" : { "connectionString" : "<your storage connection string>" },
        "container" : { "name" : "my-container", "query" : null },
        "dataDeletionDetectionPolicy" : {
            "@odata.type" :"#Microsoft.Azure.Search.SoftDeleteColumnDeletionDetectionPolicy",
            "softDeleteColumnName" : "IsDeleted",
            "softDeleteMarkerValue" : "true"
        }
    }
    ```

1. Jakmile indexer zpracuje objekt BLOB a odstraní ho z indexu, můžete tento objekt BLOB odstranit v úložišti objektů BLOB v Azure.

### <a name="reindexing-undeleted-blobs-using-custom-metadata"></a>Přeindexování neodstraněných objektů BLOB (pomocí vlastních metadat)

Jakmile indexer zpracuje odstraněný objekt BLOB a odstraní odpovídající hledaný dokument z indexu, nebude ho znovu navštěvovat, pokud ho později obnovíte, pokud `LastModified` je časové razítko objektu BLOB starší než poslední spuštění indexeru.

Pokud chcete tento dokument znovu indexovat, změňte `"softDeleteMarkerValue" : "false"` pro tento objekt BLOB a znovu spusťte indexer.

## <a name="next-steps"></a>Další kroky

+ [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
+ [Jak nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md)
+ [Přehled indexování objektů BLOB](search-blob-storage-integration.md)