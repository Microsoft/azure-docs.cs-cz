---
title: Archivovat diagnostické protokoly Azure
description: Zjistěte, jak archivace diagnostických protokolů pro dlouhodobé uchovávání v účtu úložiště Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 07/18/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: 17b03ee9521ddf1980e27ee9a63d2ba74ea6e6b7
ms.sourcegitcommit: 4de6a8671c445fae31f760385710f17d504228f8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39629680"
---
# <a name="archive-azure-diagnostic-logs"></a>Archivovat diagnostické protokoly Azure

V tomto článku vám ukážeme, jak pomocí webu Azure portal, rutin prostředí PowerShell, rozhraní příkazového řádku nebo rozhraní REST API pro archivaci vaše [diagnostické protokoly Azure](monitoring-overview-of-diagnostic-logs.md) v účtu úložiště. Tato možnost je užitečná, pokud byste chtěli zachovat diagnostických protokolů k zásadám volitelné uchovávání informací pro auditování, statické analýzy nebo pro zálohování. Účet úložiště nemusí být ve stejném předplatném jako prostředek, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.

> [!WARNING]
> 1. listopadu 2018 se formát dat protokolů v účtu úložiště změní na řádky JSON. [Informace o dopadu a postup pro aktualizaci nástrojů, aby si s novým formátem poradily, najdete v tomto článku](./monitor-diagnostic-logs-append-blobs.md). 
>
> 

## <a name="prerequisites"></a>Požadavky

Než začnete, budete muset [vytvořit účet úložiště](../storage/storage-create-storage-account.md) ke kterému můžete archivovat diagnostické protokoly. Důrazně doporučujeme, že nepoužíváte existující účet úložiště, který obsahuje jiné, než monitorování data uložená v něm může lépe řídit přístup k datům monitorování. Pokud jsou také archivace protokolu aktivit a diagnostických metrik na účet úložiště, ale může být použití účtu úložiště pro diagnostické protokoly a aby se všechna data monitorování v centrálním umístění.

> [!NOTE]
>  Momentálně nelze archivovat data do úložiště účtu, který za zabezpečené virtuální síti.

## <a name="diagnostic-settings"></a>Nastavení diagnostiky

