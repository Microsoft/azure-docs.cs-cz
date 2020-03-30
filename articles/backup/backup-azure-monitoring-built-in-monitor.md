---
title: Monitorování úloh chráněných zálohováním Azure
description: V tomto článku se dozvíte o možnostech monitorování a oznámení pro úlohy azure zálohování pomocí portálu Azure.
ms.topic: conceptual
ms.date: 03/05/2019
ms.assetid: 86ebeb03-f5fa-4794-8a5f-aa5cbbf68a81
ms.openlocfilehash: de5a82f5ad1d8113b27c07484f2f08f4cf97c759
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80294926"
---
# <a name="monitoring-azure-backup-workloads"></a>Monitorování úloh azure zálohování

Azure Backup poskytuje několik řešení zálohování na základě požadavku na zálohování a topologie infrastruktury (místní vs. Azure). Každý uživatel zálohy nebo správce by měl vidět, co se děje ve všech řešeních a očekává se, že bude upozorněn v důležitých scénářích. Tento článek podrobně popisuje možnosti monitorování a oznámení poskytované službou Azure Backup.

## <a name="backup-jobs-in-recovery-services-vault"></a>Zálohovací úlohy v trezoru služby Recovery Services

Azure Backup poskytuje integrované funkce monitorování a upozorňování pro úlohy chráněné službou Azure Backup. V nastavení trezoru služby Recovery Services poskytuje část **Monitorování** vestavěné úlohy a výstrahy.

![Rs vault zabudované monitorování](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltmonitoring.png)

Úlohy jsou generovány při provádění operací, jako je konfigurace zálohování, zálohování, obnovení, odstranění zálohy a tak dále.

Úlohy z následujících řešení Azure Backup jsou zobrazeny zde:

- Zálohování virtuálních počítačů Azure
- Zálohování souborů Azure
- Zálohování úloh Azure, jako je SQL a SAP HANA
- Agent Azure Backup (MAB)

Úlohy ze Správce ochrany dat system center (SC-DPM), Microsoft Azure Backup Server (MABS) se nezobrazují.

> [!NOTE]
> Úlohy Azure, jako jsou zálohy SQL a SAP HANA v rámci virtuálních virtuálních počítačů Azure, mají obrovské množství úloh zálohování. Například zálohy protokolu lze spustit každých 15 minut. Proto pro takové úlohy DB jsou zobrazeny pouze operace aktivované uživatelem. Plánované operace zálohování nejsou zobrazeny.

## <a name="backup-alerts-in-recovery-services-vault"></a>Výstrahy zálohování v trezoru služby Recovery Services

Výstrahy jsou především scénáře, kde jsou uživatelé upozorněni, aby mohli provést příslušnou akci. V části **Výstrahy zálohování** se zobrazují výstrahy generované službou Azure Backup. Tyto výstrahy jsou definovány službou a uživatel nemůže vytvořit vlastní výstrahy.

### <a name="alert-scenarios"></a>Scénáře výstrah

Následující scénáře jsou definovány službou jako scénáře s výstrahou.

- Selhání zálohování nebo obnovení
- Zálohování proběhlo úspěšně s upozorněními pro agenta Azure Backup (MAB)
- Zastavit ochranu s ochranou zachycovat data/Zastavit s odstraněním dat

### <a name="alerts-from-the-following-azure-backup-solutions-are-shown-here"></a>Upozornění z následujících řešení Azure Backup jsou zobrazeny zde

- Zálohování virtuálních počítačů Azure
- Zálohování souborů Azure
- Zálohy úloh Azure, jako je SQL, SAP HANA
- Agent Azure Backup (MAB)

> [!NOTE]
> Výstrahy od Správce ochrany dat system center (SC-DPM), Microsoft Azure Backup Server (MABS) se zde nezobrazují.

### <a name="consolidated-alerts"></a>Konsolidované výstrahy

Pro řešení zálohování úloh Azure, jako je SQL a SAP HANA, zálohy protokolů lze generovat velmi často (až každých 15 minut podle zásad). Takže je také možné, že selhání zálohování protokolu jsou také velmi časté (až každých 15 minut). V tomto scénáři bude koncový uživatel zahlceni, pokud je vyvolána výstraha pro každý výskyt selhání. Takže výstraha je odeslána pro první výskyt a pokud následné chyby jsou z důvodu stejné příčiny, pak další výstrahy nejsou generovány. První výstraha je aktualizována počtem selhání. Pokud je však výstraha deaktivována uživatelem, další výskyt aktivuje další výstrahu a bude považována za první výstrahu pro tento výskyt. To je způsob, jakým Azure Backup provádí konsolidaci výstrah pro zálohy SQL a SAP HANA.

