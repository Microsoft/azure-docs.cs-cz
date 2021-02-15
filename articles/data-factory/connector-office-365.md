---
title: Kopírování dat z Office 365 pomocí Azure Data Factory
description: Naučte se, jak kopírovat data z Office 365 na podporovaná úložiště dat jímky pomocí aktivity kopírování v kanálu Azure Data Factory.
author: linda33wj
ms.service: data-factory
ms.topic: conceptual
ms.date: 10/20/2019
ms.author: jingwang
ms.openlocfilehash: a7df69e7c5701074b40d6fa8340d8a0e247f00de
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100392999"
---
# <a name="copy-data-from-office-365-into-azure-using-azure-data-factory"></a>Kopírování dat z Office 365 do Azure pomocí Azure Data Factory
[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Azure Data Factory se integruje s [Microsoft Graph Data Connect](/graph/data-connect-concept-overview). díky tomu můžete v tenantovi Office 365 přenášet bohatě rozsáhlá data organizace do Azure, a to škálovatelným způsobem a sestavovat analytické aplikace a extrahovat přehledy na základě těchto cenných datových assetů. Integrace s Privileged Access Management poskytuje zabezpečený řízení přístupu pro cenná data v Office 365.  V [tomto odkazu](/graph/data-connect-concept-overview) najdete přehled informací o Microsoft Graph Data Connect a informace o licencích najdete v [tomto](/graph/data-connect-policies#licensing) odkazu.

Tento článek popisuje, jak pomocí aktivity kopírování v nástroji Azure Data Factory kopírovat data z Office 365. Sestaví se v článku [Přehled aktivity kopírování](copy-activity-overview.md) , který představuje obecný přehled aktivity kopírování.

## <a name="supported-capabilities"></a>Podporované možnosti
Konektor ADF Office 365 a Microsoft Graph Data Connect umožňuje škálování příjmu různých typů datových sad z poštovních schránek s povoleným e-mailem Exchange, včetně kontaktů adresáře, událostí kalendáře, e-mailových zpráv, informací o uživateli, nastavení poštovní schránky atd.  Pokud chcete zobrazit úplný seznam dostupných datových sad, podívejte se [sem](/graph/data-connect-datasets) .

V rámci jedné aktivity kopírování teď můžete **Kopírovat data z Office 365 do [Azure Blob Storage](connector-azure-blob-storage.md), [Azure Data Lake Storage Gen1](connector-azure-data-lake-store.md)a [Azure Data Lake Storage Gen2](connector-azure-data-lake-storage.md) ve formátu JSON** (setOfObjects). Pokud chcete sadu Office 365 načíst do jiných typů úložišť dat nebo v jiných formátech, můžete vytvořit zřetězení první aktivity kopírování s následnou aktivitou kopírování, aby se další data načetla do některého z [podporovaných cílových úložišť ADF](copy-activity-overview.md#supported-data-stores-and-formats) (v tabulce podporovaná úložiště dat a formáty odkazují na sloupec podporované jako jímka).

>[!IMPORTANT]
>- Předplatné Azure obsahující datovou továrnu a úložiště dat jímky musí být v rámci stejného tenanta Azure Active Directory (Azure AD) jako tenant Office 365.
>- Ujistěte se, že oblast Azure Integration Runtime použitá pro aktivitu kopírování a cíl je ve stejné oblasti, ve které se nachází poštovní schránka uživatelů klienta Office 365. Informace o tom, jak se určuje Azure IR umístění, najdete [tady](concepts-integration-runtime.md#integration-runtime-location) . Seznam podporovaných oblastí Office a odpovídajících oblastí Azure najdete v [tabulce](/graph/data-connect-datasets#regions) .
>- Ověřování instančního objektu je jediným ověřovacím mechanismem podporovaným pro Azure Blob Storage, Azure Data Lake Storage Gen1 a Azure Data Lake Storage Gen2 jako s cílovými úložišti.

## <a name="prerequisites"></a>Požadavky

Pokud chcete kopírovat data z Office 365 do Azure, musíte provést následující požadované kroky:

- Správce tenanta Office 365 musí dokončit operace zprovoznění, jak je popsáno [zde](/graph/data-connect-get-started).
- Vytvoření a konfigurace webové aplikace Azure AD v Azure Active Directory.  Pokyny najdete v tématu [Vytvoření aplikace Azure AD](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal).
- Poznamenejte si následující hodnoty, které použijete k definování propojené služby pro Office 365:
    - ID tenanta Pokyny najdete v tématu [získání ID tenanta](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
    - ID aplikace a klíč aplikace  Pokyny najdete v tématu [získání ID aplikace a ověřovacího klíče](../active-directory/develop/howto-create-service-principal-portal.md#get-tenant-and-app-id-values-for-signing-in).
- Přidejte identitu uživatele, která bude vytvářet žádost o přístup k datům jako vlastník webové aplikace Azure AD (ze služby Azure AD Web Application > nastavení > vlastníci > přidat vlastníka). 
    - Identita uživatele musí být v organizaci Office 365, ze které získáváte data, a nesmí se jednat o uživatele typu Host.

## <a name="approving-new-data-access-requests"></a>Schvalování nových požadavků na přístup k datům

Pokud pro tento kontext požadujete data poprvé (kombinace toho, ke které tabulce dat přistupuje, který cílový účet je data, která jsou načítána do a která identita uživatele vytváří požadavek na přístup k datům), zobrazí se stav aktivity kopírování jako "probíhá" a pouze když kliknete na [odkaz Podrobnosti v části akce](copy-activity-overview.md#monitoring) , zobrazí se stav "RequestingConsent".  Člen skupiny schvalovatelů přístupu k datům musí schválit žádost v Privileged Access Management předtím, než může pokračovat v extrakci dat.

[Tady](/graph/data-connect-tips#approve-pam-requests-via-office-365-admin-portal) najdete informace o tom, jak schvalovatel může žádost o přístup k datům schválit, a [tady](/graph/data-connect-pam) se můžete podívat na vysvětlení celkové integrace s Privileged Access Management, včetně toho, jak nastavit skupinu schvalovatelů přístupu k datům.

## <a name="policy-validation"></a>Ověřování zásad

Pokud se ADF vytvoří jako součást spravované aplikace a přiřazení zásad Azure se provádí u prostředků v rámci skupiny prostředků správy, pak se pro každé spuštění aktivity kopírování zkontroluje a zachová se, aby se zajistilo uplatnění přiřazení zásad. Seznam podporovaných zásad najdete [tady](/graph/data-connect-policies#policies) .

## <a name="getting-started"></a>Začínáme

>[!TIP]
>Návod k používání konektoru sady Office 365 najdete v článku o [načtení dat z office 365](load-office-365-data.md) .

Kanál s aktivitou kopírování můžete vytvořit pomocí jednoho z následujících nástrojů nebo sad SDK. Vyberte odkaz pro přechod na kurz s podrobnými pokyny k vytvoření kanálu s aktivitou kopírování. 

- [Azure Portal](quickstart-create-data-factory-portal.md)
- [.NET SDK](quickstart-create-data-factory-dot-net.md)
- [Python SDK](quickstart-create-data-factory-python.md)
- [Azure PowerShell](quickstart-create-data-factory-powershell.md)
- [REST API](quickstart-create-data-factory-rest-api.md)
- [Šablona Azure Resource Manager](quickstart-create-data-factory-resource-manager-template.md). 

Následující části obsahují podrobné informace o vlastnostech, které se používají k definování Data Factory entit specifických pro konektor sady Office 365.

## <a name="linked-service-properties"></a>Vlastnosti propojené služby

Pro propojenou službu Office 365 jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type musí být nastavená na: **Office 365** . | Yes |
| office365TenantId | ID tenanta Azure, ke kterému patří účet Office 365. | Yes |
| servicePrincipalTenantId | Zadejte informace o tenantovi, pod kterým se nachází webová aplikace Azure AD. | Yes |
| servicePrincipalId | Zadejte ID klienta aplikace. | Yes |
| servicePrincipalKey | Zadejte klíč aplikace. Označte toto pole jako SecureString a bezpečně ho uložte do Data Factory. | Yes |
| connectVia | Integration Runtime, která se má použít pro připojení k úložišti dat  Pokud není zadaný, použije se výchozí Azure Integration Runtime. | No |

>[!NOTE]
> Rozdíl mezi **office365TenantId** a **servicePrincipalTenantId** a odpovídající hodnotou, kterou chcete poskytnout:
>- Pokud jste podnikovým vývojářem, který vyvíjí aplikaci proti datům Office 365 pro použití vaší vlastní organizace, měli byste pro obě vlastnosti dodat stejné ID tenanta, což je ID tenanta AAD vaší organizace.
>- Pokud jste vývojář ISV, který vyvíjí aplikaci pro vaše zákazníky, pak bude office365TenantId jako ID tenanta AAD vašeho zákazníka (instalační program aplikace) a servicePrincipalTenantId bude ID tenanta AAD vaší společnosti.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování datových sad, naleznete v článku [datové sady](concepts-datasets-linked-services.md) . V této části najdete seznam vlastností podporovaných datovou sadou Office 365.

Chcete-li kopírovat data z Office 365, jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type datové sady musí být nastavená na: **Office365Table** . | Yes |
| tableName | Název datové sady, která se má extrahovat z Office 365. Seznam datových sad Office 365, které jsou k dispozici pro extrakci, najdete [tady](/graph/data-connect-datasets#datasets) . | Yes |

Pokud jste nacházeli `dateFilterColumn` , `startTime` , `endTime` , a `userScopeFilterUri` v datové sadě, je stále podporováno tak, jak jsou, a Vy jste navrženi použití nového modelu ve zdroji aktivity.

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

Úplný seznam oddílů a vlastností, které jsou k dispozici pro definování aktivit, najdete v článku [kanály](concepts-pipelines-activities.md) . V této části najdete seznam vlastností podporovaných zdrojem Office 365.

### <a name="office-365-as-source"></a>Office 365 jako zdroj

Pokud chcete kopírovat data ze sady Office 365, v části **zdroje** aktivity kopírování jsou podporovány následující vlastnosti:

| Vlastnost | Popis | Povinné |
|:--- |:--- |:--- |
| typ | Vlastnost Type zdroje aktivity kopírování musí být nastavená na: **Office365Source** . | Yes |
| allowedGroups | Predikát výběru skupiny  Tato vlastnost slouží k výběru až 10 skupin uživatelů, pro které budou data načtena.  Pokud nejsou zadané žádné skupiny, vrátí se data pro celou organizaci. | No |
| userScopeFilterUri | Pokud `allowedGroups` vlastnost není zadána, můžete použít výraz predikátu, který je použit v celém tenantovi k filtrování konkrétních řádků pro extrakci z Office 365. Formát predikátu by měl odpovídat formátu dotazu Microsoft Graph rozhraní API, např. `https://graph.microsoft.com/v1.0/users?$filter=Department eq 'Finance'` . | No |
| dateFilterColumn | Název sloupce filtru DateTime Pomocí této vlastnosti můžete omezit časový rozsah, pro který se mají extrahovat data sady Office 365. | Ano, pokud má datová sada jeden nebo více sloupců typu DateTime. Seznam datových sad, které vyžadují tento filtr DateTime, najdete [tady](/graph/data-connect-filtering#filtering) . |
| startTime | Počáteční hodnota DateTime, která se má filtrovat | Ano, pokud `dateFilterColumn` je zadána |
| endTime | Koncová hodnota DateTime, která se má filtrovat | Ano, pokud `dateFilterColumn` je zadána |
| outputColumns | Pole sloupců, které mají být zkopírovány do jímky. | No |

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
Seznam úložišť dat podporovaných jako zdroje a jímky aktivity kopírování v Azure Data Factory najdete v části [podporovaná úložiště dat](copy-activity-overview.md#supported-data-stores-and-formats).