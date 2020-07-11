---
title: Účty úložiště vlastněné zákazníkem pro ingestování protokolů
description: Použijte vlastní účet úložiště pro příjem dat protokolu do Log Analyticsho pracovního prostoru v Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 05/20/2020
ms.openlocfilehash: 05eb92e2fb887b5c64e2c73576fe85a4543ac1b7
ms.sourcegitcommit: ec682dcc0a67eabe4bfe242fce4a7019f0a8c405
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2020
ms.locfileid: "86184493"
---
# <a name="customer-owned-storage-accounts-for-log-ingestion-in-azure-monitor"></a>Účty úložiště vlastněné zákazníkem pro přijímání protokolů v Azure Monitor

Azure Monitor používá účty úložiště v procesu přijímání některých datových typů, jako jsou například [vlastní protokoly](data-sources-custom-logs.md) a některé [protokoly Azure](azure-storage-iis-table.md). Během procesu příjmu se protokoly nejdřív odesílají do účtu úložiště a později se ingestují do Log Analytics nebo Application Insights. Pokud chcete mít kontrolu nad daty během přijímání, můžete místo úložiště spravovaného službou použít vlastní účty úložiště. Pomocí vlastního účtu úložiště získáte kontrolu nad přístupem, obsahem, šifrováním a uchováváním protokolů během přijímání. Odkazujeme na to, že Přineste si vlastní úložiště nebo BYOS. 

Jedním z scénářů, které vyžadují BYOS, je izolace sítě prostřednictvím privátních odkazů. Pokud používáte virtuální síť, je často požadavek na izolaci sítě a přístup k veřejnému Internetu je omezený. V takových případech je přístup k úložišti služby Azure Monitor pro ingestování protokolu buď úplně blokovaný, nebo se považuje za špatný postup. Místo toho by se měly protokoly přijímat prostřednictvím účtu úložiště ve vlastnictví zákazníka v rámci virtuální sítě nebo snadno dostupného.

Dalším scénářem je šifrování protokolů pomocí klíčů spravovaných zákazníkem (CMK). Zákazníci mohou šifrovat protokolovaná data pomocí CMK v clusterech, které ukládají protokoly. Stejný klíč lze také použít k šifrování protokolů během procesu příjmu.

## <a name="data-types-supported"></a>Podporované datové typy

K datovým typům, které se ingestují z účtu úložiště, patří následující. Další informace o přijímání těchto typů najdete v tématu [shromáždění dat z rozšíření Azure Diagnostics pro Azure monitor protokolů](azure-storage-iis-table.md) .

| Type | Informace o tabulce |
|:-----|:------------------|
| Protokoly IIS | Objekt BLOB: wad-IIS-LogFiles|
|Protokoly událostí Windows | Tabulka: WADWindowsEventLogsTable |
| Syslog | Tabulka: LinuxsyslogVer2v0 |
| Protokoly ETW systému Windows | Tabulka: WADETWEventTable|
| Service Fabric | Tabulka: WADServiceFabricSystemEventTable <br/> WADServiceFabricReliableActorEventTable<br/> WADServiceFabricReliableServicEventTable |
| Vlastní protokoly | Není k dispozici |
| Soubory výpisu Azure Security Center Watson | Není k dispozici|  

## <a name="storage-account-requirements"></a>Požadavky na účet úložiště 
Účet úložiště musí splňovat následující požadavky:

- Přístup k prostředkům ve vaší virtuální síti, které zapisují protokoly do úložiště.
- Musí být ve stejné oblasti jako pracovní prostor, ke kterému je propojený.
- Povolení přístupu Azure Monitor – Pokud jste zvolili omezení přístupu účtu úložiště k vybraným sítím, ujistěte se, že jste tuto výjimku povolili: *pro přístup k tomuto účtu úložiště měli důvěryhodné služby Microsoftu*.

## <a name="process-to-configure-customer-owned-storage"></a>Postup konfigurace úložiště ve vlastnictví zákazníka
Základní proces používání vlastního účtu úložiště pro přijímání je následující:

1. Vytvořte účet úložiště nebo vyberte existující účet.
2. Připojte účet úložiště k pracovnímu prostoru Log Analytics.
3. Úložiště můžete spravovat tak, že zkontrolujete jeho zatížení a uchování, abyste měli jistotu, že funguje podle očekávání.

Jedinou metodou, kterou je možné vytvořit a odebrat, je REST API. Podrobnosti o konkrétní žádosti rozhraní API vyžadované pro jednotlivé procesy jsou k dispozici v následujících částech.

## <a name="command-line-and-rest-api"></a>Příkazový řádek a REST API

