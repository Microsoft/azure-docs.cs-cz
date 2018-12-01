---
title: Přehled o diagnostické protokoly Azure
description: Další diagnostické protokoly Azure a jak je můžete použít k pochopení události, ke kterým dochází v rámci prostředku Azure.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 06/07/2018
ms.author: johnkem
ms.component: logs
ms.openlocfilehash: fe564e9809a3621ca04e4dad75488fb255f7dc0e
ms.sourcegitcommit: cd0a1514bb5300d69c626ef9984049e9d62c7237
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/30/2018
ms.locfileid: "52682941"
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Shromažďování a zpracování dat protokolu z vašich prostředků Azure

## <a name="what-are-azure-monitor-diagnostic-logs"></a>Co jsou diagnostické protokoly Azure monitoru

**Diagnostické protokoly Azure monitoru** jsou protokoly generované pomocí služby Azure, které poskytují bohatou, časté informace o fungování dané služby. Azure Monitor je k dispozici dva typy diagnostických protokolů:
* **Tenant protokoly** – protokoly pocházejí z služby na úrovni tenanta, které existují mimo předplatné Azure, jako je Azure Active Directory protokoly.
* **Protokoly prostředku** – protokoly pocházejí ze služby Azure, které nasazení prostředků v rámci předplatného Azure, jako jsou skupiny zabezpečení sítě nebo účty úložiště.

    ![Protokoly diagnostiky zdroje vs jiné typy protokolů ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

Obsah tyto protokoly se liší podle typu prostředků a služeb Azure. Například počítadla pravidel skupin zabezpečení sítě a služby Key Vault audity jsou dva typy diagnostických protokolů.

Tyto protokoly se liší od [protokolu aktivit](monitoring-overview-activity-logs.md). Protokol aktivit poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném pomocí Resource Manageru, třeba vytvoření virtuálního počítače nebo odstranění aplikace logiky. Protokol aktivit je protokol úrovně předplatného. Úroveň prostředků diagnostické protokoly poskytují přehled o operacích provedených v rámci tohoto vlastního prostředku, například získání tajného klíče ze služby Key Vault.

Tyto protokoly se také liší od hostovaného operačního systému – úroveň diagnostických protokolů. Diagnostické protokoly hostovaného operačního systému jsou tyto shromážděné agentem běžících v rámci virtuálního počítače nebo jiné podporovaný typ prostředku. Diagnostické protokoly na úrovni prostředku vyžadují specifické podle prostředků data z platformy Azure, bez agenta a zachycení, zatímco diagnostické protokoly úrovni operačního systému hosta zachytávat data z operační systém a aplikace běžící na virtuálním počítači.

Ne všechny služby podpory diagnostické protokoly, které jsou zde popsány. [Tento článek obsahuje části najdou služby, které podporují diagnostické protokoly](./monitoring-diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Co můžete dělat s využitím diagnostických protokolů
Tady jsou některé věci, které vám pomůžou s využitím diagnostických protokolů:

![Logické umístění diagnostické protokoly](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* Uložte je do [ **účtu úložiště** ](monitoring-archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Můžete určit pomocí uchování (ve dnech) **nastavení diagnostiky prostředků**.
* [Stream je **Event Hubs** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) za účelem ingestování datových vlastní analýzy řešení, jako je například Power BI nebo služby třetích stran.
* Analyzovat pomocí [Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md)

Můžete použít účet úložiště nebo oboru názvů Event Hubs, která není ve stejném předplatném jako ta, které vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít správný přístup RBAC k oběma předplatným.

> [!NOTE]
>  Nelze aktuálně archivovat protokoly toku network na účet úložiště, který je za zabezpečené virtuální síti.

> [!WARNING]
> 1. listopadu 2018 se formát dat protokolů v účtu úložiště změní na řádky JSON. [Informace o dopadu a postup pro aktualizaci nástrojů, aby si s novým formátem poradily, najdete v tomto článku](./monitor-diagnostic-logs-append-blobs.md). 
>
> 

## <a name="diagnostic-settings"></a>Nastavení diagnostiky

Protokolů diagnostiky prostředků je konfigurovat pomocí nastavení diagnostiky prostředku. Diagnostické protokoly tenanta se konfigurují pomocí nastavení diagnostiky tenanta. **Nastavení diagnostiky** pro řízení služeb:

* Diagnostické protokoly a metriky se odešle (účet úložiště, Event Hubs, a/nebo Log Analytics).
* Kategorie protokolu, které se odesílají a určuje, zda metrika data jsou taktéž odeslána.
* Jak dlouho se každá kategorie protokolu uchovávat v účtu úložiště
    - Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě hodnota může být libovolný počet dnů mezi 1 a 2147483647.
    - Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pokud pouze jsou vybrané možnosti služby Event Hubs nebo Log Analytics), zásady uchovávání informací nemají žádný vliv.
    - Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště.

Tato nastavení se snadno konfigurovat přes nastavení diagnostiky na portálu, prostřednictvím Azure Powershellu a příkazy rozhraní příkazového řádku nebo prostřednictvím [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="how-to-enable-collection-of-diagnostic-logs"></a>Jak povolit shromažďování diagnostických protokolů

Je možné povolit shromažďování diagnostických protokolů [při vytváření prostředku v šabloně Resource Manageru](./monitoring-enable-diagnostic-logs-using-template.md) nebo po vytvoření prostředku ze stránky tohoto prostředku na portálu. Můžete také povolit shromažďování v libovolném bodě pomocí příkazů prostředí Azure PowerShell nebo rozhraní příkazového řádku nebo pomocí REST API služby Azure Monitor.

> [!TIP]
> Tyto pokyny se nemusí vztahovat přímo ke každému prostředku. Zobrazit schéma odkazy v dolní části této stránky můžete porozumět speciální kroky, které se můžou vztahovat na určité typy prostředků.

### <a name="enable-collection-of-diagnostic-logs-in-the-portal"></a>Povolit shromažďování diagnostických protokolů na portálu

Po vytvoření prostředku tak, že přejdete ke konkrétnímu prostředku nebo tak, že přejdete do Azure monitoru, můžete povolit shromažďování protokolů diagnostiky prostředků na webu Azure Portal. Aby to bylo prostřednictvím služby Azure Monitor:

1. V [webu Azure portal](http://portal.azure.com), přejděte do Azure monitoru a klikněte na **nastavení diagnostiky**

    ![Monitorování bodu služby Azure Monitor](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Volitelně můžete filtrovat seznam podle skupiny prostředků nebo prostředek typu a potom klikněte na prostředek, pro kterou chcete nastavit nastavení diagnostiky.

3. Pokud neexistuje žádná nastavení pro prostředek jste vybrali, se zobrazí výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Pokud existuje stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou už nakonfigurovaná na tento prostředek. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Zadejte název nastavení, zaškrtněte políčka pro každý cíl, do které chcete odesílat data a konfigurace, který prostředek se používá pro jednotlivé cíle. Volitelně můžete nastavit počet dnů uchování tyto protokoly s použitím **uchování (dny)** jezdce (platí jenom pro cílový účet úložiště). 0 dnů uchování dat po neomezenou dobu ukládá protokoly.

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly jsou odeslány do zadaného umístění poté, co je vygenerována nová data události.

Diagnostické nastavení tenanta se nakonfigurovat jenom v okně portálu pro tenanta služby – tato nastavení nejsou zobrazeny v okně nastavení diagnostiky Azure Monitor. Například jsou nakonfigurované protokoly auditování Azure Active Directory po kliknutí na **exportovat nastavení dat** v okně protokoly auditu.

![Nastavení diagnostiky AAD](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-aad.png)

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Povolit shromažďování protokolů diagnostiky prostředků pomocí Powershellu

Chcete-li povolit shromažďování protokolů diagnostiky prostředků pomocí Azure Powershellu, použijte následující příkazy:

Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště, použijte tento příkaz:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

ID účtu úložiště je ID prostředku pro účet úložiště, do kterého chcete odeslat protokoly.

Pokud chcete povolit streamování diagnostických protokolů do centra událostí, použijte tento příkaz:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

ID pravidla služby Service bus je řetězec v tomto formátu: `{Service Bus resource ID}/authorizationrules/{key name}`.

Pokud chcete povolit odesílání diagnostických protokolů do pracovního prostoru Log Analytics, použijte tento příkaz:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Můžete získat ID prostředku pracovního prostoru Log Analytics pomocí následujícího příkazu:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Tyto parametry pro povolení více možností výstupu můžete kombinovat.

Nelze momentálně nakonfigurovat tenanta diagnostických nastavení pomocí Azure Powershellu.

### <a name="enable-collection-of-resource-diagnostic-logs-via-the-azure-cli"></a>Povolit shromažďování protokolů diagnostiky prostředků prostřednictvím rozhraní příkazového řádku Azure

Povolit shromažďování protokolů diagnostiky prostředků pomocí Azure CLI, použijte [az monitor diagnostiky – nastavení vytváření](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) příkazu.

Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště:

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

`--resource-group` Argument je pouze požadováno pokud `--storage-account` není ID objektu.

Pokud chcete povolit streamování diagnostických protokolů do centra událostí:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --event-hub <event hub name> \
    --event-hub-rule <event hub rule ID> \
    --resource <target resource object ID> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

ID pravidla je řetězec v tomto formátu: `{Service Bus resource ID}/authorizationrules/{key name}`.

Pokud chcete povolit odesílání diagnostických protokolů do pracovního prostoru Log Analytics:

```azurecli
az monitor diagnostic-settings create --name <diagnostic name> \
    --workspace <log analytics name or object ID> \
    --resource <target resource object ID> \
    --resource-group <log analytics workspace resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true
    }
    ]'
```

`--resource-group` Argument je pouze požadováno pokud `--workspace` není ID objektu

S jakýkoli příkaz můžete přidat další kategorie do protokolu diagnostiky tak, že přidáte slovníky předané jako pole JSON `--logs` parametru. Můžete kombinovat `--storage-account`, `--event-hub`, a `--workspace` můžete aktivovat více možností výstupu.

Nelze momentálně nakonfigurovat tenanta diagnostických nastavení pomocí rozhraní příkazového řádku.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Povolit shromažďování protokolů diagnostiky prostředků prostřednictvím rozhraní REST API

Chcete-li změnit nastavení diagnostiky pomocí REST API služby Azure Monitor, [tento dokument](https://docs.microsoft.com/rest/api/monitor/).

Nelze momentálně nakonfigurovat tenanta diagnostických nastavení pomocí REST API služby Azure Monitor.

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Spravovat nastavení diagnostiky prostředků na portálu

Ujistěte se, že všechny prostředky z nastavení je nastavení diagnostiky. Přejděte do **monitorování** v portálu a otevřete **nastavení diagnostiky**.

![Okno diagnostické protokoly na portálu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Možná budete muset klikněte na tlačítko "Všechny služby" najít v části monitorování.

Tady si můžete zobrazit a filtrovat všechny prostředky, které podporují nastavení diagnostiky. Pokud chcete zobrazit, pokud mají zapnutou diagnostiku. Můžete také přejít na najdete v tématu Pokud několik nastavení jsou nastavené na prostředku a zkontrolujte, které účet úložiště, obor názvů Event Hubs a/nebo pracovní prostor Log Analytics, která tok dat do.

![Diagnostické protokoly výsledky v portálu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Přidání nastavení diagnostiky zobrazí zobrazení nastavení diagnostiky, kde můžete povolit, zakázat nebo změnit nastavení diagnostiky pro vybraný prostředek.

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Podporované služby, kategorie a schémata pro diagnostické protokoly

[Najdete v článku](monitoring-diagnostic-logs-schema.md) úplný seznam podporovaných služeb a kategorie protokolu a schémat, které používají tyto služby.

## <a name="next-steps"></a>Další postup

* [Stream protokolů diagnostiky prostředků k **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Změnit nastavení diagnostiky prostředků pomocí REST API služby Azure Monitor](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analýza protokolů ze služby Azure storage s využitím Log Analytics](../azure-monitor/platform/collect-azure-metrics-logs.md)
