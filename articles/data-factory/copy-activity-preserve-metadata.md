---
title: Zachování metadat a aklů pomocí aktivity kopírování v Azure Data Factory
description: Zjistěte, jak zachovat metadata a seznamy ACL během kopírování pomocí aktivity kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 03/24/2020
ms.author: jingwang
ms.openlocfilehash: b73cd73a18d286f221c7be2c624719e1d23d7c06
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153824"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Zachování metadat a aklů pomocí aktivity kopírování v Azure Data Factory

Když použijete aktivitu kopírování Azure Data Factory ke kopírování dat ze zdroje do jímky, v následujících scénářích můžete také zachovat metadata a akly podél.

## <a name="preserve-metadata-for-lake-migration"></a><a name="preserve-metadata"></a>Zachovat metadata pro migraci jezera

Při migraci dat z jednoho datového jezera do jiného, včetně [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), můžete zachovat metadata souboru spolu s daty.

Aktivita kopírování podporuje zachování následujících atributů během kopírování dat:

- **Všechna metadata zadaná zákazníkem** 
- A následujících **pět vlastností integrovaného systému úložiště dat**: `contentDisposition` `cacheControl` `contentType`, `contentLanguage` (s výjimkou Amazon S3), `contentEncoding`, .

Když kopírujete soubory tak, jak jsou z Amazon S3/Azure Data Lake Storage Gen2/Azure Blob do Azure Data Lake Storage Gen2/Azure Blob s binárním formátem, najdete možnost **Zachovat** na kartě **Kopírovat nastavení aktivit** > **Settings** pro vytváření aktivit nebo na stránce **Nastavení** v nástroji Kopírovat data.

![Kopírovat aktivitu zachovat metadata](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Zde je příklad kopírování aktivity JSON `preserve`konfigurace (viz ): 

```json
"activities":[
    {
        "name": "CopyAndPreserveMetadata",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AmazonS3ReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "Attributes"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset Amazon S3/Azure Blob/ADLS Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset for Azure Blob/ADLS Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="preserve-acls-from-data-lake-storage-gen1gen2-to-gen2"></a><a name="preserve-acls"></a>Zachovat akly z úložiště datového jezera Gen1/Gen2 do Gen2

Při upgradu z Azure Data Lake Storage Gen1 na Gen2 nebo kopírování dat mezi ADLS Gen2, můžete se rozhodnout zachovat seznamy řízení přístupu POSIX (ACLs) spolu s datovými soubory. Další informace o řízení přístupu najdete [v tématu Řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) a [řízení přístupu v Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Aktivita kopírování podporuje zachování následujících typů aklů během kopírování dat. Můžete vybrat jeden nebo více typů:

- **ACL**: Kopírování a zachování seznamů řízení přístupu POSIX v souborech a adresářích. Zkopíruje úplné existující acy ze zdroje do jímky. 
- **Vlastník**: Zkopírujte a zachovejte vlastnící uživatele souborů a adresářů. Super-uživatelský přístup k jímky Data Lake Storage Gen2 je vyžadován.
- **Skupina**: Kopírování a zachování vlastnící skupiny souborů a adresářů. Super-uživatelský přístup k jímky Data Lake Storage Gen2 nebo vlastnící uživatele (pokud vlastnící uživatel je také členem cílové skupiny) je vyžadováno.

Pokud zadáte kopírování ze složky, Data Factory replikuje seznamy ACL pro danou složku `recursive` a soubory a adresáře pod ní, pokud je nastavena na hodnotu true. Pokud zadáte kopírování z jednoho souboru, acls v tomto souboru se zkopírují.

>[!NOTE]
>Při použití ADF zachovat ACLs z Data Lake Storage Gen1/Gen2 na Gen2, existující seznamy ACL na jímce Gen2 odpovídající složky/soubory budou přepsány.

>[!IMPORTANT]
>Pokud se rozhodnete zachovat počet aklů, ujistěte se, že udělujete dostatečně vysoká oprávnění pro data factory pracovat proti vašeho účtu dřezu Data Lake Storage Gen2. Můžete například použít ověřování pomocí klíče účtu nebo přiřadit roli vlastníka dat objektu blob úložiště k instančnímu objektu nebo spravované identitě.

Když nakonfigurujete zdroj jako Úložiště datového jezera Gen1/Gen2 s binárním formátem nebo možností kopírování binárního formátu a jíme jako Data Lake Storage Gen2 s binárním formátem nebo možností kopírování, můžete najít možnost **Zachovat** na stránce **Nastavení** v nástroji Kopírovat data nebo na kartě Kopírovat**nastavení** **aktivity** > pro vytváření aktivit.

![Úložiště datového jezera Gen1/Gen2 až Gen2 Preserve ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Zde je příklad kopírování aktivity JSON `preserve`konfigurace (viz ): 

```json
"activities":[
    {
        "name": "CopyAndPreserveACLs",
        "type": "Copy",
        "typeProperties": {
            "source": {
                "type": "BinarySource",
                "storeSettings": {
                    "type": "AzureDataLakeStoreReadSettings",
                    "recursive": true
                }
            },
            "sink": {
                "type": "BinarySink",
                "storeSettings": {
                    "type": "AzureBlobFSWriteSettings"
                }
            },
            "preserve": [
                "ACL",
                "Owner",
                "Group"
            ]
        },
        "inputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1/Gen2 source>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen2 sink>",
                "type": "DatasetReference"
            }
        ]
    }
]
```

## <a name="next-steps"></a>Další kroky

Podívejte se na další články aktivity kopírování:

- [Kopírovat přehled aktivit](copy-activity-overview.md)
- [Kopírovat výkon aktivity](copy-activity-performance.md)
