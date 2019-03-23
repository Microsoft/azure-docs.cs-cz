---
title: Stream diagnostické protokoly Azure do Log Analytics
description: Naučíte se Streamovat diagnostické protokoly Azure do pracovního prostoru Log Analytics.
author: johnkemnetz
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 04/04/2018
ms.author: johnkem
ms.subservice: logs
ms.openlocfilehash: bd760fca20a602127e7d33913547dcb2c6bc95f6
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351542"
---
# <a name="stream-azure-diagnostic-logs-to-log-analytics"></a>Stream diagnostické protokoly Azure do Log Analytics

**[Diagnostické protokoly Azure](diagnostic-logs-overview.md)**  můžete streamování v reálném čase do služby Azure Log Analytics pomocí portálu, rutin prostředí PowerShell nebo rozhraní příkazového řádku Azure.

## <a name="what-you-can-do-with-diagnostics-logs-in-log-analytics"></a>Co můžete dělat s diagnostikou protokolů v Log Analytics

Azure Log Analytics je flexibilní protokolu vyhledávání a analýzy nástroj, který umožňuje získat přehled o Nezpracovaný protokol data vygenerovaná prostředky Azure. Některé funkce patří:

* **Prohledávání protokolů** – zápis upřesňujících dotazů nad vašimi daty protokolů, korelovat protokoly z různých zdrojů a dokonce i generovat grafy, které je možné připnout na řídicí panel Azure.
* **Výstrahy** -rozpoznat, kdy se jeden nebo více událostí odpovídají konkrétním dotazu a informováni pomocí e-mailu nebo webhooku volání.
* **Řešení** – použijte předem sestavené zobrazení a řídicí panely, které vám poskytnou okamžitý přehled o datech log.
* **Pokročilé analýzy** – použití strojového učení a vzor odpovídající algoritmy s cílem identifikovat možné problémy zjištěné při vaše protokoly.

## <a name="enable-streaming-of-diagnostic-logs-to-log-analytics"></a>Povolení streamování diagnostických protokolů do Log Analytics

Streamování diagnostických protokolů prostřednictvím kódu programu, prostřednictvím portálu, nebo pomocí můžete povolit [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/diagnosticsettings). V obou případech můžete vytvořit nastavení diagnostiky v které zadáte, pracovnímu prostoru Log Analytics a kategorie protokolů a metrik, které chcete odeslat daném pracovním prostoru. Diagnostika **kategorie protokolu** je typ protokolu, který může poskytnout zdroje.

Pracovní prostor Log Analytics, nemusí být ve stejném předplatném jako prostředek, které vysílá protokoly za předpokladu, že uživatel, který konfiguruje nastavení má odpovídající přístup RBAC k oběma předplatným.

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika 'Příchozí zprávy' v Centru událostí můžete prozkoumat a převést na graf úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="stream-diagnostic-logs-using-the-portal"></a>Diagnostické protokoly Stream pomocí portálu
1. Na portálu přejděte do Azure monitoru a klikněte na **nastavení diagnostiky**

    ![Monitorování bodu služby Azure Monitor](media/diagnostic-logs-stream-log-store/diagnostic-settings-blade.png)

2. Volitelně můžete filtrovat seznam podle skupiny prostředků nebo prostředek typu a potom klikněte na prostředek, pro kterou chcete nastavit nastavení diagnostiky.

3. Pokud neexistuje žádná nastavení pro prostředek jste vybrali, se zobrazí výzva k vytvoření nastavení. Klikněte na tlačítko "Zapnout diagnostiku."

   ![Přidejte nastavení diagnostiky – žádná existující nastavení](media/diagnostic-logs-stream-log-store/diagnostic-settings-none.png)

   Pokud existuje stávající nastavení na prostředek, zobrazí se seznam nastavení, které jsou už nakonfigurovaná na tento prostředek. Klikněte na tlačítko "Přidat nastavení diagnostiky."

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/diagnostic-logs-stream-log-store/diagnostic-settings-multiple.png)

3. Zadejte název nastavení a zaškrtněte políčko u **odesílat do Log Analytics**, pak vyberte pracovní prostor Log Analytics.

   ![Přidejte nastavení diagnostiky – stávající nastavení](media/diagnostic-logs-stream-log-store/diagnostic-settings-configure.png)

4. Klikněte na **Uložit**.

Po chvíli se nové nastavení se zobrazí v seznamu nastavení pro tento prostředek a diagnostické protokoly se streamují do tohoto pracovního prostoru, jakmile je vygenerována nová data události. Všimněte si, že může být až 15 minut mezi při události je vygenerován a pokud je zobrazeno v Log Analytics.

### <a name="via-powershell-cmdlets"></a>Pomocí rutin prostředí PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Pokud chcete povolit streamování prostřednictvím [rutin prostředí Azure PowerShell](../../azure-monitor/platform/powershell-quickstart-samples.md), můžete použít `Set-AzDiagnosticSetting` rutiny s těmito parametry:

```powershell
Set-AzDiagnosticSetting -ResourceId [your resource ID] -WorkspaceID [resource ID of the Log Analytics workspace] -Categories [list of log categories] -Enabled $true
```

Všimněte si, že vlastnost ID pracovního prostoru trvá úplné Azure resource ID pracovního prostoru, není pracovní prostor ID a klíč uvedené na portálu Log Analytics.

