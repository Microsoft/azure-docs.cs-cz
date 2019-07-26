---
title: Nejčastější dotazy týkající se monitorování Azure Backup
description: 'Odpovědi na běžné otázky týkající se: Výstraha monitorování Azure Backup'
author: srinathvasireddy
manager: sivan
ms.service: backup
ms.topic: conceptual
ms.date: 07/08/2019
ms.author: srinathv
ms.openlocfilehash: dc68b68ffa0ae2ee4f1bdd8f2c7ac0d7ff5ebb8c
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/24/2019
ms.locfileid: "68465853"
---
# <a name="azure-backup-monitoring-alert---faq"></a>Výstraha monitorování Azure Backup – Nejčastější dotazy
Tento článek obsahuje odpovědi na nejčastější dotazy týkající se výstrahy monitorování Azure.

## <a name="configure-azure-backup-reports"></a>Konfigurace sestav Azure Backup

### <a name="how-do-i-check-if-reporting-data-has-started-flowing-into-a-storage-account"></a>Návody ověřit, jestli se data vytváření sestav začala přesměrovat do účtu úložiště?
Přejít na účet úložiště, který jste nakonfigurovali, a vyberte kontejnery. Pokud má kontejner záznam pro Insights-logs-azurebackupreport, znamená to, že se data pro vytváření sestav spustila v.

### <a name="what-is-the-frequency-of-data-push-to-a-storage-account-and-the-azure-backup-content-pack-in-power-bi"></a>Jaká je četnost vkládání dat do účtu úložiště a Azure Backup balíčku obsahu v Power BI?
  Pro uživatele dne 0 trvá zápis dat do účtu úložiště během 24 hodin. Po dokončení tohoto počátečního nabízení oznámení se data aktualizují o frekvenci znázorněnou na následujícím obrázku.

  * Data související s **úlohami**, **výstrahami**, zálohovanými **položkami**, trezory, chráněnými **servery**a **zásadami** se odešlou do účtu úložiště zákazníka jako a při jejich zaznamenání.

  * Data týkající se **úložiště** se každých 24 hodin odesílají do účtu úložiště zákazníka.

       ![Frekvence odesílání dat sestav Azure Backup](./media/backup-azure-configure-reports/reports-data-refresh-cycle.png)

  * Power BI má [naplánovanou aktualizaci jednou denně](https://powerbi.microsoft.com/documentation/powerbi-refresh-data/#what-can-be-refreshed). Můžete provést ruční aktualizaci dat v Power BI balíčku obsahu.

### <a name="how-long-can-i-retain-reports"></a>Jak dlouho mohu uchovávat sestavy?
Když nakonfigurujete účet úložiště, můžete pro data sestavy v účtu úložiště vybrat dobu uchování. Postupujte podle kroku 6 v části [Konfigurace účtu úložiště pro sestavy](backup-azure-configure-reports.md#configure-storage-account-for-reports) . Můžete také [analyzovat sestavy v aplikaci Excel](https://powerbi.microsoft.com/documentation/powerbi-service-analyze-in-excel/) a ušetřit je po delší dobu uchovávání podle vašich potřeb.

### <a name="will-i-see-all-my-data-in-reports-after-i-configure-the-storage-account"></a>Zobrazí se po konfiguraci účtu úložiště všechna moje data v sestavách?
 Všechna data vygenerovaná po nakonfigurování účtu úložiště se odešlou do účtu úložiště a jsou dostupná v sestavách. Probíhající úlohy nejsou nabízeny pro vytváření sestav. Až se úloha dokončí nebo dojde k jejímu výpadku, pošle se do sestav.

### <a name="if-i-already-configured-the-storage-account-to-view-reports-can-i-change-the-configuration-to-use-another-storage-account"></a>Když už mám nakonfigurovaný účet úložiště pro zobrazování sestav, můžu změnit konfiguraci tak, aby používala jiný účet úložiště?
Ano, konfiguraci můžete změnit tak, aby odkazovala na jiný účet úložiště. Při připojování k balíčku obsahu Azure Backup použijte nově nakonfigurovaný účet úložiště. Po nakonfigurování jiného účtu úložiště se taky v tomto účtu úložiště vytvoří nové datové toky. Starší data (před změnou konfigurace) zůstávají stále ve starším účtu úložiště.

### <a name="can-i-view-reports-across-vaults-and-subscriptions"></a>Můžu zobrazit sestavy napříč trezory a předplatnými?
Ano, můžete nakonfigurovat stejný účet úložiště v různých trezorech a zobrazit tak sestavy mezi trezory. Můžete taky nakonfigurovat stejný účet úložiště pro trezory v rámci předplatných. Pak můžete tento účet úložiště použít při připojení k Azure Backup balíčku obsahu v Power BI k zobrazení sestav. Vybraný účet úložiště musí být ve stejné oblasti jako trezor Recovery Services.

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá, než se stav úlohy agenta Azure Backup projeví na portálu?
Azure Portal může trvat až 15 minut, než bude odpovídat stavu úlohy agenta Azure Backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>V případě, že se úloha zálohování nezdařila, jak dlouho trvá vyvolání výstrahy?
Výstraha se vyvolá do 20 minut od selhání zálohování Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existuje případ, kdy se e-mail neposílá, pokud jsou nakonfigurovaná oznámení?
Ano. V následujících situacích se oznámení neodesílají.

* Pokud jsou oznámení konfigurována každou hodinu a je vyvolána a vyřešena v průběhu hodiny
* Při zrušení úlohy
* Pokud druhá úloha zálohování neproběhne úspěšně, protože probíhá původní úloha zálohování

## <a name="recovery-services-vault"></a>Recovery Services trezor

### <a name="how-long-does-it-take-for-the-azure-backup-agent-job-status-to-reflect-in-the-portal"></a>Jak dlouho trvá, než se stav úlohy agenta Azure Backup projeví na portálu?
Azure Portal může trvat až 15 minut, než bude odpovídat stavu úlohy agenta Azure Backup.

### <a name="when-a-backup-job-fails-how-long-does-it-take-to-raise-an-alert"></a>V případě, že se úloha zálohování nezdařila, jak dlouho trvá vyvolání výstrahy?
Výstraha se vyvolá do 20 minut od selhání zálohování Azure.

### <a name="is-there-a-case-where-an-email-wont-be-sent-if-notifications-are-configured"></a>Existuje případ, kdy se e-mail neposílá, pokud jsou nakonfigurovaná oznámení?
Ano. V následujících situacích se oznámení neodesílají.

* Pokud jsou oznámení konfigurována každou hodinu a je vyvolána a vyřešena v průběhu hodiny
* Při zrušení úlohy
* Pokud druhá úloha zálohování neproběhne úspěšně, protože probíhá původní úloha zálohování

## <a name="next-steps"></a>Další postup

Přečtěte si další Nejčastější dotazy:

- [Běžné dotazy](backup-azure-vm-backup-faq.md) k zálohování virtuálních počítačů Azure.
- [Běžné otázky](backup-azure-file-folder-backup-faq.md) týkající se agenta Azure Backup
