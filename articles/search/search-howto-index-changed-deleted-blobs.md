---
title: Změněné a odstraněné objekty blob
titleSuffix: Azure Cognitive Search
description: Po sestavení počátečního indexu vyhledávání, které importuje ze služby Azure Blob Storage, může následné indexování vyzvednutí jenom těch objektů blob, které se změnily nebo odstranily. Tento článek popisuje podrobnosti.
manager: nitinme
author: MarkHeff
ms.author: maheff
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 09/25/2020
ms.openlocfilehash: 210a4a352f5130bc67d70f47bdf6305c5a992282
ms.sourcegitcommit: dc68a2c11bae2e9d57310d39fbed76628233fd7f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/28/2020
ms.locfileid: "91403771"
---
# <a name="how-to-set-up-change-and-deletion-detection-for-blobs-in-azure-cognitive-search-indexing"></a>Jak nastavit detekci změn a odstraňování objektů BLOB v Azure Kognitivní hledání indexování

Po vytvoření indexu počátečního vyhledávání můžete chtít nakonfigurovat další úlohy indexeru, aby se vybraly jenom ty dokumenty, které byly vytvořené nebo odstraněné od počátečního spuštění. Pro hledání obsahu, který pochází ze služby Azure Blob Storage, se detekce změny provede automaticky při použití plánu k aktivaci indexování. Ve výchozím nastavení služba přeindexuje jenom změněné objekty blob, jak Určuje `LastModified` časové razítko objektu BLOB. Na rozdíl od jiných zdrojů dat podporovaných indexery vyhledávání mají objekty blob vždycky časové razítko, což eliminuje nutnost ručního nastavení zásad zjišťování změn.

I když je zjišťování změn dané, zjišťování odstranění není. Pokud chcete zjistit odstraněné dokumenty, ujistěte se, že používáte přístup "obnovitelné odstranění". Pokud dojde k pravému odstranění objektů blob, odpovídající dokumenty nebudou odebrány z indexu vyhledávání.

Existují dva způsoby, jak implementovat postup obnovitelného odstranění. Obě jsou popsány níže.

## <a name="native-blob-soft-delete-preview"></a>Obnovitelné odstranění nativního objektu BLOB (Preview)

> [!IMPORTANT]
> Podpora pro nativní odstranění objektu BLOB je ve verzi Preview. Funkce Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro produkční úlohy. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Tato funkce poskytuje [REST API verze 2020-06-30-Preview](./search-api-preview.md) . V tuto chvíli není k dispozici žádný portál ani podpora sady .NET SDK.

> [!NOTE]
> Při použití zásad podmíněného odstranění v nativním objektu BLOB musí být klíče dokumentu pro dokumenty v indexu buď vlastnost objektu blob, nebo metadata objektu BLOB.

V této metodě použijete nativní funkci [obnovitelného odstranění objektů BLOB](../storage/blobs/soft-delete-blob-overview.md) nabízenou úložištěm Azure Blob Storage. Pokud je v účtu úložiště zapnutá možnost nativní odstranění nativního objektu blob, má zdroj dat nativní sadu zásad pro tiché odstranění a indexer nalezne objekt blob, který byl převeden na měkký odstraněný stav, indexer odebere tento dokument z indexu. Při indexování objektů BLOB z Azure Data Lake Storage Gen2 není podporováno nativní zásady podmíněného odstranění objektu BLOB.

Použijte k tomu následující postup:

1. Povolí [nativní obnovitelné odstranění pro úložiště objektů BLOB v Azure](../storage/blobs/soft-delete-blob-overview.md). Doporučujeme nastavit zásady uchovávání informací na hodnotu, která je mnohem vyšší než plán intervalu indexeru. Tímto způsobem, pokud dojde k potížím s indexerem nebo pokud máte velký počet dokumentů k indexování, je dostatek času, aby indexer mohl nakonec zpracovat obnovitelné odstraněné objekty blob. Indexery Azure Kognitivní hledání odstraní jenom dokument z indexu, pokud je objekt BLOB zpracovává, když je ve stavu, kdy je odstraněný.

