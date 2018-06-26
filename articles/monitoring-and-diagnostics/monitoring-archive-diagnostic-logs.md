---
title: Archivovat Azure diagnostických protokolů
description: Zjistěte, jak k archivaci k diagnostickým protokolům Azure pro dlouhodobé uchovávání v účtu úložiště.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: d48828c8d2ec439f389fe4eddabb59599cc1680b
ms.sourcegitcommit: 6eb14a2c7ffb1afa4d502f5162f7283d4aceb9e2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/25/2018
ms.locfileid: "36752822"
---
# <a name="archive-azure-diagnostic-logs"></a>Archivovat Azure diagnostických protokolů

V tomto článku jsme ukazují, jak pomocí portálu Azure, rutiny prostředí PowerShell, rozhraní příkazového řádku nebo REST API pro archivaci vaše [Azure diagnostické protokoly](monitoring-overview-of-diagnostic-logs.md) v účtu úložiště. Tato možnost je užitečná, pokud chcete zachovat k diagnostickým protokolům zásadami volitelné uchování pro audit, statické analýzy nebo zálohování. Účet úložiště nemusí být ve stejném předplatném jako prostředek emitování protokoly tak dlouho, dokud uživatel, který konfiguruje nastavení, má odpovídající přístup RBAC do oba odběry.

## <a name="prerequisites"></a>Požadavky

Než začnete, budete muset [vytvořit účet úložiště](../storage/storage-create-storage-account.md) ke kterému archivujete diagnostické protokoly. Důrazně doporučujeme nepoužívejte stávající účet úložiště, který má jiné, -monitoring data v ní uloženy, takže může lépe řídit přístup k datům monitorování. Ale pokud jsou také archivaci protokolu aktivit a diagnostiky metriky na účet úložiště, má smysl pro použití tohoto účtu úložiště pro svoje diagnostické protokoly pro monitorování všech dat v centrálním umístění. Účet úložiště, které používáte, musí být účet úložiště obecné účely, nikoli účet úložiště objektů blob.

> [!NOTE]
>  Nelze archivovat aktuálně dat do úložiště účtu, který za zabezpečené virtuální sítě.

## <a name="diagnostic-settings"></a>Nastavení diagnostiky