### <a name="exceptions-when-an-alert-is-not-raised"></a>Výjimky, pokud není vyvolána výstraha

Existuje několik výjimek, pokud není vyvolána výstraha při selhání. Jsou to tyto:

- Uživatel výslovně zrušil spuštěnou úlohu.
- Úloha se nezdaří, protože probíhá další úloha zálohování (nic, na co bychom mohli jednat, protože musíme počkat na dokončení předchozí úlohy)
- Úloha zálohování virtuálních počítači se nezdaří, protože zálohovaný virtuální počítač Azure už neexistuje.
- [Konsolidované výstrahy](#consolidated-alerts)

Výše uvedené výjimky jsou navrženy z pochopení, že výsledek těchto operací (primárně spouštěný uživatelem) se zobrazí okamžitě na portal/PS/CLI klienty. Takže uživatel je okamžitě vědom a nepotřebuje oznámení.

### <a name="alert-types"></a>Typy výstrah

Na základě závažnosti výstrahy lze výstrahy definovat ve třech typech:

- **Kritické**: V zásadě jakékoli selhání zálohování nebo obnovení (plánované nebo aktivované uživatelem) by vedlo ke generování výstrahy a bylo by zobrazeno jako kritická výstraha a také destruktivní operace, jako je odstranění zálohy.
- **Upozornění**: Pokud je operace zálohování úspěšná, ale s několika upozorněními, jsou uvedeny jako varovné výstrahy.
- **Informační**: Od dnešního dne služba Azure Backup generuje žádnou informační výstrahu.

## <a name="notification-for-backup-alerts"></a>Oznámení pro výstrahy zálohování

> [!NOTE]
> Konfiguraci oznámení lze provést jenom prostřednictvím portálu Azure Portal. Podpora ps/CLI/REST API/Azure Resource Manager šablony není podporována.

Jakmile je vyvolána výstraha, uživatelé jsou upozorněni. Azure Backup poskytuje integrovaný mechanismus oznámení prostřednictvím e-mailu. Můžete určit jednotlivé e-mailové adresy nebo distribuční seznamy, které mají být upozorněny při generování výstrahy. Můžete také zvolit, zda chcete získat oznámení pro každou jednotlivou výstrahu nebo je seskupit do hodinové hospodařících a poté být upozorněni.

![Vestavěné e-mailové oznámení rs vault](media/backup-azure-monitoring-laworkspace/rs-vault-inbuiltnotification.png)

Po konfiguraci oznámení obdržíte uvítací nebo úvodní e-mail. Tím se potvrdí, že Azure Backup můžete odesílat e-maily na tyto adresy, když je vyvolána výstraha.<br>

Pokud byla frekvence nastavena na hodinovou digest a výstraha byla vyvolána a vyřešena během jedné hodiny, nebude součástí nadcházejícího hodinového digestu.

> [!NOTE]
>
> - Pokud se provede destruktivní operace, jako je **například ochrana proti zastavení s odstraněním dat,** je aktivována výstraha a je odeslán e-mail vlastníkům předplatného, správcům a spolusprávcům, i když oznámení nejsou nakonfigurována pro trezor obnovení služby.
> - Chcete-li nakonfigurovat oznámení pro úspěšné úlohy, použijte [protokolovou analýzu](backup-azure-monitoring-use-azuremonitor.md#using-log-analytics-workspace).

## <a name="inactivating-alerts"></a>Deaktivace výstrah

Chcete-li deaktivovat/vyřešit aktivní výstrahu, můžete kliknout na položku seznamu odpovídající výstraze, kterou chcete deaktivovat. Tím se otevře obrazovka, která zobrazuje podrobné informace o upozornění s tlačítkem "Inaktivovat" nahoře. Kliknutím na toto tlačítko změníte stav výstrahy na Neaktivní. Výstrahu můžete také deaktivovat kliknutím pravým tlačítkem myši na položku seznamu odpovídající této výstraze a výběrem možnosti "Inaktivovat".

![Deaktivace výstrahrs Vault](media/backup-azure-monitoring-laworkspace/vault-alert-inactivation.png)

## <a name="next-steps"></a>Další kroky

[Monitorování úloh zálohování Azure pomocí Azure Monitoru](backup-azure-monitoring-use-azuremonitor.md)