1. Nakonfigurujte zásady detekce nativního odstranění objektů BLOB ve zdroji dat. Příklad najdete níže. Vzhledem k tomu, že je tato funkce ve verzi Preview, musíte použít REST API verze Preview.

1. Spusťte indexer nebo nastavte indexer tak, aby běžel podle plánu. Když indexer spustí a zpracuje objekt blob, dokument se odebere z indexu.

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

### <a name="reindexing-un-deleted-blobs-using-native-soft-delete-policies"></a>Přeindexování neodstraněných objektů BLOB (pomocí nativních zásad obnovitelného odstranění)

Pokud odstraníte objekt BLOB ze služby Azure Blob Storage s povoleným nativním jemným odstraněním na vašem účtu úložiště, objekt BLOB se převede na stav nepodmíněného odstranění a nabídne vám možnost zrušit odstranění tohoto objektu BLOB v rámci doby uchování. Pokud zrušíte odstranění po jeho dokončení indexeru, indexer nebude vždy indexovat obnovený objekt BLOB. Důvodem je skutečnost, že indexer určuje, které objekty BLOB se mají indexovat na základě `LastModified` časového razítka objektu BLOB. Pokud se odstraněný objekt BLOB zruší, jeho `LastModified` časové razítko se neaktualizuje, takže pokud indexer již zpracoval objekty BLOB s novějšími `LastModified` časovými razítky, nebude znovu indexován neodstraněného objektu BLOB. 

Aby se zajistilo, že se neodstraněný objekt BLOB přeindexuje, budete muset aktualizovat `LastModified` časové razítko objektu BLOB. Jedním ze způsobů, jak to provést, je uložení metadat tohoto objektu BLOB. Nemusíte měnit metadata, ale pokud metadata znovu uložíte, aktualizuje se `LastModified` časové razítko objektu BLOB tak, aby indexer věděl, že potřebuje k Reindexování tohoto objektu BLOB.

## <a name="soft-delete-using-custom-metadata"></a>Obnovitelné odstranění pomocí vlastních metadat

V této metodě použijete metadata objektu BLOB k označení, kdy by měl být dokument odebrán z indexu vyhledávání. Tato metoda vyžaduje dvě samostatné akce, odstranění vyhledávacího dokumentu z indexu a za ním i odstranění objektu BLOB v Azure Storage.

Použijte k tomu následující postup:

1. Přidejte do objektu BLOB vlastní dvojici klíč-hodnota metadat, abyste označili Azure Kognitivní hledání, že se logicky odstraní.

1. Nakonfigurujte zásady detekce sloupce tichého odstranění ve zdroji dat. Příklad najdete níže.

1. Jakmile indexer zpracuje objekt BLOB a odstraní ho z indexu, můžete tento objekt BLOB odstranit v úložišti objektů BLOB v Azure.

Například následující zásady považují objekt blob, který se má odstranit, pokud má vlastnost metadata `IsDeleted` s hodnotou `true` :

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

### <a name="reindexing-un-deleted-blobs-using-custom-metadata"></a>Přeindexování neodstraněných objektů BLOB (pomocí vlastních metadat)

Jakmile indexer zpracuje odstraněný objekt BLOB a odstraní odpovídající hledaný dokument z indexu, nebude ho znovu navštěvovat, pokud ho později obnovíte, pokud `LastModified` je časové razítko objektu BLOB starší než poslední spuštění indexeru.

Pokud chcete tento dokument znovu indexovat, změňte `"softDeleteMarkerValue" : "false"` pro tento objekt BLOB a znovu spusťte indexer.

## <a name="see-also"></a>Viz také

* [Indexery ve službě Azure Cognitive Search](search-indexer-overview.md)
* [Jak nakonfigurovat indexer objektů BLOB](search-howto-indexing-azure-blob-storage.md)
* [Přehled indexování objektů BLOB](search-blob-storage-integration.md)