---
title: Obnovení dat ze Azure Backup serveru
description: Obnovte data, která jste chránili do trezoru služby Recovery Services z jakékoli Azure Backup Server zaregistrovaný do tohoto trezoru.
services: backup
author: kasinh
manager: vijayts
ms.service: backup
ms.topic: conceptual
ms.date: 07/09/2019
ms.author: kasinh
ms.openlocfilehash: 770baeeacb5f3808eba05f9e262bcbca75c6baad
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705214"
---
# <a name="recover-data-from-azure-backup-server"></a>Obnovení dat z Azure Backup Serveru
Použití Azure Backup serveru k obnovení dat, které jste zálohovali pro trezor služby Recovery Services. Proces pro to tak je integrovaná Konzola pro správu Azure Backup serveru a je podobný postupu obnovení pro jiné komponenty Azure Backup.

> [!NOTE]
> Tento článek týká se to [System Center Data Protection Manager 2012 R2 s UR7 nebo novější](https://support.microsoft.com/en-us/kb/3065246)kombinované s [nejnovější verzi agenta Azure Backup](https://aka.ms/azurebackup_agent).
>
>

Chcete-li obnovit data ze Azure Backup serveru:

1. Z **obnovení** kartu konzoly pro správu Azure Backup Server, klikněte na tlačítko **přidat externí DPM** (v levém horním rohu obrazovky).   
    ![Přidat externí DPM](./media/backup-azure-alternate-dpm-server/add-external-dpm.png)
2. Stáhnout nové **přihlašovací údaje trezoru** z trezoru přidružené **Azure Backup serveru** tam, kde probíhá obnovení dat, zvolte Azure Backup serveru ze seznamu serverů Azure Backup zaregistrované pomocí služby Recovery Services trezoru a zadejte **šifrovací heslo** přidružené k serveru, jehož data obnovena.

    ![Přihlašovací údaje externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-credentials.png)

   > [!NOTE]
   > Pouze servery pro zálohování Azure přidružené k stejnému trezoru registraci můžete obnovit data uživatele toho druhého.
   >
   >

    Po úspěšném přidání externí Azure Backup serveru můžete procházet data z externího serveru a místní Azure Backup serveru z **obnovení** kartu.
3. Seznam dostupných provozních serverech, chráněné službou externí Azure Backup serveru procházením vyberte příslušný zdroj dat.

    ![Procházet externí DPM Server](./media/backup-azure-alternate-dpm-server/browse-external-dpm.png)
4. Vyberte **měsíci a roce** z **body obnovení** rozevírací seznam, vyberte požadované **datum obnovení** bod obnovení byl vytvořen, a vyberte **Čas obnovení**.

    Seznam souborů a složek se zobrazí v dolním podokně, které můžete procházet a obnovit do libovolného umístění.

    ![Body obnovení serveru externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recoverypoint.png)
5. Klikněte pravým tlačítkem myši klikněte na příslušnou položku a klikněte na tlačítko **obnovit**.

    ![Externí obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/recover.png)
6. Zkontrolujte **obnovení výběru**. Zkontrolujte data a času záložní kopie, Probíhá obnovení, jakož i zdroj, ze kterého byl vytvořen záložní kopie. Pokud je výběr správné, klikněte na tlačítko **zrušit** přejděte zpátky na kartě obnovení vyberte bod obnovení odpovídající. Pokud je výběr správné, klikněte na tlačítko **Další**.

    ![Externí souhrnu obnovení aplikace DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-summary.png)
7. Vyberte **obnovení do alternativního umístění**. **Procházet** do správného umístění pro obnovení.

    ![Externí aplikace DPM pro obnovení alternativního umístění](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-alternate-location.png)
8. Zvolte si možnost související s **vytvořit kopii**, **přeskočit**, nebo **přepsat**.

   * **Vytvořit kopii** -vytvoří kopii souboru, pokud dojde ke kolizi názvu.
   * **Přeskočit** – Pokud je kolize názvů, nedojde k odstranění souboru, což ponechá původní soubor.
   * **Přepsat** – Pokud je kolize názvů, přepíše stávající kopie souboru.

     Vyberte odpovídající možnost **obnovit zabezpečení**. Můžete použít nastavení zabezpečení cílového počítače, ve kterém se obnovuje data nebo nastavení zabezpečení, které byly pro produkt v době vytvoření bodu obnovení.

     Určení, zda **oznámení** přijde, po úspěšném dokončení obnovení.

     ![Oznámení o obnovení externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-notifications.png)
9. **Souhrn** obrazovky je uveden seznam možností zatím zvolili. Po kliknutí na **"Obnovit"** , data budou obnovena do odpovídající místní umístění.

    ![Souhrn možností obnovení externí DPM](./media/backup-azure-alternate-dpm-server/external-dpm-recovery-options-summary.png)

   > [!NOTE]
   > Úloha obnovení můžete sledovat v **monitorování** kartu Azure Backup serveru.
   >
   >

    ![Monitorování obnovení](./media/backup-azure-alternate-dpm-server/monitoring-recovery.png)
10. Můžete kliknout na **vymazat externí DPM** na **obnovení** kartu serveru DPM, odeberete zobrazení externího serveru aplikace DPM.

    ![Vymazat externí DPM](./media/backup-azure-alternate-dpm-server/clear-external-dpm.png)

## <a name="troubleshooting-error-messages"></a>Řešení potíží s chybové zprávy
| Ne. | Chybová zpráva | Postup při řešení potíží |
|:---:|:--- |:--- |
| 1. |Tento server není registrovaný pro trezor určený přihlašovacími údaji. |**Příčina:** Tato chyba se zobrazí, když vybraný soubor přihlašovacích údajů trezoru nepatří do trezoru služby Recovery Services, které jsou spojené s Azure Backup Server, na kterém dojde k pokusu o obnovení. <br> **Řešení:** Stáhněte si soubor s přihlašovacími údaji trezoru z trezoru služby Recovery Services, na které je registrované Azure Backup serveru. |
| 2. |Buď obnovitelná data nejsou k dispozici, nebo vybraný server není DPM server. |**Příčina:** Nejsou že žádné jiné servery Azure Backup zaregistrované do trezoru služby Recovery Services, nebo na serverech ještě nepřidali metadata nebo vybraný server není Server služby Azure Backup (s použitím Windows serveru nebo klienta Windows). <br> **Řešení:** Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, ujistěte se, že je nainstalovaný nejnovější agent Azure Backup. <br>Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, počkejte po instalaci zahájit proces obnovení za den. Noční úlohu odešlete metadata pro všechny chráněné zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 3. |Žádným jiným serverem aplikace DPM je registrované k tomuto trezoru. |**Příčina:** Nejsou žádné další Azure Backup servery, které jsou zaregistrované do trezoru, ze kterého je Probíhá pokus o obnovení.<br>**Řešení:** Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, ujistěte se, že je nainstalovaný nejnovější agent Azure Backup.<br>Pokud jsou že servery Azure Backup zaregistrované do trezoru služby Recovery Services, počkejte po instalaci zahájit proces obnovení za den. Noční úlohu ukládání metadat pro všechny chráněné zálohování do cloudu. Data budou k dispozici pro obnovení. |
| 4. |Zadané šifrovací heslo neodpovídá heslu přidruženému k následujícímu serveru:  **\<název serveru >** |**Příčina:** Šifrovací heslo použít Probíhá šifrování dat od Azure Backup Server, který se obnovuje neodpovídá zadané šifrovací heslo. Agent se nemůže data dešifrovat. Proto obnovení není úspěšné.<br>**Řešení:** Zadejte prosím přesné stejné šifrovací heslo přidružené k Azure Backup serveru, jehož data obnovena. |

## <a name="next-steps"></a>Další kroky

Přečtěte si další nejčastější dotazy:

- [Běžné otázky](backup-azure-vm-backup-faq.md) o záloh virtuálních počítačů Azure
- [Běžné otázky](backup-azure-file-folder-backup-faq.md) o agenta Azure Backup
