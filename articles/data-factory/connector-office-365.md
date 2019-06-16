---
title: Kopírování dat z Office 365 pomocí Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírovat data z Office 365 do úložišť dat podporovaných jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: craigg
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: jingwang
ms.openlocfilehash: 1a8d622aa280794d9a4d6fe7320ddcc21ac044f4
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66475666"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Zkopírovat data z Office 365 do Azure pomocí Azure Data Factory

Azure Data Factory se integruje s [datech Microsoft Graphu připojit](https://docs.microsoft.com/graph/data-connect-concept-overview), abyste mohli přenést bohaté datům organizace v Office 365 tenanta do Azure v škálovatelným způsobem a vytvářet analytické aplikace a extrahovat přehledy na základě Tyto prostředky cenná data. Integrace s Privileged Access Management zajišťující řízení zabezpečeného přístupu cenné zpracovaná data v Office 365.  Najdete [tento odkaz](https://docs.microsoft.com/graph/data-connect-concept-overview) přehled o datech Microsoft Graphu připojení a odkazovat na [tento odkaz](https://docs.microsoft.com/graph/data-connect-policies#licensing) informace o licencích.

Tento článek ukazuje, jak použít aktivitu kopírování ke zkopírování dat z Office 365 ve službě Azure Data Factory. Je nástavbou [přehled aktivit kopírování](copy-activity-overview.md) článek, který nabízí obecný přehled o aktivitě kopírování.

## <a name="supported-capabilities"></a>Podporované funkce
Konektor ADF Office 365 a datech Microsoft Graphu připojení umožňuje na škálování příjmu různé druhy datových sad z poštovní schránky povolená e-mailu Exchange, včetně kontakty z adresáře, události v kalendáři, e-mailové zprávy, informace o uživateli, nastavení poštovní schránky, a atd.  Přečtěte si [tady](https://docs.microsoft.com/graph/data-connect-datasets) zobrazíte úplný seznam datových sad, které jsou k dispozici.

Prozatím se v rámci jednoho kopírování můžete pouze **kopírování dat z Office 365 do [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md), a [Azure Data Lake Storage Gen2 ](connector-azure-data-lake-storage.md) ve formátu JSON** (zadejte setOfObjects). Pokud chcete načíst do jiné typy úložišť dat, nebo v jiných formátů Office 365, můžete vytvořit řetězové první aktivitu kopírování s aktivitou kopírování dalších dál načítat data do všech [podporovaná ADF cílového úložiště](copy-activity-overview.md#supported-data-stores-and-formats) (viz část o" podporované jako jímka"sloupce v tabulce"Podporovaná úložiště dat a formátech").

>[!IMPORTANT]
>- Předplatné Azure, který obsahuje objekt pro vytváření dat a úložiště dat jímky musí být ve stejném tenantovi Azure Active Directory (Azure AD) jako tenant Office 365.
>- Zajištění oblasti Azure Integration Runtime používané pro aktivitu kopírování, stejně jako cíl je ve stejné oblasti, kde se nachází poštovní schránky uživatelů tenanta Office 365. Přečtěte si [tady](concepts-integration-runtime.md#integration-runtime-location) pochopit, jak je určeno umístění prostředí Azure IR. Odkazovat na [tabulku tady](https://docs.microsoft.com/graph/data-connect-datasets#regions) seznam podporovaných oblastí pro Office a odpovídající oblasti Azure.
>- Ověřování instančního objektu je pouze mechanismus ověřování podporované pro Azure Blob Storage, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2 jako cílové úložiště.

## <a name="prerequisites"></a>Požadavky

Chcete-li kopírovat data z Office 365 do Azure, proveďte následující nutné kroky:

- Váš správce tenanta Office 365 musíte provést akce v rámci zprovozňování, jak je popsáno [tady](https://docs.microsoft.com/graph/data-connect-get-started).
- Vytvoření a konfigurace webové aplikace Azure AD ve službě Azure Active Directory.  Pokyny najdete v tématu [vytvořit aplikaci Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Poznamenejte si následující hodnoty, které se používají k definování propojené služby pro Office 365:
    - ID tenanta. Pokyny najdete v tématu [získání ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - Klíč ID aplikace a aplikace.  Pokyny najdete v tématu [Get aplikace ID a ověřovacího klíče](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Přidejte identitu uživatele, kteří budou využívat žádost o přístup dat jako vlastník webovou aplikaci Azure AD (ze služby Azure AD webová aplikace > Nastavení > vlastníky > Přidat vlastníka). 
    - Identita uživatele musí být v organizaci využívající Office 365 se získávání dat z a nesmí být uživatele typu Host.

## <a name="approving-new-data-access-requests"></a>Schvalování nové žádosti o data access

Pokud je to poprvé, kdy požadujete data pro tento kontext (kombinace dat, které tabulky se přístup, které cílového účtu je načítají do data a které identity uživatele je vidět data žádost o přístup), zobrazí se aktivita kopírování stav "Probíhá", a pouze v případě, že kliknete na [odkaz "Details" v části Akce](copy-activity-overview.md#monitoring) se zobrazí stav jako "RequestingConsent".  Člen skupiny schvalovatel přístupu dat musí schválit žádost v Privileged Access Management extrakce dat mohl pokračovat.

Přečtěte si [tady](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) na tom, jak můžete schválit schvalující přístup k žádosti o data a najdete v [tady](https://docs.microsoft.com/graph/data-connect-pam) vysvětlení na celkovou integraci s Privileged Access managementu, včetně nastavení dat Schvalovatel skupiny pro řetězce klíčů.

## <a name="policy-validation"></a>Ověření zásad

Pokud ADF je vytvořen jako součást spravované aplikace a přiřazení zásady Azure jsou provedeny s prostředky v rámci skupiny pro správu prostředků, pak pro každou aktivitu kopírování, spuštění, ADF zkontroluje Ujistěte se, že se vynucují přiřazení zásad. Přečtěte si [tady](https://docs.microsoft.com/graph/data-connect-policies#policies) seznam podporovaných zásad.

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
| type | Vlastnost type musí být nastavená na: **Office365** | Ano |
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
| type | Vlastnost type datové sady, musí být nastavená na: **Office365Table** | Ano |
| tableName | Název datové sady, chcete-li extrahovat z Office 365. Přečtěte si [tady](https://docs.microsoft.com/graph/data-connect-datasets#datasets) seznam k dispozici pro extrahování datových sad Office 365. | Ano |
| allowedGroups | Predikát výběr skupiny.  Chcete-li vybrat až 10 skupin uživatelů, pro které se budou načítat data pomocí této vlastnosti.  Pokud nejsou zadány žádné skupiny, pak vrátí data pro celou organizaci. | Ne |
| userScopeFilterUri | Když `allowedGroups` vlastnost neurčí, můžete použít predikátu výraz, který se použije v celém tenantovi, chcete-li filtrovat konkrétní řádky, které mají extrahovat z Office 365. Výrazový formát by měl odpovídat formátu dotazů rozhraní Microsoft Graph API, například `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`. | Ne |
| dateFilterColumn | Název sloupce filtru data a času. Pomocí této vlastnosti můžete omezit časový rozsah, pro které Office 365 jsou extrahována data. | Ano, pokud datová sada obsahuje jeden nebo více sloupců data a času. Přečtěte si [tady](https://docs.microsoft.com/graph/data-connect-filtering#filtering) seznam datových sad, které vyžadují tento filtr data a času. |
| startTime | Počáteční hodnota data a času pro filtrování. | Ano, pokud `dateFilterColumn` určena |
| endTime | Koncová hodnota data a času pro filtrování. | Ano, pokud `dateFilterColumn` určena |

**Příklad**

```json
{
    "name": "DS_May2019_O365_Message",
    "properties": {
        "type": "Office365Table",
        "linkedServiceName": {
            "referenceName": "<Office 365 linked service name>",
            "type": "LinkedServiceReference"
        },
        "structure": [
            {
                "name": "Id",
                "type": "String",
                "description": "The unique identifier of the event."
            },
            {
                "name": "CreatedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was created."
            },
            {
                "name": "LastModifiedDateTime",
                "type": "DateTime",
                "description": "The date and time that the event was last modified."
            },
            {
                "name": "ChangeKey",
                "type": "String",
                "description": "Identifies the version of the event object. Every time the event is changed, ChangeKey changes as well. This allows Exchange to apply changes to the correct version of the object."
            },
            {
                "name": "Categories",
                "type": "String",
                "description": "The categories associated with the event. Format: ARRAY<STRING>"
            },
            {
                "name": "OriginalStartTimeZone",
                "type": "String",
                "description": "The start time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "OriginalEndTimeZone",
                "type": "String",
                "description": "The end time zone that was set when the event was created. See DateTimeTimeZone for a list of valid time zones."
            },
            {
                "name": "ResponseStatus",
                "type": "String",
                "description": "Indicates the type of response sent in response to an event message. Format: STRUCT<Response: STRING, Time: STRING>"
            },
            {
                "name": "iCalUId",
                "type": "String",
                "description": "A unique identifier that is shared by all instances of an event across different calendars."
            },
            {
                "name": "ReminderMinutesBeforeStart",
                "type": "Int32",
                "description": "The number of minutes before the event start time that the reminder alert occurs."
            },
            {
                "name": "IsReminderOn",
                "type": "Boolean",
                "description": "Set to true if an alert is set to remind the user of the event."
            },
            {
                "name": "HasAttachments",
                "type": "Boolean",
                "description": "Set to true if the event has attachments."
            },
            {
                "name": "Subject",
                "type": "String",
                "description": "The text of the event's subject line."
            },
            {
                "name": "Body",
                "type": "String",
                "description": "The body of the message associated with the event.Format: STRUCT<ContentType: STRING, Content: STRING>"
            },
            {
                "name": "Importance",
                "type": "String",
                "description": "The importance of the event: Low, Normal, High."
            },
            {
                "name": "Sensitivity",
                "type": "String",
                "description": "Indicates the level of privacy for the event: Normal, Personal, Private, Confidential."
            },
            {
                "name": "Start",
                "type": "String",
                "description": "The start time of the event. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "End",
                "type": "String",
                "description": "The date and time that the event ends. Format: STRUCT<DateTime: STRING, TimeZone: STRING>"
            },
            {
                "name": "Location",
                "type": "String",
                "description": "Location information of the event. Format: STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>>"
            },
            {
                "name": "IsAllDay",
                "type": "Boolean",
                "description": "Set to true if the event lasts all day. Adjusting this property requires adjusting the Start and End properties of the event as well."
            },
            {
                "name": "IsCancelled",
                "type": "Boolean",
                "description": "Set to true if the event has been canceled."
            },
            {
                "name": "IsOrganizer",
                "type": "Boolean",
                "description": "Set to true if the message sender is also the organizer."
            },
            {
                "name": "Recurrence",
                "type": "String",
                "description": "The recurrence pattern for the event. Format: STRUCT<Pattern: STRUCT<Type: STRING, `Interval`: INT, Month: INT, DayOfMonth: INT, DaysOfWeek: ARRAY<STRING>, FirstDayOfWeek: STRING, Index: STRING>, `Range`: STRUCT<Type: STRING, StartDate: STRING, EndDate: STRING, RecurrenceTimeZone: STRING, NumberOfOccurrences: INT>>"
            },
            {
                "name": "ResponseRequested",
                "type": "Boolean",
                "description": "Set to true if the sender would like a response when the event is accepted or declined."
            },
            {
                "name": "ShowAs",
                "type": "String",
                "description": "The status to show: Free, Tentative, Busy, Oof, WorkingElsewhere, Unknown."
            },
            {
                "name": "Type",
                "type": "String",
                "description": "The event type: SingleInstance, Occurrence, Exception, SeriesMaster."
            },
            {
                "name": "Attendees",
                "type": "String",
                "description": "The collection of attendees for the event. Format: ARRAY<STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>, Status: STRUCT<Response: STRING, Time: STRING>, Type: STRING>>"
            },
            {
                "name": "Organizer",
                "type": "String",
                "description": "The organizer of the event. Format: STRUCT<EmailAddress: STRUCT<Name: STRING, Address: STRING>>"
            },
            {
                "name": "WebLink",
                "type": "String",
                "description": "The URL to open the event in Outlook Web App."
            },
            {
                "name": "Attachments",
                "type": "String",
                "description": "The FileAttachment and ItemAttachment attachments for the message. Navigation property. Format: ARRAY<STRUCT<LastModifiedDateTime: STRING, Name: STRING, ContentType: STRING, Size: INT, IsInline: BOOLEAN, Id: STRING>>"
            },
            {
                "name": "BodyPreview",
                "type": "String",
                "description": "The preview of the message associated with the event. It is in text format."
            },
            {
                "name": "Locations",
                "type": "String",
                "description": "The locations where the event is held or attended from. The location and locations properties always correspond with each other. Format:  ARRAY<STRUCT<DisplayName: STRING, Address: STRUCT<Street: STRING, City: STRING, State: STRING, CountryOrRegion: STRING, PostalCode: STRING>, Coordinates: STRUCT<Altitude: DOUBLE, Latitude: DOUBLE, Longitude: DOUBLE, Accuracy: DOUBLE, AltitudeAccuracy: DOUBLE>, LocationEmailAddress: STRING, LocationUri: STRING, LocationType: STRING, UniqueId: STRING, UniqueIdType: STRING>>"
            },
            {
                "name": "OnlineMeetingUrl",
                "type": "String",
                "description": "A URL for an online meeting. The property is set only when an organizer specifies an event as an online meeting such as a Skype meeting"
            },
            {
                "name": "OriginalStart",
                "type": "DateTime",
                "description": "The start time that was set when the event was created in UTC time."
            },
            {
                "name": "SeriesMasterId",
                "type": "String",
                "description": "The ID for the recurring series master item, if this event is part of a recurring series."
            }
        ],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1",
            "dateFilterColumn": "CreatedDateTime",
            "startTime": "2019-04-28T16:00:00.000Z",
            "endTime": "2019-05-05T16:00:00.000Z",
            "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'"
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
