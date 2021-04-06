---
title: Nejčastější dotazy k monitorování výstrah a sestav
description: V tomto článku najdete odpovědi na běžné otázky týkající se výstrahy monitorování Azure Backup a Azure Backup sestav.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: 4e2585009b531512e54ffa889a05ec50038dd619
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98805861"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Výstraha monitorování Azure Backup – Nejčastější dotazy

Tento článek obsahuje odpovědi na běžné dotazy týkající se Azure Backup monitorování a vytváření sestav.

## <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Návody ověřit, zda byla data vytváření sestav spouštěna v pracovním prostoru Log Analytics (LA)?

Přejděte do pracovního prostoru LA, který jste nakonfigurovali. Přejděte do položky nabídky **protokoly** a spusťte dotaz `CoreAzureBackup | take 1` . Pokud se zobrazí vracený záznam, znamená to, že se data začala přesměrovat do pracovního prostoru. Počáteční nabízení dat může trvat až 24 hodin.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Jaká je frekvence nabízených dat do pracovního prostoru LA?

Diagnostická data z trezoru se do Log Analyticsho pracovního prostoru napumpa s určitou prodlevou. Každá událost se doručí na Log Analytics pracovní prostor od 20 do 30 minut od jeho vložení z trezoru Recovery Services. Tady jsou další podrobnosti o prodlevě:

* V rámci všech řešení se integrované výstrahy služby Backup odešlou hned po jejich vytvoření. Proto se obvykle zobrazují v pracovním prostoru Log Analytics po 20 až 30 minutách.
* V rámci všech řešení se úlohy zálohování na vyžádání a úlohy obnovení odešlou hned po dokončení.
* Pro všechna řešení kromě zálohování SQL se naplánované úlohy zálohování odešlou hned po dokončení.
* U služby SQL Backup, protože k zálohování protokolů může docházet každých 15 minut, informace o všech dokončených úlohách plánovaného zálohování, včetně protokolů, jsou dávkové a vložené každých 6 hodin.
* V rámci všech řešení se pro všechny ostatní informace, jako je například zálohovaná položka, zásada, body obnovení, úložiště a tak dále, je alespoň jednou denně nabízena.
* Změna v konfiguraci zálohování (například změna zásady nebo zásady úprav) aktivuje vložení všech souvisejících informací o zálohování.

### <a name="how-long-can-i-retain-reporting-data"></a>Jak dlouho mohu uchovávat data vytváření sestav?

Po vytvoření pracovního prostoru LA se můžete rozhodnout, že budete uchovávat data po dobu maximálně 2 let. Ve výchozím nastavení uchovává pracovní prostor LA data po dobu 31 dnů.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Budou se po konfiguraci pracovního prostoru LA zobrazovat všechna moje data v sestavách?

 Všechna data vygenerovaná po konfiguraci nastavení diagnostiky se odešlou do pracovního prostoru LA a jsou dostupná v sestavách. Probíhající úlohy nejsou nabízeny pro vytváření sestav. Až se úloha dokončí nebo dojde k jejímu výpadku, pošle se do sestav.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Můžu zobrazit sestavy napříč trezory a předplatnými?

Ano, sestavy můžete zobrazit v rámci trezorů a odběrů i oblastí. Vaše data můžou být uložená v jednom pracovním prostoru LA nebo skupině pracovních prostorů LA.

### <a name="can-i-view-reports-across-tenants"></a>Můžu zobrazit sestavy v klientech?

Pokud jste uživatelem [Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) , který má delegovaný přístup k pracovním prostorům pro vaše zákazníky nebo k pracovním prostorům La, můžete pomocí sestav zálohování zobrazit data ve všech klientech.

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá, než se na portálu projeví stav úlohy Azure Backup agenta?

Azure Portal může trvat až 15 minut, než bude odpovídat stavu úlohy agenta Azure Backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Když úloha zálohování selže, jak dlouho trvá aktivace upozornění?

K vyvolání výstrahy dojde během 20 minut od Azure Backup chyby.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existuje případ, kdy se e-mail neposílá, pokud jsou nakonfigurovaná oznámení?

Ano. V následujících situacích se oznámení neodesílají:

* Pokud jsou oznámení konfigurována každou hodinu a je vyvolána a vyřešena v průběhu hodiny
* Při zrušení úlohy
* Pokud druhá úloha zálohování neproběhne úspěšně, protože probíhá původní úloha zálohování

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

* [Běžné dotazy](backup-azure-vm-backup-faq.yml) týkající se zálohování virtuálních počítačů Azure
* [Běžné dotazy](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
