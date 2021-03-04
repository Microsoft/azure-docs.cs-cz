---
title: Přehled centra zálohování
description: Tento článek poskytuje přehled služby Backup Center pro Azure.
ms.topic: conceptual
ms.date: 09/30/2020
ms.openlocfilehash: fee7bf41d0c72238231d9c4aba52a26cac9fb960
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102099994"
---
# <a name="overview-of-backup-center"></a>Přehled centra zálohování

Centrum zálohování nabízí v Azure **jednotně sjednocené prostředí pro správu** , které umožňuje podnikům řídit, monitorovat, provozovat a analyzovat zálohy ve velkém měřítku. V takovém případě je konzistentní s nativním prostředím pro správu Azure.

Mezi klíčové výhody služby Backup Center patří:

* **Samostatné podokno pro správu zálohování** – centrum zálohování je navržené tak, aby fungovalo dobře v rozsáhlém a distribuovaném prostředí Azure. Pomocí služby Backup Center můžete efektivně spravovat zálohy zahrnující různé typy úloh, trezory, odběry, oblasti a klienty [Azure Lighthouse](../lighthouse/overview.md) .
* Správa zaměřená na **zdroje dat** – centrum zálohování poskytuje zobrazení a filtry, které jsou soustředěné do zdrojů dat, které zálohujete (například virtuální počítače a databáze). Vlastník prostředku nebo správci zálohování tak můžou monitorovat a provozovat zálohy položek, aniž by se museli soustředit na to, na který trezor se položka zálohuje. Klíčovou funkcí tohoto návrhu je schopnost filtrovat zobrazení podle vlastností specifických pro zdroj dat, jako je například předplatné zdroje dat, skupina prostředků zdroje dat a značky DataSource. Pokud například vaše organizace postupuje při přiřazování různých značek k virtuálním počítačům patřícím do různých oddělení, můžete pomocí centra zálohování filtrovat informace o zálohování na základě značek zálohovaných podkladových virtuálních počítačů, aniž byste se museli soustředit na značku trezoru.
* **Prostředí s připojením** – služba Backup Center poskytuje nativní integraci do stávajících služeb Azure, které umožňují správu ve velkém měřítku. Centrum zálohování například používá [Azure Policy](../governance/policy/overview.md) prostředí, které vám pomůžou s tím, jak se budou vaše zálohy řídit. Také využívá [sešity Azure](../azure-monitor/visualize/workbooks-overview.md) a [protokoly Azure monitor](../azure-monitor/logs/data-platform-logs.md) , které vám pomůžou zobrazit podrobné sestavy o zálohování. Takže nemusíte se učit žádné nové principy používání různých funkcí, které centrum zálohování nabízí. Můžete také zjistit komunitní prostředky ze služby Backup Center.

## <a name="supported-scenarios"></a>Podporované scénáře

* Centrum zálohování se v současné době podporuje pro zálohování virtuálních počítačů Azure, SQL v zálohování virtuálních počítačů Azure, SAP HANA v zálohování virtuálních počítačů Azure, zálohování souborů Azure, zálohování objektů blob Azure, zálohování Azure Managed Disks a Azure Database for PostgreSQL zálohování serveru.
* Podrobný seznam podporovaných a nepodporovaných scénářů najdete v tématu věnovaném [matrici podpory](backup-center-support-matrix.md) .

## <a name="get-started"></a>Začínáme

Pokud chcete začít používat centrum zálohování, vyhledejte **Centrum zálohování** v Azure Portal a přejděte na řídicí panel **služby Backup Center** .

![Hledání ve službě Backup Center](./media/backup-center-overview/backup-center-search.png)

První zobrazená obrazovka je **Přehled**. Obsahuje dvě dlaždice – **úlohy** a **instance zálohování**.

![Dlaždice centra zálohování](./media/backup-center-overview/backup-center-overview-widgets.png)

V dlaždici **úlohy** získáte souhrnné zobrazení všech úloh týkajících se zálohování a obnovení, které byly aktivovány v rámci služby zálohování za posledních 24 hodin. Můžete si prohlédnout informace o počtu úloh, které se dokončily, nezdařily a probíhají. Výběrem kteréhokoli z čísel v této dlaždici můžete zobrazit další informace o úlohách pro určitý typ zdroje dat, typ operace a stav.

Na dlaždici **zálohování instancí** získáte souhrnné zobrazení všech instancí zálohování napříč vaší zálohou. Můžete například zobrazit počet instancí zálohy, které jsou ve stavu tichého odstranění v porovnání s počtem instancí, které jsou pro ochranu stále nakonfigurovány. Výběr kterékoli z čísel v této dlaždici vám umožní zobrazit další informace o instancích zálohy pro určitý typ zdroje dat a stav ochrany. Můžete také zobrazit všechny instance zálohování, jejichž podkladový zdroj dat nebyl nalezen (zdroj dat může být odstraněn nebo nemáte přístup ke zdroji dat).

Podívejte se na následující video a pochopte možnosti centra zálohování:

> [!VIDEO https://www.youtube.com/embed/pFRMBSXZcUk?t=497]

Postupujte podle [dalších kroků](#next-steps) a pochopte různé možnosti, které poskytuje centrum zálohování, a způsob, jak můžete tyto možnosti využít ke efektivní správě služby Backup.

## <a name="next-steps"></a>Další kroky

* [Monitorování a provozování záloh](backup-center-monitor-operate.md)
* [Řízení vaší záložní nemovitosti](backup-center-govern-environment.md)
* [Získejte přehled o vašich zálohách](backup-center-obtain-insights.md)
* [Provádění akcí pomocí centra zálohování](backup-center-actions.md)