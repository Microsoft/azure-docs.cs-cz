---
title: Monitorování výstrah nejčastější dotazy k Azure Backup
description: 'Odpovědi na běžné dotazy týkající se: Výstrahy monitorování Azure Backup'
services: backup
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: bb684f65539b4429862b2dce0e378d8f659d2975
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705028"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Monitorování upozornění – nejčastější dotazy k Azure Backup
Tento článek obsahuje odpovědi na běžné dotazy týkající se Azure monitorovací upozornění.

## <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Jak můžu ověřit, pokud vytváření sestav dat bylo zahájeno přenášejí do účtu úložiště?
Přejděte na účet úložiště, které jste nakonfigurovali a vyberte kontejnery. Pokud kontejner má záznam pro přehledy. protokoly azurebackupreport, znamená to, data pro vytváření sestav byla spuštěna tok.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Co je frekvence datová oznámení k účtu úložiště a balíčku obsahu Azure Backup v Power BI?
  Pro uživatele 0 dne trvá přibližně 24 hodin k zápisu dat do účtu úložiště. Po ukončení tohoto počátečního nabízená data se aktualizují s frekvencí je znázorněno na následujícím obrázku.

  * Data související s **úlohy**, **výstrahy**, **zálohování položek**, **trezory**, **chráněné servery**a  **Zásady** se vloží do účtu úložiště zákazníka, jako je, a když se zaznamená.

  * Data související s **úložiště** se vloží do účtu úložiště zákazníka každých 24 hodin.

       ![Azure Backup Reports data nabízená frekvence](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Má Power BI [plánovanou aktualizaci jednou denně](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Můžete provést ruční aktualizaci dat v Power BI pro balíček obsahu.

### <a name="how-long-can-i-retain-reports"></a>Jak dlouho může uchovávat sestavy?
Když konfigurujete účet úložiště, můžete vybrat takovou dobu uchování dat sestavy v účtu úložiště. Postupujte podle kroku 6 v [konfigurace účtu úložiště pro sestavy](backup-azure-configure-reports.md#configure-storage-account-for-reports) oddílu. Můžete také [analyzovat sestav v aplikaci Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) a uloží pro delší doba uchovávání dat, na základě vašich potřeb.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Se zobrazí všechna data v sestavách, po nakonfigurovat účet úložiště?
 Všechna data vygeneruje, když nakonfigurujete účet úložiště se vloží do účtu úložiště a je k dispozici v sestavách. Pro vytváření sestav, se nepřenášejí probíhající úlohy. Po dokončení úlohy nebo se nezdaří, jsou odeslána do sestavy.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Pokud už jsem nakonfiguroval účet úložiště, chcete-li zobrazit sestavy, můžu změnit konfiguraci na jiný účet úložiště?
Ano, můžete změnit konfiguraci tak, aby odkazoval na jiný účet úložiště. Při připojení k balíčku obsahu Azure Backup, použijte nově nakonfigurovaný účet úložiště. Po dokončení konfigurace jiný účet úložiště je nová data se přenášejí také v tomto účtu úložiště. Starší data (před změnou konfigurace) zůstává ve starší účtu úložiště.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Můžete zobrazit sestavy úložišť a předplatná?
Ano, můžete konfigurovat stejného účtu úložiště v rámci různých úložišť zobrazit trezor různé sestavy. Kromě toho můžete nakonfigurovat stejného účtu úložiště pro trezory napříč předplatnými. Tento účet úložiště můžete použít při připojení k balíčku obsahu Azure Backup v Power BI k zobrazení sestavy. Vybraný účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá stav úlohy agenta Azure backup tak, aby odrážely na portálu?
Na webu Azure portal může trvat až 15 minut tak, aby odrážel stav úlohy agenta Azure backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Pokud se úloha zálohování nezdaří, jak dlouho trvá vydat výstrahu?
V rámci 20 minut selhání zálohování Azure je vydána výstraha.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Je případ, kdy e-mail neodešle, pokud jsou oznámení nakonfigurovaná?
Ano. V následujících situacích nedocházelo k jejich odesílání.

* Pokud jsou oznámení nakonfigurovaná každou hodinu a výstraha se vyvolá a vyřeší během hodiny.
* Když se zruší úlohu
* Pokud se druhá úloha zálohování se nezdaří, protože probíhá původní úlohy zálohování

## <a name="recovery-services-vault"></a>Trezor služby Recovery Services

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá stav úlohy agenta Azure backup tak, aby odrážely na portálu?
Na webu Azure portal může trvat až 15 minut tak, aby odrážel stav úlohy agenta Azure backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>Pokud se úloha zálohování nezdaří, jak dlouho trvá vydat výstrahu?
V rámci 20 minut selhání zálohování Azure je vydána výstraha.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Je případ, kdy e-mail neodešle, pokud jsou oznámení nakonfigurovaná?
Ano. V následujících situacích nedocházelo k jejich odesílání.

* Pokud jsou oznámení nakonfigurovaná každou hodinu a výstraha se vyvolá a vyřeší během hodiny.
* Když se zruší úlohu
* Pokud se druhá úloha zálohování se nezdaří, protože probíhá původní úlohy zálohování

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

- [Běžné otázky](backup-azure-vm-backup-faq.md) o záloh virtuálních počítačů Azure.
- [Běžné otázky](backup-azure-file-folder-backup-faq.md) o agenta Azure Backup
