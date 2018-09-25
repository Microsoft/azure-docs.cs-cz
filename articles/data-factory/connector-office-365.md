---
title: Kopírování dat z Office 365 pomocí Azure Data Factory (Preview) | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Office 365 do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: jingwang
ms.openlocfilehash: a077c7d154235205126d77e5523c0acd96e70ad5
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "47031607"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory-preview"></a>Zkopírovat data z Office 365 do Azure pomocí Azure Data Factory (Preview) 

Azure Data Factory umožňuje vložit bohaté datům organizace v Office 365 tenanta do Azure v škálovatelným způsobem a vytvářet analytické aplikace a extrahování přehledů, které jsou založené na těchto prostředcích cenná data. Integrace s Privileged Access Management zajišťující řízení zabezpečeného přístupu cenné zpracovaná data v Office 365.  Další informace o spravované přístup k Microsoft Graphu v Microsoft Azure Preview najdete [tento odkaz](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki).

Tento článek ukazuje, jak použít aktivitu kopírování ke zkopírování dat z Office 365 ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce

Prozatím se v rámci jednoho kopírování můžete pouze **kopírování dat z Office 365 do [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), a [(Azure Data Lake Storage Gen2 Ve verzi Preview)](connector-azure-data-lake-storage.md) ve formátu JSON** (zadejte setOfObjects). Pokud chcete načíst do jiné typy úložišť dat, nebo v jiných formátů Office 365, můžete vytvořit řetězové první aktivitu kopírování s aktivitou kopírování dalších dál načítat data do všech [podporovaná ADF cílového úložiště](copy-activity-overview.md#supported-data-stores-and-formats) (viz část o" podporované jako jímka"sloupce v tabulce"Podporovaná úložiště dat a formátech").

>[!IMPORTANT]
>- Předplatné Azure, který obsahuje objekt pro vytváření dat a úložiště dat jímky musí být ve stejném tenantovi Azure Active Directory (Azure AD) jako tenant Office 365.
>- Zajištění oblasti Azure Integration Runtime používané pro aktivitu kopírování, stejně jako cíl je ve stejné oblasti, kde se nachází poštovní schránky uživatelů tenanta Office 365. Přečtěte si [tady](concepts-integration-runtime.md#integration-runtime-location) pochopit, jak je určeno umístění prostředí Azure IR. Odkazovat na [tabulku tady](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#data-regions) seznam podporovaných oblastí pro Office a odpovídající oblasti Azure.
>-  Pokud nahráváte data Office 365 do **Azure Blob Storage** jako cíl, ujistěte se, že používáte **[ověřování instančních objektů](connector-azure-blob-storage.md#service-principal-authentication)** při definování propojené Služby Azure Blob Storage a bez použití [klíč účtu](connector-azure-blob-storage.md#account-key-authentication), [sdílený přístupový podpis](connector-azure-blob-storage.md#shared-access-signature-authentication) nebo [se identita spravované služby ](connector-azure-blob-storage.md#managed-service-identity-authentication) ověření.
>-  Pokud nahráváte data Office 365 do **Azure Data Lake Storage Gen1** jako cíl, ujistěte se, že používáte [ **ověřování instančních objektů** ](connector-azure-data-lake-store.md#using-service-principal-authentication) při definování Propojená služba Azure Data Lake Storage Gen1 a bez použití [spravovaná služba ověření identity](connector-azure-data-lake-store.md#using-managed-service-identity-authentication).

## <a name="prerequisites"></a>Požadavky

Chcete-li kopírovat data z Office 365 do Azure, proveďte následující nutné kroky:

- Váš správce tenanta Office 365 musíte provést akce v rámci zprovozňování, jak je popsáno [tady](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding).
- Vytvoření a konfigurace webové aplikace Azure AD ve službě Azure Active Directory.  Pokyny najdete v tématu [vytvořit aplikaci Azure AD](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Poznamenejte si následující hodnoty, které se používají k definování propojené služby pro Office 365:
    - ID tenanta.  Pokyny najdete v tématu [získání ID tenanta](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-tenant-id).
    - Klíč ID aplikace a aplikace.  Pokyny najdete v tématu [Get aplikace ID a ověřovacího klíče](../azure-resource-manager/resource-group-create-service-principal-portal.md#get-application-id-and-authentication-key).
- Přidejte identitu uživatele, kteří budou využívat žádost o přístup dat jako vlastník webovou aplikaci Azure AD (ze služby Azure AD webová aplikace > Nastavení > vlastníky > Přidat vlastníka).

## <a name="approving-new-data-access-requests"></a>Schvalování nové žádosti o data access

Pokud je to poprvé, kdy požadujete data pro tento kontext (kombinace dat, které tabulky se přístup, které cílového účtu je načítají do data a které identity uživatele je vidět data žádost o přístup), zobrazí se aktivita kopírování stav "Probíhá", a pouze v případě, že kliknete na [odkaz "Details" v části Akce](copy-activity-overview.md#monitoring) se zobrazí stav jako "RequestingConsent".  Člen skupiny schvalovatel přístupu dat musí schválit žádost v Privileged Access Management extrakce dat mohl pokračovat.

Přečtěte si [tady](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Approving-a-data-access-request) na tom, jak můžete schválit schvalující přístup k žádosti o data a najdete v [tady](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/On-boarding) vysvětlení na celkovou integraci s Privileged Access managementu, včetně nastavení dat Schvalovatel skupiny pro řetězce klíčů.

## <a name="policy-validation"></a>Ověření zásad

Pokud ADF je vytvořen jako součást spravované aplikace a přiřazení zásady Azure jsou provedeny s prostředky v rámci skupiny pro správu prostředků, pak pro každou aktivitu kopírování, spuštění, ADF zkontroluje Ujistěte se, že se vynucují přiřazení zásad. Přečtěte si [tady](https://github.com/OfficeDev/ManagedAccessMSGraph/wiki/Capabilities#policies) seznam podporovaných zásad.

## <a name="getting-started"></a>Začínáme

>[!TIP]
>Postup použití konektoru Office 365, najdete v části [načítat data z Office 365](load-office-365-data.md) článku.

Vytvoříte kanál s aktivitou kopírování pomocí jedné z následujících nástrojů nebo sad SDK. Vyberte odkaz přejděte na kurz s podrobnými pokyny k vytvoření kanálu s aktivitou kopírování. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Šablona Azure Resource Manageru](quickstart-create-data-factory-resource-manager-template.md). 

Následující části obsahují podrobnosti o vlastnostech, které se používají k definování entit služby Data Factory konkrétní konektor Office 365.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro Office 365, propojené služby jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Office 365** | Ano |
| office365TenantId | ID klienta Azure, ke kterému patří účet Office 365. | Ano |
| servicePrincipalTenantId | Zadejte informace o tenantovi umístěn webové aplikace Azure AD. | Ano |
| servicePrincipalId | Zadejte ID klienta vaší aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString bezpečně uložit ve službě Data Factory. | Ano |
| connectVia | Integration Runtime, který se má použít pro připojení k úložišti.  Pokud není zadán, použije výchozí prostředí Azure Integration Runtime. | Ne |

>[!NOTE]
> Rozdíl mezi **office365TenantId** a **servicePrincipalTenantId** a odpovídající hodnotou poskytnout:
>- Pokud jste vývojář enterprise vyvíjet aplikace pro Office 365 data pro používání ve vaší vlastní organizaci, pak by mělo nabízet stejného tenanta ID pro obě vlastnosti, které je vaše organizace tenanta AAD ID.
>- Pokud jste vývojář ISV vyvíjet aplikace pro vaše zákazníky office365TenantId bude vašeho zákazníka (Instalační program aplikace) ID tenanta AAD a servicePrincipalTenantId budou vaší společnosti ID tenanta AAD

**Příklad:**

```json
{
    "name": "Office365LinkedService",
    "properties": {
        "type": "Office365",
        "typeProperties": {
            "office365TenantId": "<Office 365 tenant id>",
            "servicePrincipalTenantId": "<AAD app service principal tenant id>",
            "servicePrincipalId": "<AAD app service principal id>",
            "servicePrincipalKey": {
                "type": "SecureString",
                "value": "<AAD app service principal key>"
            }
        }
    }
}
```

## <a name="dataset-properties"></a>Vlastnosti datové sady

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování datové sady, najdete v článku [datových sad](concepts-datasets-linked-services.md) článku. Tato část obsahuje seznam vlastností, které podporuje datové sady Office 365.

Pokud chcete zkopírovat data z Office 365, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost typ datové sady, musí být nastavena na: **Office365Table** | Ano |
| tableName | Název datové sady, chcete-li extrahovat z Office 365. Tady najdete seznam datových sad Office 365 k dispozici pro extrahování. | Ano |
| Predikát | Predikátu výraz, který můžete použít k filtrování konkrétní řádky, které mají extrahovat z Office 365.  Najdete zde zjistit sloupce, které lze použít pro filtrování predikát pro každou tabulku a formát výrazu filtru. | Ne<br>(Pokud není zadána žádná predikátu, výchozí hodnota je extrahovat data za posledních 30 dní) |

**Příklad**

```json
{
    "name": "Office365Table1",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "ReceivedDateTime",
                "type": "datetime"
            },
            {
                "name": "SentDateTime",
                "type": "datetime"
            },
            {
                "name": "HasAttachments",
                "type": "boolean"
            },
            {
                "name": "InternetMessageId",
                "type": "string"
            },
            {
                "name": "Subject",
                "type": "string"
            },
            {
                "name": "Importance",
                "type": "string"
            },
            {
                "name": "ParentFolderId",
                "type": "string"
            },
            {
                "name": "Sender",
                "type": "string"
            },
            {
                "name": "From",
                "type": "string"
            },
            {
                "name": "ToRecipients",
                "type": "string"
            },
            {
                "name": "CcRecipients",
                "type": "string"
            },
            {
                "name": "BccRecipients",
                "type": "string"
            },
            {
                "name": "ReplyTo",
                "type": "string"
            },
            {
                "name": "ConversationId",
                "type": "string"
            },
            {
                "name": "UniqueBody",
                "type": "string"
            },
            {
                "name": "IsDeliveryReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsReadReceiptRequested",
                "type": "boolean"
            },
            {
                "name": "IsRead",
                "type": "boolean"
            },
            {
                "name": "IsDraft",
                "type": "boolean"
            },
            {
                "name": "WebLink",
                "type": "string"
            },
            {
                "name": "CreatedDateTime",
                "type": "datetime"
            },
            {
                "name": "LastModifiedDateTime",
                "type": "datetime"
            },
            {
                "name": "ChangeKey",
                "type": "string"
            },
            {
                "name": "Categories",
                "type": "string"
            },
            {
                "name": "Id",
                "type": "string"
            },
            {
                "name": "Attachments",
                "type": "string"
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Contact_v0",
            "predicate": "CreatedDateTime >= 2018-07-11T16:00:00.000Z AND CreatedDateTime <= 2018-07-18T16:00:00.000Z"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastnosti, které jsou k dispozici pro definování aktivit najdete v článku [kanály](concepts-pipelines-activities.md) článku. Tato část obsahuje seznam vlastností podporovaných aplikací Office 365.

### <a name="office-365-as-source"></a>Office 365 jako zdroj

Ke zkopírování dat z Office 365, nastavte typ zdroje v aktivitě kopírování do **Office365Source**. Žádné další vlastnosti jsou podporovány v aktivitě kopírování **zdroj** oddílu.

**Příklad:**

```json
"activities": [
    {
        "name": "CopyFromO365ToBlob",
        "type": "Copy",
        "inputs": [
            {
                "referenceName": "<Office 365 input dataset name>",
                "type": "DatasetReference"
            }
        ],
        "outputs": [
            {
                "referenceName": "<output dataset name>",
                "type": "DatasetReference"
            }
        ],
        "typeProperties": {
            "source": {
                "type": "Office365Source"
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další postup
Seznam úložišť dat podporovaných jako zdroje a jímky v aktivitě kopírování ve službě Azure Data Factory najdete v tématu [podporovanými úložišti dat](copy-activity-overview.md#supported-data-stores-and-formats).
