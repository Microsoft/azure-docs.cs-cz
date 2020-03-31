---
title: Nejčastější dotazy k monitorování výstrah a zpráv
description: V tomto článku se dozvíte odpovědi na běžné otázky týkající se azure backup monitoring alert a Azure Backup sestavy.
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 07/08/2019
ms.openlocfilehash: f5be97458ba658f315c31ae34e540842b64e3ec4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76989565"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Výstraha monitorování zálohování Azure – nejčastější dotazy

Tento článek odpovídá na běžné otázky týkající se monitorování a vytváření sestav azure backup.

## <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-log-analytics-la-workspace"></a>Jak zkontroluji, jestli data přehledů nezačala proudit do pracovního prostoru Analýzy protokolů (LA)?

Přejděte do pracovního prostoru LA, který jste nakonfigurovali, přejděte na položku nabídky **Protokoly** a spusťte dotaz CoreAzureBackup | trvat 1. Pokud se zobrazí záznam, který se vrací, znamená to, že data začala proudit do pracovního prostoru. Počáteční nabízení dat může trvat až 24 hodin.

### <a name="what-is-the-frequency-of-data-push-to-an-la-workspace"></a>Jaká je frekvence nabízení dat do pracovního prostoru LA?

Diagnostická data z trezoru jsou čerpána do pracovního prostoru Log Analytics s určitým zpožděním. Každá událost dorazí do pracovního prostoru Log Analytics 20 až 30 minut po jeho vytlačení z trezoru služby Recovery Services. Zde jsou další podrobnosti o zpoždění:

* Ve všech řešeních jsou integrované výstrahy služby zálohování nabízeny ihned po jejich vytvoření. Takže se obvykle zobrazí v pracovním prostoru Log Analytics po 20 až 30 minutách.
* Ve všech řešeních jsou úlohy zálohování na vyžádání a úlohy obnovení posunuty, jakmile skončí.
* Pro všechna řešení s výjimkou zálohování SQL jsou naplánované úlohy zálohování posunuty, jakmile skončí.
* Pro zálohování SQL, protože zálohy protokolu může dojít každých 15 minut, informace pro všechny dokončené naplánované úlohy zálohování, včetně protokolů, je dávkově a nabízeny každých 6 hodin.
* Ve všech řešeních jsou další informace, jako je položka zálohování, zásady, body obnovení, úložiště a tak dále, posunuty alespoň jednou denně.
* Změna konfigurace zálohování (například změna zásad nebo zásad úprav) spustí nabízení všech souvisejících informací o zálohování.

### <a name="how-long-can-i-retain-reporting-data"></a>Jak dlouho mohu uchovávat údaje o vykazování?

Po vytvoření pracovního prostoru LA můžete uchovávat data maximálně po dobu 2 let. Ve výchozím nastavení zachová pracovní prostor LA data po dobu 31 dnů.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-la-workspace"></a>Zobrazí se po konfiguraci pracovního prostoru LA všechna data v sestavách?

 Všechna data vygenerovaná po konfiguraci nastavení diagnostiky jsou převedena do pracovního prostoru LA a jsou k dispozici v sestavách. Probíhající úlohy nejsou pro vytváření sestav tlačeny. Po dokončení nebo selhání úlohy je odeslána do sestav.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Můžu zobrazit sestavy napříč trezory a předplatnými?

Ano, sestavy můžete zobrazit v trezorech a předplatných i v oblastech. Vaše data mohou být umístěna v jednom pracovním prostoru LA nebo ve skupině pracovních prostorů LA.

### <a name="can-i-view-reports-across-tenants"></a>Můžu zobrazit sestavy mezi tenanty?

Pokud jste uživatel [Služby Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) s delegovaným přístupem k předplatným vašich zákazníků nebo k pracovním prostorům LA, můžete pomocí sestav zálohování zobrazit data ve všech vašich tenantech.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá, než se stav úlohy agenta zálohování Azure projeví na portálu?

Portál Azure může trvat až 15 minut, než bude odrážet stav úlohy agenta zálohování Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Když úloha zálohování selže, jak dlouho trvá aktivace upozornění?

Výstraha se zobrazí do 20 minut od selhání zálohování Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existuje případ, kdy e-mail nebude odeslán, pokud jsou nakonfigurována oznámení?

Ano. V následujících situacích nejsou odesílána oznámení.

* Pokud jsou oznámení konfigurována každou hodinu a výstraha je vyvolána a vyřešena během hodiny
* Když je úloha zrušena
* Pokud se druhá úloha zálohování nezdaří, protože probíhá původní úloha zálohování

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá, než se stav úlohy agenta zálohování Azure projeví na portálu?

Portál Azure může trvat až 15 minut, než bude odrážet stav úlohy agenta zálohování Azure.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Když úloha zálohování selže, jak dlouho trvá aktivace upozornění?

Výstraha se zobrazí do 20 minut od selhání zálohování Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existuje případ, kdy e-mail nebude odeslán, pokud jsou nakonfigurována oznámení?

Ano. V následujících situacích nejsou odesílána oznámení:

* Pokud jsou oznámení konfigurována každou hodinu a výstraha je vyvolána a vyřešena během hodiny
* Když je úloha zrušena
* Pokud se druhá úloha zálohování nezdaří, protože probíhá původní úloha zálohování

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

* [Běžné dotazy](backup-azure-vm-backup-faq.md) týkající se zálohování virtuálních počítačů Azure
* [Běžné dotazy](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
