---
title: Získání přehledů pomocí centra zálohování
description: Naučte se analyzovat historické trendy a získat hlubší přehled o zálohách pomocí centra zálohování.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: c0f687ee123abe2f95ad0d23a6fe302f4a5c22e1
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173757"
---
# <a name="obtain-insights-using-backup-center"></a>Získání přehledů pomocí centra zálohování

K analýze historických trendů a získání hlubších přehledů o zálohách poskytuje centrum zálohování rozhraní pro [zálohování sestav](configure-reports.md), které používá [protokoly Azure monitor](../azure-monitor/platform/data-platform-logs.md) a [sešity Azure](../azure-monitor/platform/workbooks-overview.md). Sestavy zálohování nabízí následující možnosti:

- Přidělování a prognózování spotřebovaného cloudového úložiště.

- Auditování záloh a obnovení.

- Identifikujte klíčové trendy v různých úrovních členitosti.

- Získejte přehled o příležitostech k optimalizaci nákladů pro vaše zálohy a přehledy.

## <a name="supported-scenarios"></a>Podporované scénáře

- Sestavy zálohování nejsou aktuálně k dispozici pro zálohování Azure Database for PostgreSQL serveru.

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