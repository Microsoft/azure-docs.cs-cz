---
title: Monitorování Azure Backup chráněných úloh
description: V tomto článku se seznámíte s možnostmi monitorování a oznamování pro Azure Backup úlohy pomocí Azure Portal.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: 74669a1347fac9f61d028d9cb1f3da174bb71f96
ms.sourcegitcommit: 5b926f173fe52f92fcd882d86707df8315b28667
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/04/2021
ms.locfileid: "99550335"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorování úloh Azure Backup

Azure Backup poskytuje několik zálohovacích řešení na základě požadavků na zálohování a topologie infrastruktury (místní vs Azure). Každý uživatel, který má záložní účet nebo správce, by měl vidět, co se ve všech řešeních provede, a očekává se, že bude v důležitých scénářích upozorněn Tento článek podrobně popisuje možnosti monitorování a oznámení poskytované službou Azure Backup Service.

[!INCLUDE [backup-center.md](../../includes/backup-center.md)]

## <a name="backup-items-in-recovery-services-vault"></a>Zálohované položky v trezoru Recovery Services

Všechny zálohované položky můžete monitorovat pomocí Recovery Services trezoru. Když přejdete do části **zálohované položky** v trezoru, otevře se zobrazení, které poskytuje počet zálohovaných položek každého typu úlohy přidružené k trezoru. Kliknutím na libovolný řádek otevřete podrobné zobrazení obsahující všechny zálohované položky daného typu úlohy s informacemi o stavu poslední zálohy pro každou položku, nejnovější dostupný bod obnovení a tak dále.

![Zálohované položky trezoru RS](media/backup-azure-monitoring-laworkspace/backup-items-view.png)

> [!NOTE]
> Pro položky zálohované do Azure pomocí DPM se v seznamu zobrazí všechny chráněné zdroje dat (na disku i v online režimu) pomocí serveru DPM. Pokud je ochrana pro zdroj dat zastavena se zachovanými zálohovanými daty, bude zdroj dat stále uveden na portálu. Můžete přejít na Podrobnosti zdroje dat a zjistit, jestli se body obnovení nacházejí na disku, online nebo obojím. Zdroje dat, pro které se online ochrana zastaví, ale data se zachovají, fakturace pro body obnovení online pokračuje, dokud se data zcela neodstraní.
>
> Aby se zálohované položky zobrazovaly na portálu Recovery Services trezoru aplikace DPM, musí mít verzi DPM 1807 (5.1.378.0) nebo DPM 2019 (verze 10.19.58.0 nebo vyšší).

## <a name="backup-jobs-in-recovery-services-vault"></a>Úlohy zálohování v trezoru Recovery Services

Azure Backup poskytuje integrované funkce monitorování a upozorňování pro úlohy, které chrání Azure Backup. V nastavení trezoru Recovery Services jsou v části **monitorování** uvedeny předem vytvořené úlohy a výstrahy.

![Integrované monitorování trezoru RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Úlohy se generují, pokud se provádí operace, jako je konfigurace zálohování, zálohování, obnovení, odstranění zálohy atd.

Tady jsou uvedené úlohy z následujících řešení Azure Backup:

- Zálohování virtuálních počítačů Azure
- Zálohování souborů Azure
- Zálohování úloh Azure, jako je SQL a SAP HANA
- Agent Microsoft Azure Recovery Services (MARS)

Úlohy z nástroje System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) se nezobrazují.

> [!NOTE]
> Úlohy Azure, jako jsou zálohování SQL a SAP HANA v rámci virtuálních počítačů Azure, mají velký počet úloh zálohování. Například zálohování protokolů může běžet každých 15 minut. Pro takové databázové úlohy se ale zobrazí jenom operace aktivované uživatelem. Naplánované operace zálohování se nezobrazují.

## <a name="backup-alerts-in-recovery-services-vault"></a>Výstrahy zálohování v trezoru Recovery Services

> [!NOTE]
> Zobrazení výstrah napříč trezory není v současné době v centru zálohování podporováno. Pokud chcete zobrazit výstrahy pro tento trezor, musíte přejít do jednotlivého trezoru.

Výstrahy jsou primárně ve scénářích, kdy se uživatelům pošle oznámení, aby mohli provádět příslušné akce. Část **výstrahy zálohování** zobrazuje výstrahy vygenerované službou Azure Backup. Tyto výstrahy definuje služba a uživatel nemůže vlastní výstrahy vytvořit.

### <a name="alert-scenarios"></a>Scénáře upozornění

Následující scénáře jsou definovány službou jako scénáře s možností upozorňování.

- Selhání zálohování nebo obnovení
- Zálohování bylo úspěšné s upozorněními pro agenta Microsoft Azure Recovery Services (MARS).
- Zastavení ochrany s uchováním dat/zastavení ochrany pomocí odstranění dat

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Tady jsou uvedené výstrahy z následujících řešení Azure Backup.

- Zálohování virtuálních počítačů Azure
- Zálohování souborů Azure
- Zálohování úloh Azure, jako je SQL, SAP HANA
- Agent Microsoft Azure Recovery Services (MARS)

