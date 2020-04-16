---
title: Kopírování dat z Office 365 pomocí Azure Data Factory
description: Zjistěte, jak kopírovat data z Office 365 do podporovaných úložišť dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
services: data-factory
documentationcenter: ''
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: ea68fa8d9326e6d9ebb4f475d16ac83959cae6e5
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416873"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopírování dat z Office 365 do Azure pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory se integruje s [Microsoft Graph data connect](https://docs.microsoft.com/graph/data-connect-concept-overview), což vám umožní přenést bohatá organizační data ve vašem tenantovi Office 365 do Azure škálovatelným způsobem a vytvářet analytické aplikace a extrahovat přehledy založené na těchto cenných datových prostředkůch. Integrace se správou privilegovaného přístupu poskytuje zabezpečené řízení přístupu pro cenná kurátorská data v Office 365.  Přehled připojení dat microsoft graphu naleznete v [tomto odkazu](https://docs.microsoft.com/graph/data-connect-concept-overview) a informace o licencích naleznete v [tomto odkazu.](https://docs.microsoft.com/graph/data-connect-policies#licensing)

Tento článek popisuje, jak pomocí kopírovat aktivitu v Azure Data Factory ke kopírování dat z Office 365. Vychází z článku [přehledu aktivity kopírování,](copy-activity-overview.md) který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti
Konektor ADF Office 365 a připojení dat Microsoft Graphu umožňují při škálování při řání různých typů datových sad z poštovních schránek s povoleným e-mailem Exchange, včetně kontaktů adresáře, událostí kalendáře, e-mailových zpráv, informací o uživateli, nastavení poštovní schránky a tak dále.  Zde [here](https://docs.microsoft.com/graph/data-connect-datasets) naleznete úplný seznam dostupných datových sad.

Prozatím můžete v rámci jedné aktivity kopírování kopírovat jenom **data z Office 365 do [Azure Blob Storage](connector-azure-blob-storage.md), Azure Data Lake Storage [Gen1](connector-azure-data-lake-store.md)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) ve formátu JSON** (typ setOfObjects). Pokud chcete načíst Office 365 do jiných typů úložišť dat nebo v jiných formátech, můžete zřetězit první aktivitu kopírování s následnou aktivitou kopírování a dále načíst data do některého z [podporovaných cílových úložišť ADF](copy-activity-overview.md#supported-data-stores-and-formats) (viz sloupec "Podporováno jako jímka" v tabulce Podporovaná úložiště a formáty dat).

>[!IMPORTANT]
>- Předplatné Azure obsahující datovou továrnu a úložiště dat jímky musí být pod stejným tenantem Azure Active Directory (Azure AD) jako tenant Office 365.
>- Ujistěte se, že oblast Runtime integrace Azure používaná pro aktivitu kopírování i cíl se nachází ve stejné oblasti, kde se nachází poštovní schránka uživatelů klienta Office 365. Zde [zjistíte,](concepts-integration-runtime.md#integration-runtime-location) jak se určuje umístění Azure IR. Seznam podporovaných oblastí Office a odpovídajících oblastí Azure [najdete v tabulce zde.](https://docs.microsoft.com/graph/data-connect-datasets#regions)
>- Ověřování na uvažuje o jedinou ověřovací mechanismnou podporovanou pro Azure Blob Storage, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2 jako cílové úložiště.

## <a name="prerequisites"></a>Požadavky

Pokud chcete zkopírovat data z Office 365 do Azure, musíte provést následující nezbytné kroky:

- Správce klienta Office 365 musí dokončit akce nástupu na palubu, jak je popsáno [zde](https://docs.microsoft.com/graph/data-connect-get-started).
- Vytvořte a nakonfigurujte webovou aplikaci Azure AD ve službě Azure Active Directory.  Pokyny najdete [v tématu Vytvoření aplikace Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#create-an-azure-active-directory-application).
- Poznamenejte si následující hodnoty, které použijete k definování propojené služby pro Office 365:
    - ID klienta. Pokyny najdete v [tématu Získání ID klienta](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
    - ID aplikace a klíč aplikace.  Pokyny naleznete v [tématu Získání ID aplikace a ověřovacího klíče](../active-directory/develop/howto-create-service-principal-portal.md#get-values-for-signing-in).
- Přidejte identitu uživatele, který bude pořštit žádost o přístup k datům jako vlastník webové aplikace Azure AD (z webové aplikace Azure AD > Nastavení > vlastníky > Přidat vlastníka). 
    - Identita uživatele musí být v organizaci Office 365, ze které získáváte data, a nesmí být uživatelem Typu Host.

## <a name="approving-new-data-access-requests"></a>Schvalování nových žádostí o přístup k datům

Pokud je to poprvé, kdy požadujete data pro tento kontext (kombinace, která tabulka dat je přístup, který cílový účet je data, která jsou načítána do a která identita uživatele provádí žádost o přístup k datům), zobrazí se stav aktivity kopírování jako "Probíhá" a pouze po kliknutí na [odkaz "Podrobnosti" v části Akce](copy-activity-overview.md#monitoring) se zobrazí stav "RequestingConsent".  Člen skupiny schvalovatel přístupu k datům musí schválit požadavek v privilegovaném přístupu managementu před extrakce dat může pokračovat.

Zde se podívejte na [to,](https://docs.microsoft.com/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) jak může schvalovatel schválit žádost o přístup k datům, a [shledejte zde](https://docs.microsoft.com/graph/data-connect-pam) vysvětlení celkové integrace se správou privilegovaného přístupu, včetně nastavení skupiny schvalovatelů přístupu k datům.

## <a name="policy-validation"></a>Ověřování zásad

Pokud je adf vytvořen jako součást spravované aplikace a přiřazení zásad Azure se provádějí na prostředcích v rámci skupiny prostředků pro správu, pak pro každé spuštění aktivity kopírování ADF zkontroluje, zda jsou vynucena přiřazení zásad. Seznam podporovaných zásad [naleznete zde.](https://docs.microsoft.com/graph/data-connect-policies#policies)

## <a name="getting-started"></a>Začínáme

>[!TIP]
>Návod k používání konektoru Office 365 najdete v článku [Načtení dat z Office 365.](load-office-365-data.md)

Můžete vytvořit kanál s aktivitou kopírování pomocí jednoho z následujících nástrojů nebo sad SDK. Vyberte odkaz pro přejděte do kurzu s podrobnými pokyny k vytvoření kanálu s aktivitou kopírování. 

- [portál Azure](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Šablona Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

V následujících částech jsou uvedeny podrobnosti o vlastnostech, které se používají k definování entit Data Factory specifických pro konektor Office 365.

## <a name="linked-service-properties"></a>Vlastnosti propojených služeb

Pro propojenou službu Office 365 jsou podporované následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type musí být nastavená na: **Office365** | Ano |
| office365TenantId | ID klienta Azure, ke kterému patří účet Office 365. | Ano |
| servicePrincipalTenantId | Zadejte informace o tenantovi, pod kterým se nachází vaše webová aplikace Azure AD. | Ano |
| servicePrincipalId | Zadejte ID klienta aplikace. | Ano |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako securestring bezpečně ukládat v datové továrně. | Ano |
| connectVia | Prostředí Integrace Runtime, které se má použít k připojení k úložišti dat.  Pokud není zadán, používá výchozí Azure Integration Runtime. | Ne |

>[!NOTE]
> Rozdíl mezi **office365TenantId** a **servicePrincipalTenantId** a odpovídající hodnotu poskytnout:
>- Pokud jste podnikový vývojář vyvíjející aplikaci proti datům Office 365 pro použití vlastní organizace, měli byste zadat stejné ID klienta pro obě vlastnosti, což je ID klienta ad vaší organizace.
>- Pokud jste vývojář isv vývoj aplikace pro vaše zákazníky, pak office365TenantId bude vašeho zákazníka (instalace aplikace) AAD ID klienta a servicePrincipalTenantId bude vaše společnost AAD id klienta.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datových sad.](concepts-datasets-linked-services.md) Tato část obsahuje seznam vlastností podporovaných datovou sadou Office 365.

Pokud chcete kopírovat data z Office 365, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type datové sady musí být nastavena na: **Office365Table.** | Ano |
| tableName | Název datové sady, kterou chcete extrahovat z Office 365. Seznam datových sad Office 365, které jsou k dispozici pro extrakci, [najdete zde.](https://docs.microsoft.com/graph/data-connect-datasets#datasets) | Ano |

Pokud jste `dateFilterColumn`nastavili `endTime`, `userScopeFilterUri` `startTime`, a v datové sadě, je stále podporována tak, jak je, zatímco se doporučuje použít nový model ve zdroji aktivity do budoucna.

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
        "schema": [],
        "typeProperties": {
            "tableName": "BasicDataSet_v0.Event_v1"
        }
    }
}
```

## <a name="copy-activity-properties"></a>Vlastnosti aktivity kopírování

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, naleznete v článku [Kanály.](concepts-pipelines-activities.md) Tato část obsahuje seznam vlastností podporovaných zdrojem Office 365.

### <a name="office-365-as-source"></a>Office 365 jako zdroj

Chcete-li kopírovat data z Office 365, jsou v části **zdroje** aktivity kopírování podporovány následující vlastnosti:

| Vlastnost | Popis | Požaduje se |
|:--- |:--- |:--- |
| type | Vlastnost type zdroje aktivit y kopírování musí být nastavena na: **Office365Source** | Ano |
| allowedGroups | Predikát výběru skupiny.  Pomocí této vlastnosti můžete vybrat až 10 skupin uživatelů, pro které budou data načtena.  Pokud nejsou zadány žádné skupiny, budou vrácena data pro celou organizaci. | Ne |
| userScopeFilterUri | Pokud `allowedGroups` vlastnost není zadána, můžete použít predikát výraz, který se použije na celý tenant filtrovat konkrétní řádky extrahovat z Office 365. Formát predikátu by měl odpovídat formátu dotazu rozhraní `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'`API aplikace Microsoft Graph, například . | Ne |
| dateFilterColumn | Název sloupce filtru DateTime. Pomocí této vlastnosti můžete omezit časový rozsah, pro který se extrahují data Office 365. | Ano, pokud datová sada má jeden nebo více sloupce DateTime. Zde [naleznete](https://docs.microsoft.com/graph/data-connect-filtering#filtering) seznam datových sad, které vyžadují tento filtr DateTime. |
| startTime | Spustit hodnotu DateTime, podle které chcete filtrovat. | Ano, `dateFilterColumn` pokud je zadán |
| endTime | Konec DateTime hodnota pro filtrování. | Ano, `dateFilterColumn` pokud je zadán |
| outputColumns | Pole sloupce zkopírovat do jímky. | Ne |

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
                "type": "Office365Source",
                "dateFilterColumn": "CreatedDateTime",
                "startTime": "2019-04-28T16:00:00.000Z",
                "endTime": "2019-05-05T16:00:00.000Z",
                "userScopeFilterUri": "https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'",
                "outputColumns": [
                    {
                        "name": "Id"
                    },
                    {
                        "name": "CreatedDateTime"
                    },
                    {
                        "name": "LastModifiedDateTime"
                    },
                    {
                        "name": "ChangeKey"
                    },
                    {
                        "name": "Categories"
                    },
                    {
                        "name": "OriginalStartTimeZone"
                    },
                    {
                        "name": "OriginalEndTimeZone"
                    },
                    {
                        "name": "ResponseStatus"
                    },
                    {
                        "name": "iCalUId"
                    },
                    {
                        "name": "ReminderMinutesBeforeStart"
                    },
                    {
                        "name": "IsReminderOn"
                    },
                    {
                        "name": "HasAttachments"
                    },
                    {
                        "name": "Subject"
                    },
                    {
                        "name": "Body"
                    },
                    {
                        "name": "Importance"
                    },
                    {
                        "name": "Sensitivity"
                    },
                    {
                        "name": "Start"
                    },
                    {
                        "name": "End"
                    },
                    {
                        "name": "Location"
                    },
                    {
                        "name": "IsAllDay"
                    },
                    {
                        "name": "IsCancelled"
                    },
                    {
                        "name": "IsOrganizer"
                    },
                    {
                        "name": "Recurrence"
                    },
                    {
                        "name": "ResponseRequested"
                    },
                    {
                        "name": "ShowAs"
                    },
                    {
                        "name": "Type"
                    },
                    {
                        "name": "Attendees"
                    },
                    {
                        "name": "Organizer"
                    },
                    {
                        "name": "WebLink"
                    },
                    {
                        "name": "Attachments"
                    },
                    {
                        "name": "BodyPreview"
                    },
                    {
                        "name": "Locations"
                    },
                    {
                        "name": "OnlineMeetingUrl"
                    },
                    {
                        "name": "OriginalStart"
                    },
                    {
                        "name": "SeriesMasterId"
                    }
                ]
            },
            "sink": {
                "type": "BlobSink"
            }
        }
    }
]
```

## <a name="next-steps"></a>Další kroky
Seznam úložišť dat podporovaných jako zdroje a propady aktivitou kopírování v Azure Data Factory najdete v [tématu podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).