Archivace diagnostických protokolů některou z níže uvedených způsobů je nastavit **nastavení diagnostiky** určitého prostředku. Nastavení diagnostiky pro prostředek definuje kategorie protokolů a metrik data odeslaná do cíle (účet úložiště, obor názvů Event Hubs nebo Log Analytics). Definuje také zásady uchovávání informací (počet dní uchování) pro události z každé kategorie protokolu a data metriky, které jsou uložené v účtu úložiště. Pokud zásady uchovávání informací je nastavena na hodnotu nula, události pro dané kategorie protokolu se ukládají po neomezenou dobu (které je třeba navždy). Zásady uchovávání informací v opačném případě může být libovolný počet dnů mezi 1 a 2147483647. [Další informace o nastavení diagnostiky zde](monitoring-overview-of-diagnostic-logs.md#diagnostic-settings). Zásady uchovávání informací jsou použité za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště. 

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Archivace diagnostických protokolů pomocí portálu

1. Na portálu přejděte do Azure monitoru a klikněte na **nastavení diagnostiky**

    ![Monitorování bodu služby Azure Monitor](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Volitelně můžete filtrovat seznam podle skupiny prostředků nebo prostředek typu a potom klikněte na prostředek, pro kterou chcete nastavit nastavení diagnostiky.

3. Pokud neexistuje žádná nastavení pro prostředek jste vybrali, se zobrazí výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   Pokud existuje stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou už nakonfigurovaná na tento prostředek. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko u **exportovat do účtu úložiště**, pak vyberte účet úložiště. Volitelně můžete nastavit počet dnů uchování tyto protokoly s použitím **uchování (dny)** posuvníky. 0 dnů uchování dat po neomezenou dobu ukládá protokoly.

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly jsou archivovány na tento účet úložiště, jakmile je vygenerována nová data události.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Archivace diagnostických protokolů pomocí Azure Powershellu

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| ID prostředku |Ano |ID prostředku prostředku, na kterém chcete nastavit nastavení diagnostiky. |
| StorageAccountId |Ne |ID prostředku účtu úložiště, ke kterému má být uložen diagnostické protokoly. |
| Kategorie |Ne |Čárkou oddělený seznam kategorií protokolu povolit. |
| Povoleno |Ano |Logická hodnota označující, jestli jsou povolené nebo zakázané pro tento prostředek diagnostiku. |
| RetentionEnabled |Ne |Logická hodnota označující, zda jsou povoleny zásady uchovávání informací pro tento prostředek. |
| RetentionInDays |Ne |Počet dní, pro které události uchování mezi 1 a 2147483647. Hodnota nula ukládá protokoly po neomezenou dobu. |

## <a name="archive-diagnostic-logs-via-the-azure-cli-20"></a>Archivace diagnostických protokolů přes rozhraní příkazového řádku Azure CLI 2.0

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Můžete přidat další kategorie pro protokol diagnostiky tak, že přidáte slovníky předané jako pole JSON `--logs` parametru.

`--resource-group` Argument je pouze požadováno pokud `--storage-account` není ID objektu. Kompletní dokumentaci k archivace diagnostických protokolů do služby storage, najdete v článku [reference k příkazům rozhraní příkazového řádku](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Archivace diagnostických protokolů přes rozhraní REST API

[Najdete v tomto dokumentu](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings) informace o tom, jak můžete nastavit nastavení diagnostiky pomocí REST API služby Azure Monitor.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schéma diagnostické protokoly v aktuálním účtu úložiště

Když jste nastavili archivaci, kontejner úložiště se vytvoří v účtu úložiště, poté, co dojde k události v jednom z kategorie protokolu, které jste povolili. Objekty BLOB v kontejneru podle stejné zásady vytváření názvů v protokolech aktivit a diagnostické protokoly, jak je znázorněno zde:

```
insights-logs-{log category name}/resourceId=/SUBSCRIPTIONS/{subscription ID}/RESOURCEGROUPS/{resource group name}/PROVIDERS/{resource provider name}/{resource type}/{resource name}/y={four-digit numeric year}/m={two-digit numeric month}/d={two-digit numeric day}/h={two-digit 24-hour clock hour}/m=00/PT1H.json
```

Například může být název objektu blob:

```
insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json
```

Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Minut (m = 00) je vždy 00, protože události protokolu diagnostiky se rozdělují do jednotlivých objektů BLOB po hodinách.

V rámci souboru pt1h.JSON, když se ukládají všechny události v poli "záznamy", za tento formát:

``` JSON
{
    "records": [
        {
            "time": "2016-07-01T00:00:37.2040000Z",
            "systemId": "46cdbb41-cb9c-4f3d-a5b4-1d458d827ff1",
            "category": "NetworkSecurityGroupRuleCounter",
            "resourceId": "/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUPS/TESTNSG",
            "operationName": "NetworkSecurityGroupCounters",
            "properties": {
                "vnetResourceGuid": "{12345678-9012-3456-7890-123456789012}",
                "subnetPrefix": "10.3.0.0/24",
                "macAddress": "000123456789",
                "ruleName": "/subscriptions/ s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg/securityRules/default-allow-rdp",
                "direction": "In",
                "type": "allow",
                "matchedConnections": 1988
            }
        }
    ]
}
```

| Název elementu | Popis |
| --- | --- |
| time |Časové razítko události vygenerované službou Azure zpracování požadavku odpovídající události. |
| resourceId |ID prostředku ovlivněných prostředků. |
| operationName |Název operace |
| category |Kategorie protokolu události. |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |

> [!NOTE]
> Vlastnosti a využití těchto vlastností se může lišit v závislosti na prostředku.

## <a name="next-steps"></a>Další postup

* [Stažení objektů blob pro analýzu](../storage/storage-dotnet-how-to-use-blobs.md)
* [Stream diagnostické protokoly na obor názvů služby Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Archivovat protokoly služby Azure Active Directory prostřednictvím služby Azure Monitor](../active-directory/reports-monitoring/quickstart-azure-monitor-route-logs-to-storage-account.md)
* [Další informace o diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md)