K archivaci k diagnostickým protokolům pomocí kteréhokoli z následujících metod, můžete nastavit **nastavení diagnostiky** pro určitý prostředek. Nastavení diagnostiky pro prostředek definuje kategorie protokolů a metriky dat odesílaných do cílového umístění (účet úložiště, obor názvů služby Event Hubs nebo analýzy protokolů). Definuje také zásady uchovávání informací (počet dní, které chcete zachovat) pro události pro každé kategorie protokolu a metriky data uložená v účtu úložiště. Pokud zásady uchovávání informací je nastaven na hodnotu nula, události pro tuto kategorii protokolu se ukládají po neomezenou dobu (tzn. Tím vyjádříte navždy). Zásady uchovávání informací v opačném případě může být libovolný počet dnů od 1 do 2147483647. [Další informace o nastavení pro diagnostiku zde](monitoring-overview-of-diagnostic-logs.md#resource-diagnostic-settings). Zásady uchovávání informací jsou použité denní, takže na konci za den (UTC), protokoly dnem, který je teď nad rámec uchovávání se zásada odstraní. Například pokud jste měli zásady uchovávání informací jeden den, od začátku dnešní den protokoly z včerejšek před den by odstraněn. Proces odstraňování začíná na půlnoc UTC, ale Všimněte si, že může trvat až 24 hodin protokolů k odstranění z vašeho účtu úložiště. 

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="archive-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly archivu pomocí portálu

1. Na portálu, přejděte do monitorování Azure a klikněte na **nastavení diagnostiky**

    ![Monitorování části monitorování Azure](media/monitoring-archive-diagnostic-logs/diagnostic-settings-blade.png)

2. Volitelně můžete seznam filtrovat podle skupiny prostředků nebo typ prostředku, a potom klikněte na prostředek, pro kterou chcete provést nastavení diagnostiky.

3. Pokud neexistuje žádné nastavení na prostředku jste vybrali, zobrazí se výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiky."

   ![Přidat nastavení diagnostiky - žádná existující nastavení](media/monitoring-archive-diagnostic-logs/diagnostic-settings-none.png)

   Pokud máte stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou již nakonfigurována na tomto prostředku. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-archive-diagnostic-logs/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko pro **exportovat do účtu úložiště**, pak vyberte účet úložiště. Volitelně můžete nastavit počet dní, abyste tyto protokoly uchovávat pomocí **uchovávání dat (dny)** posuvníky. Uchování nulový počet dnů po neomezenou dobu ukládá protokoly.

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-archive-diagnostic-logs/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly jsou archivovány k tomuto účtu úložiště, jakmile se vygeneruje nová data události.

## <a name="archive-diagnostic-logs-via-azure-powershell"></a>Archiv diagnostických protokolů pomocí Azure PowerShell

```
Set-AzureRmDiagnosticSetting -ResourceId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/testresourcegroup/providers/Microsoft.Network/networkSecurityGroups/testnsg -StorageAccountId /subscriptions/s1id1234-5679-0123-4567-890123456789/resourceGroups/myrg1/providers/Microsoft.Storage/storageAccounts/my_storage -Categories networksecuritygroupevent,networksecuritygrouprulecounter -Enabled $true -RetentionEnabled $true -RetentionInDays 90
```

| Vlastnost | Požaduje se | Popis |
| --- | --- | --- |
| ID prostředku |Ano |ID prostředku prostředku, na kterém chcete nastavit nastavení diagnostiky. |
| storageAccountId |Ne |ID prostředku účtu úložiště, který má být uložen diagnostické protokoly. |
| Kategorie |Ne |Čárkami oddělený seznam kategorií protokolu povolit. |
| Povoleno |Ano |Logická hodnota, která určuje, zda diagnostiky jsou zapnutá nebo vypnutá u tohoto prostředku. |
| RetentionEnabled |Ne |Logická hodnota, která udává, pokud jsou zásady uchovávání informací na tento prostředek povolené. |
| RetentionInDays |Ne |Počet dní, pro které by měl být zachován události od 1 do 2147483647. Hodnota nula ukládá protokoly bez omezení. |

## <a name="archive-diagnostic-logs-via-the-azure-cli-20"></a>Archiv diagnostických protokolů pomocí Azure CLI 2.0

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

Přidáním dalších kategorií do protokolů diagnostiky tak, že přidáte do pole JSON, který je předán jako slovník `--logs` parametr.

`--resource-group` Argument je jenom v případě požadované `--storage-account` není ID objektu. Úplnou dokumentaci k archivaci diagnostických protokolů do úložiště najdete v tématu [reference k příkazům rozhraní příkazového řádku](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create).

## <a name="archive-diagnostic-logs-via-the-rest-api"></a>Diagnostické protokoly archivu přes REST API

[Najdete v tomto dokumentu](https://docs.microsoft.com/en-us/rest/api/monitor/diagnosticsettings) informace o tom, jak můžete nastavit pomocí rozhraní REST API Azure monitorování diagnostické nastavení.

## <a name="schema-of-diagnostic-logs-in-the-storage-account"></a>Schéma diagnostických protokolů v účtu úložiště

Jakmile jste nastavili archivace, kontejner úložiště se vytvoří v účtu úložiště, jakmile dojde k události v jedné z protokolu kategorií, které jste povolili. Objekty BLOB v kontejneru použijte stejný formát napříč diagnostické protokoly a protokolu aktivity. Struktura tyto objekty BLOB je:

> insights - logs-{název kategorie protokolu} / resourceId = v odběry / {ID předplatného} /RESOURCEGROUPS/ {název skupiny prostředků} /PROVIDERS/ {název zprostředkovatele prostředků} / {resource type} nebo {název prostředku} / y = {čtyřciferné číselné year} / m = {dvoumístné číslo měsíce} / d = {letopočty numerický den} / h = {hour}/m=00/PT1H.json letopočty 24 hodin

Nebo více jednoduše

> insights - logs-{název kategorie protokolu} / resourceId = / {Id prostředku} / y = {čtyřciferné číselné year} / m = {dvoumístné číslo měsíce} / d = {letopočty numerický den} / h = {hour}/m=00/PT1H.json letopočty 24 hodin

Například může být název objektu blob:

> insights-logs-networksecuritygrouprulecounter/resourceId=/SUBSCRIPTIONS/s1id1234-5679-0123-4567-890123456789/RESOURCEGROUPS/TESTRESOURCEGROUP/PROVIDERS/MICROSOFT.NETWORK/NETWORKSECURITYGROUP/TESTNSG/y=2016/m=08/d=22/h=18/m=00/PT1H.json

Každý objekt blob PT1H.json obsahuje objekt blob ve formátu JSON událostí, ke kterým došlo během hodiny zadané v adrese URL objektu blob (například h=12). Během aktuální hodiny se události připojují do souboru PT1H.json, když k nim dojde. Hodnota minutu (m = 00) je vždy 00, protože protokolů diagnostiky události jsou rozdělená do jednotlivých objektů blob za hodinu.

V souboru PT1H.json se ukládají všechny události v poli "záznamy" následující tento formát:

```
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
| time |Časové razítko při zpracování požadavku odpovídající události služby Azure vygenerovalo událost. |
| resourceId |ID prostředku ovlivněné prostředku. |
| operationName |Název operace |
| category |Kategorie protokolu události. |
| properties |Sada `<Key, Value>` páry (tj. slovník) popisující podrobnosti o události. |

> [!NOTE]
> Vlastnosti a využití tyto vlastnosti se může lišit v závislosti na prostředek.

## <a name="next-steps"></a>Další postup

* [Stáhnout objekty BLOB pro analýzu](../storage/storage-dotnet-how-to-use-blobs.md)
* [Diagnostické protokoly datový proud na obor názvů služby Event Hubs](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Další informace o diagnostických protokolů](monitoring-overview-of-diagnostic-logs.md)
