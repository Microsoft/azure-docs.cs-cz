---
title: Získání přehledů pomocí centra zálohování
description: Naučte se analyzovat historické trendy a získat hlubší přehled o zálohách pomocí centra zálohování.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 3086224e4dbb1be2ccfadbfcfcb7441b68a5ff6b
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/17/2021
ms.locfileid: "100592021"
---
# <a name="obtain-insights-using-backup-center-preview"></a>Získání přehledů pomocí centra zálohování (Preview)

K analýze historických trendů a získání hlubších přehledů o zálohách poskytuje centrum zálohování rozhraní pro [zálohování sestav (Preview)](configure-reports.md), které používá [protokoly Azure monitor](../azure-monitor/logs/data-platform-logs.md) a [sešity Azure](../azure-monitor/visualize/workbooks-overview.md). Sestavy zálohování nabízí následující možnosti:

- Přidělování a prognózování spotřebovaného cloudového úložiště.

- Auditování záloh a obnovení.

- Identifikujte klíčové trendy v různých úrovních členitosti.

- Získejte přehled o příležitostech k optimalizaci nákladů pro vaše zálohy a přehledy.

## <a name="supported-scenarios"></a>Podporované scénáře

- Sestavy zálohování (Preview) nejsou aktuálně k dispozici pro zálohování Azure Database for PostgreSQL serveru.

- Podrobný seznam podporovaných a nepodporovaných scénářů najdete v tématu věnovaném [matrici podpory](backup-center-support-matrix.md) .

## <a name="get-started"></a>Začínáme

### <a name="configure-your-vaults-to-send-data-to-a-log-analytics-workspace"></a>Konfigurace trezorů pro posílání dat do Log Analyticsho pracovního prostoru

[Naučte se konfigurovat nastavení diagnostiky ve velkém měřítku pro vaše trezory.](./configure-reports.md#get-started)

### <a name="view-backup-reports-in-the-backup-center-portal"></a>Zobrazení sestav zálohování na portálu služby Backup Center

Po výběru položky nabídky **sestavy zálohování** v centru zálohování se otevřou sestavy. Vyberte jeden nebo více pracovních prostorů Log Analytics pro zobrazení a analýzu klíčových informací o zálohování.

![Sestavy zálohování v centru záloh](./media/backup-center-obtain-insights/backup-center-backup-reports.png)

K dispozici jsou následující zobrazení:

1. **Souhrn** – pomocí této karty získáte podrobný přehled vaší služby zálohování. [Další informace](./configure-reports.md#summary)

1. **Zálohované položky** – pomocí této karty můžete zobrazit informace a trendy v cloudovém úložišti spotřebovaném na úrovni záložních položek. [Další informace](./configure-reports.md#backup-items)

1. **Využití** – pomocí této karty můžete zobrazit klíčové parametry fakturace pro vaše zálohy. [Další informace](./configure-reports.md#usage)

1. **Úlohy** – pomocí této karty můžete zobrazit dlouhotrvající trendy na úlohách, jako je počet neúspěšných úloh za den a nejvyšší příčiny selhání úlohy. [Další informace](./configure-reports.md#jobs)

1. **Zásady** – pomocí této karty můžete zobrazit informace o všech aktivních zásadách, jako je třeba počet přidružených položek a celkové cloudové úložiště spotřebované položkami zálohovanými v rámci dané zásady. [Další informace](./configure-reports.md#policies)

1. **Optimalizovat** – pomocí této karty získáte přehled o potenciálních možnostech optimalizace nákladů pro vaše zálohy. [Další informace](./configure-reports.md#optimize)

## <a name="next-steps"></a>Další kroky

- [Monitorování a provozování záloh](backup-center-monitor-operate.md)
- [Řízení vaší záložní nemovitosti](backup-center-govern-environment.md)
- [Provádění akcí pomocí centra zálohování](backup-center-actions.md)