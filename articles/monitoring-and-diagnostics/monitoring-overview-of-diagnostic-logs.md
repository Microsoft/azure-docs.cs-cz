---
title: Přehled Azure diagnostické protokoly | Microsoft Docs
description: Zjistěte, co je Azure diagnostické protokoly a jak je můžete využít pochopit události, ke kterým došlo na prostředek služby Azure.
author: johnkemnetz
manager: orenr
editor: ''
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: fe8887df-b0e6-46f8-b2c0-11994d28e44f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/04/2018
ms.author: johnkem; magoedte
ms.openlocfilehash: 00b990181e5bb96f122ff1ae530dd027a772b210
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
# <a name="collect-and-consume-log-data-from-your-azure-resources"></a>Shromažďování a využití dat protokolu z vašich prostředků Azure

## <a name="what-are-azure-resource-diagnostic-logs"></a>Jaké jsou diagnostické protokoly prostředků Azure

**Diagnostické protokoly Azure úrovni prostředků** vygenerované prostředek protokoly, které poskytují bohatou a často data o operaci prostředku. Obsah tyto protokoly se liší podle typu prostředku. Například pravidlo čítače skupinu zabezpečení sítě a audity Key Vault jsou dvě kategorie protokoly prostředku.

Diagnostické protokoly na úrovni prostředků se liší od [protokol aktivit](monitoring-overview-activity-logs.md). Protokol aktivit poskytuje náhled do činnosti, které byly provedeny v prostředky ve vašem předplatném pomocí Resource Manager, například vytváření virtuálního počítače nebo odstranění aplikace logiky. Protokol aktivit je protokolu úrovni předplatného. Diagnostické protokoly na úrovni prostředků získat přehled o operace, které byly provedeny v rámci prostředku samostatně, například získání tajného klíče z Key Vault.

Diagnostické protokoly na úrovni prostředků se také liší od hostovaného operačního systému na úrovni diagnostických protokolů. Diagnostické protokoly hostovaného operačního systému jsou tyto shromažďují agenta spuštěného v rámci virtuálního počítače nebo jiné podporované typ prostředku. Diagnostické protokoly na úrovni prostředků vyžadují specifické pro zdroje dat z Azure k samotné platformě nástroje, žádné agenta a zachycení, zatímco hostovaného operačního systému na úrovni diagnostických protokolů zaznamenání dat z operačního systému a aplikací, které běží na virtuálním počítači.

Ne všechny prostředky podporu nového typu prostředku diagnostické protokoly zde popsané. Tento článek obsahuje oddíl výpis, které typy prostředků podporují nové diagnostické protokoly úrovni prostředků.

![Diagnostika prostředků protokoly vs jiné typy protokolů ](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_vs_other_logs_v5.png)

## <a name="what-you-can-do-with-resource-level-diagnostic-logs"></a>Co můžete dělat s diagnostické protokoly na úrovni prostředků
Tady jsou některé z akcí, které můžete provést prostředků diagnostických protokolů:

![Logické umístění prostředků diagnostických protokolů](./media/monitoring-overview-of-diagnostic-logs/Diagnostics_Logs_Actions.png)

* Uložte je do [ **účet úložiště** ](monitoring-archive-diagnostic-logs.md) pro auditování nebo ruční kontroly. Můžete zadat uchování (ve dnech) pomocí **nastavení diagnostiky pro prostředek**.
* [Stream, aby **Event Hubs** ](monitoring-stream-diagnostic-logs-to-event-hubs.md) pro přijímání službu třetí strany nebo řešení vlastní analýzy, jako je například PowerBI.
* Analyzovat, s [analýzy protokolů](../log-analytics/log-analytics-azure-storage.md)

Můžete vytvořit účet úložiště nebo obor názvů služby Event Hubs, který není ve stejném předplatném jako emitování protokoly. Uživatel, který konfiguruje nastavení, musí mít odpovídající RBAC přístup na oba odběry.

## <a name="resource-diagnostic-settings"></a>Nastavení diagnostiky pro prostředek

Diagnostické protokoly prostředků pro jiné výpočetní, že prostředky jsou nakonfigurovány pomocí nastavení diagnostiky pro prostředek. **Nastavení diagnostiky pro prostředek** pro ovládací prvek prostředku:

