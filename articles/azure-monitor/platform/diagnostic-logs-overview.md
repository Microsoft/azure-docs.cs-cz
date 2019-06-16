---
title: Přehled o diagnostické protokoly Azure
description: Přečtěte si o Azure diagnostické protokoly Azure monitoru a jak je použít k pochopení události, ke kterým dochází v rámci prostředku Azure.
author: nkiest
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 05/20/2019
ms.author: nikiest
ms.subservice: logs
ms.openlocfilehash: 8902e29baa5802e3416bcda97ca59a5576d41829
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66244863"
---
# <a name="overview-of-azure-diagnostic-logs"></a>Přehled o diagnostické protokoly Azure

**Diagnostické protokoly** poskytují bohatě vybaveným a časté informace o fungování prostředek Azure. Azure Monitor je k dispozici dva typy diagnostických protokolů:

* **Tenant protokoly** – protokoly pocházejí z služby na úrovni tenanta, které existují mimo předplatné Azure, jako je Azure Active Directory protokoly.
* **Protokoly prostředku** – protokoly pocházejí ze služby Azure, které nasazení prostředků v rámci předplatného Azure, jako jsou skupiny zabezpečení sítě nebo účty úložiště.

    ![Protokoly diagnostiky zdroje vs jiné typy protokolů](media/diagnostic-logs-overview/Diagnostics_Logs_vs_other_logs_v5.png)

Obsah tyto protokoly se liší podle typu prostředků a služeb Azure. Například počítadla pravidel skupin zabezpečení sítě a služby Key Vault audity jsou dva typy diagnostických protokolů.

Tyto protokoly se liší od [protokolu aktivit](activity-logs-overview.md). Protokol aktivit poskytuje podrobné informace o operacích provedených na prostředky ve vašem předplatném pomocí Resource Manageru, třeba vytvoření virtuálního počítače nebo odstranění aplikace logiky. Protokol aktivit je protokol úrovně předplatného. Úroveň prostředků diagnostické protokoly poskytují přehled o operacích provedených v rámci tohoto vlastního prostředku, například získání tajného klíče ze služby Key Vault.

Tyto protokoly se také liší od hostovaného operačního systému – úroveň diagnostických protokolů. Diagnostické protokoly hostovaného operačního systému jsou tyto shromážděné agentem běžících v rámci virtuálního počítače nebo jiné podporovaný typ prostředku. Diagnostické protokoly na úrovni prostředku vyžadují specifické podle prostředků data z platformy Azure, bez agenta a zachycení, zatímco diagnostické protokoly úrovni operačního systému hosta zachytávat data z operační systém a aplikace běžící na virtuálním počítači.

Ne všechny služby podpory diagnostické protokoly, které jsou zde popsány. [Tento článek obsahuje části najdou služby, které podporují diagnostické protokoly](./../../azure-monitor/platform/diagnostic-logs-schema.md).

## <a name="what-you-can-do-with-diagnostic-logs"></a>Co můžete dělat s využitím diagnostických protokolů
Tady jsou některé věci, které vám pomůžou s využitím diagnostických protokolů:

![Logické umístění diagnostické protokoly](./media/diagnostic-logs-overview/Diagnostics_Logs_Actions.png)

* Uložte je do [ **účtu úložiště** ](../../azure-monitor/platform/archive-diagnostic-logs.md) pro auditování nebo ruční kontrolu. Můžete určit pomocí uchování (ve dnech) **nastavení diagnostiky prostředků**.
* [Stream je **Event Hubs** ](diagnostic-logs-stream-event-hubs.md) za účelem ingestování datových služby třetích stran nebo vlastních analytics řešení, jako je Power BI.
* Analyzovat pomocí [Azure Monitor](../../azure-monitor/platform/collect-azure-metrics-logs.md), kde data zapsaná okamžitě do Azure Monitor bez nutnosti nejprve zapíše data do úložiště.  

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

Můžete použít účet úložiště nebo oboru názvů Event Hubs, která není ve stejném předplatném jako ta, které vysílá protokoly. Uživatel, který konfiguruje nastavení, musí mít správný přístup RBAC k oběma předplatným.

> [!NOTE]
>  Nelze aktuálně archivovat protokoly toku network na účet úložiště, který je za zabezpečené virtuální síti.

## <a name="diagnostic-settings"></a>Nastavení diagnostiky

Protokolů diagnostiky prostředků je konfigurovat pomocí nastavení diagnostiky prostředku. Diagnostické protokoly tenanta se konfigurují pomocí nastavení diagnostiky tenanta. **Nastavení diagnostiky** pro řízení služeb:

* Diagnostické protokoly a metriky se odešle (účet úložiště, Event Hubs a Azure Monitor).
* Kategorie protokolu, které se odesílají a určuje, zda metrika data jsou taktéž odeslána.
* Jak dlouho se uchovávají každou kategorii protokolů v účtu úložiště.
    - Uchování 0 dnů znamená, že protokoly se uchovávají navždy. V opačném případě hodnota může být libovolný počet dnů od 1 do 365.
    - Pokud nejsou nastavené zásady uchovávání informací, ale ukládání protokolů v účtu úložiště je zakázaný (například pokud pouze jsou vybrané možnosti služby Event Hubs nebo Log Analytics), zásady uchovávání informací nemají žádný vliv.
    - Zásady uchovávání informací jsou použitých za den, takže na konci za den (UTC), tento počet protokolů ze dne, který je nyní mimo uchovávání se zásada odstraní. Například pokud máte zásady uchovávání informací o jeden den, na začátku dne dnes protokoly ze včerejška před den se odstraní. Proces odstraňování začíná o půlnoci UTC, ale Všimněte si, že může trvat až 24 hodin pro protokoly, které mají být odstraněny z vašeho účtu úložiště.

Tato nastavení jsou nakonfigurované v nastavení diagnostiky na portálu s příkazy prostředí Azure PowerShell a rozhraní příkazového řádku nebo pomocí [REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/).

> [!NOTE]
> Odesílání vícedimenzionálních metrik přes nastavení diagnostiky se v současné době nepodporuje. Metriky s dimenzemi se exportují jako ploché jednodimenzionální metriky agregované napříč hodnotami dimenzí.
>
> *Například*: Metrika 'Příchozí zprávy' v Centru událostí můžete prozkoumat a převést na graf úrovni jednotlivých front. Pokud se však metrika exportuje přes nastavení diagnostiky, bude reprezentovaná jako všechny příchozí zprávy ve všech frontách v centru událostí.
>
>

## <a name="supported-services-categories-and-schemas-for-diagnostic-logs"></a>Podporované služby, kategorie a schémata pro diagnostické protokoly

[Najdete v článku](../../azure-monitor/platform/diagnostic-logs-schema.md) úplný seznam podporovaných služeb a kategorie protokolu a schémat, které používají tyto služby.

## <a name="next-steps"></a>Další postup

* [Stream protokolů diagnostiky prostředků k **Event Hubs**](diagnostic-logs-stream-event-hubs.md)
* [Změnit nastavení diagnostiky prostředků pomocí REST API služby Azure Monitor](https://docs.microsoft.com/rest/api/monitor/)
* [Analýza protokolů ze služby Azure storage pomocí Azure monitoru](collect-azure-metrics-logs.md)
