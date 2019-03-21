---
title: 'Azure Backup: Monitorování Azure Backup chráněných pracovních vytížení'
description: Monitorování úloh Azure Backup pomocí webu Azure portal
services: backup
author: pvrk
manager: shivamg
keywords: Azure Backup; Výstrahy;
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: pullabhk
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 8d3e3257f16fe4e0f846c2268bfefc2771387de6
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "58112496"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorování úloh Azure Backup

Azure Backup poskytuje několik řešení zálohování na základě zálohování požadavek a infrastruktury topologie (místní nebo Azure). Každý záložní uživatel nebo správce měli vidět, co se děje ve všech řešení a má být upozorněni důležitých scénářů. Tento článek podrobně popisuje možnosti monitorování a oznámení poskytovaný službou Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Úlohy zálohování v trezoru služby Recovery Services

Azure Backup poskytuje integrované monitorování a upozorňování pro úlohy, které jsou chráněné službou Azure Backup. V služby Recovery Services vault nastavení, **monitorování** část obsahuje integrované úlohy a výstrahy.

![RS trezoru, integrované monitorování](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Úlohy jsou generovány, pokud se provádí operace, jako jsou konfigurace zálohování, zálohování, obnovení, odstranění zálohování a tak dále.

Úlohy z následujících řešení Azure Backup se tady zobrazí:

  - Zálohování virtuálních počítačů Azure
  - Zálohování souborů Azure
  - Zálohování úloh Azure jako SQL
  - Agent Azure Backup (MAB)

Úlohy ze System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) nejsou zobrazeny.

> [!NOTE]
> Azure úlohy, jako je například SQL záloh v rámci virtuálních počítačů Azure mají velký počet úloh zálohování. Například můžete spustit zálohování protokolů pro každých 15 minut. Pro tyto úlohy DB proto operace pouze uživatele, aktivuje se zobrazí. Plánovaná operace zálohování se nezobrazí.

## <a name="backup-alerts-in-recovery-services-vault"></a>Výstrahy záloh v trezoru služby Recovery Services

Výstrahy jsou primárně scénáře, ve kterém se uživatelům oznamuje tak, aby mohl reagovat relevantní. **Výstrahy zálohování** část zobrazuje výstrahy generované službou Azure Backup. Tyto výstrahy jsou definovány službou a uživatel nemůže vlastní vytvořit všechny výstrahy.

### <a name="alert-scenarios"></a>Scénáře výstrah
Následující scénáře jsou definovány službou jako výstražné scénáře.

  - Selhání zálohování a obnovení
  - Zálohování bylo úspěšně dokončeno s upozorněními.
  - Zastavte ochranu při zachování dat a zastavení ochrany a odstraňte data

### <a name="exceptions-when-an-alert-is-not-raised"></a>Výjimky, pokud není vyvolána výstraha
Existují několika výjimkami, kdy není vyvolána výstraha při selhání, jsou:

  - Uživatel zrušil explicitně běžící úlohu
  - Úloha se nezdaří, protože jiná úloha zálohování se (není nic tak, aby fungoval na tady, protože máme pouze čekat na dokončení předchozí úlohy)
  - Úlohy zálohování virtuálního počítače se nezdaří, protože virtuální počítač Azure zálohovanou už neexistuje.

Výše uvedené výjimky jsou navržené tak, že z souhlasem, že výsledkem těchto operací (primárně uživatel aktivuje) se zobrazí okamžitě na klientech portál/PS/CLI. Uživatel proto okamžitě zohledňuje a nemusí oznámení.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Výstrahy od následujících řešení Azure Backup se tady zobrazí:

  - Zálohování virtuálních počítačů Azure
  - Zálohování souborů Azure
  - Zálohování úloh Azure jako SQL
  - Agent Azure Backup (MAB)

> [!NOTE]
> Zde nejsou zobrazeny výstrahy ze System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS).

### <a name="alert-types"></a>Typy výstrah
Podle závažnost výstrahy, oznámení lze definovat v tři typy:

  - **Kritické**: V zásadě, žádné zálohování nebo obnovení selhání (naplánované nebo aktivované uživatele) by mohlo dojít k generování výstrahy a by se zobrazovat jako kritickou výstrahu a také destruktivní operace, jako je odstranit zálohy.
  - **Upozornění**: Pokud je záložní operace úspěšná, ale s několika varováními, jsou uvedeny jako upozorňující výstrahy.
  - **Informační**: K dnešnímu dni žádná informační výstraha vygeneruje služby Azure Backup.

## <a name="notification-for-backup-alerts"></a>Oznámení pro výstrahy zálohování

> [!NOTE]
> Konfigurace oznámení může provést jenom prostřednictvím portálu Azure Portal. Není dostupná podpora šablony PS nebo rozhraní příkazového řádku nebo rozhraní REST API nebo Azure Resource Manageru.

Jakmile je vyvolána výstraha, uživatelům se zobrazí oznámení. Azure Backup poskytuje mechanismus interních oznámení prostřednictvím e-mailu. Jeden můžete zadat jednotlivé e-mailové adresy nebo distribuční skupiny, která vás upozorní, když je vygenerována výstraha. Můžete také zvolit, jestli se chcete dostat upozornění pro každé jednotlivé výstrahy nebo má seskupovat je do hodinový přehled a pak dostanete oznámení.

![Trezor RS interních e-mailová oznámení](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Pokud je nakonfigurovaná oznámení, zobrazí se úvodní nebo úvodní e-mailu. Tím potvrdíte, že Azure Backup můžete posílat e-maily na tyto adresy, když je vydána výstraha.<br>

Pokud frekvence je nastavená na hodinový přehled a výstraha se vyvolá a vyřeší během hodiny, nebude součástí budoucích hodinový přehled.

> [!NOTE]
> 
> * Pokud destruktivní operace, jako **zastavení ochrany a odstraňte data** je provést, je vyvolána výstraha a odešle e-mail na vlastníky těchto předplatných, správci a spolusprávci i v případě, že oznámení nakonfigurovaná není pro obnovení služby trezor.
> * Konfigurace oznámení pro úspěšné úlohy používají [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="next-steps"></a>Další postup

[Monitorování úloh Azure backup pomocí Azure monitoru](backup-azure-monitoring-use-azuremonitor.md)