### <a name="via-azure-cli"></a>Via Azure CLI

Pokud chcete povolit streamování prostřednictvím [rozhraní příkazového řádku Azure](../../azure-monitor/platform/cli-samples.md), můžete použít [az monitor diagnostiky – nastavení vytváření](/cli/azure/monitor/diagnostic-settings#az-monitor-diagnostic-settings-create) příkazu.

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

Můžete přidat další kategorie pro protokol diagnostiky tak, že přidáte slovníky předané jako pole JSON `--logs` parametru.

`--resource-group` Argument je pouze požadováno pokud `--workspace` není ID objektu.

## <a name="how-do-i-query-the-data-in-log-analytics"></a>Jak dotaz na data v Log Analytics?

V okně hledání v protokolu na portálu nebo Advanced Analytics prostředí jako součást Log Analytics se můžete dotazovat diagnostické protokoly jako součást řešení Správa protokolů ve složce AzureDiagnostics tabulky. Existují také [několik řešení pro prostředky Azure](../../azure-monitor/insights/solutions.md) instalací získat okamžitý přehled o data protokolu odesílají do Log Analytics.

### <a name="known-limitation-column-limit-in-azurediagnostics"></a>Známá omezení: omezení počtu sloupců v AzureDiagnostics
Protože mnoho prostředků odeslat datové typy se odesílají do stejné tabulky (_AzureDiagnostics_), schéma v této tabulce je velmi sadu schémat všech různých datových typů shromažďují. Například pokud jste vytvořili nastavení diagnostiky pro kolekci následující typy dat, všechny odesílají do stejného pracovního prostoru:
- Auditovat protokoly 1 prostředek (s schématu, který se skládá ze sloupců A, B a C)  
- Protokoly chyb prostředků 2 (schéma skládající se z sloupců D, E a F s)  
- Protokoly toku dat 3 prostředků (skládající se z sloupců G, H a jsem schéma s)  
 
V tabulce AzureDiagnostics bude vypadat následovně, s ukázkovými daty:  
 
| ResourceProvider | Kategorie | A | B | C | D | E | F | G | H | I |
| -- | -- | -- | -- | -- | -- | -- | -- | -- | -- | -- |
| Microsoft.Resource1 | AuditLogs | x1 | y1 | z1 |
| Microsoft.Resource2 | ErrorLogs | | | | q1 | w1 | e1 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j1 | k1 | l1|
| Microsoft.Resource2 | ErrorLogs | | | | q2 | w2 | e2 |
| Microsoft.Resource3 | DataFlowLogs | | | | | | | j3 | k3 | l3|
| Microsoft.Resource1 | AuditLogs | x5 | y5 | z5 |
| ... |
 
Je omezený na explicitní jakékoli dané tabulky protokolu Azure nemá více než 500 sloupce. Po jeho dosažení se v době příjmu se zahodí všechny řádky obsahující data s žádným sloupcem mimo prvních 500. V tabulce AzureDiagnostics je zejména napadnutelné přes bezpečnostní bude dopad na toto omezení. To obvykle proběhne buď protože širokou škálu zdrojů dat se odesílají do stejného pracovního prostoru, nebo několik zdrojů velmi podrobné údaje odesílané do stejného pracovního prostoru. 
 
#### <a name="azure-data-factory"></a>Azure Data Factory  
Azure Data Factory, z důvodu velmi podrobné sadu protokolů, je prostředek, který je známé hlavně ovlivní tento limit. Zejména:  
- *Uživatel parametry definované pro všechny aktivity v kanálu*: bude nový sloupec, který vytvoří pro každý parametr jedinečně pojmenovaná uživatele pro všechny aktivity. 
- *Aktivita vstupy a výstupy*: tyto aktivity aktivity se liší a generují velké množství sloupců z důvodu jejich podrobné povahy. 
 
Jako s širší návrhy řešení níže, se doporučuje k izolaci ADF protokoly do své vlastní pracovní prostor, který minimalizuje riziko tyto protokoly vliv na jiné typy protokolů shromažďují ve vašich pracovních prostorů. Očekáváme, že připravili protokoly služby Azure Data Factory dostupná roku 2019 poloviny dubna.
 
#### <a name="workarounds"></a>Alternativní řešení
Krátkodobé, dokud předefinovat limit 500 sloupce, doporučujeme k oddělení do samostatných pracovních prostorů pro snížení rizika vzniku dosažení limitu podrobné datové typy.
 
Dlouhodobější, diagnostiky Azure se přesouvají směrem od jednotné a řídkých schématu do jednotlivých tabulek na jednotlivé typy dat; spárovat s podporou pro dynamické typy, značně tím zlepšíte použitelnost data přicházející do protokolů Azure pomocí Azure Diagnostics mechanismu. Už to pro uvidíte vyberte typy prostředků Azure, například [Azure Active Directory](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-analyze-activity-logs-log-analytics) nebo [Intune](https://docs.microsoft.com/intune/review-logs-using-azure-monitor) protokoly. Podívejte se prosím pro nejnovější zprávy o nové typy prostředků v Azure podporuje tyto kurátorované protokoly na [aktualizace Azure](https://azure.microsoft.com/updates/) blogu!


## <a name="next-steps"></a>Další postup

* [Další informace o diagnostických protokolech Azure](diagnostic-logs-overview.md)