* Kde prostředků diagnostické protokoly a metriky se odesílají (účet úložiště, Event Hubs nebo analýzy protokolů).
* Kategorie protokolu, které se odesílají a jestli metriky data jsou taktéž odeslána.
* Jak dlouho každou kategorii protokolu se uchovávají v účtu úložiště
    - Uchování nulový počet dnů znamená, že jsou protokoly v nekonečné smyčce. Hodnota, jinak hodnota může být libovolný počet dnů od 1 do 2147483647.
    - Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pokud jenom jsou vybrané možnosti služby Event Hubs nebo analýzy protokolů), zásady uchovávání informací nemají žádný vliv.
    - Zásady uchovávání informací jsou použité denní, takže na konci za den (UTC), protokoly dnem, který je teď nad rámec uchovávání se zásada odstraní. Například pokud jste měli zásady uchovávání informací jeden den, od začátku dnešní den protokoly z včerejšek před den by odstraněn.

Tato nastavení jsou konfigurována snadno prostřednictvím nastavení diagnostiky pro prostředek v portálu Azure, pomocí prostředí Azure PowerShell a rozhraní příkazového řádku nebo pomocí [REST API služby Azure monitorování](https://msdn.microsoft.com/library/azure/dn931943.aspx).

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Příklad:* Metriku Příchozí zprávy v centru událostí je možné zkoumat a převést na graf na úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

> [!WARNING]
> Diagnostické protokoly a metriky z hostovaného operačního systému vrstvy výpočetní prostředky (například virtuální počítače nebo Service Fabric) použití [samostatné mechanismus konfigurace a výběr výstupy](../azure-diagnostics.md).

## <a name="how-to-enable-collection-of-resource-diagnostic-logs"></a>Postup povolení shromažďování diagnostických protokolů prostředků

Povolením shromažďování diagnostických protokolů prostředků [jako součást vytvoření prostředku v šabloně Resource Manager](./monitoring-enable-diagnostic-logs-using-template.md) nebo po vytvoření prostředku ze stránky tohoto prostředku na portálu. Můžete také povolit kolekce v libovolném bodě pomocí příkazů prostředí Azure PowerShell nebo rozhraní příkazového řádku nebo pomocí rozhraní REST API Azure monitorování.

> [!TIP]
> Tyto pokyny nemusí vztahovat přímo na každý prostředek. V tématech schématu v dolní části této stránky pochopit speciální kroky, které se mohou vztahovat k určitým typům prostředků.

### <a name="enable-collection-of-resource-diagnostic-logs-in-the-portal"></a>Povolit shromažďování diagnostických protokolů prostředků na portálu

Po vytvoření prostředku přechodem na konkrétní prostředek nebo přechodem na Azure monitorování můžete povolit shromažďování diagnostických protokolů prostředků na portálu Azure. Uděláte to pomocí Azure monitorování:

1. V [portál Azure](http://portal.azure.com), přejděte do monitorování Azure a klikněte na **nastavení diagnostiky**

    ![Monitorování části monitorování Azure](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

2. Volitelně můžete seznam filtrovat podle skupiny prostředků nebo typ prostředku, a potom klikněte na prostředek, pro kterou chcete provést nastavení diagnostiky.

3. Pokud neexistuje žádné nastavení na prostředku jste vybrali, zobrazí se výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiky."

   ![Přidat nastavení diagnostiky - žádná existující nastavení](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-none.png)

   Pokud máte stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou již nakonfigurována na tomto prostředku. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-multiple.png)

3. Zadejte název nastavení, zaškrtněte políčka pro každý cíl, do které chcete odesílat data a konfigurace, který prostředek se používá pro jednotlivé cíle. Volitelně můžete nastavit počet dní, abyste tyto protokoly uchovávat pomocí **uchovávání dat (dny)** posuvníky (platí jenom pro cílový účet úložiště). Uchování nulový počet dnů po neomezenou dobu ukládá protokoly.

   ![Přidat nastavení diagnostiky - stávající nastavení](media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly jsou odeslány do zadaného umístění také se vygeneruje nová data události.

### <a name="enable-collection-of-resource-diagnostic-logs-via-powershell"></a>Povolit shromažďování diagnostických protokolů prostředků prostřednictvím PowerShell

Chcete-li povolit shromažďování diagnostických protokolů prostředků pomocí Azure PowerShell, použijte následující příkazy:

Pokud chcete povolit ukládání diagnostických protokolů v účtu úložiště, použijte tento příkaz:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -StorageAccountId [your storage account id] -Enabled $true
```

ID účtu úložiště je ID prostředku pro účet úložiště, do kterého chcete odeslat protokoly.

Pokud chcete povolit vysílání datového proudu diagnostických protokolů do centra událostí, použijte tento příkaz:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -ServiceBusRuleId [your Service Bus rule id] -Enabled $true
```

ID pravidla service bus je řetězec s Tento formát: `{Service Bus resource ID}/authorizationrules/{key name}`.

Pokud chcete povolit odesílání diagnostických protokolů do pracovního prostoru analýzy protokolů, použijte tento příkaz:

```powershell
Set-AzureRmDiagnosticSetting -ResourceId [your resource id] -WorkspaceId [resource id of the log analytics workspace] -Enabled $true
```

Můžete získat ID prostředku pracovní prostor analýzy protokolů pomocí následujícího příkazu:

```powershell
(Get-AzureRmOperationalInsightsWorkspace).ResourceId
```

Tyto parametry povolení více možností výstupu můžete kombinovat.

### <a name="enable-collection-of-resource-diagnostic-logs-via-azure-cli-20"></a>Povolit shromažďování diagnostických protokolů prostředků pomocí Azure CLI 2.0

Chcete-li povolit shromažďování diagnostických protokolů prostředků pomocí Azure CLI 2.0, je použít [vytvořit az monitorování diagnostiky nastavení](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) příkaz.

Chcete-li povolit ukládání diagnostických protokolů v účtu úložiště:

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

`--resource-group` Argument je jenom v případě požadované `--storage-account` není ID objektu.

Chcete-li povolit vysílání datového proudu diagnostických protokolů do centra událostí:

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

ID pravidla je řetězec s Tento formát: `{Service Bus resource ID}/authorizationrules/{key name}`.

Chcete-li povolit odesílání diagnostických protokolů do pracovního prostoru analýzy protokolů:

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

`--resource-group` Argument je jenom v případě požadované `--workspace` není ID objektu

Se všemi příkazy, můžete přidat další kategorie do protokolů diagnostiky přidáním slovník do pole JSON, který je předán jako `--logs` parametr. Můžete kombinovat `--storage-account`, `--event-hub`, a `--workspace` povolení více možností výstupní parametry.

### <a name="enable-collection-of-resource-diagnostic-logs-via-rest-api"></a>Povolit shromažďování diagnostických protokolů prostředků prostřednictvím REST API

Chcete-li změnit nastavení pro diagnostiku pomocí rozhraní REST API Azure monitorování, [tento dokument](https://msdn.microsoft.com/library/azure/dn931931.aspx).

## <a name="manage-resource-diagnostic-settings-in-the-portal"></a>Spravovat nastavení pro diagnostiku prostředků na portálu

Ujistěte se, že všechny vaše prostředky nastavení je nastavení pro diagnostiku. Přejděte na **monitorování** v portálu a otevřete **nastavení pro diagnostiku**.

![Diagnostické protokoly okno na portálu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-nav.png)

Možná budete muset klikněte na tlačítko "Všechny služby" najít v části monitorování.

Zde si můžete zobrazit a vyfiltrovat všechny prostředky, které podporují diagnostické nastavení zobrazit, pokud mají diagnostiky povoleno. Můžete také přejít na najdete Pokud několik nastavení jsou nastavené na prostředku a zkontrolujte, které účet úložiště, obor názvů služby Event Hubs a pracovní prostor analýzy protokolů, které jsou k toku dat.

![Diagnostické protokoly výsledky v portálu](./media/monitoring-overview-of-diagnostic-logs/diagnostic-settings-blade.png)

Přidání nastavení diagnostiky se vyvolá nastavení pro diagnostiku zobrazení, kde můžete povolit, zakázat nebo změnit nastavení diagnostiky pro vybraný zdroj.

## <a name="supported-services-categories-and-schemas-for-resource-diagnostic-logs"></a>Podporované služby, kategorie a schémat pro diagnostické protokoly prostředků

[Najdete v článku](monitoring-diagnostic-logs-schema.md) úplný seznam podporovaných služeb a kategorií protokolu a schémata v těchto služeb.

## <a name="next-steps"></a>Další postup

* [Stream prostředků do diagnostickým protokolům **Event Hubs**](monitoring-stream-diagnostic-logs-to-event-hubs.md)
* [Změňte nastavení pro diagnostiku prostředků pomocí rozhraní REST API Azure monitorování](https://msdn.microsoft.com/library/azure/dn931931.aspx)
* [Analýza protokolů z úložiště Azure s analýzy protokolů](../log-analytics/log-analytics-azure-storage.md)
