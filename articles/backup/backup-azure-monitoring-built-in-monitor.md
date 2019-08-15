---
title: 'Azure Backup: Monitorování Azure Backup chráněných úloh'
description: Monitorování úloh Azure Backup pomocí Azure Portal
ms.reviewer: pullabhk
author: dcurwin
manager: carmonm
keywords: Azure Backup; Generoval
ms.service: backup
ms.topic: conceptual
ms.date: 03/05/2019
ms.author: dacurwin
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: bae05e890ef76ada9f775293c673cb8baa82c8bf
ms.sourcegitcommit: 0f54f1b067f588d50f787fbfac50854a3a64fff7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/12/2019
ms.locfileid: "68954586"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorování úloh Azure Backup

Azure Backup poskytuje několik zálohovacích řešení na základě požadavků na zálohování a topologie infrastruktury (místní vs Azure). Každý uživatel nebo správce zálohování by měl vidět, co se ve všech řešeních a očekává, že budou informováni v důležitých scénářích. Tento článek podrobně popisuje možnosti monitorování a oznámení poskytované službou Azure Backup Service.

## <a name="backup-jobs-in-recovery-services-vault"></a>Úlohy zálohování v trezoru Recovery Services

Azure Backup poskytuje integrované funkce monitorování a upozorňování pro úlohy, které chrání Azure Backup. V nastavení trezoru Recovery Services jsou v části **monitorování** uvedeny předem vytvořené úlohy a výstrahy.

![Integrované monitorování trezoru RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Úlohy se generují, pokud se provádí operace, jako je konfigurace zálohování, zálohování, obnovení, odstranění zálohy atd.

Tady jsou uvedené úlohy z následujících řešení Azure Backup:

  - Zálohování virtuálních počítačů Azure
  - Zálohování souborů Azure
  - Zálohování úloh Azure, jako je SQL
  - Agent Azure Backup (MAB)

Úlohy z nástroje System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) se nezobrazí.

> [!NOTE]
> Úlohy Azure, jako jsou zálohy SQL v rámci virtuálních počítačů Azure, mají velký počet úloh zálohování. Například zálohování protokolů může běžet každých 15 minut. Proto se pro tyto úlohy databáze zobrazí jenom operace aktivované uživatelem. Naplánované operace zálohování se nezobrazují.

## <a name="backup-alerts-in-recovery-services-vault"></a>Výstrahy zálohování v trezoru Recovery Services

Výstrahy jsou primárně ve scénářích, kdy se uživatelům pošle oznámení, aby mohli provádět příslušné akce. Část **výstrahy zálohování** zobrazuje výstrahy vygenerované službou Azure Backup. Tyto výstrahy definuje služba a uživatel nemůže vlastní výstrahy vytvořit.

### <a name="alert-scenarios"></a>Scénáře upozornění
Následující scénáře jsou definovány službou jako scénáře s možností upozorňování.

  - Selhání zálohování nebo obnovení
  - Zálohování proběhlo úspěšně s upozorněními pro agenta Azure Backup (MAB)
  - Zastavení ochrany s uchováním dat/zastavení ochrany pomocí odstranění dat

### <a name="exceptions-when-an-alert-is-not-raised"></a>Výjimky při vyvolání výstrahy
Existuje několik výjimek, pokud není při selhání vyvolána výstraha, jedná se o tyto akce:

  - Uživatel explicitně zrušil spuštěnou úlohu.
  - Úloha se nezdařila, protože právě probíhá jiná úloha zálohování (nic se nestane, protože musíme počkat na dokončení předchozí úlohy)
  - Úloha zálohování virtuálního počítače selhala, protože zálohovaný virtuální počítač Azure už neexistuje.

Výše uvedené výjimky jsou navržené z porozumění, že výsledek těchto operací (primárně aktivovaný uživatelem) se okamžitě zobrazuje na klientech portálu, PS/CLI. Proto je uživatel okamžitě informován a nepotřebuje oznámení.

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Tady jsou uvedené výstrahy z následujících řešení Azure Backup:

  - Zálohování virtuálních počítačů Azure
  - Zálohování souborů Azure
  - Zálohování úloh Azure, jako je SQL
  - Agent Azure Backup (MAB)

> [!NOTE]
> Výstrahy z nástroje System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) se tady nezobrazují.

### <a name="alert-types"></a>Typy výstrah
Na základě závažnosti výstrahy je možné výstrahy definovat ve třech typech:

  - **Kritické**: V zásadě by jakékoli selhání zálohování nebo obnovení (naplánované nebo aktivované uživatelem) vedlo k vytvoření výstrahy a mělo by se zobrazit jako kritická výstraha a také ničivé operace, jako je odstranění zálohy.
  - **Upozornění**: Pokud se operace zálohování zdaří úspěšně, ale s malým počtem upozornění, zobrazí se jako varovné výstrahy.
  - **Informační**: Od dnešního dne se služba Azure Backup Service negeneruje žádná informační výstraha.

## <a name="notification-for-backup-alerts"></a>Oznámení pro výstrahy zálohování

> [!NOTE]
> Konfigurace oznámení se dá provést jenom prostřednictvím webu Azure Portal. Podpora šablon PS/CLI/REST API/Azure Resource Manager není podporována.

Po vyvolání výstrahy budou uživatelé upozorněni. Azure Backup poskytuje integrovaný mechanizmus oznámení prostřednictvím e-mailu. Jedním z nich může být zadání jednotlivých e-mailových adres nebo distribučních seznamů, které budou oznamovány při vygenerování výstrahy. Můžete si také vybrat, jestli se má zobrazit upozornění na každou jednotlivou výstrahu, nebo je seskupit do hodinových Digest a pak získat oznámení.

![E-mailové oznámení trezoru RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

> [!NOTE]
> Výstrahy pro zálohy SQL budou konsolidovány a e-mail bude odeslán pouze pro první výskyt. Pokud se ale výstraha neaktivuje uživatelem, další výskyt spustí další e-mail.

Po nakonfigurování oznámení obdržíte uvítací nebo úvodní e-mail. Tím se potvrdí, že při vyvolání výstrahy Azure Backup může posílat e-maily na tyto adresy.<br>

Pokud byla frekvence nastavena na hodinový výtah a výstraha byla vyvolána a vyřešena během hodiny, nebude součástí nadcházející hodinové mineralizace.

> [!NOTE]
>
> * Pokud se provede destruktivní operace, jako je **zastavení ochrany pomocí odstranit data** , vygeneruje se výstraha a vlastníkům předplatného, správcům a spolupracovníkům se pošle e-mail, i když pro trezor služby RECOVERy není nakonfigurované oznámení.
> * K nakonfigurování oznámení pro úspěšné úlohy použijte [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Deaktivace výstrah

Pokud chcete deaktivovat nebo vyřešit aktivní výstrahu, můžete kliknout na položku seznamu, která odpovídá výstraze, kterou chcete deaktivovat. Otevře se obrazovka, která zobrazí podrobné informace o výstraze s tlačítkem "deaktivovat" v horní části. Po kliknutí na toto tlačítko se změní stav výstrahy na neaktivní. Výstrahu můžete také deaktivovat pouhým kliknutím pravým tlačítkem myši na položku seznamu, která odpovídá dané výstraze, a výběrem možnosti deaktivovat.

![Deaktivace výstrah trezoru RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)



## <a name="next-steps"></a>Další postup

[Monitorování úloh Azure Backup pomocí Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
