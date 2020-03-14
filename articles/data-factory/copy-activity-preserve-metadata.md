---
title: Zachování metadat a seznamů ACL pomocí aktivity kopírování v Azure Data Factory
description: Přečtěte si, jak zachovat metadata a seznamy ACL během kopírování pomocí aktivity kopírování v Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: jingwang
ms.openlocfilehash: 056909f5fd5838e5ae50fb84bd3535029d862acf
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/13/2020
ms.locfileid: "79260836"
---
#  <a name="preserve-metadata-and-acls-using-copy-activity-in-azure-data-factory"></a>Zachování metadat a seznamů ACL pomocí aktivity kopírování v Azure Data Factory

Když použijete aktivitu Azure Data Factory kopírování ke kopírování dat ze zdroje do jímky, můžete také v následujících scénářích zachovat metadata a seznamy ACL společně.

## <a name="preserve-metadata"></a>Zachovat metadata pro migraci Lake

Když migrujete data z jedné služby Data Lake do druhé včetně [Amazon S3](connector-amazon-simple-storage-service.md), [Azure Blob](connector-azure-blob-storage.md)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md), můžete se rozhodnout zachovat metadata souboru spolu s daty.

Aktivita kopírování podporuje zachování následujících atributů během kopírování dat:

- **Všechna zadaná metadata zákazníka** 
- A následující **pět integrovaných systémových vlastností úložiště**: `contentType`, `contentLanguage` (s výjimkou Amazon S3), `contentEncoding``contentDisposition``cacheControl`.

Když kopírujete soubory tak, jak jsou z webu Amazon S3/Azure Data Lake Storage Gen2/Azure Blob pro Azure Data Lake Storage Gen2/Azure Blob s binárním formátem, najdete možnost **zachovat** na kartě **Nastavení** > **aktivity kopírování** pro vytváření aktivit nebo na stránce **Nastavení** v nástroji pro kopírování dat.

![Aktivita kopírování – zachovat metadata](./media/copy-activity-preserve-metadata/copy-activity-preserve-metadata.png)

Tady je příklad konfigurace JSON aktivity kopírování (viz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
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

## <a name="preserve-acls"></a>Zachování seznamů ACL z Data Lake Storage Gen1 na Gen2

Když upgradujete z Azure Data Lake Storage Gen1 na Gen2, můžete se rozhodnout zachovat seznamy řízení přístupu (ACL) POSIX spolu s datovými soubory. Další informace o řízení přístupu najdete v tématu [řízení přístupu v Azure Data Lake Storage Gen1](../data-lake-store/data-lake-store-access-control.md) a [řízení přístupu v Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-access-control.md).

Aktivita kopírování podporuje zachování následujících typů seznamů ACL během kopírování dat. Můžete vybrat jeden nebo více typů:

- **Seznam ACL**: Zkopírujte a zachovejte seznamy řízení přístupu POSIX pro soubory a adresáře. Zkopíruje všechny existující seznamy ACL ze zdroje do jímky. 
- **Vlastník**: Zkopírujte a zachovejte vlastnícího uživatele souborů a adresářů. Vyžaduje se přístup super-uživatel k Data Lake Storage Gen2 jímky.
- **Skupina**: Zkopírujte a zachovejte vlastnící skupinu souborů a adresářů. Je vyžadován přístup super uživatele k jímky Data Lake Storage Gen2 nebo vlastnícímu uživateli (Pokud je vlastnící uživatel také členem cílové skupiny).

Pokud určíte, že se má kopírovat ze složky, Data Factory replikuje seznamy ACL pro danou složku a soubory a adresáře pod ním, pokud je `recursive` nastavené na true. Pokud určíte, že se mají kopírovat z jednoho souboru, seznamy ACL tohoto souboru se zkopírují.

>[!NOTE]
>Když použijete ADF k zachování seznamů ACL z Data Lake Storage Gen1 do Gen2, budou přepsány existující seznamy ACL pro Gen2's odpovídající složky nebo soubory.

>[!IMPORTANT]
>Pokud se rozhodnete zachovat seznamy řízení přístupu (ACL), ujistěte se, že udělíte dostatečná oprávnění, aby Data Factory fungovala s Data Lake Storage Gen2m účtem jímky. Použijte například ověřování pomocí klíče účtu nebo přiřaďte roli vlastníka dat objektu BLOB úložiště k instančnímu objektu nebo spravované identitě.

Když nakonfigurujete zdroj jako Data Lake Storage Gen1 s binárním formátem nebo možností binárního kopírování a jímku jako Data Lake Storage Gen2 s binárním formátem nebo možností binárního kopírování, můžete na stránce **Nastavení** v nástroji pro kopírování dat nebo na kartě nastavení **aktivity kopírování** >  pro vytváření aktivit najít možnost **zachovat** .

![Data Lake Storage Gen1 Gen2 zachovat seznam ACL](./media/connector-azure-data-lake-storage/adls-gen2-preserve-acl.png)

Tady je příklad konfigurace JSON aktivity kopírování (viz `preserve`): 

```json
"activities":[
    {
        "name": "CopyFromGen1ToGen2",
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
                "referenceName": "<Binary dataset name for Azure Data Lake Storage Gen1 source>",
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

Zobrazit další články o aktivitě kopírování:

- [Přehled aktivit kopírování](copy-activity-overview.md)
- [Výkon aktivity kopírování](copy-activity-performance.md)
