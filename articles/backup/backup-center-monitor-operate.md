---
title: Monitorování a správa zálohování s využitím centra zálohování
description: Tento článek vysvětluje, jak monitorovat a provozovat zálohy ve velkém měřítku pomocí centra zálohování.
ms.topic: conceptual
ms.date: 09/01/2020
ms.openlocfilehash: 29e09e502e83ea67af290f206ee0e68b847b2069
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90995272"
---
# <a name="monitor-and-operate-backups-using-backup-center"></a>Monitorování a správa zálohování s využitím centra zálohování

Jako správce zálohování můžete pomocí služby Backup Center monitorovat vaše úlohy a inventář záloh na každodenní bázi. Pomocí služby Backup Center můžete také provádět běžné operace, jako je třeba reakce na požadavky na zálohování na vyžádání, obnovení záloh, vytváření zásad zálohování atd.

## <a name="supported-scenarios"></a>Podporované scénáře

* Centrum zálohování se v současné době podporuje pro zálohování virtuálních počítačů Azure a zálohování serveru Azure Database for PostgreSQL.
* Podrobný seznam podporovaných a nepodporovaných scénářů najdete v tématu věnovaném [matrici podpory](backup-center-support-matrix.md) .

## <a name="backup-instances"></a>Instance zálohování

Centrum zálohování umožňuje snadné vyhledávání a zjistitelnost instancí zálohování napříč vaší zálohou.

Výběrem karty **instance zálohování** v centru zálohování můžete zobrazit podrobnosti o všech instancích služby Backup, ke kterým máte přístup.

 Můžete si zobrazit následující informace o jednotlivých instancích služby Backup:

* Předplatné zdroje dat
* Skupina prostředků DataSource
* Nejnovější bod obnovení
* Trezor přidružený k instanci zálohování
* Stav ochrany

 Seznam instancí zálohy můžete filtrovat také v následujících parametrech:

* Předplatné zdroje dat
* Skupina prostředků DataSource
* Umístění zdroje dat
* Typ zdroje dat
* Trezor
* Stav ochrany
* Značky DataSource

Kliknutím pravým tlačítkem myši na libovolnou položku v mřížce můžete v dané instanci zálohování provádět akce, jako je například navigace k prostředku, aktivace zálohování a obnovení na vyžádání nebo zastavení zálohování.

![Centrum zálohování – instance](./media/backup-center-monitor-operate/backup-center-instances.png)

## <a name="backup-jobs"></a>Úlohy zálohování

Centrum zálohování umožňuje zobrazit podrobné informace o všech úlohách, které byly vytvořeny v rámci své zálohy, a provádět příslušné akce při selhání úloh.

Výběr položky nabídky **úlohy zálohování** v centru zálohování nabízí zobrazení všech úloh. Každá úloha obsahuje následující informace:

* Instance zálohy přidružená k úloze
* Předplatné zdroje dat
* Skupina prostředků DataSource
* Umístění zdroje dat
* Operace úlohy
* Stav úlohy
* Čas spuštění úlohy
* Doba trvání úlohy

Výběr položky v mřížce vám umožní zobrazit další podrobnosti o dané úloze. Kliknutí pravým tlačítkem myši na položku vám pomůže přejít k prostředku, aby bylo možné provést potřebnou akci.

![Centrum zálohování – úlohy](./media/backup-center-monitor-operate/backup-center-jobs.png)

Pomocí karty **úlohy zálohování** můžete zobrazit úlohy až do posledních sedmi dnů. Chcete-li zobrazit starší úlohy, použijte [sestavy zálohování](backup-center-obtain-insights.md).

## <a name="vaults"></a>Trezory

Výběr položky nabídky **trezory** v centru zálohování vám umožní zobrazit seznam všech [trezorů Recovery Services](backup-azure-recovery-services-vault-overview.md) a [trezorů záloh](backup-vault-overview.md) , ke kterým máte přístup. Seznam můžete filtrovat s následujícími parametry:

* Předplatné trezoru
* Skupina prostředků trezoru
* Název trezoru
* Typ zdroje dat přidružený k zásadě

Výběr libovolné položky v seznamu vám umožní přejít do daného trezoru.

![Centrum zálohování – trezory](./media/backup-center-monitor-operate/backup-center-vaults.png)

## <a name="backup-policies"></a>Zásady zálohování

Centrum zálohování umožňuje zobrazit a upravit informace o klíčích pro všechny zásady zálohování.

Výběrem položky nabídky **zásady zálohování** můžete zobrazit všechny zásady, které jste vytvořili v rámci své služby zálohování. Seznam můžete filtrovat podle předplatného trezoru, skupiny prostředků, typu zdroje dat a trezoru. Kliknutím pravým tlačítkem myši na položku v mřížce můžete zobrazit přidružené položky pro tuto zásadu, upravit zásadu nebo dokonce v případě potřeby odstranit.

![Centrum zálohování – zásady](./media/backup-center-monitor-operate/backup-center-policies.png)

## <a name="next-steps"></a>Další kroky

* [Řízení vaší záložní nemovitosti](backup-center-govern-environment.md)
* [Provádění akcí pomocí centra zálohování](backup-center-actions.md)
* [Získejte přehled o vašich zálohách](backup-center-obtain-insights.md)