> [!NOTE]
> Výstrahy z nástroje System Center Data Protection Manager (SC-DPM), Microsoft Azure Backup Server (MABS) se tady nezobrazí.

### <a name="consolidated-alerts"></a>Konsolidované výstrahy

V případě řešení zálohování úloh Azure, jako je SQL a SAP HANA, se zálohy protokolů dají generovat velmi často (až každých 15 minut podle zásady). Je také možné, že selhání zálohování protokolu jsou také velmi časté (až do 15 minut). V tomto scénáři bude koncový uživatel zahlcený, pokud dojde k vyvolání výstrahy pro každý výskyt selhání. Proto se pošle výstraha na první výskyt a v případě pozdějšího selhání z důvodu stejné hlavní příčiny nebudou vygenerovány další výstrahy. První upozornění se aktualizuje podle počtu selhání. Pokud však uživatel výstrahu deaktivuje, další výskyt spustí další výstrahu, která bude považována za první upozornění pro daný výskyt. Tímto způsobem Azure Backup provádí konsolidaci výstrah pro SQL a zálohování SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Výjimky při vyvolání výstrahy

Je-li výstraha při selhání vyvolána, existuje několik výjimek. Jsou to tyto:

- Uživatel explicitně zrušil spuštěnou úlohu.
- Úloha se nezdařila, protože právě probíhá jiná úloha zálohování (nic se nestane, protože musíme počkat na dokončení předchozí úlohy)
- Úloha zálohování virtuálního počítače selhala, protože zálohovaný virtuální počítač Azure už neexistuje.
- [Konsolidované výstrahy](#consolidated-alerts)

Výše uvedené výjimky jsou navržené z porozumění, že výsledek těchto operací (primárně aktivovaný uživatelem) se okamžitě zobrazuje na klientech portálu/PS/CLI. Takže uživatel je okamžitě informován a nepotřebuje oznámení.

### <a name="alert-types"></a>Typy výstrah

Na základě závažnosti výstrahy je možné výstrahy definovat ve třech typech:

- **Kritické**: v zásadě by jakékoli selhání zálohování nebo obnovení (naplánované nebo aktivované uživatelem) vedlo k vytvoření výstrahy a zobrazilo se jako kritická výstraha a také ničivé operace, jako je odstranění zálohy.
- **Upozornění**: Pokud se operace zálohování zdaří úspěšně, ale s malým počtem upozornění, zobrazí se jako varovné výstrahy. Výstrahy upozornění jsou aktuálně k dispozici pouze pro Azure Backup zálohy agenta.
- **Informativní**: v současné době není Azure Backup službě generována žádná informační výstraha.

## <a name="notification-for-backup-alerts"></a>Oznámení pro výstrahy zálohování

> [!NOTE]
> Konfiguraci oznámení lze provést pouze prostřednictvím Azure Portal. Podpora šablon PS/CLI/REST API/Azure Resource Manager není podporována.

Po vyvolání výstrahy budou uživatelé upozorněni. Azure Backup poskytuje integrovaný mechanizmus oznámení prostřednictvím e-mailu. Jedním z nich může být zadání jednotlivých e-mailových adres nebo distribučních seznamů, které budou oznamovány při vygenerování výstrahy. Můžete si také vybrat, jestli se má zobrazit upozornění na každou jednotlivou výstrahu, nebo je seskupit do hodinových Digest a pak získat oznámení.

![E-mailové oznámení trezoru RS](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Po nakonfigurování oznámení obdržíte uvítací nebo úvodní e-mail. Tím se potvrdí, že při vyvolání výstrahy Azure Backup může posílat e-maily na tyto adresy.<br>

Pokud byla frekvence nastavena na hodinový výtah a výstraha byla vyvolána a vyřešena během hodiny, nebude součástí nadcházející hodinové Digest.

> [!NOTE]
>
> - Pokud se provede destruktivní operace, jako je **zastavení ochrany pomocí odstranění dat** , vygeneruje se výstraha a vlastníkům, správcům a spolupracovníkům předplatného se pošle e-mail, i když nejsou oznámení nakonfigurovaná pro Recovery Services trezor.
> - K nakonfigurování oznámení pro úspěšné úlohy použijte [Log Analytics](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Deaktivace výstrah

Pokud chcete deaktivovat nebo vyřešit aktivní výstrahu, můžete vybrat položku seznamu odpovídající výstraze, kterou chcete deaktivovat. Otevře se obrazovka, která zobrazuje podrobné informace o výstraze s tlačítkem pro zrušení **Aktivace** v horní části. Výběrem tohoto tlačítka se změní stav výstrahy na **neaktivní**. Výstrahu můžete také deaktivovat tak, že pravým tlačítkem myši kliknete na položku seznamu, která odpovídá dané výstraze, a vyberete **deaktivovat**.

![Deaktivace výstrah trezoru RS](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Další kroky

[Monitorování úloh Azure Backup pomocí Azure Monitor](backup-azure-monitoring-use-azuremonitor.md)
