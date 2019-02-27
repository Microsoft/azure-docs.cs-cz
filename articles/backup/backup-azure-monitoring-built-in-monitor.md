---
title: 'Azure Backup: Monitorování Azure Backup chráněných pracovních vytížení'
description: Monitorování úloh Azure Backup pomocí webu Azure portal
services: backup
author: pvrk
manager: shivamg
keywords: Azure Backup; Výstrahy;
ms.service: backup
ms.topic: conceptual
ms.date: 02/26/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 2513f2e7a2e82a541fa5638b70d0543192c84765
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/26/2019
ms.locfileid: "56885351"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorování úloh Azure Backup

Azure Backup poskytuje několik řešení zálohování na základě zálohování požadavek a infrastruktury topologie (místní nebo Azure). Žádné zálohy uživatelem nebo správcem měli vidět, co se děje ve všech řešení a má být upozorněni důležitých scénářů. Tento článek podrobně popisuje možnosti monitorování a oznámení poskytovaný službou Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Úlohy zálohování v trezoru služby Recovery Services

Azure Backup poskytuje integrované monitorování a upozorňování pro úlohy, které jsou chráněné službou Azure Backup. V nastavení trezor služby Recovery Services v části 'monitorování' poskytuje integrované úlohy a výstrahy.

![RS trezoru, integrované monitorování](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Úlohy jsou generovány, pokud se provádí operace, jako jsou konfigurace zálohování operace zálohování, operaci obnovení, odstranění zálohování atd.
Úlohy z následujících řešení Azure Backup se tady zobrazí.

- Zálohování virtuálních počítačů Azure
- Zálohování souborů Azure
- Zálohování úloh Azure jako SQL
- Agent Azure Backup (MAB)

Úlohy ze System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) nejsou zobrazeny zde.

> [!NOTE]
> S ohledem na úlohy v Azure jako je například SQL záloh v rámci virtuálních počítačů Azure jsou velký počet úloh zálohování. Například můžete spustit zálohování protokolů pro každých 15 minut. Pro tyto úlohy DB proto operace pouze uživatele, aktivuje se zobrazí. Plánovaná operace zálohování se nezobrazí.

## <a name="backup-alerts-in-recovery-services-vault"></a>Výstrahy záloh v trezoru služby Recovery Services

Výstrahy jsou primárně scénáře, kde zákazník musí získat oznámení tak, aby mohl reagovat relevantní. V části "Výstrahy zálohování" zobrazuje výstrahy generované službou Azure Backup. Tyto výstrahy jsou definovány službou a uživatel nemůže vlastní vytvořit všechny výstrahy. Následující scénáře jsou definovány službou jako výstražné scénáře

- Selhání zálohování a obnovení
- Zálohování bylo úspěšně dokončeno s upozorněními.
- Zastavte ochranu při zachování dat a zastavení ochrany a odstraňte data

Existují několika výjimkami, kdy není vyvolána výstraha při selhání.

- Uživatel zrušil explicitně běžící úlohu
- Úloha se nezdaří, protože jiná úloha zálohování se (není nic tak, aby fungoval na tady, protože máme pouze čekat na dokončení předchozí úlohy)
- Úlohy zálohování virtuálního počítače se nezdaří, protože virtuální počítač Azure zálohovanou už neexistuje.

Výše uvedené výjimky jsou navržené tak, že z souhlasem, že výsledkem těchto operací (primárně uživatel aktivuje) se zobrazí okamžitě na klientech portál/PS/CLI. Uživatel proto okamžitě zohledňuje a nemusí oznámení.

Výstrahy od následujících řešení Azure Backup se tady zobrazí.

- Zálohování virtuálních počítačů Azure
- Zálohování souborů Azure
- Zálohování úloh Azure jako SQL
- Agent Azure Backup (MAB)

> [!NOTE]
> Zde nejsou zobrazeny výstrahy ze System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS).

Podle závažnost výstrahy, oznámení lze definovat v tři typy:

- **Důležité:** Jakékoli neúspěchy zálohování nebo obnovení (naplánované nebo aktivované uživatele) v zásadě, by mohlo dojít k generování výstrahy a bude zobrazený jako kritickou výstrahu. Také destruktivní operace, jako je zálohování odstranit také vygeneruje kritické výstrahy.
- **Upozornění**: Pokud operace zálohování byla úspěšná, ale s několika varováními, jsou uvedeny jako upozorňující výstrahy.
- **Informační**: K dnešnímu dni žádná informační výstraha vygeneruje služby Azure Backup.

### <a name="notification-for-backup-alerts"></a>Oznámení pro výstrahy zálohování

> [!NOTE]
> Konfigurace oznámení může provést jenom prostřednictvím portálu Azure Portal. Podpora PS nebo rozhraní příkazového řádku nebo rozhraní REST API nebo Azure Resource Manageru šablony není k dispozici dosud.

Jakmile je vyvolána výstraha, zákazník potřebuje dostanete oznámení. Azure Backup poskytuje mechanismus interních oznámení prostřednictvím e-mailu. Jeden můžete zadat jednotlivé e-mailové adresy nebo distribuční skupiny, která vás upozorní, když získá vygenerována výstraha. Můžete také zvolit, jestli se chcete dostat upozornění pro každé jednotlivé výstrahy nebo má seskupovat je do hodinový přehled a pak dostanete oznámení.

![Trezor RS interních e-mailová oznámení](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Jakmile začnete konfigurovat oznámení, zobrazí se úvodní/uvítací e-mail, kterým se potvrdí, že Azure Backup můžete posílat e-maily na tyto adresy, když je vydána výstraha. Pokud frekvence je nastavená na hodinový přehled a výstraha se vyvolá a vyřeší během hodiny, nebude součástí budoucích hodinový přehled.

> [!NOTE]
> Pokud provádíte destruktivní operace, jako například "zastavení ochrany a odstraňte data", je vyvolána výstraha a odešle e-mail na vlastníky těchto předplatných, správci a spolusprávci i v případě, že oznámení nakonfigurovaná není pro trezor RS.

## <a name="next-steps"></a>Další postup

[Monitorování úloh Azure backup pomocí Azure monitoru](backup-azure-monitoring-use-azuremonitor.md)