### <a name="command-line"></a>Příkazový řádek
Pokud chcete vytvořit a spravovat propojené účty úložiště, použijte příkaz [AZ monitor Log-Analytics pracovní prostor propojený-Storage](https://docs.microsoft.com/cli/azure/monitor/log-analytics/workspace/linked-storage). Tento příkaz může propojit účty úložiště a odpojit je od pracovního prostoru a zobrazit seznam propojených účtů úložiště.

### <a name="request-and-cli-values"></a>Hodnoty požadavků a CLI

#### <a name="datasourcetype"></a>dataSourceType 

- AzureWatson – použijte tuto hodnotu pro Azure Security Center soubory výpisu paměti Azure Watson.
- CustomLogs – použijte tuto hodnotu pro následující typy dat:
  - Vlastní protokoly
  - Protokoly IIS
  - Události (Windows)
  - Syslog (Linux)
  - Protokoly ETW
  - Události Service Fabric
  - Data posouzení  

#### <a name="storage_account_resource_id"></a>storage_account_resource_id
Tato hodnota používá následující strukturu:

```
subscriptions/{subscriptionId}/resourcesGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName1}
```


### <a name="get-linked-storage-accounts-for-all-data-source-types"></a>Získat propojené účty úložiště pro všechny typy zdrojů dat

#### <a name="api-request"></a>Požadavek rozhraní API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Odpověď

```json
{
    [
        {
            "properties":
            {
                "dataSourceType": "CustomLogs",
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_1>",
                    "<storage_account_resource_id_2>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
            "name": "CustomLogs",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        },
        {
            "properties":
            {
                "dataSourceType": " AzureWatson "
                "storageAccountIds  ": 
                [  
                    "<storage_account_resource_id_3>",
                    "<storage_account_resource_id_4>"
                ],
            },
            "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/AzureWatson",
            "name": "AzureWatson",
            "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
        }
    ]
}
```


### <a name="get-linked-storage-accounts-for-a-specific-data-source-type"></a>Získat propojené účty úložiště pro určitý typ zdroje dat

#### <a name="api-request"></a>Požadavek rozhraní API

```
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

#### <a name="response"></a>Odpověď 

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs",
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
    "id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
    "name": "CustomLogs",
    "type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```

## <a name="create-or-modify-a-link"></a>Vytvoří nebo upraví odkaz.

Po propojení účtu úložiště s pracovním prostorem ho Log Analytics začít používat místo účtu úložiště, který vlastní služba. Můžete zaregistrovat seznam účtů úložiště ve stejnou dobu a můžete použít stejný účet úložiště pro několik pracovních prostorů.

Pokud pracovní prostor zpracovává jak prostředky virtuální sítě, tak prostředky mimo virtuální síť, měli byste zajistit, aby neodmítala přenosy přicházející z Internetu. Vaše úložiště by mělo mít stejné nastavení jako váš pracovní prostor a mělo by být dostupné pro prostředky mimo vaši virtuální síť. 

### <a name="api-request"></a>Požadavek rozhraní API

```
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

### <a name="payload"></a>Délka

```json
{
    "properties":
    {
        "storageAccountIds  " : 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    }
}
```

### <a name="response"></a>Odpověď

```json
{
    "properties":
    {
        "dataSourceType": "CustomLogs"
        "storageAccountIds  ": 
        [  
            "<storage_account_resource_id_1>",
            "<storage_account_resource_id_2>"
        ],
    },
"id":"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/microsoft. operationalinsights/workspaces/{resourceName}/linkedStorageAccounts/CustomLogs",
"name": "CustomLogs",
"type": "Microsoft.OperationalInsights/workspaces/linkedStorageAccounts"
}
```


## <a name="unlink-a-storage-account"></a>Zrušení propojení účtu úložiště
Pokud se rozhodnete ukončit používání účtu úložiště pro ingestování nebo chcete použít pracovní prostor, který použijete, měli byste Odpojit úložiště od pracovního prostoru.

Odpojení všech účtů úložiště z pracovního prostoru znamená, že se ingestování pokusí spoléhat na účty úložiště spravované službou. Pokud jsou vaši agenti spuštěni ve virtuální síti s omezeným přístupem k Internetu, předpokládá se, že je přijímání příjmu neúspěšné. Pracovní prostor musí mít propojený účet úložiště, který je dosažitelný z monitorovaných prostředků.

Než odstraníte účet úložiště, měli byste se ujistit, že všechna data, která obsahuje, byla v pracovním prostoru ingestovaná. Je potřeba zajistit, aby byl váš účet úložiště po propojení s alternativním úložiště dostupný. Odstraňte ho jenom po ingestování veškerého jeho obsahu a uvidíte, že nová data se zapisují do nově připojeného účtu úložiště.


### <a name="api-request"></a>Požadavek rozhraní API
```
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.OperationalInsights/workspaces/{workspaceName}/linkedStorageAccounts/{dataSourceType}?api-version=2019-08-01-preview  
```

## <a name="replace-a-storage-account"></a>Výměna účtu úložiště

Pokud chcete nahradit účet úložiště, který se používá pro ingestování, nejdřív vytvořte odkaz na nový účet úložiště. Agenti protokolování budou mít aktualizovanou konfiguraci a začnou odesílat data do nového úložiště také.

Další odpojte starý účet úložiště, aby agenti přestali zapisovat na odebraný účet. Proces příjmu uchovává data z tohoto účtu, dokud není vše ingestované. Účet úložiště neodstraňujte, dokud neuvidíte, že se všechny protokoly ingestují.

Konfigurace agenta se po několika minutách aktualizuje a přepne se na nové úložiště.

## <a name="manage-storage-account"></a>Spravovat účet úložiště

### <a name="load"></a>Načítání

Účty úložiště mohou zpracovávat určité zatížení požadavků na čtení a zápis před tím, než začnou žádosti o omezení začínat. Omezení ovlivňuje dobu potřebnou k ingestování protokolů a může způsobit ztrátu dat. Pokud je vaše úložiště přetížené, zaregistrujte další účty úložiště a rozšíříte zatížení mezi nimi. 

### <a name="related-charges"></a>Související poplatky

Účty úložiště se účtují podle objemu uložených dat, typů úložiště a typu redundance. Podrobnosti najdete v tématu [ceny objektů blob bloku](https://azure.microsoft.com/pricing/details/storage/blobs/) a [ceny Table Storage](https://azure.microsoft.com/pricing/details/storage/tables/).

Pokud je registrovaný účet úložiště pracovního prostoru v jiné oblasti, bude se vám účtovat výstup podle těchto [údajů o cenách šířky pásma](https://azure.microsoft.com/pricing/details/bandwidth/).



## <a name="next-steps"></a>Další kroky

- Další informace o nastavení privátního odkazu najdete v tématu [použití privátního odkazu Azure k bezpečnému připojení sítí k Azure monitor](private-link-security.md)
